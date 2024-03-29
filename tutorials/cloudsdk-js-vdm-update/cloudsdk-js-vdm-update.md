---
parser: v2
auto_validation: true
time: 15
tags: [ tutorial>intermediate, products>sap-business-technology-platform, topic>javascript, topic>odata]
primary_tag: software-product>sap-s-4hana-cloud
---

# Update OData Entities with the SAP Cloud SDK's Virtual Data Model Test Green upd
<!-- description --> Update OData entities with the SAP Cloud SDK's virtual data model to build an address manager application.

## Prerequisites
 - Have `Node.js` and `npm` [installed on your machine](s4sdkjs-prerequisites)
 - Access to an SAP S/4HANA Cloud system or the [SAP API Business Hub Sandbox](https://api.sap.com/getting-started), or use the [Business Partner Mock Service](https://sap.github.io/cloud-s4-sdk-book/pages/mock-odata.html)
 - Basic knowledge of OData is recommended, but not required



## Intro
> ## We migrate tutorials to our [documentation](https://sap.github.io/cloud-sdk/)
> This tutorial is not actively maintained and might be partially outdated.
> Always up-to-date documentation is published on our [documentation portal](https://sap.github.io/cloud-sdk/).
> We will provide a link to the updated version of this tutorial as soon as we release it.
> In this tutorial, version 1 of the SAP Cloud SDK for TypeScript/JavaScript is used.

## You will learn
  - How to use the Virtual Data Model to update an existing entity
  - How to trigger an update request from an API endpoint exposed by your application

The goal of this tutorial group is to show you how to implement a JavaScript application that allows you to manage the addresses of business partners. This application will be using `NestJS` and the SAP Cloud SDK for JavaScript. In this tutorial, we use the SAP Cloud SDK's OData Virtual Data Model to update an existing address and make this functionality available via an API endpoint.

---

### Add an API endpoint


1. This follows the implementation in the previous tutorials. `updateBusinessPartnerAddress()` does not do anything useful yet, but you will implement it in the next step. Finally, register the `controller` and `service` in the root application module `app.module.ts`:

	```JavaScript / TypeScript
	import { Module } from '@nestjs/common';
	import { AppController } from './app.controller';
	import { AppService } from './app.service';
	import { BusinessPartnerController } from './business-partner.controller';
	import { BusinessPartnerService } from './business-partner.service';

	@Module({
	 imports: [],
	 controllers: [AppController, BusinessPartnerController],
	 providers: [AppService, BusinessPartnerService]
	})
	export class AppModule {}
	```

2. Note, that we used the `PUT` method to update a business-partner address. So we need to send a `PUT` request. Restart your server and send a `PUT` request to `http://localhost:3000/business-partners/1/address/2` with some body (e. g. `{"key": "Some test body."}`). The server should response with:


```
This is your request data:
businessPartnerId: 1
addressId: 2
request body: {"key":"Some test body."}
```

3. Test test


### Update a business partner address


1. Next, we use the VDM to update an existing business partner address. Open `business-partner.controller.ts` and implement the `updateBusinessPartnerAddress()` function as shown below:

```JavaScript / TypeScript
function updateBusinessPartnerAddress(address: BusinessPartnerAddress): Promise<BusinessPartnerAddress> {
  return BusinessPartnerAddress.requestBuilder()
    .update(address)
    .execute({
      url: 'https://my.s4hana.ondemand.com/'
    });
}
```

2. The `update` function takes as parameter the entity that should be updated. When creating a new entity, the service will automatically generate things like key fields, the creation date, etc. and return it. The VDM makes this available to you by returning a `Promise<BusinessPartnerAddress>`. As a final step we need to adjust the `updateBusinessPartnerAddress()` in the controller to trigger an update:

```JavaScript / TypeScript
import { Controller, Body, Param, Put } from '@nestjs/common';
import { BusinessPartnerService } from './business-partner.service';
import { BusinessPartnerAddress } from '@sap/cloud-sdk-vdm-business-partner-service';

@Controller('business-partners')
export class BusinessPartnerController {
  constructor(private readonly businessPartnerService: BusinessPartnerService) {}

  @Put('/:businessPartnerId/address/:addressId')
  updateBusinessPartnerAddress(
    @Body() requestBody,
    @Param('businessPartnerId') businessPartnerId,
    @Param('addressId') addressId
  ): Promise<BusinessPartnerAddress> {
    const address = this.businessPartnerService.buildAddress(requestBody, businessPartnerId, addressId);
    return this.businessPartnerService.updateBusinessPartnerAddress(address);
  }
}
```

3. Restart your server and send a `PUT` request to `http://localhost:3000/business-partners/1003764/address/28238` with the following body (or replace the IDs with the ones you used in the previous tutorial):

```JSON
{
    "cityName": "Berlin",
    "businessPartner": "1003764",
    "addressId": "28238"
}
```

4. This will update the business partner address and set its `CityName` to `"Berlin"`.


### Optional: Concurrency and version identifiers


In the example presented here, we don't do anything with the `BusinessPartnerAddress` returned by the update request. In a real-world application, it is recommend to store the entity somewhere. Whenever an entity is received from a service, the VDM will keep track of this state (we call it remote state). If you change the entity locally and then send an update request, the VDM will only send the difference between the current local state and the last known remote state.

You might now ask: "But what if the remote state changes in the meantime?"
To protect from this, OData uses so-called version identifiers (in the form of an [entity tag or `eTag`](https://en.wikipedia.org/wiki/HTTP_ETag))\. Every entity will be assigned a version identifier for a given state by the service\. If the state of the entity changes, the version identifier changes as well\. When a request to update or delete an entity is sent to the service, it can compare the version identifier sent with the request to the one it has currently assigned to that entity\. If they match, the request will be handled. Otherwise it will be rejected, which prevents clients from making changes to an entity based on an outdated state\. This concept is also known as "optimistic concurrency".

The version identifier of an entity is sent via the [`If-Match` HTTP header](http://www.rfc-editor.org/rfc/rfc2616.txt) (section 14.24 "If-Match")\. Instead of sending a version identifier, it is also possible so send `*`, meaning that any version identifier should be matched\. This is semantically equivalent to forcing a request to be handled, ignoring differences between the remote state and the local state\.
For update requests, the VDM will handle version identifiers for you\. Whenever an entity is received from a service, the VDM will store the version identifier and automatically send it on subsequent requests\. Should you need to force a request to be handled, you can ignore the version identifier like this:

```JavaScript / TypeScript
import { BusinessPartner } from '@sap/cloud-sdk-vdm-business-partner-service';

// pre-existing businessPartner instance

BusinessPartner.requestBuilder()
  .update(businessPartner)
  .ignoreVersionIdentifier()
  .execute({
    url: 'https://my.s4hana.ondemand.com/'
  });
```


---
