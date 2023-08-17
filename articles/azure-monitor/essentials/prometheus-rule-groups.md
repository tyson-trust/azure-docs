---
title: Rule groups in Azure Monitor Managed Service for Prometheus
description: Description of rule groups in Azure Monitor managed service for Prometheus which alerting and data computation.
author: EdB-MSFT
ms-author: edbaynash
ms.topic: conceptual
ms.custom: ignite-2022
ms.date: 09/28/2022
---

# Azure Monitor managed service for Prometheus rule groups
Rules in Prometheus act on data as it's collected. They're configured as part of a Prometheus rule group, which is stored in [Azure Monitor workspace](azure-monitor-workspace-overview.md). Rules are run sequentially in the order they're defined in the group.


## Rule types
There are two types of Prometheus rules as described in the following table.

| Type | Description |
|:---|:---|
| Alert |[Alert rules ](https://aka.ms/azureprometheus-promio-alertrules)let you create an Azure Monitor alert based on the results of a Prometheus Query Language (Prom QL) query.  Alerts fired by Azure Managed Prometheus alert rules are processed and trigger notifications in similar way to other Azure Monitor alerts.|
| Recording |[Recording rules](https://aka.ms/azureprometheus-promio-recrules) allow you to precompute frequently needed or computationally extensive expressions and store their result as a new set of time series. Time series created by recording rules are ingested back to your Azure Monitor workspace as new Prometheus metrics. |

## Create Prometheus rules
Azure Managed Prometheus rule groups, recording rules and alert rules can be created and configured using The Azure resource type **Microsoft.AlertsManagement/prometheusRuleGroups**, where the alert rules and recording rules are defined as part of the rule group properties.Prometheus rule groups are defined with a scope of a specific [Azure Monitor workspace](azure-monitor-workspace-overview.md).  Prometheus rule groups can be created using Azure Resource Manager (ARM) templates, API, Azure CLI, or PowerShell. 

> [!NOTE]
> For your AKS or ARC Kubernetes clusters, you can use some of the recommended alerts rules. See pre-defined alert rules [here](../containers/container-insights-metric-alerts.md#enable-prometheus-alert-rules).

### Limiting rules to a specific cluster

You can optionally limit the rules in a rule group to query data originating from a specific cluster, using the rule group `clusterName` property.
You should limit rules to a single cluster if your Azure Monitor workspace contains a large amount of data from multiple clusters. In such a case, there's a concern that running a single set of rules on all the data may cause performance or throttling issues. By using the `clusterName` property, you can create multiple rule groups, each configured with the same rules, and therefore limit each group to cover a different cluster. 

- The `clusterName` value must be identical to the `cluster` label that is added to the metrics from a specific cluster during data collection.
- If `clusterName` isn't specified for a specific rule group, the rules in the group query all the data in the workspace from all clusters.

### Creating Prometheus rule group using Resource Manager template

You can use a Resource Manager template to create and configure Prometheus rule groups, alert rules, and recording rules. Resource Manager templates enable you to programmatically create and configure rule groups in a consistent and reproducible way across all your environments.

The basic steps are as follows:

1. Use the following template as a JSON file that describes how to create the rule group.
2. Deploy the template using any deployment method, such as [Azure portal](../../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md), or [Rest API](../../azure-resource-manager/templates/deploy-rest.md).

### Template example for a Prometheus rule group
Following is a sample template that creates a Prometheus rule group, including one recording rule and one alert rule. This template creates a resource of type `Microsoft.AlertsManagement/prometheusRuleGroups`. The rules are executed in the order they appear within a group. 

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
           "name": "sampleRuleGroup",
           "type": "Microsoft.AlertsManagement/prometheusRuleGroups",
           "apiVersion": "2023-03-01",
           "location": "northcentralus",
           "properties": {
                "description": "Sample Prometheus Rule Group",
                "scopes": [
                    "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.monitor/accounts/<azure-monitor-workspace-name>"
                ],
                "enabled": true,
                "clusterName": "<myCLusterName>",
                "interval": "PT1M",
                "rules": [
                    {
                        "record": "instance:node_cpu_utilisation:rate5m",
                        "expression": "1 - avg without (cpu) (sum without (mode)(rate(node_cpu_seconds_total{job=\"node\", mode=~\"idle|iowait|steal\"}[5m])))",
                        "labels": {
                            "workload_type": "job"
                        },
                        "enabled": true
                    },
                    {
                        "alert": "KubeCPUQuotaOvercommit",
                        "expression": "sum(min without(resource) (kube_resourcequota{job=\"kube-state-metrics\", type=\"hard\", resource=~\"(cpu|requests.cpu)\"})) /  sum(kube_node_status_allocatable{resource=\"cpu\", job=\"kube-state-metrics\"}) > 1.5",
                        "for": "PT5M",
                        "labels": {
                            "team": "prod"
                        },
                        "annotations": {
                            "description": "Cluster has overcommitted CPU resource requests for Namespaces.",
                            "runbook_url": "https://github.com/kubernetes-monitoring/kubernetes-mixin/tree/master/runbook.md#alert-name-kubecpuquotaovercommit",
                            "summary": "Cluster has overcommitted CPU resource requests."
                        },
                        "enabled": true,
                        "severity": 3,
                        "resolveConfiguration": {
                            "autoResolved": true,
                            "timeToResolve": "PT10M"
                        },
                        "actions": [
                            {
                               "actionGroupId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/actiongroups/<action-group-name>"
                            }
                        ]
                    }
                ]
            }
        }
    ]
}        
```

The following tables describe each of the properties in the rule definition.

### Rule group
The rule group contains the following properties.

| Name | Required | Type | Description |
|:---|:---|:---|:---|
| `name` | True | string | Prometheus rule group name |
| `type` | True | string | `Microsoft.AlertsManagement/prometheusRuleGroups` |
| `apiVersion` | True | string | `2023-03-01` |
| `location` | True | string | Resource location from regions supported in the preview |
| `properties.description` | False | string | Rule group description |
| `properties.scopes` | True | string[] | Target Azure Monitor workspace. Only one scope currently supported |
| `properties.enabled` | False | boolean | Enable/disable group. Default is true. |
| `properties.clusterName` | False | string | Apply rule to data from a specific cluster. Default is apply to all data in workspace. |
| `properties.interval` | False | string | Group evaluation interval. Default = PT1M |

### Recording rules
The `rules` section contains the following properties for recording rules.

| Name | Required | Type | Description |
|:---|:---|:---|:---|
| `record` | True | string | Recording rule name. This name is used for the new time series. |
| `expression` | True | string | PromQL expression to calculate the new time series value. |
| `labels` | True | string | Prometheus rule labels key-value pairs. These labels are added to the recorded time series. |
| `enabled` | False | boolean | Enable/disable group. Default is true. |

### Alerting rules
The `rules` section contains the following properties for alerting rules.

| Name | Required | Type | Description | Notes |
|:---|:---|:---|:---|:---|
| `alert` | False | string | Alert rule name  |
| `expression` | True | string | PromQL expression to evaluate. |
| `for` | False | string | Alert firing timeout. Values - 'PT1M', 'PT5M' etc. |
| `labels` | False | object | labels key-value pairs | Prometheus alert rule labels. These labels are added to alerts fired by this rule. |
| `rules.annotations` | False | object | Annotations key-value pairs to add to the alert. |
| `enabled` | False | boolean | Enable/disable group. Default is true. |
| `rules.severity` | False | integer | Alert severity. 0-4, default is 3 (informational) |
| `rules.resolveConfigurations.autoResolved` | False | boolean | When enabled, the alert is automatically resolved when the condition is no longer true. Default = true |
| `rules.resolveConfigurations.timeToResolve` | False | string | Alert auto resolution timeout. Default = "PT5M" |
| `rules.action[].actionGroupId` | false | string | One or more action group resource IDs. Each is activated when an alert is fired. |

### Converting Prometheus rules file to a Prometheus rule group ARM template

If you have a [Prometheus rules configuration file](https://prometheus.io/docs/prometheus/latest/configuration/recording_rules/#configuring-rules) (in YAML format), you can now convert it to an Azure Prometheus rule group ARM template, using the [az-prom-rules-converter utility](https://github.com/Azure/prometheus-collector/tree/main/tools/az-prom-rules-converter#az-prom-rules-converter). The rules file can contain definition of one or more rule groups.

In addition to the rules file, you can provide the utility with additional properties that are needed to create the Azure Prometheus rule groups, including: subscription, resource group, location, target Azure Monitor workspace, target cluster name, and action groups (used for alert rules). The utility creates a template file that can be deployed directly or within a deployment pipe providing some of these properties as parameters. Note that properties provided to the utility are used for all the rule groups in the template, e.g., all rule groups in the file will be created in the same subscription/resource group/location, using the same Azure Monitor workspace, etc. If an action group is provided as a parameter to the utility, the same action group will be used in all the alert rules in the template. If you want to change this default configuration (e.g., use different action groups in different rules) you can edit the resulting template according to your needs, before deploying it.

> [!NOTE] 
> !The az-prom-convert-utility is provided as a courtesy tool. We recommend that you review the resulting template and verify it matches your intended configuration.

### Creating Prometheus rule group using Azure CLI

You can use Azure CLI to create and configure Prometheus rule groups, alert rules, and recording rules. The following code examples use [Azure Cloud Shell](../../cloud-shell/overview.md). 

1. In the [portal](https://portal.azure.com/), select **Cloud Shell**. At the prompt, use the commands that follow.

2. To create a Prometheus rule group, use the `az alerts-management prometheus-rule-group create` command. You can see detailed documentation on the Prometheus rule group create command in the `az alerts-management prometheus-rule-group create` section of the [Azure CLI commands for creating and managing Prometheus rule groups](/cli/azure/alerts-management/prometheus-rule-group#commands).

Example: Create a new Prometheus rule group with rules

```azurecli
 az alerts-management prometheus-rule-group create -n TestPrometheusRuleGroup -g TestResourceGroup -l westus --enabled --description "test" --interval PT10M --scopes "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.monitor/accounts/testaccount" --rules [{"record":"test","expression":"test","labels":{"team":"prod"}},{"alert":"Billing_Processing_Very_Slow","expression":"test","enabled":"true","severity":2,"for":"PT5M","labels":{"team":"prod"},"annotations":{"annotationName1":"annotationValue1"},"resolveConfiguration":{"autoResolved":"true","timeToResolve":"PT10M"},"actions":[{"actionGroupId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.insights/actionGroups/test-action-group-name1","actionProperties":{"key11":"value11","key12":"value12"}},{"actionGroupId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.insights/actionGroups/test-action-group-name2","actionProperties":{"key21":"value21","key22":"value22"}}]}]
```

### Create a new Prometheus rule group with PowerShell

To create a Prometheus rule group using PowerShell, use the [new-azprometheusrulegroup](/powershell/module/az.alertsmanagement/new-azprometheusrulegroup) cmdlet.

Example: Create Prometheus rule group definition with rules.

```powershell
$rule1 = New-AzPrometheusRuleObject -Record "job_type:billing_jobs_duration_seconds:99p5m"
$action =  New-AzPrometheusRuleGroupActionObject -ActionGroupId /subscriptions/fffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/MyresourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup -ActionProperty @{"key1" = "value1"}
$Timespan = New-TimeSpan -Minutes 15
$rule2 = New-AzPrometheusRuleObject -Alert Billing_Processing_Very_Slow -Expression "job_type:billing_jobs_duration_seconds:99p5m > 30" -Enabled $false -Severity 3 -For $Timespan -Label @{"team"="prod"} -Annotation @{"annotation" = "value"} -ResolveConfigurationAutoResolved $true -ResolveConfigurationTimeToResolve $Timespan -Action $action
$rules = @($rule1, $rule2)
$scope = "/subscriptions/fffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/MyresourceGroup/providers/microsoft.monitor/accounts/MyAccounts"
New-AzPrometheusRuleGroup -ResourceGroupName MyresourceGroup -RuleGroupName MyRuleGroup -Location eastus -Rule $rules -Scope $scope -Enabled
```

## View Prometheus rule groups
You can view the rule groups and their included rules in the Azure portal by selecting **Rule groups** from the Azure Monitor workspace.

:::image type="content" source="media/prometheus-metrics-rule-groups/azure-monitor-workspace-rule-groups.png" lightbox="media/prometheus-metrics-rule-groups/azure-monitor-workspace-rule-groups.png"  alt-text="Screenshot of rule groups in an Azure Monitor workspace.":::

## Disable and enable rules
To enable or disable a rule, select the rule in the Azure portal. Select either **Enable** or **Disable** to change its status.

:::image type="content" source="media/prometheus-metrics-rule-groups/enable-rule.png" lightbox="media/prometheus-metrics-rule-groups/enable-rule.png"  alt-text="Screenshot of Prometheus rule detail with enable option.":::

> [!NOTE] 
> After you disable or re-enable a rule or a rule group, it may take few minutes for the rule group list to reflect the updated status of the rule or the group.


## Next steps

- [Learn more about the Azure alerts](../alerts/alerts-types.md).
- [Prometheus documentation for recording rules](https://aka.ms/azureprometheus-promio-recrules).
- [Prometheus documentation for alerting rules](https://aka.ms/azureprometheus-promio-alertrules).


