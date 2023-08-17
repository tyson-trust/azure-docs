---
title: Use Planned Maintenance to schedule and control upgrades for your Azure Kubernetes Service (AKS) cluster (preview)
titleSuffix: Azure Kubernetes Service
description: Learn how to use Planned Maintenance to schedule and control cluster and node image upgrades in Azure Kubernetes Service (AKS).
ms.topic: article
ms.custom: devx-track-azurecli
ms.date: 01/17/2023
ms.author: nickoman
author: nickomang
---

# Use Planned Maintenance to schedule and control upgrades for your Azure Kubernetes Service (AKS) cluster (preview)

Your AKS cluster has regular maintenance performed on it automatically. By default, this work can happen at any time. Planned Maintenance allows you to schedule weekly maintenance windows to perform updates and minimize workload impact. Once scheduled, upgrades occur only during the window you selected.

There are currently three available configuration types: `default`, `aksManagedAutoUpgradeSchedule`, `aksManagedNodeOSUpgradeSchedule`:

- `default` corresponds to a basic configuration that is mostly suitable for basic scheduling of [weekly releases][release-tracker].

- `aksManagedAutoUpgradeSchedule` controls when cluster upgrades scheduled by your designated auto-upgrade channel are performed. More finely controlled cadence and recurrence settings are possible than in a `default` configuration. For more information on cluster auto-upgrade, see [Automatically upgrade an Azure Kubernetes Service (AKS) cluster][aks-upgrade].

- `aksManagedNodeOSUpgradeSchedule` controls when node operating system upgrades scheduled by your node OS auto-upgrade channel are performed. More finely controlled cadence and recurrence settings are possible than in a `default configuration. For more information on node OS auto-upgrade, see [Automatically patch and update AKS cluster node images][node-image-auto-upgrade]

We recommend using `aksManagedAutoUpgradeSchedule` for all cluster upgrade scenarios and `aksManagedNodeOSUpgradeSchedule` for all node image upgrade scenarios, while `default` is meant exclusively for weekly releases. You can port `default` configurations to `aksManagedAutoUpgradeSchedule` configurations via the `az aks maintenanceconfiguration update` command.

To configure Planned Maintenance using pre-created configurations, see [Use Planned Maintenance pre-created configurations to schedule AKS weekly releases][pm-weekly].

## Before you begin

This article assumes that you have an existing AKS cluster. If you need an AKS cluster, see the AKS quickstart [using the Azure CLI][aks-quickstart-cli], [using Azure PowerShell][aks-quickstart-powershell], or [using the Azure portal][aks-quickstart-portal].

Be sure to upgrade Azure CLI to the latest version using [`az upgrade`](/cli/azure/update-azure-cli#manual-update).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### Limitations

When you use Planned Maintenance, the following restrictions apply:

- AKS reserves the right to break these windows for unplanned/reactive maintenance operations that are urgent or critical.
- Currently, performing maintenance operations are considered *best-effort only* and aren't guaranteed to occur within a specified window.
- Updates can't be blocked for more than seven days.

### Install aks-preview CLI extension

You also need the *aks-preview* Azure CLI extension version 0.5.124 or later. Install the *aks-preview* Azure CLI extension by using the [az extension add][az-extension-add] command. Or install any available updates by using the [az extension update][az-extension-update] command.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## Creating a maintenance window

To create a maintenance window, you can use the `az aks maintenanceconfiguration add` command using the  `--name` value `default`, `aksManagedAutoUpgradeSchedule`, or `aksManagedNodeOSUpgradeSchedule`. The name value should reflect the desired configuration type. Using any other name will cause your maintenance window not to run.

> [!NOTE]
> When using auto-upgrade, to ensure proper functionality, use a maintenance window with a duration of four hours or more.

Planned Maintenance windows are specified in Coordinated Universal Time (UTC).

A `default` maintenance window has the following properties:

|Name|Description|Default value|
|--|--|--|
|`timeInWeek`|In a `default` configuration, this property contains the `day` and `hourSlots` values defining a maintenance window|N/A|
|`timeInWeek.day`|The day of the week to perform maintenance in a `default` configuration|N/A|
|`timeInWeek.hourSlots`|A list of hour-long time slots to perform maintenance on a given day in a `default` configuration|N/A|
|`notAllowedTime`|Specifies a range of dates that maintenance can't run, determined by `start` and `end` child properties. Only applicable when creating the maintenance window using a config file|N/A|

An `aksManagedAutoUpgradeSchedule` or `aksManagedNodeOSUpgradeSchedule` maintenance window has the following properties:

|Name|Description|Default value|
|--|--|--|
|`utcOffset`|Used to determine the timezone for cluster maintenance|`+00:00`|
|`startDate`|The date on which the maintenance window begins to take effect|The current date at creation time|
|`startTime`|The time for maintenance to begin, based on the timezone determined by `utcOffset`|N/A|
|`schedule`|Used to determine frequency. Three types are available: `Weekly`, `AbsoluteMonthly`, and `RelativeMonthly`|N/A|
|`intervalDays`|The interval in days for maintenance runs. Only applicable to `aksManagedNodeOSUpgradeSchedule`|N/A|
|`intervalWeeks`|The interval in weeks for maintenance runs|N/A|
|`intervalMonths`|The interval in months for maintenance runs|N/A|
|`dayOfWeek`|The specified day of the week for maintenance to begin|N/A|
|`durationHours`|The duration of the window for maintenance to run|N/A|
|`notAllowedDates`|Specifies a range of dates that maintenance cannot run, determined by `start` and `end` child properties. Only applicable when creating the maintenance window using a config file|N/A|

### Understanding schedule types

There are currently four available schedule types: `Daily`, `Weekly`, `AbsoluteMonthly`, and `RelativeMonthly`. These schedule types are only applicable to `aksManagedClusterAutoUpgradeSchedule` and `aksManagedNodeOSUpgradeSchedule` configurations. `Daily` schedules are only applicable to `aksManagedNodeOSUpgradeSchedule` types.

> [!NOTE]
> All of the fields shown for each respective schedule type are required.

#### Daily schedule

> [!NOTE]
> Daily schedules are only applicable to `aksManagedNodeOSUpgradeSchedule` configuration types.

A `Daily` schedule may look like *"every three days"*:

```json
"schedule": {
    "daily": {
        "intervalDays": 2
    }
}
```

#### Weekly schedule

A `Weekly` schedule may look like *"every two weeks on Friday"*:

```json
"schedule": {
    "weekly": {
        "intervalWeeks": 2,
        "dayOfWeek": "Friday"
    }
}
```

#### AbsoluteMonthly schedule

An `AbsoluteMonthly` schedule may look like *"every three months, on the first day of the month"*:

```json
"schedule": {
    "absoluteMonthly": {
        "intervalMonths": 3,
        "dayOfMonth": 1
    }
}
```

#### RelativeMonthly schedule

A `RelativeMonthly` schedule may look like *"every two months, on the last Monday"*:

```json
"schedule": {
    "relativeMonthly": {
        "intervalMonths": 2,
        "dayOfWeek": "Monday",
        "weekIndex": "Last"
    }
}
```

Valid values for `weekIndex` are `First`, `Second`, `Third`, `Fourth`, and `Last`.

## Add a maintenance window configuration with Azure CLI

The following example shows a command to add a new `default` configuration that schedules maintenance to run from 1:00am to 2:00am every Monday:

```azurecli-interactive
az aks maintenanceconfiguration add -g myResourceGroup --cluster-name myAKSCluster --name default --weekday Monday --start-hour 1
```

> [!NOTE]
> When using a `default` configuration type, to allow maintenance anytime during a day omit the `--start-time` parameter.

The following example shows a command to add a new `aksManagedAutoUpgradeSchedule` configuration that schedules maintenance to run every third Friday between 12:00 AM and 8:00 AM in the `UTC+5:30` timezone:

```azurecli-interactive
az aks maintenanceconfiguration add -g myResourceGroup --cluster-name myAKSCluster -n aksManagedAutoUpgradeSchedule --schedule-type Weekly --day-of-week Friday --interval-weeks 3 --duration 8 --utc-offset +05:30 --start-time 00:00
```

## Add a maintenance window configuration with a JSON file

You can also use a JSON file create a maintenance configuration instead of using parameters. This method has the added benefit of allowing maintenance to be prevented during a range of dates, specified by `notAllowedTimes` for `default` configurations and `notAllowedDates` for `aksManagedAutoUpgradeSchedule` configurations.

Create a `default.json` file with the following contents:

```json
{
  "timeInWeek": [
    {
      "day": "Tuesday",
      "hour_slots": [
        1,
        2
      ]
    },
    {
      "day": "Wednesday",
      "hour_slots": [
        1,
        6
      ]
    }
  ],
  "notAllowedTime": [
    {
      "start": "2021-05-26T03:00:00Z",
      "end": "2021-05-30T12:00:00Z"
    }
  ]
}
```

The above JSON file specifies maintenance windows every Tuesday at 1:00am - 3:00am and every Wednesday at 1:00am - 2:00am and at 6:00am - 7:00am in the `UTC` timezone. There's also an exception from *2021-05-26T03:00:00Z* to *2021-05-30T12:00:00Z* where maintenance isn't allowed even if it overlaps with a maintenance window.

Create an `autoUpgradeWindow.json` file with the following contents:

```json
{
  "properties": {
    "maintenanceWindow": {
        "schedule": {
            "absoluteMonthly": {
                "intervalMonths": 3,
                "dayOfMonth": 1
            }
        },
        "durationHours": 4,
        "utcOffset": "-08:00",
        "startTime": "09:00",
        "notAllowedDates": [
            {
                "start": "2023-12-23",
                "end": "2024-01-05"
            }
        ]
    }
  }
}
```

The above JSON file specifies maintenance windows every three months on the first of the month between 9:00 AM - 1:00 PM in the `UTC-08` timezone. There's also an exception from *2023-12-23* to *2024-01-05* where maintenance isn't allowed even if it overlaps with a maintenance window.

The following command adds the maintenance windows from `default.json` and `autoUpgradeWindow.json`:

```azurecli-interactive
az aks maintenanceconfiguration add -g myResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json

az aks maintenanceconfiguration add -g myResourceGroup --cluster-name myAKSCluster --name aksManagedAutoUpgradeSchedule --config-file ./autoUpgradeWindow.json
```

## Update an existing maintenance window

To update an existing maintenance configuration, use the `az aks maintenanceconfiguration update` command.

```azurecli-interactive
az aks maintenanceconfiguration update -g myResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 2
```

## List all maintenance windows in an existing cluster

To see all current maintenance configuration windows in your AKS cluster, use the `az aks maintenanceconfiguration list` command.

```azurecli-interactive
az aks maintenanceconfiguration list -g myResourceGroup --cluster-name myAKSCluster
```

## Show a specific maintenance configuration window in an AKS cluster

To see a specific maintenance configuration window in your AKS Cluster, use the `az aks maintenanceconfiguration show` command.

```azurecli-interactive
az aks maintenanceconfiguration show -g myResourceGroup --cluster-name myAKSCluster --name aksManagedAutoUpgradeSchedule
```

The following example output shows the maintenance window for *aksManagedAutoUpgradeSchedule*:

```json
{
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/aksManagedAutoUpgradeSchedule",
  "maintenanceWindow": {
    "durationHours": 4,
    "notAllowedDates": [
      {
        "end": "2024-01-05",
        "start": "2023-12-23"
      }
    ],
    "schedule": {
      "absoluteMonthly": {
        "dayOfMonth": 1,
        "intervalMonths": 3
      },
      "daily": null,
      "relativeMonthly": null,
      "weekly": null
    },
    "startDate": "2023-01-20",
    "startTime": "09:00",
    "utcOffset": "-08:00"
  },
  "name": "aksManagedAutoUpgradeSchedule",
  "notAllowedTime": null,
  "resourceGroup": "myResourceGroup",
  "systemData": null,
  "timeInWeek": null,
  "type": null
}
```

## Delete a certain maintenance configuration window in an existing AKS Cluster

To delete a certain maintenance configuration window in your AKS Cluster, use the `az aks maintenanceconfiguration delete` command.

```azurecli-interactive
az aks maintenanceconfiguration delete -g myResourceGroup --cluster-name myAKSCluster --name autoUpgradeSchedule
```

## Next steps

- To get started with upgrading your AKS cluster, see [Upgrade an AKS cluster][aks-upgrade]

<!-- LINKS - Internal -->
[aks-quickstart-cli]: ./learn/quick-kubernetes-deploy-cli.md
[aks-quickstart-portal]: ./learn/quick-kubernetes-deploy-portal.md
[aks-quickstart-powershell]: ./learn/quick-kubernetes-deploy-powershell.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[release-tracker]: release-tracker.md
[auto-upgrade]: auto-upgrade-cluster.md
[node-image-auto-upgrade]: auto-upgrade-node-image.md
[pm-weekly]: ./aks-planned-maintenance-weekly-releases.md
