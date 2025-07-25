---
title: Configure your SAP system for the Microsoft Sentinel solution
titleSuffix: Microsoft Sentinel
description: Learn about extra preparations required in your SAP system to install the SAP data connector agent and connect Microsoft Sentinel to your SAP system.
author: batamig
ms.author: bagol
ms.topic: how-to
ms.date: 12/02/2024
appliesto:
    - Microsoft Sentinel in the Microsoft Defender portal
    - Microsoft Sentinel in the Azure portal
ms.collection: usx-security
#Customer intent: As an SAP BASIS team member, I want to configure SAP authorizations and deploy optional SAP Change Requests so that I can ensure proper connectivity and log retrieval from SAP systems for security monitoring.
zone_pivot_groups: sentinel-sap-connection

---

# Configure your SAP system for the Microsoft Sentinel solution

This article describes how to prepare your SAP environment for connecting to the SAP data connector. Preparation differs, depending on whether you're using the containerized data connector agent. Select the option at the top of the page that matches your environment.

:::zone pivot="connection-agent"
This article is part of the second step in deploying the Microsoft Sentinel solution for SAP applications. 

:::image type="content" source="media/deployment-steps/prepare-sap-environment.png" alt-text="Diagram of the deployment flow for the Microsoft Sentinel solution for SAP applications, with the preparing SAP step highlighted." border="false":::

The procedures in this article are typically performed by your **SAP BASIS** team.
:::zone-end

:::zone pivot="connection-agentless"
This article is part of the second step in deploying the Microsoft Sentinel solution for SAP applications. While steps that are performed in Microsoft Sentinel require that the solution be installed first, other preparations in the SAP environment can happen in parallel.

:::image type="content" source="media/deployment-steps/prepare-sap-environment-agentless.png" alt-text="Diagram of the deployment flow for the Microsoft Sentinel solution for SAP applications, with the preparing SAP step highlighted." border="false":::

Many of the procedures in this article are typically performed by your **SAP BASIS** team. Some steps include your **security** team too.
:::zone-end

> [!IMPORTANT]
> Microsoft Sentinel's agentless data connector for SAP is currently in PREVIEW. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

## Prerequisites

- Before you start, make sure to review the [prerequisites for deploying the Microsoft Sentinel solution for SAP applications](prerequisites-for-deploying-sap-continuous-threat-monitoring.md).
:::zone pivot="connection-agentless"
- If you're working with the agentless data connector, some steps are performed in Microsoft Sentinel and require that the [solution be installed first](deploy-sap-security-content.md).

:::zone-end

## Configure the Microsoft Sentinel role

To allow the SAP data connector to connect to your SAP system, you must create an SAP system role specifically for this purpose.

:::zone pivot="connection-agent"

We recommend creating this role by deploying the *NPLK900271* SAP change request (CR): [K900271.NPL](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/CR/K900271.NPL) | [R900271.NPL](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/CR/R900271.NPL)

Deploy the CRs on your SAP system as needed just as you'd deploy other CRs. We strongly recommend that deploying SAP CRs is done by an experienced SAP system administrator. For more information, see the [SAP documentation](https://help.sap.com/docs/ABAP_PLATFORM_NEW/4a368c163b08418890a406d413933ba7/e15d9acae75c11d2b451006094b9ea64.html?locale=en-US&version=LATEST).

Alternately, load the role authorizations from the [**MSFTSEN_SENTINEL_CONNECTOR**](https://aka.ms/SAP_Sentinel_Connector_Role) file, which includes all the basic permissions for the data connector to operate.

Experienced SAP administrators might choose to create the role manually and assign it the appropriate permissions. In such cases, create a role manually with the relevant authorizations required for the logs you want to ingest. For more information, see [Required ABAP authorizations](required-abap-authorizations.md). Examples in our documentation use the **/MSFTSEN/SENTINEL_RESPONDER** name.

When configuring the role, we recommend that you:

- Generate an active role profile for Microsoft Sentinel by running the **PFCG** transaction.
- Use `/MSFTSEN/SENTINEL_RESPONDER` as the role name.

:::zone-end

:::zone pivot="connection-agentless"

Create a role using the [**MSFTSEN_SENTINEL_READER**](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/Sample%20Authorizations%20Role%20File/MSFTSEN_SENTINEL_READER.SAP) template, which includes all the basic permissions for the data connector to operate.

:::zone-end

For more information, see the [SAP documentation](https://help.sap.com/doc/saphelp_nw73ehp1/7.31.19/en-US/48/e8eb38f94cb138e10000000a114084/frameset.htm) on creating roles.

### Create a user

The Microsoft Sentinel solution for SAP applications requires a user account to connect to your SAP system. When creating your user:

:::zone pivot="connection-agent"
- Make sure to create a system user.
- Assign the **/MSFTSEN/SENTINEL_RESPONDER** role to the user, which you'd created in the [previous step](#configure-the-microsoft-sentinel-role).
:::zone-end

:::zone pivot="connection-agentless"
- Make sure to create a system user.
- Assign the **MSFTSEN_SENTINEL_READER** role to the user, which you'd created in the [previous step](#configure-the-microsoft-sentinel-role).
:::zone-end

For more information, see the [SAP documentation](https://help.sap.com/docs/ABAP_PLATFORM_NEW/ad77b44570314f6d8c3a8a807273084c/4cb5f7ac9cb33c94e10000000a42189c.html?version=LATEST).

## Configure SAP auditing

Some installations of SAP systems might not have audit logging enabled by default. For best results in evaluating the performance and efficacy of the Microsoft Sentinel solution for SAP applications, enable auditing of your SAP system and configure the audit parameters. If you want to ingest SAP HANA DB logs, make sure to also enable auditing for SAP HANA DB.

We recommend that you configure auditing for *all* messages from the audit log, instead of only specific logs. Ingestion cost differences are generally minimal and the data is useful for Microsoft Sentinel detections and in post-compromise investigations and hunting.

:::zone pivot="connection-agentless"
For full monitoring coverage with the agentless data connector, we recommend that you enable monitoring on all client IDs of your monitored SAP systems, including clients 000 and 066.
:::zone-end

For more information, see the [SAP community](https://community.sap.com/t5/application-development-blog-posts/analysis-and-recommended-settings-of-the-security-audit-log-sm19-rsau/ba-p/13297094) and [Collect SAP HANA audit logs in Microsoft Sentinel](collect-sap-hana-audit-logs.md).

## Configure your system to use SNC for secure connections

By default, the SAP data connector agent connects to an SAP server using a remote function call (RFC) connection and a username and password for authentication.

However, you might need to make the connection on an encrypted channel or use client certificates for authentication. In these cases, use Smart Network Communications (SNC) from SAP to secure your data connections, as described in this section.

In a production environment, we strongly recommend that your consult with SAP administrators to create a deployment plan for configuring SNC. For more information, see the [SAP documentation](https://help.sap.com/docs/ABAP_PLATFORM_NEW/e73bba71770e4c0ca5fb2a3c17e8e229/e656f466e99a11d1a5b00000e835363f.html). 

When configuring SNC:

- If the client certificate was issued by an enterprise certification authority, transfer the issuing CA and root CA certificates to the system where you plan to create the data connector agent.
- If you're using the data connector agent, make sure to also enter the relevant values and use the relevant procedures when [configuring the SAP data connector agent container](deploy-data-connector-agent-container.md). If you're using the agentless data connector, the SNC configuration is done in the SAP Cloud Connector.


For more information about SNC, see [Getting started with SAP SNC for RFC integrations - SAP blog](https://community.sap.com/t5/enterprise-resource-planning-blogs-by-members/getting-started-with-sap-snc-for-rfc-integrations/ba-p/13983462).

:::zone pivot="connection-agent"

## Configure support for extra data retrieval (recommended)

While this step is optional, we recommend that you enable the SAP data connector to retrieve the following content information from your SAP system:

- **DB Table** and **Spool Output** logs
- **Client IP address information** from the security audit logs

1. Deploy the relevant CRs from the [Microsoft Sentinel GitHub repository](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR), according to your SAP version:

    | SAP BASIS versions | Recommended CR |
    | --- | --- | --- |
    | **750 and higher** | *NPLK900202*: [K900202.NPL](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/CR/K900202.NPL), [R900202.NPL](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/CR/R900202.NPL) <br><br>When deploying this CR any of the following SAP versions, also deploy [2641084 - Standardized read access to data of Security Audit Log](https://launchpad.support.sap.com/#/notes/2641084): <br>- 750 SP04 to SP12<br>- 751 SP00 to SP06<br>- 752 SP00 to SP02  | 
    | **740**  | *NPLK900201*: [K900201.NPL](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/CR/K900201.NPL), [R900201.NPL](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/CR/R900201.NPL) |

    Deploy the CRs on your SAP system as needed just as you'd deploy other CRs. We strongly recommend that deploying SAP CRs is done by an experienced SAP system administrator. For more information, see the [SAP documentation](https://help.sap.com/docs/ABAP_PLATFORM_NEW/4a368c163b08418890a406d413933ba7/e15d9acae75c11d2b451006094b9ea64.html?locale=en-US&version=LATEST).

    For more information, see the [SAP Community](https://community.sap.com/t5/application-development-blog-posts/analysis-and-recommended-settings-of-the-security-audit-log-sm19-rsau/ba-p/13297094) and the [SAP documentation](https://help.sap.com/docs/ABAP_PLATFORM_NEW/4a368c163b08418890a406d413933ba7/e15d9acae75c11d2b451006094b9ea64.html?locale=en-US&version=LATEST).

1. To support SAP BASIS versions 7.31-7.5 SP12 in sending client IP address information to Microsoft Sentinel, activate logging for SAP table USR41. For more information, see the [SAP documentation](https://help.sap.com/doc/saphelp_scm700_ehp02/7.0.2/en-US/73/86ce4dc98d461283f25940367dd9c3/frameset.htm).


## Verify that the PAHI table is updated at regular intervals

The SAP PAHI table includes data on the history of the SAP system, the database, and SAP parameters. In some cases, the Microsoft Sentinel solution for SAP applications can't monitor the SAP PAHI table at regular intervals, due to missing or faulty configuration. It's important to update the PAHI table and to monitor it frequently, so that the Microsoft Sentinel solution for SAP applications can alert on suspicious actions that might happen at any time throughout the day. For more information, see:

- [SAP note 12103](https://launchpad.support.sap.com/#/notes/12103)
- [Monitoring the configuration of static SAP security parameters (Preview)](sap-solution-security-content.md#monitor-the-configuration-of-static-sap-security-parameters-preview)

If the PAHI table is updated regularly, the `SAP_COLLECTOR_FOR_PERFMONITOR` job is scheduled and runs hourly. If the `SAP_COLLECTOR_FOR_PERFMONITOR` job doesn't exist, make sure to configure it as needed. 

For more information, see [Database Collector in Background Processing](https://help.sap.com/doc/saphelp_nw75/7.5.5/c4/3a735b505211d189550000e829fbbd/frameset.htm) and [Configuring the Data Collector](https://help.sap.com/docs/SAP_NETWEAVER_AS_ABAP_752/3364beced9d145a5ad185c89a1e04658/c43a818c505211d189550000e829fbbd.html).

:::zone-end

:::zone pivot="connection-agentless"

## Configure SAP BTP settings

1. In your SAP BTP subaccount, add entitlements for the following services:

    - SAP Integration Suite
    - SAP Process Integration Runtime
    - Cloud Foundry Runtime

1. Create an instance of Cloud Foundry Runtime, and then also create a Cloud Foundry space.

1. Create an instance of SAP Integration Suite.

1. Assign the SAP BTP **Integration_Provisioner** role to your SAP BTP subaccount user account.

1. In the SAP Integration Suite, add the cloud integration capability.

1. Assign the following process integration roles to your user account:

    - **PI_Administrator**
    - **PI_Integration_Developer**
    - **PI_Business_Expert**

    These roles are available only after you activate the cloud integration capability.

1. Create an instance of the SAP Process Integration Runtime in your subaccount using service plan **integration-flow** (not api!).

1. Create a service key for the SAP Process Integration Runtime and save the JSON contents to a secure location. You must activate the cloud integration capability before creating a service key for SAP Process Integration Runtime.

For more information, see the [SAP documentation](https://help.sap.com/docs/integration-suite/sap-integration-suite/initial-setup).

## Configure the connector in Microsoft Sentinel and in your SAP system

This procedure has steps both in Microsoft Sentinel and your SAP system, and requires coordination with the SAP administrator.

1. In Microsoft Sentinel, go to the **Configuration > Data connectors** page and locate the **Microsoft Sentinel for SAP - agentless (Preview)** data connector.

1. In the **Configuration** section, expand and follow the instructions in the **Initial connector configuration - Run the steps below once:** section. These steps will require both your SecuritySOC engineer and the SAP admin.
    1. Trigger automatic deployment of Azure resources (SOC Engineer).
       If, after you deploy the Azure resources, the values in the steps 2 and 3 aren't automatically populated, close and re-expand step 1 to refresh the values in steps 2 and 3.
   1. Deploy an OAuth2 client credentials artifact in the SAP Integration (SAP Admin).
   1. Deploy a Secure Parameter artifact in SAP Integration (SAP Admin) named **workspaceKey** containing the Log Analytics workspace key visible in the data connector UI.
      
    1. Deploy the SAP agentless data connector package to the SAP Integration Suite (SAP Admin).
        1. Download the [integration package](https://aka.ms/SAPAgentlessPackage) and upload it to your SAP Integration Suite. For more information, see the [SAP documentation](https://help.sap.com/docs/integration-suite/sap-integration-suite/importing-integration-packages).
        1. Open the package and go to the **Artifacts** tab. Then select the **Data Collector** configuration. For more information, see the [SAP documentation](https://help.sap.com/docs/integration-suite/sap-integration-suite/importing-integration-packages).
        1. Configure the integration flow with the **LogIngestionURL** and the **DCRImmutableID**.
        1. Deploy the i-flow using SAP Cloud Integration as the runtime service.


## Run the prerequisite checker
1. The **Prerequisite checker** iflow is included in the package. We recommend running this iflow before continuing to the next step to ensure that your SAP system meets the system prerequisites.

    [!INCLUDE [sap-agentless-prerequisites](../includes/sap-agentless-prerequisites.md)]

1. Scroll further down in the **Configuration** area, and expand and follow the instructions in the **Add monitored SAP Systems - Run the steps below for each monitored SAP system:** area for each SAP system you want to monitor.

    When you get to step **2. Connect SAP System to Microsoft Sentinel / SOC Engineer**, continue with [Connect your SAP system to Microsoft Sentinel](deploy-data-connector-agent-container.md).

## Configure SAP Cloud Connector settings

1. Install the SAP Cloud Connector. For more information, see the [SAP documentation](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/installation).

1. Sign in at the cloud connector interface, and add the subaccount using the relevant credentials. For more information, see the [SAP documentation](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/managing-subaccounts).

1. In your cloud connector subaccount, add a new system mapping to the backend system to map the ABAP system to the RFC protocol.

1. Define load balancing options and enter your backend ABAP server details. In this step, copy the name of the virtual host to a secure location to use later in the deployment process.

1. Add new resources to the system mapping for each of the following function names:

   - **RSAU_API_GET_LOG_DATA**, to fetch SAP security audit log data
      
   - **BAPI_USER_GET_DETAIL**, to retrieve SAP user details
      
   - **RFC_READ_TABLE**, to read data from required tables
      
   - **SIAG_ROLE_GET_AUTH**, to retrieve security role authorizations
      
   - **/OSP/SYSTEM_TIMEZONE**, to retrieve SAP system timezone details
      
1. Add a new destination in SAP BTP that points the virtual host you'd created earlier. Use the following details to populate the new destination:

   - **Name:** Enter the name you want to use for the Microsoft Sentinel connection
      
   - **Type** `RFC`
      
   - **Proxy Type:** `On-Premise`
      
   - **User**: Enter the [ABAP user account](#create-a-user) you created earlier for Microsoft Sentinel
      
   - **Authorization Type:** `CONFIGURED USER`
      
   - **Additional properties:**
   
      - `jco.client.ashost = <virtual host name>`
            
      - `jco.client.client = <client e.g. 001>`
            
      - `jco.client.sysnr = <system number = 00>`
            
      - `jco.client.lang = EN`
            
   - **Location**: Only required when you connect multiple Cloud Connectors to the same BTP subaccount.  For more information, see the [SAP Documentation](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/parameters-influencing-communication-behavior).
   

:::zone-end


## Next step

> [!div class="nextstepaction"]
> [Connect your SAP system to Microsoft Sentinel](deploy-data-connector-agent-container.md)

