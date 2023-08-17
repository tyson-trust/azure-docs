---
author: karlerickson
ms.author: v-shilichen
ms.service: spring-apps
ms.custom: event-tier1-build-2022
ms.topic: include
ms.date: 08/09/2023
---

<!-- 
For clarity of structure, a separate markdown file is used to describe how to deploy to Azure Spring Apps with Basic/Standard plan.

[!INCLUDE [deploy-app-with-basic-standard-plan](includes/quickstart/deploy-app-with-basic-standard-plan.md)]

-->

## 2. Prepare the Spring project

### [Azure portal](#tab/Azure-portal)

[!INCLUDE [prepare-spring-project](../../includes/quickstart/prepare-spring-project.md)]

### [Azure Developer CLI](#tab/Azure-Developer-CLI)

Use the following steps to initialize the application from the Azure Developer CLI templates.

1. Open a terminal window, create a new, empty folder, and change directory into it.
1. Use the following command to initialize the project:

    ```bash
    azd init --template spring-guides/gs-spring-boot-for-azure
    ```

   The following list describes the command interactions:

   - **OAuth2 login**: You need to authorize the login to Azure based on the OAuth2 protocol.
   - **Please enter a new environment name**: Provide an environment name, which is used as a suffix for the resource group created to hold all Azure resources. This name should be unique within your Azure subscription.

   The console outputs messages similar the ones in the following example:

   ```output
   Initializing a new project (azd init)
   
   (✓) Done: Initialized git repository
   (✓) Done: Downloading template code to: <your-local-path>
   Enter a new environment name: <your-env-name>
   SUCCESS: New project initialized!
   You can view the template code in your directory: <your-local-path>
   Learn more about running 3rd party code on our DevHub: https://aka.ms/azd-third-party-code-notice
   ```

---

## 3. Prepare the cloud environment

The main resource you need to run this sample is an Azure Spring Apps instance. Use the following steps to create this resource.

### [Azure portal](#tab/Azure-portal)

### 3.1. Sign in to the Azure portal

Open your web browser and go to the [Azure portal](https://portal.azure.com/). Enter your credentials to sign in to the portal. The default view is your service dashboard.

### 3.2. Create an Azure Spring Apps instance

[!INCLUDE [provision-spring-apps](../../includes/quickstart/provision-basic-azure-spring-apps.md)]

### [Azure Developer CLI](#tab/Azure-Developer-CLI)

1. Use the following command to log in Azure with OAuth2. Ignore this step if you've already logged in.

   ```bash
   azd auth login
   ```

1. Use the following command to enable the Azure Spring Apps feature:

   ```bash
   azd config set alpha.springapp on
   ```

1. Use the following command to set the template using the Standard plan:

   ```bash
   azd env set PLAN standard
   ```

1. Use the following command to package a deployable copy of your application, provision the template's infrastructure to Azure, and deploy the application code to those newly provisioned resources:

   ```bash
   azd provision
   ```

   The following list describes the command interactions:

   - **Select an Azure Subscription to use**: Use arrows to move, type to filter, then press <kbd>Enter</kbd>.
   - **Select an Azure location to use**: Use arrows to move, type to filter, then press <kbd>Enter</kbd>.

   The console outputs messages similar to the ones in the following example:

   ```output
   SUCCESS: Your application was provisioned in Azure in xx minutes xx seconds.
   You can view the resources created under the resource group rg-<your-environment-name>-<random-string>> in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group>/overview
   ```

   > [!NOTE]
   > This may take a while to complete. You see a progress indicator as it provisions Azure resources.

---

## 4. Deploy the app to Azure Spring Apps

This section provides the steps to deploy your application to Azure Spring Apps.

### [Azure portal](#tab/Azure-portal)

Use the following steps to deploy using the [Maven plugin for Azure Spring Apps](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Spring-Apps):

1. Navigate to the *complete* directory and then run the following command to configure the app in Azure Spring Apps:

   ```bash
   ./mvnw com.microsoft.azure:azure-spring-apps-maven-plugin:1.17.0:config
   ```

   The following list describes the command interactions:

   - **OAuth2 login**: You need to authorize the login to Azure based on the OAuth2 protocol.
   - **Select subscription**: Select the subscription list number of the Azure Spring Apps instance you created, which defaults to the first subscription in the list. If you use the default number, press <kbd>Enter</kbd> directly.
   - **Select Azure Spring Apps for deployment**: Select the list number of the Azure Spring Apps instance you created. If you use the default number, press <kbd>Enter</kbd> directly.
   - **Input the app name**: Provide an app name. If you use the default project artifact ID, press <kbd>Enter</kbd> directly.
   - **Expose public access for this app (boot-for-azure)?**: Press <kbd>y</kbd>.
   - **Confirm to save all the above configurations (Y/n)**: Press <kbd>y</kbd>. If you press <kbd>n</kbd>, the configuration isn't saved in the POM files.

1. Use the following command to deploy the app:

   ```bash
   ./mvnw com.microsoft.azure:azure-spring-apps-maven-plugin:1.17.0:deploy
   ```

   The following list describes the command interactions:

   - **OAuth2 login**: You need to authorize the login to Azure based on the OAuth2 protocol.

   After the command is executed, you can see from the following log messages that the deployment was successful:

   ```output
   [INFO] Deployment(default) is successfully updated.
   [INFO] Deployment Status: Running
   [INFO]   InstanceName:demo-default-x-xxxxxxxxxx-xxxxx  Status:Running Reason:null       DiscoverStatus:UNREGISTERED
   [INFO] Getting public url of app(demo)...
   [INFO] Application url: https://<your-Azure-Spring-Apps-instance-name>-demo.azuremicroservices.io
   ```

### [Azure Developer CLI](#tab/Azure-Developer-CLI)

Use the following steps to package the app, provision the Azure resources required by the web application, and then deploy to Azure Spring Apps:

1. Use the following command to package a deployable copy of your application:

   ```bash
   azd package
   ```

1. Use the following command to deploy the application code to those newly provisioned resources:

   ```bash
   azd deploy
   ```

   The console outputs messages similar to the ones in the following example:

   ```output
   Deploying services (azd deploy)
   
   WARNING: Feature 'springapp' is in alpha stage.
   To learn more about alpha features and their support, visit https://aka.ms/azd-feature-stages.
   
   ...
   
   Deploying service demo (Fetching endpoints for spring app service)
   - Endpoint: https://<your-Azure-Spring-Apps-instance-name>-demo.azuremicroservices.io/
   
   
   SUCCESS: Your application was deployed to Azure in xx minutes xx seconds.
   You can view the resources created under the resource group rg-<your-environment-name> in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/<your-subscription-id>/resourceGroups/rg-<your-environment-name>/overview
   ```

> [!NOTE]
> You can also use `azd up` to combine the previous three commands: `azd package` (packages a deployable copy of your application), `azd provision` (provisions Azure resources), and `azd deploy` (deploys application code). For more information, see [spring-guides/gs-spring-boot-for-azure](https://github.com/spring-guides/gs-spring-boot-for-azure).

---
