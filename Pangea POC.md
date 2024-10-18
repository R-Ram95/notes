---
Author: Rohinesh Ram
tags:
  - Frontend
  - Project-Notes
---
- Overview> [!important]  
    > OverviewGoal: Take a DTLS Application which is built on SAPUI5 or OpenUI5 and put it into a Docker containerThis is the Fiori Launch Pad showing the DTLS Modules![[/Untitled 60.png|Untitled 60.png]]It is best to do one of the maintenance apps for the POCProcess Builder is 80% of DTLSConfiguration and maintenance apps are in service of Process BuilderApps must adhere to Fiori Style GuidelinesSAP uses Business Application Studio IDE for building the applicationsTwo flavours of the ui5 ⇒ SAPUI5 and OpenUI5MVCPangae launchpad shares the space with other SAP Applications → this is why they need to adhere to the SAPUI5 application guidlines:![[/Untitled 1 41.png|Untitled 1 41.png]]if we were to build a Custom Launchpad ⇒ we need to able to integrate the other applications into itwe will still rely on SAP because we need to use SAP Systems to register our Applications and integrate them with the SAP Authorization  
    
- Background> [!important]  
    > BackgroundHow Fiori Launch pad works![[/Untitled 2 32.png|Untitled 2 32.png]]Fiori apps run on the DE2 system ⇒ their on-prem servers which are actually run on AWSall of the front end development is done through a web-based IDE which is running on BTP![[/Untitled 3 31.png|Untitled 3 31.png]]SAP BTP Subaccount is the FE (Fiori Launchpad) where the DTLS applications are “embedded” (accessed?)  
    
- Questions> [!important]  
    > Questionswhat app are we going to put into a container?dtls-equipment-masterwhat is Fiori? what is Fiori UI5?Fiori: SAP Fiori is a design concept and user experience (UX) approach developed by SAPFiori Apps are accessed through the Fiori Launchpad ⇒ a central entry point where users can find and launch appsFiori UI5 = SAPUI5 = SAP Fiori UI5: JS Based framework for developing SAP ApplicationsMVC ArchitectureData binding ⇒ devs can bind UI elements to data modleswhat is odata?Open Data Protocolstandardized protocol for building and consuming web APIsSupports RESTSupports multiple formats for respresenting info: JSON, XMLwhat is UI5? what is openUI5? what is SAPUI5?UI5 = generic term for UI tech and frameworksOpenUI5 = open source version of SAPUI5SAPUI5 = proprietary JavaScript framework developed by SAPSAPUI5 is used within the SAP ecosystem, OpenUI5 is intended for broader usage, including non-SAP projectsWhat is SAP ABAP?programming language developed by SAPtied to SAPs NetWeaver Application ServerWhat is SAP BTP?SAP Business Technology PlatformSuite for technologies and services offered by SAP to support businesses in digital transformationIncludes:Database and Data Management: e.g. SAP HANAAnalyticsApplication Development and Integration: e.g IDEetc.What is NetWeaver?Application Server which provides the Runtime Env for SAP ApplicationsManages the Execution of ABAPIncludes a Web Application Server → supports development and deployment of web-based applicationswhat is cache buster?invalidate browser cache when there is a new deployment of a UI5 app  
    
- UI5 Documentation Review> [!important]  
    > UI5 Documentation ReviewWhat is SAPUI5/OpenUI5?SAPUI5 (SAP User Interface for HTML5) and OpenUI5 are JavaScript frameworks developed by SAP for building responsive and cross-browser enterprise web applications. They share a common codebase, but SAPUI5 is primarily used within the SAP ecosystem, while OpenUI5 is an open-source version available to the broader community.Environments:Server Environment:SAP Environment (for SAPUI5): SAPUI5 applications are often integrated with SAP systems, and the server-side logic can be implemented using SAP NetWeaver or SAP Cloud Platform.OpenUI5 Environment: OpenUI5 applications can be deployed on any web server. They are not tied to SAP-specific environments, providing more flexibility.Client Environment:Browser: Both SAPUI5 and OpenUI5 applications run on web browsers, leveraging HTML5, CSS3, and JavaScript for rendering and interaction.Tools:IDE (Integrated Development Environment):SAP Web IDE (for SAPUI5): SAP provides a dedicated IDE for developing SAPUI5 applications. It offers features like code completion, project management, and integration with SAP backend systems.Any Code Editor (for OpenUI5): Since OpenUI5 is open source and platform-agnostic, developers can use their preferred code editors like Visual Studio Code, Sublime Text, or Atom.Build Tools:UI5 Tooling (for both): UI5 Tooling is a command-line tool that facilitates the development and build process for both SAPUI5 and OpenUI5. It helps with tasks like bundling, transpilation, and dependency management.Version Control:Git (for both): Developers commonly use Git for version control, allowing collaborative development, branching, and merging.Development Concepts:Component-Based Architecture:SAPUI5 and OpenUI5: Both frameworks follow a component-based architecture, where the application is divided into reusable and modular components. Components encapsulate UI and logic, promoting maintainability and reusability.Data Binding:SAPUI5 and OpenUI5: Both frameworks support data binding, allowing the synchronization of UI elements with underlying data models. This ensures that changes in the data automatically reflect in the UI and vice versa.Relating to React:While SAPUI5/OpenUI5 and React serve different purposes, there are some conceptual similarities:Component-Based Approach:Both SAPUI5/OpenUI5 and React emphasize a component-based approach, encouraging the development of modular, reusable components.Data Binding:React also supports a form of data binding through its virtual DOM, allowing efficient updates of the UI in response to changes in the underlying data.Development Tools:React developers commonly use tools like npm, Webpack, and Babel for building and bundling, similar to the UI5 Tooling used in SAPUI5/OpenUI5.Open Source:OpenUI5, being open source, is more similar to React in terms of community-driven development and flexibility, compared to the more SAP-centric SAPUI5.In summary, while the specific tools and environments may differ, the underlying principles of component-based development and data binding are shared among SAPUI5, OpenUI5, and React.SAPUI5 and OpenUI5 are primarily client-side frameworks, meaning that the rendering of the user interface occurs on the client's web browser. The server's role is to deliver the necessary HTML, JavaScript, CSS, and other static assets to the client, but the actual rendering and interaction take place on the user's device.Both frameworks rely on a single-page application (SPA) architecture, where a single HTML page is loaded initially, and subsequent interactions with the application result in dynamic updates without full page reloads.While SAPUI5 and OpenUI5 are client-side focused, they can interact with server-side logic through various mechanisms, such as making asynchronous calls (AJAX) to server APIs, communicating with backend services, or integrating with SAP systems. However, the core rendering of the UI components and the user experience is handled on the client side.Where is Launchpad Deployed?The SAP Fiori launchpad can be deployed in various environments, and the choice often depends on the specific requirements and preferences of the organization. Here are some common deployment options for the Fiori launchpad:On-Premises Deployment:Organizations may choose to deploy the Fiori launchpad on their own on-premises infrastructure, typically using SAP NetWeaver Application Server for ABAP or SAP HANA.Cloud Deployment:SAP Fiori can be deployed in the cloud, leveraging services such as SAP Cloud Platform (SCP). This allows for greater scalability, flexibility, and the ability to take advantage of cloud-related benefits.Hybrid Deployment:Some organizations opt for a hybrid deployment, combining on-premises and cloud solutions. This approach provides the flexibility to leverage both existing on-premises infrastructure and cloud services as needed.SAP Fiori Launchpad on SAP S/4HANA:SAP Fiori is often tightly integrated with SAP S/4HANA, SAP's next-generation enterprise resource planning (ERP) suite. In this case, Fiori is deployed alongside or as part of the SAP S/4HANA landscape.Mobile Deployment:Fiori apps are designed to be responsive and mobile-friendly. Organizations can deploy Fiori in a mobile environment, enabling users to access applications on various devices, such as smartphones and tablets.The choice of deployment depends on factors such as the existing IT landscape, infrastructure preferences, scalability requirements, and the overall IT strategy of the organization. SAP provides flexibility in deployment options to accommodate diverse enterprise needs. It's essential for organizations to assess their specific requirements and consult with SAP experts to determine the most suitable deployment approach for their Fiori launchpad implementation.  
    
- DTLS Equipment Manger Code Review> [!important]  
    > DTLS Equipment Manager Code ReviewRunning app locallyuses BE proxy ⇒ http://de2:8001uses the SAPUI5 Framework, not OpenUI5neo-app.json - Project settings for SAP Web IDEmanifest.json - configuration for entry Componentui5-local.yaml - local configuration when running app locallypackage.json Commandspackage.json Commands build_old: "ui5 build --clean-dest --include-task=generateManifestBundle generateCachebusterInfo"start: "fiori run --open \\"test/flpSandbox.html?sap-client=900&sap-ui-xx-viewCache=false\#app-tile\\""start-local: "fiori run --config ./ui5-local.yaml --open \\"test/flpSandboxMockServer.html?sap-client=900&sap-ui-xx-viewCache=false#app-tile\\"",start-noflp: "fiori run --open \\"index.html?sap-client=900&sap-ui-xx-viewCache=false\\"",start-variants-management: "fiori run --open \\"preview.html?sap-client=900&sap-ui-xx-viewCache=false&fiori-tools-rta-mode=true&sap-ui-rta-skip-flex-validation=true\#preview-app\\"",build: "ui5 build -a --clean-dest --include-task=generateCachebusterInfo",deploy: "npm run build && fiori deploy --config ui5-deploy.yaml && rimraf archive.zip",deploy-config: "fiori add deploy-config",start-mock: "fiori run --open \\"test/flpSandboxMockServer.html?sap-client=900&sap-ui-xx-viewCache=false#app-tile\\"",undeploy: "fiori undeploy --config ui5-deploy.yaml",deploy-test: "npm run build && fiori deploy --config ui5-deploy.yaml --testMode true"what does fiori run do?what is flp?Fiori Launch Pad: start-local launches in a sandbox Fiori Launch Pagestart-noflp: starts the app in standalone mode (No Launch Pad)manifest.jsonmanifest.jsonContains component metadataexpresses the component dependencies and configuration{  
    "_version": "1.12.0",  
    "sap.app": {  
    "id": "ca.dtls.equipment",  
    "type": "application",  
    "i18n": "i18n/i18n.properties",  
    "applicationVersion": {  
    "version": "1.0.1"  
    },  
    "title": "{{appTitle}}",  
    "description": "{{appDescription}}",  
    "sourceTemplate": {  
    "id": "servicecatalog.connectivityComponentForManifest",  
    "version": "0.0.0",  
    "toolsId": "d8427761-fb68-4fc2-87f7-407b64071a3f"  
    },  
    "dataSources": {  
    "ZDTLS_PB_GW_SRV": {  
    "uri": "/sap/opu/odata/SAP/ZDTLS_PB_GW_SRV/",  
    "type": "OData",  
    "settings": {  
    "odataVersion": "2.0",  
    "localUri": "localService/metadata.xml"  
    }  
    },  
    "auth": {  
    "uri": "/sap/opu/odata/SAP/ZDTLS_AUTH_GW_SRV/",  
    "type": "OData",  
    "settings": {  
    "odataVersion": "2.0"  
    }  
    }  
    }  
    },  
    "sap.ui": {  
    "fullWidth": true,  
    "technology": "UI5",  
    "icons": {  
    "icon": "",  
    "favIcon": "",  
    "phone": "",  
    "phone@2": "",  
    "tablet": "",  
    "tablet@2": ""  
    },  
    "deviceTypes": {  
    "desktop": true,  
    "tablet": true,  
    "phone": true  
    }  
    },  
    "sap.ui5": {  
    "flexEnabled": false,  
    "rootView": {  
    "viewName": "ca.dtls.equipment.view.App",  
    "type": "XML",  
    "async": true,  
    "id": "App"  
    },  
    "dependencies": {  
    "minUI5Version": "1.65.6",  
    "libs": {  
    "sap.ui.layout": {},  
    "sap.ui.core": {},  
    "sap.m": {},  
    "sap.f": {}  
    }  
    },  
    "contentDensities": {  
    "compact": true,  
    "cozy": true  
    },  
    "models": {  
    "i18n": {  
    "type": "sap.ui.model.resource.ResourceModel",  
    "settings": {  
    "bundleName": "ca.dtls.equipment.i18n.i18n"  
    }  
    },  
    "fclModel": {  
    "type": "sap.ui.model.json.JSONModel"  
    },  
    "json": {  
    "type": "ca.dtls.equipment.model.JsonModel"  
    },  
    "odata": {  
    "type": "ca.dtls.equipment.model.ODataModel",  
    "settings": {  
    "defaultBindingMode": "TwoWay",  
    "defaultCountMode": "Inline",  
    "defaultUpdateMethod": "PUT",  
    "refreshAfterChange": false,  
    "useBatch": true  
    },  
    "dataSource": "ZDTLS_PB_GW_SRV"  
    },  
    "auth": {  
    "type": "ca.dtls.equipment.model.AuthModel",  
    "dataSource": "auth",  
    "settings": {  
    "defaultBindingMode": "OneWay",  
    "defaultCountMode": "Inline"  
    }  
    }  
    },  
    "resources": {  
    "css": [  
    {  
    "uri": "css/style.css"  
    }  
    ]  
    },  
    "routing": {  
    "config": {  
    "routerClass": "sap.f.routing.Router",  
    "viewType": "XML",  
    "async": true,  
    "viewPath": "ca.dtls.equipment.view",  
    "controlId": "app"  
    },  
    "routes": [  
    {  
    "name": "equipmentList",  
    "pattern": ":layout:",  
    "target": [  
    "equipmentList"  
    ]  
    },  
    {  
    "name": "equipmentDetail",  
    "pattern": "{Equipment}/{ChangeCounter}/{layout}",  
    "target": [  
    "equipmentPage"  
    ]  
    },  
    {  
    "name": "equipmentCreate",  
    "pattern": "create/{layout}",  
    "target": [  
    "equipmentPage"  
    ]  
    },  
    {  
    "name": "parameterWhereUsedPage",  
    "pattern": "WhereUsed/{Equipment}/{ChangeCounter}/{layout}",  
    "target": [  
    "parameterWhereUsedPage"  
    ]  
    }  
    ],  
    "targets": {  
    "equipmentList": {  
    "viewName": "S2_EquipmentList",  
    "controlAggregation": "beginColumnPages"  
    },  
    "equipmentPage": {  
    "viewName": "S3_EquipmentPage",  
    "controlAggregation": "beginColumnPages"  
    },  
    "parameterWhereUsedPage": {  
    "viewName": "S4_WhereUsedPage",  
    "controlAggregation": "beginColumnPages"  
    }  
    }  
    },  
    "services": {  
    "ShellUIService": {  
    "factoryName": "sap.ushell.ui5service.ShellUIService"  
    }  
    }  
    },  
    "sap.platform.hcp": {  
    "uri": "webapp",  
    "_version": "1.1.0"  
    }  
    }  
    what is ZDTLS_PB_GW_SRV → the BE where the Odata comes from?what is ZDTLS_AUTH_GW_SRV → the auth server?  
    
- Fiori Launchpad
    
    Applications are developed on SAP and registered with the Fiori Launchpad
    
    - requires T-CODE
    
    - **Giving access to UI5 Applications in the Launchpad:**
- Building Custom Fiori Launchpad> [!important]  
    > Building Custom Fiori LaunchpadThe custom launchpad acts as a ComponentContainer and instantiates apps without having a local html file ⇒ This allows several apps to be displayed in the same contexti.e. each app does not need its own html file because they can be embedded into the html of the Custom Launchpad as opposed to the apps opening up in a separate windowtile-based ⇒ each tile represents an OpenUI5 app (component - these are used interchangeably) that, when clicked, will load the OpenUI5 app into the Custom Launchpadapps need to be loaded dynamically from any source entry-point of apps should be the Component.js fileeach tile should have some authorization mechanism that is role-basedthe user logs into the Custom Launchpad tiles are available based on the Users roleSome components need to be re-written (see the SAPUI5 to OpenUI5 catalog)Apps are served to the browser as static files  
      
    
- SAPUI5 to OpenUI5 Catalog
    
    Catalog of code that needs to be re-written to move form SAPUI5 to OpenUI5
    
    API for OpenUI5:
    
    > [!info] SAPUI5 SDK - Demo Kit  
    >  
    > [https://sapui5.hana.ondemand.com/#/api](https://sapui5.hana.ondemand.com/#/api)  
    
    API for SAPUI5
    
    > [!info] SAPUI5 SDK - Demo Kit  
    >  
    > [https://sapui5.hana.ondemand.com/#/api](https://sapui5.hana.ondemand.com/#/api)  
    
      
    
    > [!important]  
    > dtls-equipmentFiles that need to be changed:S2_EquipmentList.view.xml S4_WhereUsedPage.view.xmlS2_EquipmentList.controller.jsS4_WhereUsedPage.controller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar.vh (expirimental in OpenUI5)NOTE: the OpenUI5 version of filterbar does not have the filterGroupItems aggregation that is present in SAPUI5’s version ⇒ this will need to be rewritten  
    
      
    
    > [!important]  
    > dtls-assign-material-classesFiles that need to change:S2_EquipmentList.view.xmlSelectMaterialClassVH.fragment.xmlS2_EquipmentList.contoller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
    
      
    
    > [!important]  
    > dtls-characteristicFiles that need to change:S2_EquipmentList.view.xml S3_WhereUsedPage.view.xmlS2_EquipmentList.controller.jsS4_WhereUsedPage.controller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
    
      
    
    > [!important]  
    > dtls-flavour-groupFiles that need to change:S2_EquipmentList.view.xmlS3_WhereUsedPage.view.xmlS2_EquipmentList.controller.jsS4_WhereUsedPage.controller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
      
    
      
    
    > [!important]  
    > dtls-handover-to-manufacturingFiles that need to change:S3_Detail.view.xmlS3_Detail.controller.jsReason:uses sap.suite.ui.commons.networkgraph and sap.suite.ui.commons.networkgraph.layout from SAPUI5 and there is no equivalent in OpenUI5 - these files/components will need to be completely re-writtenFiles that need to change:S2_List.view.xmlS2_List.controller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
    
      
    
    > [!important]  
    > dtls-hold-times-typeFiles that need to change:S2_HoldTimesList.view.xmlS3_HoldTimesPage.view.xmlS4_WhereUsedPage.view.xmlS2_HoldTimesList.contoller.jsS3_HoldTimesPage.contoller.jsS4_WhereUsedPage.contoller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
    
      
    
    > [!important]  
    > dtls-intermediate-quality-attribute-typeFiles that need to change:S2_InterQualityAttributeList.view.xmlS4_WhereUsedPage.view.xmlS2_InterQualityAttributeList.controller.jsS4_WhereUsedPage.controller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
    
      
    
    > [!important]  
    > dtls-materialsFiles that need to change:S2_MaterialList.view.xmlS4_WhereUsed.view.xmlS2_MaterialList.controller.jsS4_WhereUsed.controller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
    
      
    
    > [!important]  
    > dtls-material-classesFiles that need to change:S2_List.view.xmlS4_WhereUsed.view.xmlS2_List.controller.jsS4_WhereUsed.controller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
    
      
    
    > [!important]  
    > dtls-process-areasFiles that need to change:S2_ProcessAreaList.view.xmlS3_WhereUsedPage.view.xmlS2_ProcessAreaList.controller.jsS3_WhereUsedPage.controller.js Reason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
    
      
    
    > [!important]  
    > dtls-process-historyFiles that need to change:S2_ProcessList.view.xmlS2_ProcessList.controller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)Files that need to change:S4_BuilderPage.view.xmlS4_BuilderPage.controller.jsS4_MilestonesPage.view.xmlS4_MilestonesPage.controller.jsReason:uses sap.suite.ui.commons.networkgraph,sap.suite.ui.commons.networkgraph.layout, and sap.suite.ui.commonsfrom SAPUI5 and there is no equivalent in OpenUI5 - these files/components will need to be completely re-written  
    
      
    
      
    
    > [!important]  
    > dtls-process-paramter-typeFiles that need to change:S2_ParameterList.view.xmlS3_ParameterPage.controller.jsS4_WhereUsedPage.view.xmlS4_WhereUsedPage.controller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
    
      
    
      
    
    > [!important]  
    > dtls-relationship-explorerFiles that need to change:S2_RelationshipList.view.xmlS2_RelationshipList.controller.jsS4_DetailsPage.controller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)Files that need to change:S3_RelationshipPage.view.xmlReason:uses sap.suite.ui.commons.networkgraph and sap.suite.ui.commons.networkgraph.layout from SAPUI5 and there is no equivalent in OpenUI5 - these files/components will need to be completely re-written  
    
      
    
    > [!important]  
    > dtls-sample-planFiles that need to change:S2_SamplePlanList.view.xmlS2_SamplePlanList.controller.jsS3_SamplePlanPage.controller.jsS4_WhereUsedPage.view.xmlS4_WhereUsedPage.controller.jsReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
    
      
    
    > [!important]  
    > dtls-teaFiles that need to change:S2_ProcessList.view.xmlS2_ProcessList.controller.jsS3_ProcessExplorer.view.xmlS3_ProcessExplorer.controller.jscqaSection.fragment.xmlReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)Files that need to change:S3_ProcessExplorerGraph.controller.jsReason:uses sap/viz/ui5/format/ChartFormatter, sap/viz/ui5/api/env/Format, and sap/viz/ui5/data/FlattenedDataset from SAPUI5, there is no OpenUI5 equivalent and this file will need to be rewritten using OpenUI5  
    
      
    
    > [!important]  
    > dtls-workflow-mainFiles that need to change:S2_ObjectList.view.xmlS2_ObjectList.controller.jsCreateNewObjectDialog.fragment.xmlViewEditObjectDialog.fragment.xmlReason:uses sap.ui.comp.filterbar ⇒ needs to be converted to use sap.ui.mdc.filterbar (expirimental in OpenUI5)  
    
      
    
      
    
- Endpoints
    
    Two Odata services hosted on de2.internal:
    
    - `/sap/opu/odata/SAP/ZDTLS_PB_GW_SRV`
        - I believe this is where the oData models live
    - `/sap/opu/odata/SAP/ZDTLS_AUTH_GW_SRV`
    
    > [!important]  
    > UserEndpoint: https://de2.internal.iss-i.ca:8001/sap/opu/odata/sap/ESH_SEARCH_SRV/Users('<current>')Response:{  
    "d": {  
    "__metadata": {  
    "id": "https://de2.internal.iss-i.ca:8001/sap/opu/odata/sap/ESH_SEARCH_SRV/Users('<USER-ID>')",  
    "uri": "https://de2.internal.iss-i.ca:8001/sap/opu/odata/sap/ESH_SEARCH_SRV/Users('<USER-ID>')",  
    "type": "ESH_SEARCH_SRV.User"  
    },  
    "Id": <USER-ID>,  
    "Name": <USERS-FULL-NAME>,  
    "FirstName": <FIRST-NAME>,  
    "LastName": <LAST-NAME>,  
    "IsCurrentUser": true,  
    "IsEnabledForPersonalizedSearch": false,  
    "ClearPersonalizedSearchHistory": false  
    }  
    }  
    
      
    
    > [!important]  
    > dtls-equipment classes  
    
      
    
      
    
      
    
- Links> [!important]  
    > LinksNameTags[[SAPUI5 vs. OpenUI5]][[SAPUI5 Quick Start Tutorial]][[SAPUI5 VSCode Setup]][[Deploying SAPUI5 to Kyma Runtime]][[NPM Package - UI5 Tools]][[How to Containerize UI5 with Nginx]][[Fiori Tools - Deploying Applications Docs]][[Docker Image for SAP Cloud Platform]][[Fiori Launchpad Setup + Fiori App Deployment]][[Creating Fiori Launchpad]][[SAP Fiori Documentation]][[Fiori Architecture]][[Launchpad Documentation]][[SAP Development Tools]][[Embed One UI5 app into Another]][[Software Wiki/WIKI/Pangea POC/Untitled/Untitled\|Untitled]]