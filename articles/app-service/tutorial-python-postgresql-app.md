---
title: 'Tutorial: Deploy a Python Django or Flask web app with PostgreSQL'
description: Create a Python Django or Flask web app with a PostgreSQL database and deploy it to Azure. The tutorial uses either the Django or Flask framework and the app is hosted on Azure App Service on Linux.
ms.devlang: python
ms.topic: tutorial
ms.date: 02/28/2023
ms.author: msangapu
ms.custom: mvc, seodec18, seo-python-october2019, cli-validate, devx-track-python, devdivchpfy22, event-tier1-build-2022, vscode-azure-extension-update-completed
zone_pivot_groups: deploy-python-web-app-postgresql
---

# Deploy a Python (Django or Flask) web app with PostgreSQL in Azure

In this tutorial, you'll deploy a data-driven Python web app (**[Django](https://www.djangoproject.com/)** or **[Flask](https://flask.palletsprojects.com/)**) to **[Azure App Service](./overview.md#app-service-on-linux)** with the **[Azure Database for PostgreSQL](../postgresql/index.yml)** relational database service. Azure App Service supports [Python](https://www.python.org/downloads/) in a Linux server environment.

:::image type="content" border="False" source="./media/tutorial-python-postgresql-app/python-postgresql-app-architecture-240px.png" lightbox="./media/tutorial-python-postgresql-app/python-postgresql-app-architecture.png" alt-text="An architecture diagram showing an App Service with a PostgreSQL database in Azure.":::

**To complete this tutorial, you'll need:**

* An Azure account with an active subscription. If you don't have an Azure account, you [can create one for free](https://azure.microsoft.com/free/python).
* Knowledge of Python with Flask development or [Python with Django development](/training/paths/django-create-data-driven-websites/)

:::zone pivot="deploy-portal"

## Sample application

Sample Python applications using the Flask and Django framework are provided to help you follow along with this tutorial. To deploy them without running them locally, skip this part. 

To run the application locally, make sure you have [Python 3.7 or higher](https://www.python.org/downloads/) and [PostgreSQL](https://www.postgresql.org/download/) installed locally. Then, download or clone the app:

### [Flask](#tab/flask)

```bash
git clone https://github.com/Azure-Samples/msdocs-flask-postgresql-sample-app.git
```

### [Django](#tab/django)

```bash
git clone https://github.com/Azure-Samples/msdocs-django-postgresql-sample-app.git
```

-----

Go to the application folder:

### [Flask](#tab/flask)

```bash
cd msdocs-python-flask-webapp-quickstart
```

### [Django](#tab/django)

```bash
cd msdocs-django-postgresql-sample-app
```

-----

Create an *.env* file as shown below using the *.env.sample* file as a guide. Set the value of `DBNAME` to the name of an existing database in your local PostgreSQL instance. Set the values of `DBHOST`, `DBUSER`, and `DBPASS` as appropriate for your local PostgreSQL instance.

```
DBNAME=<database name>
DBHOST=<database-hostname>
DBUSER=<db-user-name>
DBPASS=<db-password>
```

Create a SECRET_KEY value for your app by running the following command at a terminal prompt: `python -c 'import secrets; print(secrets.token_hex())'`.

Set the returned value as the value of `SECRET_KEY` in the .env file.

```
SECRET_KEY=<secret-key>
```

Create a virtual environment for the app:

[!INCLUDE [Virtual environment setup](<./includes/quickstart-python/virtual-environment-setup.md>)]

Install the dependencies:

```bash
pip install -r requirements.txt
```

Run the sample application with the following commands:

### [Flask](#tab/flask)

```bash
# Run database migration
flask db upgrade
# Run the app at http://127.0.0.1:5000
flask run
```

### [Django](#tab/django)

```bash
# Run database migration
python manage.py migrate
# Run the app at http://127.0.0.1:8000
python manage.py runserver
```

-----

## 1. Create App Service and PostgreSQL

In this step, you create the Azure resources. The steps used in this tutorial create a set of secure-by-default resources that include App Service and Azure Database for PostgreSQL. For the creation process, you'll specify:

* The **Name** for the web app. It's the name used as part of the DNS name for your webapp in the form of `https://<app-name>.azurewebsites.net`.
* The **Region** to run the app physically in the world.
* The **Runtime stack** for the app. It's where you select the version of Python to use for your app.
* The **Hosting plan** for the app. It's the pricing tier that includes the set of features and scaling capacity for your app.
* The **Resource Group** for the app. A resource group lets you group (in a logical container) all the Azure resources needed for the application.

Sign in to the [Azure portal](https://portal.azure.com/) and follow these steps to create your Azure App Service resources.

:::row:::
    :::column span="2":::
        **Step 1.** In the Azure portal:
        1. Enter "web app database" in the search bar at the top of the Azure portal.
        1. Select the item labeled **Web App + Database** under the **Marketplace** heading.
        You can also navigate to the [creation wizard](https://portal.azure.com/?feature.customportal=false#create/Microsoft.AppServiceWebAppDatabaseV3) directly.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-create-app-postgres-1.png" alt-text="A screenshot showing how to use the search box in the top tool bar to find the Web App + Database creation wizard." lightbox="./media/tutorial-python-postgresql-app/azure-portal-create-app-postgres-1.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 2.** In the **Create Web App + Database** page, fill out the form as follows.
        1. *Resource Group* &rarr; Select **Create new** and use a name of **msdocs-python-postgres-tutorial**.
        1. *Region* &rarr; Any Azure region near you.
        1. *Name* &rarr; **msdocs-python-postgres-XYZ** where *XYZ* is any three random characters. This name must be unique across Azure.
        1. *Runtime stack* &rarr; **Python 3.10**.
        1. *Database* &rarr;  **PostgreSQL - Flexible Server** is selected by default as the database engine. The server name and database name are also set by default to appropriate values.
        1. *Hosting plan* &rarr; **Basic**. When you're ready, you can [scale up](manage-scale-up.md) to a production pricing tier later.
        1. Select **Review + create**.
        1. After validation completes, select **Create**.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-create-app-postgres-2.png" alt-text="A screenshot showing how to configure a new app and database in the Web App + Database wizard." lightbox="./media/tutorial-python-postgresql-app/azure-portal-create-app-postgres-2.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 3.** The deployment takes a few minutes to complete. Once deployment completes, select the **Go to resource** button. You're taken directly to the App Service app, but the following resources are created:
        - **Resource group** &rarr; The container for all the created resources.
        - **App Service plan** &rarr; Defines the compute resources for App Service. A Linux plan in the *Basic* tier is created.
        - **App Service** &rarr; Represents your app and runs in the App Service plan.
        - **Virtual network** &rarr; Integrated with the App Service app and isolates back-end network traffic.
        - **Azure Database for PostgreSQL flexible server** &rarr; Accessible only from within the virtual network. A database and a user are created for you on the server.
        - **Private DNS zone** &rarr; Enables DNS resolution of the PostgreSQL server in the virtual network.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-create-app-postgres-3.png" alt-text="A screenshot showing the deployment process completed." lightbox="./media/tutorial-python-postgresql-app/azure-portal-create-app-postgres-3.png":::
    :::column-end:::
:::row-end:::

## 2. Verify connection settings

The creation wizard generated the connectivity variables for you already as [app settings](configure-common.md#configure-app-settings). App settings are one way to keep connection secrets out of your code repository. When you're ready to move your secrets to a more secure location, here's an [article on storing in Azure Key Vault](../key-vault/certificates/quick-create-python.md).

:::row:::
    :::column span="2":::
        **Step 1.** In the App Service page, in the left menu, select Configuration.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-get-connection-string-1.png" alt-text="A screenshot showing how to open the configuration page in App Service." lightbox="./media/tutorial-python-postgresql-app/azure-portal-get-connection-string-1.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 2.** In the **Application settings** tab of the **Configuration** page, verify that `AZURE_POSTGRESQL_CONNECTIONSTRING` is present. That will be injected into the runtime environment as an environment variable.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-get-connection-string-2.png" alt-text="A screenshot showing how to see the autogenerated connection string." lightbox="./media/tutorial-python-postgresql-app/azure-portal-get-connection-string-2.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 3.** In a terminal or command prompt, run the following Python script to generate a unique secret: `python -c 'import secrets; print(secrets.token_hex())'`. Copy the output value to use in the next step.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 4.** In the **Application settings** tab of the **Configuration** page, select **New application setting**. Name the setting `SECRET_KEY`. Paste the value from the previous value. Select **OK**.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-app-service-app-setting.png" alt-text="A screenshot showing how to set the SECRET_KEY app setting in the Azure portal." lightbox="./media/tutorial-python-postgresql-app/azure-portal-app-service-app-setting.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 5.** Select **Save**.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-app-service-app-setting-save.png" alt-text="A screenshot showing how to save the SECRET_KEY app setting in the Azure portal." lightbox="./media/tutorial-python-postgresql-app/azure-portal-app-service-app-setting-save.png":::
    :::column-end:::
:::row-end:::


Having issues? Check the [Troubleshooting guide](configure-language-python.md#troubleshooting).


## 3. Deploy sample code

In this step, you'll configure GitHub deployment using GitHub Actions. It's just one of many ways to deploy to App Service, but also a great way to have continuous integration in your deployment process. By default, every `git push` to your GitHub repository will kick off the build and deploy action.

### [Flask](#tab/flask)

:::row:::
    :::column span="2":::
        **Step 1.** In a new browser window:
        1. Sign in to your GitHub account.
        1. Navigate to [https://github.com/Azure-Samples/msdocs-flask-postgresql-sample-app](https://github.com/Azure-Samples/msdocs-flask-postgresql-sample-app).
        1. Select **Fork**.
        1. Select **Create fork**.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-1.png" alt-text="A screenshot showing how to create a fork of the sample GitHub repository (Flask)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-1.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 2.** In the GitHub page, open Visual Studio Code in the browser by pressing the `.` key.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-2.png" alt-text="A screenshot showing how to open the Visual Studio Code browser experience in GitHub (Flask)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-2.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 3.** In Visual Studio Code in the browser, open *azureproject/production.py* in the explorer.
        See the environment variables being used in the production environment, including the app settings that you saw in the configuration page.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-3.png" alt-text="A screenshot showing Visual Studio Code in the browser and an opened file (Flask)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-3.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 4.** Back in the App Service page, in the left menu, select **Deployment Center**. 
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-4.png" alt-text="A screenshot showing how to open the deployment center in App Service (Flask)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-4.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 5.** In the Deployment Center page:
        1. In **Source**, select **GitHub**. By default, **GitHub Actions** is selected as the build provider.        
        1. Sign in to your GitHub account and follow the prompt to authorize Azure.
        1. In **Organization**, select your account.
        1. In **Repository**, select **msdocs-flask-postgresql-sample-app**.
        1. In **Branch**, select **main**.
        1. Keep the default option selected to **Add a workflow**.
        1. In the top menu, select **Save**. App Service commits a workflow file into the chosen GitHub repository, in the `.github/workflows` directory.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-5.png" alt-text="A screenshot showing how to configure CI/CD using GitHub Actions (Flask)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-5.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 6.** In the Deployment Center page:
        1. Select **Logs**. A deployment run is already started.
        1. In the log item for the deployment run, select **Build/Deploy Logs**. 
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-6.png" alt-text="A screenshot showing how to open deployment logs in the deployment center (Flask)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-6.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 7.** You're taken to your GitHub repository and see that the GitHub action is running. The workflow file defines two separate stages, build and deploy. Wait for the GitHub run to show a status of **Complete**. It takes about 5 minutes.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-7.png" alt-text="A screenshot showing a GitHub run in progress (Flask)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-flask-7.png":::
    :::column-end:::
:::row-end:::

### [Django](#tab/django)

:::row:::
    :::column span="2":::
        **Step 1.** In a new browser window:
        1. Sign in to your GitHub account.
        1. Navigate to [https://github.com/Azure-Samples/msdocs-django-postgresql-sample-app](https://github.com/Azure-Samples/msdocs-django-postgresql-sample-app).
        1. Select **Fork**.
        1. Select **Create fork**.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-1.png" alt-text="A screenshot showing how to create a fork of the sample GitHub repository (Django)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-1.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 2.** In the GitHub page, open Visual Studio Code in the browser by pressing the `.` key.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-2.png" alt-text="A screenshot showing how to open the Visual Studio Code browser experience in GitHub (Django)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-2.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 3.** In Visual Studio Code in the browser, open *azureproject/production.py* in the explorer.
        See the environment variables being used in the production environment, including the app settings that you saw in the configuration page.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-3.png" alt-text="A screenshot showing Visual Studio Code in the browser and an opened file (Django)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-3.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 4.** Back in the App Service page, in the left menu, select **Deployment Center**. 
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-4.png" alt-text="A screenshot showing how to open the deployment center in App Service (Django)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-4.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 5.** In the Deployment Center page:
        1. In **Source**, select **GitHub**. By default, **GitHub Actions** is selected as the build provider.        
        1. Sign in to your GitHub account and follow the prompt to authorize Azure.
        1. In **Organization**, select your account.
        1. In **Repository**, select **msdocs-django-postgresql-sample-app**.
        1. In **Branch**, select **main**.
        1. In the top menu, select **Save**. App Service commits a workflow file into the chosen GitHub repository, in the `.github/workflows` directory.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-5.png" alt-text="A screenshot showing how to configure CI/CD using GitHub Actions (Django)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-5.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 6.** In the Deployment Center page:
        1. Select **Logs**. A deployment run is already started.
        1. In the log item for the deployment run, select **Build/Deploy Logs**. 
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-6.png" alt-text="A screenshot showing how to open deployment logs in the deployment center (Django)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-6.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 7.** You're taken to your GitHub repository and see that the GitHub action is running. The workflow file defines two separate stages, build and deploy. Wait for the GitHub run to show a status of **Complete**. It takes about 5 minutes.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-7.png" alt-text="A screenshot showing a GitHub run in progress (Django)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-deploy-sample-code-django-7.png":::
    :::column-end:::
:::row-end:::

-----

Having issues? Check the [Troubleshooting guide](configure-language-python.md#troubleshooting).


## 4. Generate database schema

### [Flask](#tab/flask)

With the PostgreSQL database protected by the virtual network, the easiest way to run [Flask database migrations](https://flask-migrate.readthedocs.io/en/latest/) is in an SSH session with the App Service container. 

:::row:::
    :::column span="2":::
        **Step 1.** Back in the App Service page, in the left menu, select **SSH**. 
        1. Select **Go**.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-generate-db-schema-flask-1.png" alt-text="A screenshot showing how to open the SSH shell for your app from the Azure portal (Flask)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-generate-db-schema-flask-1.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 2.** In the SSH terminal, run `flask db upgrade`. If it succeeds, App Service is [connecting successfully to the database](#i-get-an-error-when-running-database-migrations).
        Only changes to files in `/home` can persist beyond app restarts. Changes outside of `/home` aren't persisted.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-generate-db-schema-flask-2.png" alt-text="A screenshot showing the commands to run in the SSH shell and their output (Flask)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-generate-db-schema-flask-2.png":::
    :::column-end:::
:::row-end:::

### [Django](#tab/django)

With the PostgreSQL database protected by the virtual network, the easiest way to run [Django database migrations](https://docs.djangoproject.com/en/4.1/topics/migrations/) is in an SSH session with the App Service container. 

:::row:::
    :::column span="2":::
        **Step 1.** Back in the App Service page, in the left menu, select **SSH**. 
        1. Select **Go**.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-generate-db-schema-django-1.png" alt-text="A screenshot showing how to open the SSH shell for your app from the Azure portal (Django)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-generate-db-schema-django-1.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 2.** In the SSH terminal, run `python manage.py migrate`. If it succeeds, App Service is [connecting successfully to the database](#i-get-an-error-when-running-database-migrations).
        Only changes to files in `/home` can persist beyond app restarts. Changes outside of `/home` aren't persisted.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-generate-db-schema-django-2.png" alt-text="A screenshot showing the commands to run in the SSH shell and their output (Django)." lightbox="./media/tutorial-python-postgresql-app/azure-portal-generate-db-schema-django-2.png":::
    :::column-end:::
:::row-end:::

> [!TIP]
> In an SSH session, for Django you can also create users with the `python manage.py createsuperuser` command like you would with a typical Django app. For more information, see the documentation for [django django-admin and manage.py](https://docs.djangoproject.com/en/1.8/ref/django-admin/). Use the superuser account to access the `/admin` portion of the web site. For Flask, use an extension such as [Flask-admin](https://github.com/flask-admin/flask-admin) to provide the same functionality.

-----

## 5. Browse to the app

:::row:::
    :::column span="2":::
        **Step 1.** In the App Service page:
        1. From the left menu, select **Overview**.
        1. Select the URL of your app. You can also navigate directly to `https://<app-name>.azurewebsites.net`.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-browse-app-1.png" alt-text="A screenshot showing how to launch an App Service from the Azure portal." lightbox="./media/tutorial-python-postgresql-app/azure-portal-browse-app-1.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 2.** Add a few restaurants to the list.
        Congratulations, you're running a web app in Azure App Service, with secure connectivity to Azure Database for PostgreSQL.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-browse-app-2.png" alt-text="A screenshot of the Flask web app with PostgreSQL running in Azure showing restaurants and restaurant reviews." lightbox="./media/tutorial-python-postgresql-app/azure-portal-browse-app-2.png":::
    :::column-end:::
:::row-end:::

## 6. Stream diagnostic logs

Azure App Service captures all messages output to the console to help you diagnose issues with your application. The sample app includes `print()` statements to demonstrate this capability as shown below.

### [Flask](#tab/flask)

:::code language="python" source="~/msdocs-flask-postgresql-sample-app/app.py" range="37-41" highlight="3":::

### [Django](#tab/django)

:::code language="python" source="~/msdocs-django-postgresql-sample-app/restaurant_review/views.py" range="12-16" highlight="2":::

-----

:::row:::
    :::column span="2":::
        **Step 1.** In the App Service page:
        1. From the left menu, select **App Service logs**.
        1. Under **Application logging**, select **File System**.
        1. In the top menu, select **Save**. 
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-stream-diagnostic-logs-1.png" alt-text="A screenshot showing how to enable native logs in App Service in the Azure portal." lightbox="./media/tutorial-python-postgresql-app/azure-portal-stream-diagnostic-logs-1.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 2.** From the left menu, select **Log stream**. You see the logs for your app, including platform logs and logs from inside the container.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-stream-diagnostic-logs-2.png" alt-text="A screenshot showing how to view the log stream in the Azure portal." lightbox="./media/tutorial-python-postgresql-app/azure-portal-stream-diagnostic-logs-2.png":::
    :::column-end:::
:::row-end:::

Learn more about logging in Python apps in the series on [setting up Azure Monitor for your Python application](/azure/azure-monitor/app/opencensus-python).

## 7. Clean up resources

When you're finished, you can delete all of the resources from your Azure subscription by deleting the resource group.

:::row:::
    :::column span="2":::
        **Step 1.** In the search bar at the top of the Azure portal:
        1. Enter the resource group name.
        1. Select the resource group.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-clean-up-resources-1.png" alt-text="A screenshot showing how to search for and navigate to a resource group in the Azure portal." lightbox="./media/tutorial-python-postgresql-app/azure-portal-clean-up-resources-1.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 2.** In the resource group page, select **Delete resource group**.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-clean-up-resources-2.png" alt-text="A screenshot showing the location of the Delete Resource Group button in the Azure portal." lightbox="./media/tutorial-python-postgresql-app/azure-portal-clean-up-resources-2.png":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        **Step 3.** 
        1. Enter the resource group name to confirm your deletion.
        1. Select **Delete**.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/tutorial-python-postgresql-app/azure-portal-clean-up-resources-3.png" alt-text="A screenshot of the confirmation dialog for deleting a resource group in the Azure portal." lightbox="./media/tutorial-python-postgresql-app/azure-portal-clean-up-resources-3.png"::::
    :::column-end:::
:::row-end:::

## Troubleshooting

Listed below are issues you may encounter while trying to work through this tutorial and steps to resolve them.

#### I can't connect to the SSH session

If you can't connect to the SSH session, then the app itself has failed to start. Check the [diagnostic logs](#6-stream-diagnostic-logs) for details. For example, if you see an error like `KeyError: 'AZURE_POSTGRESQL_CONNECTIONSTRING'`, it may mean that the environment variable is missing (you may have removed the app setting).

#### I get an error when running database migrations

If you encounter any errors related to connecting to the database, check if the app settings (`AZURE_POSTGRESQL_CONNECTIONSTRING`) have been changed. Without that connection string, the migrate command can't communicate with the database. 

:::zone-end

:::zone pivot="deploy-azd"

## Provision and deploy using the Azure Developer CLI

Sample Python application templates using the Flask and Django framework are provided for this tutorial. The [Azure Developer CLI](/azure/developer/azure-developer-cli/overview) greatly streamlines the process of provisioning application resources and deploying code on Azure. For a more step-by-step approach using the Azure portal and other tools, toggle to the **Azure portal** approach at the top of the page.

The Azure Developer CLI (azd) provides end-to-end support for project initialization, provisioning, deploying, monitoring and scaffolding a CI/CD pipeline to run against real Azure resources. You can use `azd` to provision and deploy the resources for the sample application in an automated and streamlined way.

Follow the steps below to setup the Azure Developer CLI and provision and deploy the sample application:

1. Install the Azure Developer CLI. For a full list of supported installation options and tools, visit the [installation guide](/azure/developer/azure-developer-cli/install-azd).

    ### [Windows](#tab/windows)

    ```azdeveloper
    powershell -ex AllSigned -c "Invoke-RestMethod 'https://aka.ms/install-azd.ps1' | Invoke-Expression"
    ```

    ### [macOS/Linux](#tab/mac-linux)

    ```azdeveloper
    curl -fsSL https://aka.ms/install-azd.sh | bash 
    ```

    ---

1. Run the `azd init` command to initialize the `azd` app template. Include the `--template` parameter to specify the name of an existing `azd` template you wish to use. More information about working with templates is available on the [choose an `azd` template](/azure/developer/azure-developer-cli/azd-templates) page.

    ### [Flask](#tab/flask)

    For this tutorial, Flask users should specify the [Python (Flask) web app with PostgresSQL](https://github.com/Azure-Samples/msdocs-flask-postgresql-sample-app.git) template.
    
    ```bash
    azd init --template msdocs-flask-postgresql-sample-app
    ```
    
    ### [Django](#tab/django)

    For this tutorial, Django users should specify the [Python (Django) web app with PostgresSQL](https://github.com/Azure-Samples/msdocs-django-postgresql-sample-app.git) template.

    ```bash
    azd init --template msdocs-django-postgresql-sample-app
    ```

1. Run the `azd auth login` command to sign-in to Azure.

    ```bash
    azd auth login
    ```

1. Run the `azd up` command to provision the necessary Azure resources and deploy the app code. The `azd up` command will also prompt you to select the desired subscription and location to deploy to.

    ```bash
    azd up
    ```

1. When the `azd up` command finishes running, the URL for your deployed web app in the console will be printed. Click, or copy and paste the web app URL into your browser to explore the running app and verify that it is working correctly. All of the Azure resources and application code were set up for you by the `azd up` command.

    The name of the resource group that was created is also displayed in the console output. Locate the resource group in the Azure portal to see all of the provisioned resources. 

    :::image type="content" border="False" source="./media/tutorial-python-postgresql-app/azd-resources-small.png" lightbox="./media/tutorial-python-postgresql-app/azd-resources.png" alt-text="A screenshot showing the resources deployed by the Azure Developer CLI.":::

The Azure Developer CLI also enables you to configure your application to use a CI/CD pipeline for deployments, setup monitoring functionality, and even remove the provisioned resources if you want to tear everything down. For more information about these additional workflows, visit the project [README](https://github.com/Azure-Samples/msdocs-flask-postgresql-sample-app/blob/main/README.md).

## Explore the completed azd project template workflow

The sections ahead review the steps that `azd` handled for you in more depth. You can explore this workflow to better understand the requirements for deploying your own apps to Azure. When you ran `azd up`, the Azure Developer CLI completed the following steps:

> [!NOTE]
> You can also use the steps outlined in the **Azure portal** version of this flow to gain additional insights into the tasks that `azd` completed for you.

### 1. Cloned and initialized the project

The `azd init` command cloned the sample app project template to your machine. The project template includes the following components:

* **Source code**: The code and assets for a Flask or Django web app that can be used for local development or deployed to Azure.
* **Bicep files**: Infrastructure as code (IaC) files that are used by `azd` to create the necessary resources in Azure.
* **Configuration files**: Essential configuration files such as `azure.yaml` that are used by `azd` to provision, deploy and wire resources together to produce a fully fledged application.

### 2. Provisioned the Azure resources

The `azd up` command created all of the resources for the sample application in Azure using the Bicep files in the [`infra`](https://github.com/Azure-Samples/msdocs-flask-postgresql-sample-app/tree/main/infra) folder of the project template. [Bicep](/azure/azure-resource-manager/bicep/overview?tabs=bicep) is a declarative language used to manage Infrastructure as Code in Azure. Some of the key resources and configurations created by the template include:

* **Resource group**: A resource group was created to hold all of the other provisioned Azure resources. The resource group keeps your resources well organized and easier to manage. The name of the resource group is based off of the environment name you specified during the `azd up` initialization process.
* **Azure Virtual Network**: A virtual network was created to enable the provisioned resources to securely connect and communicate with one another. Related configurations such as setting up a private DNS zone link were also applied.
* **Azure App Service plan**: An App Service plan was created to host App Service instances. App Service plans define what compute resources are available for one or more web apps.
* **Azure App Service**: An App Service instance was created in the new App Service plan to host and run the deployed application. In this case a Linux instance was created and configured to run Python apps. Additional configurations were also applied to the app service, such as setting the Postgres connection string and secret keys. 
* **Azure Database for PostgreSQL**: A Postgres database and server were created for the app hosted on App Service to connect to. The required admin user, network and connection settings were also configured.
* **Azure Application Insights**: Application insights was set up and configured for the app hosted on the App Service. This service enables detailed telemetry and monitoring for your application.

You can inspect the Bicep files in the [`infra`](https://github.com/Azure-Samples/msdocs-flask-postgresql-sample-app/tree/main/infra) folder of the project to understand how each of these resources were provisioned in more detail. The `resources.bicep` file defines most of the different services created in Azure. For example, the App Service plan and App Service web app instance were created and connected using the following Bicep code:

### [Flask](#tab/flask)

```yaml
resource appServicePlan 'Microsoft.Web/serverfarms@2021-03-01' = {
  name: '${prefix}-service-plan'
  location: location
  tags: tags
  sku: {
    name: 'B1'
  }
  properties: {
    reserved: true
  }
}

resource web 'Microsoft.Web/sites@2022-03-01' = {
  name: '${prefix}-app-service'
  location: location
  tags: union(tags, { 'azd-service-name': 'web' })
  kind: 'app,linux'
  properties: {
    serverFarmId: appServicePlan.id
    siteConfig: {
      alwaysOn: true
      linuxFxVersion: 'PYTHON|3.10'
      ftpsState: 'Disabled'
      appCommandLine: 'startup.sh'
    }
    httpsOnly: true
  }
  identity: {
    type: 'SystemAssigned'
  }
```

### [Django](#tab/django)

```yml
resource appServicePlan 'Microsoft.Web/serverfarms@2021-03-01' = {
  name: '${prefix}-service-plan'
  location: location
  tags: tags
  sku: {
    name: 'B1'
  }
  properties: {
    reserved: true
  }
}

resource web 'Microsoft.Web/sites@2022-03-01' = {
  name: '${prefix}-app-service'
  location: location
  tags: union(tags, { 'azd-service-name': 'web' })
  kind: 'app,linux'
  properties: {
    serverFarmId: appServicePlan.id
    siteConfig: {
      alwaysOn: true
      linuxFxVersion: 'PYTHON|3.10'
      ftpsState: 'Disabled'
      appCommandLine: 'startup.sh'
    }
    httpsOnly: true
  }
  identity: {
    type: 'SystemAssigned'
  }

```

---

The Azure Database for PostgreSQL was also created using the following Bicep:

```yml
resource postgresServer 'Microsoft.DBforPostgreSQL/flexibleServers@2022-01-20-preview' = {
  location: location
  tags: tags
  name: pgServerName
  sku: {
    name: 'Standard_B1ms'
    tier: 'Burstable'
  }
  properties: {
    version: '12'
    administratorLogin: 'postgresadmin'
    administratorLoginPassword: databasePassword
    storage: {
      storageSizeGB: 128
    }
    backup: {
      backupRetentionDays: 7
      geoRedundantBackup: 'Disabled'
    }
    network: {
      delegatedSubnetResourceId: virtualNetwork::databaseSubnet.id
      privateDnsZoneArmResourceId: privateDnsZone.id
    }
    highAvailability: {
      mode: 'Disabled'
    }
    maintenanceWindow: {
      customWindow: 'Disabled'
      dayOfWeek: 0
      startHour: 0
      startMinute: 0
    }
  }

  dependsOn: [
    privateDnsZoneLink
  ]
}
```

### 3. Deployed the application

The `azd up` command also deployed the sample application code to the provisioned Azure resources. The Developer CLI understands how to deploy different parts of your application code to different services in Azure using the `azure.yaml` file at the root of the project. The `azure.yaml` file specifies the app source code location, the type of app, and the Azure Service that should host that app. 

Consider the following `azure.yaml` file. These configurations tell the Azure Developer CLI that the Python code that lives at the root of the project should be deployed to the created App Service.

### [Flask](#tab/flask)

```yml
name: flask-postgresql-sample-app
metadata:
  template: flask-postgresql-sample-app@0.0.1-beta
services:
  web:
    project: .
    language: py
    host: appservice
```

### [Django](#tab/django)

```yml
name: django-postgresql-sample-app
metadata:
  template: django-postgresql-sample-app@0.0.1-beta
services:
  web:
    project: .
    language: py
    host: appservice
```

---

## Remove the resources

Once you are finished experimenting with your sample application, you can run the `azd down` command to remove the app from Azure. Removing resources helps to avoid unintended costs or unused services in your Azure subscription.

```bash
azd down
```

:::zone-end

## Frequently asked questions

- [How much does this setup cost?](#how-much-does-this-setup-cost)
- [How do I connect to the PostgreSQL server that's secured behind the virtual network with other tools?](#how-do-i-connect-to-the-postgresql-server-thats-secured-behind-the-virtual-network-with-other-tools)
- [How does local app development work with GitHub Actions?](#how-does-local-app-development-work-with-github-actions)
- [How is the Django sample configured to run on Azure App Service?](#how-is-the-django-sample-configured-to-run-on-azure-app-service)

#### How much does this setup cost?

Pricing for the created resources is as follows:

- The App Service plan is created in **Basic** tier and can be scaled up or down. See [App Service pricing](https://azure.microsoft.com/pricing/details/app-service/linux/).
- The PostgreSQL flexible server is created in the lowest burstable tier **Standard_B1ms**, with the minimum storage size, which can be scaled up or down. See [Azure Database for PostgreSQL pricing](https://azure.microsoft.com/pricing/details/postgresql/flexible-server/).
- The virtual network doesn't incur a charge unless you configure extra functionality, such as peering. See [Azure Virtual Network pricing](https://azure.microsoft.com/pricing/details/virtual-network/).
- The private DNS zone incurs a small charge. See [Azure DNS pricing](https://azure.microsoft.com/pricing/details/dns/). 

#### How do I connect to the PostgreSQL server that's secured behind the virtual network with other tools?

- For basic access from a command-line tool, you can run `psql` from the app's SSH terminal.
- To connect from a desktop tool, your machine must be within the virtual network. For example, it could be an Azure VM that's connected to one of the subnets, or a machine in an on-premises network that has a [site-to-site VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) connection with the Azure virtual network.
- You can also [integrate Azure Cloud Shell](../cloud-shell/private-vnet.md) with the virtual network.

#### How does local app development work with GitHub Actions?

Using the autogenerated workflow file from App Service as an example, each `git push` kicks off a new build and deployment run. From a local clone of the GitHub repository, you make the desired updates and push to GitHub. For example:

```terminal
git add .
git commit -m "<some-message>"
git push origin main
```

#### How is the Django sample configured to run on Azure App Service?

> [!NOTE]
> If you are following along with this tutorial with your own app, look at the *requirements.txt* file description in each project's *README.md* file ([Flask](https://github.com/Azure-Samples/msdocs-flask-postgresql-sample-app/blob/main/README.md), [Django](https://github.com/Azure-Samples/msdocs-django-postgresql-sample-app/blob/main/README.md)) to see what packages you'll need.

The [Django sample application](https://github.com/Azure-Samples/msdocs-django-postgresql-sample-app) configures settings in the *azureproject/production.py* file so that it can run in Azure App Service. These changes are common to deploying Django to production, and not specific to App Service.

- Django validates the HTTP_HOST header in incoming requests. The sample code uses the [`WEBSITE_HOSTNAME` environment variable in App Service](reference-app-settings.md#app-environment) to add the app's domain name to Django's [ALLOWED_HOSTS](https://docs.djangoproject.com/en/4.1/ref/settings/#allowed-hosts) setting.

    :::code language="python" source="~/msdocs-django-postgresql-sample-app/azureproject/production.py" range="6-8" highlight="3":::

- Django doesn't support [serving static files in production](https://docs.djangoproject.com/en/4.1/howto/static-files/deployment/). For this tutorial, you use [WhiteNoise](https://whitenoise.evans.io/) to enable serving the files. The WhiteNoise package was already installed with requirements.txt, and its middleware is added to the list.

    :::code language="python" source="~/msdocs-django-postgresql-sample-app/azureproject/production.py" range="11-16" highlight="14":::

    Then the static file settings are configured according to the Django documentation.

    :::code language="python" source="~/msdocs-django-postgresql-sample-app/azureproject/production.py" range="25-26":::

For more information, see [Production settings for Django apps](configure-language-python.md#production-settings-for-django-apps).

## Next steps

Advance to the next tutorial to learn how to secure your app with a custom domain and certificate.

> [!div class="nextstepaction"]
> [Secure with custom domain and certificate](tutorial-secure-domain-certificate.md)

Learn how App Service runs a Python app:

> [!div class="nextstepaction"]
> [Configure Python app](configure-language-python.md)
