---
title: Get started with SAP and Microsoft integration scenarios
description: Learn about the various integration points in the Microsoft ecosystem for SAP workloads.
ms.service: sap-on-azure
ms.subservice: sap-vm-workloads
ms.topic: concept-article
ms.date: 06/22/2025
author: MartinPankraz
ms.author: mapankra

# Customer intent: "As an SAP workload administrator, I want to integrate SAP with Azure services, so that I can enhance our business processes and improve operational efficiency through seamless workflows and data management."
---
# Get started with SAP and Microsoft integration scenarios

[According to SAP over 87% of total global commerce is generated by SAP customers](https://www.sap.com/documents/2017/04/4666ecdd-b67c-0010-82c7-eda71af511fa.html) and more SAP systems are running in the cloud each year. The SAP platform provides a foundation for innovation for many companies and can handle various workloads natively. Explore our integration section further to learn how you can combine the Microsoft Azure ecosystem with your SAP workload to accelerate your business outcomes. Among the scenarios are extensions with Power Platform ("keep the ABAP core clean"), secured APIs with Azure API Management, automated business processes with Logic Apps, enriched experiences with SAP Business Technology Platform, native Microsoft integrations using ABAP Cloud, uniform data blending dashboards with the Azure Data Platform and more.

For the latest news from the SAP and  Azure world, follow the [SAP on Microsoft TechCommunity section](https://techcommunity.microsoft.com/t5/sap-on-microsoft/ct-p/SAPonMicrosoft) and the relevant Azure tags on the [SAP Community](https://community.sap.com/search/?ct=blog&q=Azure).

To learn more about the opportunities of extending SAP applications with Azure services, see this Azure Friday episode:

> [!VIDEO https://www.youtube.com/embed/72kbjv0GJAY]

We have over 30 years of partnership between SAP and Microsoft, which is a foundation to support common goals long-term, including a joint commitment by SAP and Microsoft to simplify and streamline customers’ journeys to the cloud. For more information, see:

- [Redefining Productivity: How Joule and Microsoft 365 Copilot Will Reshape Today’s Workplace](https://news.sap.com/2024/11/joule-microsoft-365-copilot-integration-redefining-productivity/)
- [SAP and Microsoft Expand Partnership and Integrate Microsoft Teams Across Solutions](https://help.sap.com/docs/SAP_S4HANA_CLOUD/0f69f8fb28ac4bf48d2b57b9637e81fa/257ec7408db6420682462cd1d000e744.html)
- [Collaborating for Success: How SAP and Microsoft are working together to accelerate customer innovation and transformation](https://www.sap.com/partners/find/microsoft.html)

## Integration resources

Select an area for resources about how to integrate SAP and Azure in that space.

| Area | Description |
| ---- | ----------- |
| [Azure OpenAI service](#azure-openai-service) | Learn how to integrate your SAP workloads with Azure OpenAI service. |
| [Microsoft Copilot](#microsoft-copilot) | Learn how to integrate your SAP workloads with Microsoft Copilots. |
| [SAP RISE managed workloads](rise-integration-services.md) | Learn how to integrate your SAP RISE managed workloads with Azure services. |
| [Microsoft Entra ID (formerly Azure Active Directory)](#microsoft-entra-id-formerly-azure-ad) | Ensure end-to-end SAP user lifecycle, authentication, and authorization with Microsoft Entra ID. Identity governance, Single sign-on (SSO) and phish-resistant multifactor authentication (MFA) are the foundation for a secure and seamless user experience. |
| [Threat Monitoring and Response Automation with Microsoft Security Services for SAP](#microsoft-security-for-sap) | Learn how to best secure your SAP workload with Microsoft Defender XDR, Defender for Cloud, the [SAP certified](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/solutions?id=s:33db1376-91ae-4f36-a435-aafa892a88d8) Microsoft Sentinel solution, and immutable vault for Azure Backup. Prevent incidents from happening, detect, and respond to threats in real-time. |
| [Microsoft Power Platform](#microsoft-power-platform) | Learn about the available out-of-the-box SAP solutions enabling your business users to achieve more with less. |
| [Microsoft Teams](#microsoft-teams) | Discover collaboration scenarios boosting your daily productivity by interacting with your SAP applications directly from Microsoft Teams. |
| [Microsoft Universal Print](#microsoft-universal-print) | Learn about the available cloud native printing capabilities for SAP. |
| [SAP Fiori](#sap-fiori) | Increase performance and security of your SAP Fiori applications by integrating them with Azure services. |
| [Microsoft Office](#microsoft-office) | Learn about Office Add-ins in Excel, doing SAP Principal Propagation with Office 365, SAP Analytics Cloud, and Data Warehouse Cloud integration and more. |
| [Azure Integration Services](#azure-integration-services) | Connect your SAP workloads with your end users, business partners, and their systems with world-class integration services. Learn about codevelopment efforts that enable SAP Event Mesh to exchange cloud events with Azure Event Grid, understand how you can achieve high-availability for services like SAP Cloud Integration, automate your SAP invoice processing with Logic Apps and Azure AI services and more. |
| [App Development in any language including ABAP and DevOps](#app-development-in-any-language-including-abap-and-devops) | Apply best-in-class developer tooling to your SAP app developments and DevOps processes. |
| [Azure Data Services](#azure-data-services) | Learn how to integrate your SAP data with Data Services like Azure Synapse Analytics, Azure Data Lake Storage, Azure Data Factory, Power BI, Data Warehouse Cloud, Analytics Cloud, which connector to choose, tune performance, efficiently troubleshoot, and more. |
| [SAP Business Technology Platform (BTP)](#sap-btp) | Discover integration scenarios like SAP Private Link to securely and efficiently connect your BTP apps to your Azure workloads. |

### Azure OpenAI Service

For more information about integration with [Azure OpenAI Service](/azure/ai-services/openai/overview), see the following Azure documentation:

- [Streamlining SAP Processes with Azure OpenAI, Copilot Studio, and Power Platform | blog](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/streamlining-sap-processes-with-azure-openai-copilot-studio-and/ba-p/4164338)
- [Microsoft AI SDK for SAP](https://microsoft.github.io/aisdkforsapabap/docs/intro)
- [ABAP SDK for Azure](https://github.com/microsoft/ABAP-SDK-for-Azure)

Also see these SAP resources:

- [empower SAP RISE enterprise users with Azure OpenAI in multicloud environment | blog](https://blogs.sap.com/2023/02/14/empower-sap-rise-enterprise-users-with-chatgpt-in-multi-cloud-environment/)
- [Consume OpenAI services (GPT) through CAP & SAP BTP, AI Core | GitHub repos](https://github.com/SAP-samples/azure-openai-aicore-cap-api)
- [SAP SuccessFactors Helps HR Solve Skills Gap with Generative AI | SAP News](https://news.sap.com/2023/05/sap-successfactors-helps-hr-solve-skills-gap-with-generative-ai/)

### Microsoft Copilot

For more information about integration with [Microsoft 365 Copilot](https://blogs.microsoft.com/blog/2023/03/16/introducing-microsoft-365-copilot-your-copilot-for-work/), see the following Microsoft resources:

- [Enhancing Copilot Studio Extensions for SAP by using Adaptive Cards and Principal Propagation](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/enhancing-copilot-studio-extensions-for-sap-by-using-adaptive/ba-p/4187096)
- [The synergy of market leaders: Exploring Microsoft and SAP’s game-changing collaboration | blog](https://azure.microsoft.com/blog/the-synergy-of-market-leaders-exploring-microsoft-and-saps-game-changing-collaboration/)

Also see these SAP resources:

- [The future of work is now: An update on generative AI at SAP SuccessFactors](https://blogs.sap.com/2023/05/15/the-future-of-work-is-now-an-update-on-generative-ai-at-sap-successfactors/)
- [SAP and Microsoft Collaborate on Joint Generative AI Offerings to Help Customers Address the Talent Gap | SAP News](https://news.sap.com/2023/05/sap-microsoft-joint-generative-ai-offerings-talent-gap/)

### Microsoft Office

For more information about integration with Microsoft Office, see the following Azure documentation:

- [Outbound E-Mail from SAP to Exchange Online](exchange-online-integration-sap-email-outbound.md)
- [Enable SAP Principal Propagation for live OData feeds with Excel](expose-sap-odata-to-power-query.md)

Also see these SAP resources:

- [SAP Analysis for Microsoft Office Excel and PowerPoint](https://help.sap.com/docs/SAP_BUSINESSOBJECTS_ANALYSIS_OFFICE/ca9c58444d64420d99d6c136a3207632/ebf198667aa54740b9049d9da804a901.html)
- [SAP Analytics Cloud, add-in for Microsoft Office](https://help.sap.com/docs/SAP_ANALYTICS_CLOUD_OFFICE/c637c9ff5d61457eb415ce161e38e57b/c9217302603a4fd6baa0fe6a6e780f8d.html)
- [Access SAP Data Warehouse Cloud with Microsoft Excel](https://blogs.sap.com/2022/05/17/access-sap-data-warehouse-cloud-with-saps-microsoft-excel-add-ins/)

### Microsoft Teams

For more information about integration with Microsoft Teams, see [Native SAP apps on the Teams marketplace](https://appsource.microsoft.com/en-us/marketplace/apps?page=1&search=sap&product=teams). Also see the following SAP resources. 

- [SAP SuccessFactors Learning](https://help.sap.com/docs/SAP_SUCCESSFACTORS_LEARNING/b5f34f583e874dd58c40525e4504b99e/e7c54e3fc9a24ee2b114a78761d3ff90.html)
- [SAP Build Work Zone, advanced edition](https://help.sap.com/docs/WZ/b03c84105ff74f809631e494bd612e83/bfa596db8219450ba9c65b809300b55d.html)
- [Embedding SAP Cloud Portal and SAP Build Work Zone into Microsoft Teams](https://blogs.sap.com/2022/01/26/integrate-sap-cloud-portal-and-launchpad-service-into-microsoft-teams-including-sso/)
- [Embed self-hosted SAP Fiori Launchpad into Microsoft Teams](https://blogs.sap.com/2022/08/02/embed-self-hosted-sap-fiori-launchpad-into-microsoft-teams/)
- [Simplify Supplier forecasting with SAP Integrated Business Planning, Ariba, and Microsoft Teams](https://blogs.sap.com/2022/10/03/using-microsoft-adaptive-cards-in-supply-chain-scenarios/)

### Microsoft Power Platform

For more information about integration with Microsoft Power Platform, see the following Power Automate resources:

- [Overview of SAP integration](/power-platform/sap/connect/connect-power-platform-and-sap)
- [SAP Principal Propagation using Azure API Management | blog](https://community.powerplatform.com/blogs/post/?postid=c6a609ab-3556-ef11-a317-6045bda95bf0)
- [Finance and operations templates for SAP process mining with Power Automate Process Advisor](/power-automate/process-mining-finance-ops-templates)
- [Hyperautomation for SAP based integration and automation with Power Automate | video series](https://flow.microsoft.com/blog/hyperautomation-special-video-series-for-sap-based-integration-automation-with-power-automate/)
- [RPA Playbook for SAP GUI Automation with Power Automate | blog](https://flow.microsoft.com/blog/rpa-playbook-for-sap-gui-automation-with-power-automate-api-flows-ui-flows-and-power-automate-desktop/)

Also see the following SAP resources:
- [SAP Principal Propagation via SAP API Management (Integration Suite)](https://community.sap.com/t5/enterprise-resource-planning-blogs-by-members/integrating-low-code-solutions-with-microsoft-using-sap-integration-suite/ba-p/13789298)
- [Snoozing SAP systems with Power Apps](https://blogs.sap.com/2021/02/10/hey-sap-systems-my-powerapp-says-snooze-but-only-if-youre-ready-yet/)
- [Use Business Rules capability of SAP Build Process Automation to expose SAP business logic to Power Apps](https://blogs.sap.com/2020/07/31/scp-business-rules-put-to-the-test-with-microsoft-power-platform/)

### Microsoft Universal Print

For more information about integration with [Microsoft Universal Print](/universal-print/fundamentals/universal-print-whatis), see the following resources:

- [Universal Print for SAP frontend scenarios](universal-print-sap-frontend.md)
- [Universal Print for SAP backend scenarios](https://github.com/Azure/universal-print-for-sap-starter-pack)

Also see the following SAP resources:

- [It has never been easier to print from SAP with Microsoft Universal Print](https://community.sap.com/t5/technology-blogs-by-members/it-has-never-been-easier-to-print-from-sap-with-microsoft-universal-print/ba-p/13672206)
- [Integrating SAP S/4HANA and Local Printers](https://help.sap.com/docs/SAP_S4HANA_CLOUD/0f69f8fb28ac4bf48d2b57b9637e81fa/1e39bb68bbda4c48af4a79d35f5837e0.html)

### SAP Fiori

For more information about integration with SAP Fiori, see the following resources:

- [Monitor SAP Fiori performance with Azure Application Insights](https://github.com/microsoft/ApplicationInsights-SAP-Fiori-Plugin)
- [Introduction to the Application Gateway WAF Triage Workbook](https://techcommunity.microsoft.com/t5/azure-network-security-blog/introducing-the-application-gateway-waf-triage-workbook/ba-p/2973341). 

Also see the following SAP resources:
- [Web Application Firewall Setup for Internet facing SAP Fiori Apps](https://blogs.sap.com/2020/12/03/sap-on-azure-application-gateway-web-application-firewall-waf-v2-setup-for-internet-facing-sap-fiori-apps/)

### Microsoft Entra ID (formerly Azure AD)

For more information about integrations with Microsoft Entra ID and Microsoft Entra ID Governance, see the following Microsoft Entra documentation:

- [SAP Scenarios Hub](https://aka.ms/EntraSAPHub)
- [Provision users from SAP SuccessFactors to Active Directory](/entra/identity/saas-apps/sap-successfactors-inbound-provisioning-tutorial)
- [Manage access to your SAP applications](/entra/id-governance/sap)
- [Secure access with SAP Cloud Identity Services and Microsoft Entra ID](/entra/fundamentals/scenario-azure-first-sap-identity-integration)
- [SAP workload security - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/sap/security)

For how to configure single sign-on, see the following Microsoft Entra documentation and tutorials:
- [SAP Cloud Identity Services - Identity Authentication](/entra/identity/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial)
- [SAP SuccessFactors](/entra/identity/saas-apps/successfactors-tutorial)
- [SAP Analytics Cloud](/entra/identity/saas-apps/sapboc-tutorial)
- [SAP Fiori](/entra/identity/saas-apps/sap-fiori-tutorial)
- [SAP Qualtrics](/entra/identity/saas-apps/qualtrics-tutorial)
- [SAP Ariba](/entra/identity/saas-apps/ariba-tutorial)
- [SAP Concur Travel and Expense](/entra/identity/saas-apps/concur-travel-and-expense-tutorial)
- [SAP Business Technology Platform](/entra/identity/saas-apps/sap-hana-cloud-platform-tutorial)
- [SAP Business ByDesign](/entra/identity/saas-apps/sapbusinessbydesign-tutorial)
- [SAP HANA](/entra/identity/saas-apps/saphana-tutorial)
- [SAP Cloud for Customer](/entra/identity/saas-apps/sap-customer-cloud-tutorial)

Also see the following SAP resources:
- [SAP GUI MFA with Microsoft Entra Private Access](https://community.sap.com/t5/technology-blog-posts-by-members/sap-gui-mfa-with-microsoft-entra-part-ii-integration-with-microsoft-entra/ba-p/13691141)
- [Azure Application Gateway Setup for Public and Internal SAP URLs](https://blogs.sap.com/2020/12/10/sap-on-azure-single-sign-on-configuration-using-saml-and-azure-active-directory-for-public-and-internal-urls/)
- [SAPGUI using Kerberos and Microsoft Entra Domain Services](https://blogs.sap.com/2018/08/03/your-sap-on-azure-part-8-single-sign-on-using-azure-ad-domain-services/)

### Azure Integration Services

For more information about using SAP with Azure Integration services, see the following Microsoft resources and Azure documentation:

- [Expose SAP Process Orchestration on Azure securely](expose-sap-process-orchestration-on-azure.md)
- [New SAP events on Azure Event Grid with SAP Event Mesh](https://techcommunity.microsoft.com/t5/messaging-on-azure-blog/new-sap-events-on-azure-event-grid/ba-p/3663372)
- [Connect to SAP from workflows in Azure Logic Apps](../../logic-apps/logic-apps-using-sap-connector.md)
- [Import SAP OData metadata as an API into Azure API Management](../../api-management/sap-api.md)
- [Apply SAP Principal Propagation to your Azure hosted APIs](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Request%20OAuth2%20access%20token%20from%20SAP%20using%20AAD%20JWT%20token.xml)
- [Using Logic Apps (Standard) to connect with SAP BAPIs and RFC](https://www.youtube.com/watch?v=ZmOPPtIYYM4)

Also see the following SAP resources:

- [Event-driven architectures for SAP ERP with Azure](https://blogs.sap.com/2021/12/09/hey-sap-where-is-my-xbox-an-insight-into-capitalizing-on-event-driven-architectures/)
- [Achieve high availability for SAP Cloud Integration (part of SAP Integration Suite) on Azure](https://blogs.sap.com/2021/09/23/black-friday-will-take-your-cpi-instance-offline-unless/)
- [Automate SAP invoice processing using Azure Logic Apps and Azure AI services](https://blogs.sap.com/2021/02/03/your-sap-on-azure-part-26-automate-invoice-processing-using-azure-logic-apps-and-cognitive-services/)

### App development in any language including ABAP and DevOps

For more information about integrating SAP with Microsoft services natively, see the following resources:

- [the ABAP SDK for Azure](https://github.com/microsoft/ABAP-SDK-for-Azure)
- [Use SAP's Cloud SDK with Azure app development services](https://github.com/Azure-Samples/app-service-javascript-sap-cloud-sdk-quickstart)
- [Use community-driven OData SDKs with Azure Functions](https://github.com/Azure/azure-sdk-for-sap-odata)

Also see the following SAP resources: 
- [SAP BTP ABAP Environment (also known as Steampunk) integration with Microsoft services](https://blogs.sap.com/2023/06/06/kick-start-your-sap-abap-platform-integration-journey-with-microsoft/)
- [SAP S/4HANA Cloud, private edition – ABAP Environment (also known as Embedded Steampunk) integration with Microsoft services](https://blogs.sap.com/2023/06/06/kick-start-your-sap-abap-platform-integration-journey-with-microsoft/)
- [dotNET speaks OData too, how to implement Azure App Service with SAP Gateway](https://blogs.sap.com/2021/08/12/.net-speaks-odata-too-how-to-implement-azure-app-service-with-sap-odata-gateway/)
- [Apply cloud native deployment practice blue-green to SAP BTP apps with Azure DevOps](https://blogs.sap.com/2019/12/20/go-blue-green-for-your-cloud-foundry-app-from-webide-with-azure-devops/)

### Azure Data Services

Learn how to **choose** the [best SAP connector for data integration](/azure/cloud-adoption-framework/scenarios/sap/sap-lza-choose-azure-connectors) and how to [tune performance including troubleshooting tips](/azure/cloud-adoption-framework/scenarios/sap/sap-lza-data-extraction-performance-troubleshooting) on our cloud adoption framework for SAP. Get started by identifying your SAP data sources [here](/azure/cloud-adoption-framework/scenarios/sap/sap-lza-identify-sap-data-sources).

Integrate with Azure OpenAI Service from SAP ABAP via the [Microsoft SDK for AI](https://microsoft.github.io/aisdkforsapabap/).

For more information about integration with Azure Data Services, see the following Microsoft and Azure resources:

- [SAP knowledge center for Azure Data Factory and Synapse](../../data-factory/industry-sap-overview.md)
- [Track end-to-end lineage of your SAP data with Microsoft Purview](https://techcommunity.microsoft.com/t5/security-compliance-and-identity/of-kings-amp-queens-and-sap-scans-to-identify-the-right-lineage/ba-p/3268816)
- [Replicating SAP data using the CDC connector](../../data-factory/sap-change-data-capture-introduction-architecture.md)
- [SAP CDC Connector and SLT - Blog series - Part 1](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/sap-cdc-connector-and-slt-part-1-overview-and-architecture/ba-p/3775190)
- [Replicating SAP data using the OData connector with Synapse Pipelines](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/extracting-sap-data-using-odata-part-1-the-first-extraction/ba-p/2841635)
- [Use SAP HANA in Power BI Desktop](/power-bi/desktop-sap-hana)
- [DirectQuery and SAP HANA](/power-bi/desktop-directquery-sap-hana)
- [Use the SAP BW Connector in Power BI Desktop](/power-bi/desktop-sap-bw-connector)
- [Enable SAP Principal Propagation for live OData feeds with Power Query](expose-sap-odata-to-power-query.md)
- [SAP HANA Connector for Power Query](/power-query/connectors/sap-hana/overview)

Also see the following SAP resources:
- [Integrate SAP Data Warehouse Cloud with Power BI and Azure Synapse Analytics](https://blogs.sap.com/2022/07/27/your-sap-on-azure-part-28-integrate-sap-data-warehouse-cloud-with-powerbi-and-azure-synapse/)
- [Extend SAP Integrated Business Planning forecasting algorithms with Azure Machine Learning](https://blogs.sap.com/2022/10/03/microsoft-azure-machine-learning-for-supply-chain-planning/)

### Microsoft Security for SAP

Protect your data, apps, and infrastructure against rapidly evolving cyber threats with cloud security services from Microsoft. Artificial intelligence (AI) and device learning (ML) backed capabilities are required to keep up with the pace.

Use [Microsoft Defender for Cloud](/azure/defender-for-cloud/defender-for-cloud-introduction) to secure your cloud-infrastructure surrounding the SAP system including automated responses.

Complimenting that, use the [SAP certified](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/solutions?id=s:33db1376-91ae-4f36-a435-aafa892a88d8) solution [Microsoft Sentinel for SAP](../../sentinel/sap/sap-solution-security-content.md) to protect your SAP system and [SAP Business Technology Platform (BTP)](../../sentinel/sap/sap-btp-solution-overview.md) instance from within using signals from the SAP Audit Log among others.

Unify all your security solutions for Microsoft 365, cloud-infrastructure, and SAP in a [single experience](/unified-secops-platform/overview-unified-security) in the Defender portal. Profit from the correlation of signals across the Microsoft ecosystem and connected third parties to detect and respond to threats in real-time.

Learn more about identity focused integration capabilities that power the analysis on Defender and Microsoft Sentinel via the [Microsoft Entra ID section](#microsoft-entra-id-formerly-azure-ad).

Use the [immutable vault for Azure Backup](/azure/backup/backup-azure-immutable-vault-concept) to protect your SAP data from ransomware attacks.

See the Microsoft Security Copilot working with an SAP Incident in action [here](https://www.youtube.com/watch?v=snV2joMnSlc&t=234s).

Discover partner offerings for SAP security on the [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/consulting-services?search=Sentinel%20for%20SAP&page=1).

#### Microsoft Sentinel for SAP

Microsoft Sentinel integrates directly with Microsoft Defender XDR and the Microsoft Defender portal. SAP solutions are available in the Defender portal as part of [Microsoft's unified security operations platform](/unified-secops-platform/), and with [Microsoft Sentinel in the Defender portal](/azure/sentinel/microsoft-sentinel-defender-portal).

For more information about [SAP certified](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/solutions?id=s:33db1376-91ae-4f36-a435-aafa892a88d8) threat monitoring with Microsoft Sentinel for SAP, see the following Microsoft resources:

- [Microsoft Sentinel solution for SAP applications](/azure/sentinel/sap/solution-overview)
- [Microsoft Sentinel solution for SAP BTP](/azure/sentinel/sap/sap-btp-solution-overview)
- [Agentless data connector using SAP Integration Suite](https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-members/microsoft-sentinel-for-sap-goes-agentless/ba-p/13960238)
- [SAP LogServ (RISE) solution for Microsoft Sentinel](https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/sap-logserv-integration-with-microsoft-sentinel-for-sap-rise-customers-is/bc-p/14089301)
- [SAP Enterprise Threat Detection (ETD) solution for Microsoft Sentinel](https://community.sap.com/t5/enterprise-resource-planning-blogs-by-sap/sap-enterprise-threat-detection-cloud-edition-joins-forces-with-microsoft/ba-p/13942075)

Also see the following SAP resources:

- [How to use Microsoft Sentinel's SOAR capabilities with SAP](https://blogs.sap.com/2023/05/22/from-zero-to-hero-security-coverage-with-microsoft-sentinel-for-your-critical-sap-security-signals-blog-series/)
- [Deploy SAP user blocking based on suspicious activity on the SAP backend](https://blogs.sap.com/2023/05/22/from-zero-to-hero-security-coverage-with-microsoft-sentinel-for-your-critical-sap-security-signals-youre-gonna-hear-me-soar-part-1/)
- [Automatically trigger reactivation of the SAP audit log on malicious deactivation](https://blogs.sap.com/2023/05/23/from-zero-to-hero-security-coverage-with-microsoft-sentinel-for-your-critical-sap-security-signals-part-3/)

See below video to experience the SAP security orchestration, automation, and response workflow with Microsoft Sentinel in action:

> [!VIDEO https://www.youtube.com/embed/b-AZnR-nQpg]

#### Microsoft Defender XDR and Defender for Cloud

The [Defender product family](/azure/defender-for-cloud/defender-for-cloud-introduction) consist of multiple products tailored to provide "cloud security posture management" (CSPM) and "cloud workload protection" (CWPP) for the various workload types. Below excerpt serves as entry point to start securing your SAP system.

- Defender for Servers (SAP hosts)
    - [Protect your SAP hosts with Defender](/azure/defender-for-cloud/defender-for-servers-introduction) including OS specific Endpoint protection with Microsoft Defender for Endpoint (MDE)
    - [Microsoft Defender for Endpoint on Linux](/microsoft-365/security/defender-endpoint/mde-linux-deployment-on-sap)
    - [Microsoft Defender for Endpoint on Windows](/microsoft-365/security/defender-endpoint/mde-sap-windows-server)
    - [Enable Defender for Servers](/azure/defender-for-cloud/tutorial-enable-servers-plan#enable-the-defender-for-servers-plan)
- Defender for Storage (SAP SMB file shares on Azure)
    - [Protect your SAP SMB file shares with Defender](/azure/defender-for-cloud/defender-for-storage-introduction)
    - [Enable Defender for Storage](/azure/defender-for-cloud/tutorial-enable-storage-plan)
- Defender for APIs (SAP Gateway, SAP Business Technology Platform, SAP SaaS)
    - [Protect your OpenAPI APIs with Defender for APIs](/azure/defender-for-cloud/defender-for-apis-introduction)
    - [Enable the Defender for APIs](/azure/defender-for-cloud/defender-for-apis-deploy)
- Defender For Cloud Apps (MDCA)
    - [SAP LeanIX integration](https://community.sap.com/t5/technology-blog-posts-by-members/sap-leanix-integrating-microsoft-defender-for-cloud-apps/ba-p/14089439)

See SAP's recommendation to use AntiVirus software for SAP hosts and systems on both Linux and Windows based platforms [here](https://wiki.scn.sap.com/wiki/display/Basis/Protecting+SAP+systems+using+antivirus+softwares). The threat landscape has evolved from file-based attacks to file-less attacks, and the protection approach has to evolve beyond pure AntiVirus capabilities too.

For more information about using Microsoft Defender for Endpoint (MDE) via Microsoft Defender for Server for SAP applications regarding `Next-generation protection` (AntiVirus) and `Endpoint Detection and Response` (EDR) see the following Microsoft resources:

- [SAP Applications and Microsoft Defender for Linux | Microsoft TechCommunity](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/sap-applications-and-microsoft-defender-for-linux/ba-p/3675480)
- [SAP Applications and Microsoft Defender for Windows Server | Microsoft TechCommunity](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/microsoft-defender-endpoint-mde-for-sap-applications-on-windows/ba-p/3912268)
- [Enable the Microsoft Defender for Endpoint integration](/azure/defender-for-cloud/enable-defender-for-endpoint)
- [Common mistakes to avoid when defining exclusions](/microsoft-365/security/defender-endpoint/common-exclusion-mistakes-microsoft-defender-antivirus)

Also see the following SAP resources:

- [3356389 - Antivirus or other security software affecting SAP operations](https://me.sap.com/notes/3356389)
- [2808515 - Installing security software on SAP servers running on Linux](https://me.sap.com/notes/2808515)
- [1730997 - Unrecommended versions of antivirus software](https://me.sap.com/notes/1730997)

> [!Note]
> It is **not recommended** to exclude files, paths or processes from EDR because it creates blind spots for Defender. If exclusions are required nevertheless, open a support case with Microsoft Support via the Defender365 Portal specifying executables and/or paths to exclude. Follow the same process for tuning of real-time scans.

> [!Note]
> Certification for the SAP Virus Scan Interface (NW-VSI) doesn't apply to MDE, because it operates outside of the SAP system. It complements Microsoft Sentinel for SAP, which interacts with the SAP system directly. See more details and the SAP certification note for Sentinel below.

> [!Tip]
> MDE was formerly called Microsoft Defender Advanced Threat Protection (ATP). Older articles or SAP notes still refer to that name.

> [!Tip]
> Microsoft Defender for Server includes Endpoint detection and response (EDR) features that are provided by Microsoft Defender for Endpoint Plan 2.

#### Immutable vault for Azure Backup for SAP

For more information about [immutable vault for Azure Backup](/azure/backup/backup-azure-immutable-vault-concept), see the following Azure documentation:

- [Backup and restore plan to protect against ransomware](/azure/security/fundamentals/backup-plan-to-protect-against-ransomware)
- [Back up SAP HANA System Replication databases on Azure VMs](/azure/backup/sap-hana-database-with-hana-system-replication-backup#create-a-recovery-services-vault)

### SAP BTP

For more information about Azure integration with SAP Business Technology Platform (BTP), see the following SAP resources:

- [SAP Discovery Center for Azure Services and Missions](https://discovery-center.cloud.sap/search/Azure)
- [Getting Started with SAP Private Link Service for Azure](https://blogs.sap.com/2021/12/29/getting-started-with-btp-private-link-service-for-azure/)
- [SAP Private Link service use cases for SAP Cloud Integration and SAP Launchpad Service](https://blogs.sap.com/2022/08/22/sap-private-link-service-use-cases-for-sap-cloud-integration-and-sap-launchpad/)
- [Automate SAP Cloud Integration flow recovery](https://blogs.sap.com/2022/03/04/how-to-notice-and-handle-faulty-cpi-iflow-states-automatically/)
- [Monitor multiple SAP Cloud Integration tenants with Azure Monitor](https://blogs.sap.com/2020/11/11/broadcast-cpi-errors-to-azure-monitor-via-scp-alert-notifications/)
- [Route Multi-Region Traffic to SAP BTP Services Intelligently with Azure Traffic Manager](https://discovery-center.cloud.sap/missiondetail/3603/)
- [Distributed Resiliency of SAP CAP applications using SAP HANA Cloud with Azure Traffic Manager](https://blogs.sap.com/2022/11/12/distributed-resiliency-of-sap-cap-applications-using-sap-hana-cloud-multi-zone-replication-with-azure-traffic-manager/)
- [Federate your data from Azure Data Explorer to SAP Datasphere](https://discovery-center.cloud.sap/missiondetail/3433/3473/)
- [Integrate globally available SAP BTP apps with Azure Cosmos DB via OData](https://blogs.sap.com/2021/06/11/sap-where-can-i-get-toilet-paper-an-implementation-of-the-geodes-pattern-with-s4-btp-and-azure-cosmosdb/)
- [Building Applications on SAP BTP with Microsoft Services | OpenSAP course](https://open.sap.com/courses/btpma1)

## Customer resources

These resources include Customer Engagement Initiatives (CEI), public BETAs, and Customer Influence programs:

- [SAP Joule integration with Microsoft Copilot private preview](https://aka.ms/CopilotJoule)
- [SAP Event Mesh integration with Microsoft Azure Event Grid - Aug 2022 | SAP Customer Influence](https://influence.sap.com/sap/ino/#campaign/2836)
- [SAP Private Link service CEI - Jul 2022 | SAP Customer Influence](https://influence.sap.com/sap/ino/#campaign/3118)

## Free developer accounts

You can use the following free developer accounts to explore integration scenarios for Azure and SAP.

- [Free trial of Azure](https://azure.microsoft.com/free/)
- [Free trial of Azure for students](https://azure.microsoft.com/free/students/)
- [Free account on SAP BTP trial](https://developers.sap.com/tutorials/hcp-create-trial-account.html). Select Singapore for Azure.
- [GitHub account](https://github.com/), which you can use to host your projects.
- [Microsoft 365 developer program account](https://developer.microsoft.com/microsoft-365/dev-program)

## Next steps

- [Discover native SAP applications available on the Microsoft Teams marketplace](https://appsource.microsoft.com/marketplace/apps?page=1&search=sap&product=teams)
- [Browse the out-of-the-box SAP applications available on Microsoft Power Platform](/power-automate/sap-integration/overview?source=recommendations#prebuilt-sap-integration-solution)
- [Understand SAP data integration with Azure - Cloud Adoption Framework](/azure/cloud-adoption-framework/scenarios/sap/sap-lza-identify-sap-data-sources)
- [Identify your SAP data sources - Cloud Adoption Framework](/azure/cloud-adoption-framework/scenarios/sap/sap-lza-identify-sap-data-sources)
- [Explore joint reference architectures on the SAP Discovery Center](https://discovery-center.cloud.sap/search/Azure)
- [Secure your SAP NetWeaver email needs with Exchange Online](./exchange-online-integration-sap-email-outbound.md)
- [Migrate your legacy SAP middleware to Azure](./expose-sap-process-orchestration-on-azure.md)
