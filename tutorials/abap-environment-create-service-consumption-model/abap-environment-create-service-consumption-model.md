---
parser: v2
auto_validation: true
time: 45
tags: [ tutorial>intermediate, products>sap-btp--abap-environment, software-product>sap-business-technology-platform, software-product>business-rules, tutorial>license]
primary_tag: topic>abap-connectivity
author_name: Julie Plummer
author_profile: https://github.com/julieplummer20
creator_name: Test Test
creator_profile: https://github.com/Oleksandra2
keywords: Tools to Manage and Access, SAP BTP Trial, SAP HANA streaming analytics, keyword sap
---

# AAA Create an OData service consumption model in an SAP Business Technology Platform
<!-- description --> Create an OData service consumption model in an SAP Business Technology Platform, ABAP Environment instance. In a second instance, prepare it for consumption.

## Prerequisites  
- **IMPORTANT**: This tutorial cannot be comasdpleted on a trial account
- The service definition **`/DMO/TRAVEL_U`** is availablasde in the **provisioning** system - You can downlasdoad the service as part of the ABAP Flight Reference Scenario, see [Downloading the ABAP Flight Reference Scenario](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/def316685ad14033b051fc4b88db07c8.html)

## You will learn
  - How to create an XML file representing the remote service and save this locally
  - How to create proxy artefacts representing the remote service in the client system, using this `metadata` file

  This tutorial is based on:

  - SAP Help Portal: [Preparing Access to the Remote OData Service](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/59a91c95137e4c42946d50b25dba3fd7.html)
  - SAP Help Portal: [SAP Help Portal: Creating a Service Consumption Model](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/96132822b3554016b653d3601bb9ff1a.html)

---

### Create package in provisioning system

1. Create a new package for the mission, by:
    - selecting your project
    - selecting **ZLOCAL**
    - if necessary, selecting your user package
    - then choosing **New > ABAP Package** from the context menu.

2. Enter a name, **`Z_A4C_TO_A4C_XXX`**, and description, **Access service from ABAP `Environment`** and choose **Next**.

3. If asked, specify the software component **ZLOCAL** and choose **Next**.

4. Accept or create a transport request and choose **Finish**.

5. Add the package to **Favorite Packages** by choosing this from the context menu.


### Create service binding

Now you need to access the OData service metadata. You derive this from the service URL, available from the service binding.
Later you will use the service metadata to create the service consumption model file (`EDMX` file), for consumption on the client system.

1. First, create a new **Service Binding** from the Service Definition, **`/DMO/TRAVEL_U`**, by selecting **`/DMO/TRAVEL_U`** and choosing **New Service Binding** from the context menu. Search for this object by choosing **Open ABAP Development Object ( **`Ctrl+Sh+A`** ).

    <!-- border -->![step2a-create-service-binding](step2a-create-service-binding.png)

2. In the wizard:
    - Choose the correct package, that is, **your** package, not **`/DMO/...`**
    - Delete the namespace `/DMO/` and enter a name: **`Z_BIND_TRAVELS_XXX`** and description **Bind Travels Service**
    - Enter the binding type: **`ODATA V2 - Web API`**
    - Choose **Next**

    <!-- border -->![step2b-create-sb-wizard](step2b-create-sb-wizard.png)

3. Accept the transport request and choose **Finish**.    

The service binding opens in a new editor.



### Save service metadata file

1. Choose **Activate**.

    On the left is the Service Definition, `/DMO/TRAVEL_U`.
    Now, on the right, is the active service, including the Entity Set and the Service URL.

    <!-- border -->![step3a-service-binding](step3a-service-binding.png)

2. Click on the link **Service URL**. The `XML` file is shown in the browser.

3. Add the suffix **`/$metadata`** to the Service URL (deleting parameters, such as `sap-client`).

    <!-- border -->![step2d-add-metadata-suffix](step2d-add-metadata-suffix.png)

    The service metadata appears.

4. Choose **Save As...** from the context menu. The file name should be pre-filled as **`$metadata.xml`**.

5. Navigate to an appropriate folder and choose **Save**.




### Create package in client system

1. As in step 1, create a package in your `ZLOCAL` package, in the **client** system: `Z_A4C_TO_A4C_XX2`.

2. Add it to **Favorite Packages**.


---
