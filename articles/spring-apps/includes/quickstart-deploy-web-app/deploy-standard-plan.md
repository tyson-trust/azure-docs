---
author: KarlErickson
ms.author: xiada
ms.service: spring-apps
ms.topic: include
ms.date: 07/11/2023
---

<!--
For clarity of structure, a separate markdown file is used to describe how to deploy to Azure Spring Apps with standard plan.

[!INCLUDE [deploy-to-azure-spring-apps-standard-plan](includes/quickstart-deploy-web-app/deploy-standard-plan.md)]

-->

## 2. Prepare the Spring project

First, prepare the Spring project to run locally.

### [Azure portal](#tab/Azure-portal)

Although you use the Azure portal in later steps, you must use the Bash command line to prepare the project locally. Use the following steps to clone and run the app locally. These steps use the 

1. Use the following command to clone the sample project from GitHub:

   ```bash
   git clone https://github.com/Azure-Samples/ASA-Samples-Web-Application.git
   ```

1. Use the following command to build the sample project:

   ```bash
   cd ASA-Samples-Web-Application
   ./mvnw clean package
   ```

1. Use the following command to run the sample application by using Maven:

   ```bash
   java -jar web/target/simple-todo-web-0.0.2-SNAPSHOT.jar
   ```

1. Go to `http://localhost:8080` in your browser to access the application.

### [Azure Developer CLI](#tab/Azure-Developer-CLI)

Use the following steps to initialize the web application from the Azure Developer CLI templates:

1. Open a terminal, create a new empty folder, and then change directory to it.
2. Run the following command to initialize the project:

   ```bash
   azd init --template https://github.com/Azure-Samples/ASA-Samples-Web-Application
   ```

   The following list describes the command interactions:

   - **OAuth2 login**: Authorize the login to Azure based on the OAuth2 protocol.
   - **Please enter a new environment name**: Provide an environment name, which is used as a suffix for the resource group created to hold all the Azure resources. This name should be unique within your Azure subscription.

   The console outputs messages similar to the following example:

   ```output
   Initializing a new project (azd init)
   (✓) Done: Initialized git repository
   (✓) Done: Downloading template code to: <your-local-path>

     Please enter a new environment name: <your-env-name>

   SUCCESS: New project initialized!
   You can view the template code in your directory: <your-local-path>
   Learn more about running 3rd party code on our DevHub: https://learn.microsoft.com/azure/developer/azure-developer-cli/azd-templates#guidelines-for-using-azd-templates
   ```

---

## 3. Prepare the cloud environment

The main resources required to run this sample are an Azure Spring Apps instance and an Azure Database for PostgreSQL instance. This section provides the steps to create these resources.

### [Azure portal](#tab/Azure-portal)

### 3.1. Sign in to the Azure portal

Open your web browser and sign in to the [Azure portal](https://portal.azure.com/). Enter your credentials to sign in to the portal. The default view is your service dashboard.

### 3.2. Create an Azure Spring Apps instance

Use the following steps to create a service instance:

1. Select **Create a resource** in the corner of the portal.

1. Select **Compute** > **Azure Spring Apps**.

   :::image type="content" source="../../media/quickstart-deploy-web-app/create-service-instance.png" alt-text="Screenshot of the Azure portal showing the Create a resource page with Azure Spring Apps highlighted." lightbox="../../media/quickstart-deploy-web-app/create-service-instance.png":::

1. Fill out the **Basics** form with the following information:

   | Setting            | Suggested value                  | Description                                                                                                                                                                                                                                                                                        |
   |--------------------|----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | **Subscription**   | Your subscription name           | The  Azure subscription that you want to use for your server. If you have multiple subscriptions, choose the subscription in which you'd like to be billed for the resource.                                                                                                                       |
   | **Resource group** | *myresourcegroup*                | A new resource group name or an existing one from your subscription.                                                                                                                                                                                                                               |
   | **Name**           | *myasa*                          | A unique name that identifies your Azure Spring Apps service. The name must be between 4 and 32 characters long and can contain only lowercase letters, numbers, and hyphens. The first character of the service name must be a letter and the last character must be either a letter or a number. |
   | **Plan**           | **Basic**                        | Pricing Tier determines the resource and cost associated with your instance.                                                                                                                                                                                                                       |
   | **Region**         | The region closest to your users | The location that is closest to your users.                                                                                                                                                                                                                                                        |
   | **Zone Redundant** | Unchecked                        | Whether to create your Azure Spring Apps service in an Azure availability zone. Currently, this feature is supported only in some regions.                                                                                                                                                         |

   :::image type="content" source="../../media/quickstart-deploy-web-app/create-basics.png" alt-text="Screenshot of the Azure portal showing the Create Azure Spring Apps page." lightbox="../../media/quickstart-deploy-web-app/create-basics.png":::

1. Select **Review and Create** to review your selections. Select **Create** to provision the Azure Spring Apps instance.

1. On the toolbar, select the **Notifications** icon (a bell) to monitor the deployment process. Once the deployment is done, you can select **Pin to dashboard**, which creates a tile for this service on your Azure portal dashboard as a shortcut to the service's **Overview** page. Select **Go to resource** to open the service's **Overview** page.

   :::image type="content" source="../../media/quickstart-deploy-web-app/notifications.png" alt-text="Screenshot of the Azure portal showing the Overview page with the Notifications pane open." lightbox="../../media/quickstart-deploy-web-app/notifications.png":::

### 3.3. Prepare the PostgreSQL instance

[!INCLUDE [provision-postgresql-flexible](./provision-postgresql.md)]

### 3.4. Connect app instance to PostgreSQL instance

Use the following steps to connect your service instances:

1. Go to your Azure Spring Apps instance in the Azure portal.

1. From the navigation pane, open the **Apps** pane and select **Create App**.

1. On the **Create App** page, fill in the app name and select *Java 17* as the runtime platform.

   :::image type="content" source="../../media/quickstart-deploy-web-app/create-app.png" alt-text="Screenshot of the Azure portal showing the Create app pane." lightbox="../../media/quickstart-deploy-web-app/create-app.png":::

1. Select **Create** to finish the app creation and select the app to view details.

1. Select **Service Connector** from the navigation pane to create a new service connection.

   :::image type="content" source="../../media/quickstart-deploy-web-app/app-service-connector.png" alt-text="Screenshot of the Azure portal showing the Service Connector page with the Create button highlighted." lightbox="../../media/quickstart-deploy-web-app/app-service-connector.png":::

1. Fill out the **Basics** tab with the following information:

   - **Service type**: **DB for PostgreSQL flexible server**
   - **Connection name**: *postgresql_e1974*
   - **Subscription**: select your subscription.
   - **PostgreSQL flexible server**: *my-demo-pgsql*
   - **PostgreSQL database**: *todo*
   - **Client type**: **SpringBoot**

   :::image type="content" source="../../media/quickstart-deploy-web-app/app-service-connector-basics.png" alt-text="Screenshot of the Azure portal showing the Basics tab of the Create connection pane for connecting to PostgreSQL." lightbox="../../media/quickstart-deploy-web-app/app-service-connector-basics.png":::

1. Configure the **Authentication** tab with the following information:

   - **Select the authentication type you'd like to use between your compute service and target service.**: Select **Connection string**.
   - **Continue with...**: Select **Database credentials**
   - **Username**: *myadmin*
   - **Password**: Enter your password.

   :::image type="content" source="../../media/quickstart-deploy-web-app/app-service-connector-authentication.png" alt-text="Screenshot of the Azure portal showing the Authentication tab of the Create connection pane with the Connection string option highlighted." lightbox="../../media/quickstart-deploy-web-app/app-service-connector-authentication.png":::

1. Select **Review and Create** to review your selections, then select **Create** to create the connection.

### [Azure Developer CLI](#tab/Azure-Developer-CLI)

1. Run the following command to log in Azure with OAuth2. Ignore this step if you've already logged in.

   ```bash
   azd auth login
   ```

1. Use the following command to enable the Azure Spring Apps feature:

   ```bash
   azd config set alpha.springapp on
   ```

1. Run the following command to provision the template's infrastructure to Azure:

   ```bash
   azd provision
   ```

   The following list describes the command interactions:

   - **Please select an Azure Subscription to use**: Use arrows to move, type to filter, then press <kbd>ENTER</kbd>.
   - **Please select an Azure location to use**: Use arrows to move, type to filter, then press <kbd>ENTER</kbd>.

---

## 4. Deploy the app to Azure Spring Apps

You can now deploy the app to Azure Spring Apps.

### [Azure portal](#tab/Azure-portal)

Use the following steps to deploy using the [Maven plugin for Azure Spring Apps](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Spring-Apps):

1. Navigate to the sample project directory and run the following command to configure the app in Azure Spring Apps:

   ```bash
   ./mvnw com.microsoft.azure:azure-spring-apps-maven-plugin:1.17.0:config
   ```

   The following list describes the command interactions:

   - **Select Maven child module**: Select the module to configure, then enter the number for the web module.
   - **OAuth2 login**: Authorize the login to Azure based on the OAuth2 protocol.
   - **Select subscription**: Select the subscription list number of the Azure Spring Apps instance you created, which defaults to the first subscription in the list. If you use the default number, press <kbd>ENTER</kbd> directly.
   - **Select Azure Spring Apps**: Select the number of the Azure Spring Apps instance you created. If you use the default number, press <kbd>ENTER</kbd> directly.
   - **Expose public access for this app?**: Press <kbd>y</kbd>.
   - **Confirm to save all the above configurations (Y/n)**: Press <kbd>y</kbd>. If you press <kbd>n</kbd>, the configuration isn't saved in the POM files.

1. Use the following command to deploy the app:

   ```bash
   ./mvnw com.microsoft.azure:azure-spring-apps-maven-plugin:1.17.0:deploy
   ```

   The following list describes the command interactions:

   - **OAuth2 login**: You need to authorize the login to Azure based on the OAuth2 protocol.

   After the command is executed, you can see output similar to the following example, which indicates that the deployment was successful:

   ```output
   [INFO] Deployment(default) is successfully updated.
   [INFO] Deployment Status: Running
   [INFO] Getting public url of app(simple-todo-web)...
   [INFO] Application url: https://<your-azure-spring-apps-name>-simple-todo-web.azuremicroservices.io
   ```

   The output **Application url** is the endpoint to access the `todo` application.

### [Azure Developer CLI](#tab/Azure-Developer-CLI)

Use the following steps to package the app, provision the Azure resources required by the web application, and then deploy to Azure Spring Apps:

1. Run the following command to package a deployable copy of your application:

   ```bash
   azd package
   ```

1. Run the following command to deploy the application code to those newly provisioned resources:

   ```bash
   azd deploy
   ```

   The console outputs messages similar to the following example:

   ```output
   |       | Deploying service simple-todo-webExecuting prepackage hook => /var/folders/m1/twqn055s7ll1bw0sstvkc0j40000gn/T/azd-prepackage-708945870.sh
   (✓) Done: Deploying service simple-todo-web
   - Endpoint: https://asa-2jqpw3tejrxdi-simple-todo-web.azuremicroservices.io/

   SUCCESS: Your Azure app has been deployed!
   You can view the resources created under the resource group rg-<your-environment-name>-<a-random-string> in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/<>your-subscription-id/resourceGroups/rg-<your-environment-name>-<a-random-string>/overview
   ```

   The output **Endpoint** is the endpoint to access the `todo` application.

> [!NOTE]
> You can also use `azd up` to combine the previous three commands: `azd package` (packages a deployable copy of your application), `azd provision` (provisions Azure resources), and `azd deploy` (deploys application code). For more information, see [Azure-Samples/ASA-Samples-Web-Application](https://github.com/Azure-Samples/ASA-Samples-Web-Application).

---
