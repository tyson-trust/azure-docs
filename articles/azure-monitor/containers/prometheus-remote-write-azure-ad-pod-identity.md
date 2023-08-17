---
title: Configure remote write for Azure Monitor managed service for Prometheus using Microsoft Azure Active Directory pod identity (preview) 
description: Configure remote write for Azure Monitor managed service for Prometheus using Azure AD pod identity (preview) 
author: EdB-MSFT
ms.author: edbaynash
ms.topic: conceptual
ms.date: 05/11/2023
ms.reviewer: rapadman
---

# Configure remote write for Azure Monitor managed service for Prometheus using Microsoft Azure Active Directory pod identity (preview) 


> [!NOTE] 
> The remote write sidecar should only be configured via the following steps only if the AKS cluster already has the Azure AD pod enabled. This approach is not recommended as AAD pod identity has been deprecated to be replace by [Azure Workload Identity](/azure/active-directory/workload-identities/workload-identities-overview)


To configure remote write for Azure Monitor managed service for Prometheus using Azure AD pod identity, follow the steps below.

1. Create user assigned identity or use an existing user assigned managed identity. For information on creating the managed identity, see [Configure remote write for Azure Monitor managed service for Prometheus using managed identity authentication](./prometheus-remote-write-managed-identity.md#get-the-client-id-of-the-user-assigned-identity).
1. Assign the `Managed Identity Operator` and `Virtual Machine Contributor` roles to the managed identity created/used in the previous step.

    ```azurecli
    az role assignment create --role "Managed Identity Operator" --assignee <managed identity clientID> --scope <NodeResourceGroupResourceId> 
          
    az role assignment create --role "Virtual Machine Contributor" --assignee <managed identity clientID> --scope <Node ResourceGroup Id> 
    ```	 

    The node resource group of the AKS cluster contains resources that you will require for other steps in this process. This resource group has the name MC_\<AKS-RESOURCE-GROUP\>_\<AKS-CLUSTER-NAME\>_\<REGION\>. You can locate it from the Resource groups menu in the Azure portal.

1. Grant user-assigned managed identity `Monitoring Metrics Publisher` roles.

    ```azurecli
    az role assignment create --role "Monitoring Metrics Publisher" --assignee <managed identity clientID> --scope <NodeResourceGroupResourceId> 
    ```

1. Create AzureIdentityBinding 

    The user assigned managed identity requires identity binding in order to be used as a pod identity. Run the following commands: 
 
    Copy the following YAML to the `aadpodidentitybinding.yaml` file.

    ```yml

    apiVersion: "aadpodidentity.k8s.io/v1" 

    kind: AzureIdentityBinding 
    metadata: 
     name: demo1-azure-identity-binding 
    spec: 
     AzureIdentity: “<AzureIdentityName>” 
     Selector: “<AzureIdentityBindingSelector>” 
     ```

    Run the following command:

    ```azurecli
    kubectl create -f aadpodidentitybinding.yaml 
    ```
 
1. Add a `aadpodidbinding` label to the Prometheus pod.  
    The `aadpodidbinding` label must be added to the Prometheus pod for the pod identity to take effect. This can be achieved by updating the `deployment.yaml` or injecting labels while deploying the sidecar as mentioned in the next step.

1. Deploy side car and configure remote write on the Prometheus server.

      1. Copy the YAML below and save to a file.  

    ```yml
    prometheus: 
      prometheusSpec: 
        podMetadata: 
          labels: 
            aadpodidbinding: <AzureIdentityBindingSelector> 
        externalLabels: 
          cluster: <AKS-CLUSTER-NAME> 
        remoteWrite: 
        - url: 'http://localhost:8081/api/v1/write' 
        containers: 
        - name: prom-remotewrite 
          image: <CONTAINER-IMAGE-VERSION> 
          imagePullPolicy: Always 
          ports: 
            - name: rw-port 
          containerPort: 8081 
          livenessProbe: 
            httpGet: 
              path: /health
              port: rw-port
              initialDelaySeconds: 10 
              timeoutSeconds: 10 
          readinessProbe: 
             httpGet: 
              path: /ready
              port: rw-port
              initialDelaySeconds: 10 
              timeoutSeconds: 10 
        env: 
          - name: INGESTION_URL 
            value: <INGESTION_URL> 
          - name: LISTENING_PORT 
            value: '8081' 
          - name: IDENTITY_TYPE 
            value: userAssigned 
          - name: AZURE_CLIENT_ID 
            value: <MANAGED-IDENTITY-CLIENT-ID> 
          # Optional parameter 
          - name: CLUSTER 
            value: <CLUSTER-NAME>         
    ```

      b. Use helm to apply the YAML file to update your Prometheus configuration with the following CLI commands.  
      
    ```azurecli
    # set context to your cluster 
    az aks get-credentials -g <aks-rg-name> -n <aks-cluster-name> 
    # use helm to update your remote write config 
    helm upgrade -f <YAML-FILENAME>.yml prometheus prometheus-community/kube-prometheus-stack --namespace <namespace where Prometheus pod resides>
    ```
