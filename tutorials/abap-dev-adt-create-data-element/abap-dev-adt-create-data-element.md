---
parser: v2
auto_validation: true
primary_tag: programming-tool>abap-development
tags: [  tutorial>beginner, software-product>sap-netweaver, software-product>analytics, software-product>sap-social-media-analytics-by-netbase, software-product>sap-sports-one--cloud-edition, software-product>sap-student-activity-hub--on-premise-edition]
time: 5
---

# You have a valid instance of an on-premise AS ABAP server Test Green upd
<!-- description --> Create a data element, which you will use in a later tutorial.

## Prerequisites  
 - You have a valid instance of an on-premise AS ABAP server, version 7.51 or higher (some ABAP Development Tools may not be available in earlier versions)
 - **Tutorial**: [Create an ABAP Project in ABAP Development Tools (ADT)](abap-create-project)

## You will learn  
- How to create a data element.
- How to use this data element to provide Boolean logic to one of the columns in the table

## Intro
Create a Data Element Test Green
---

### Change field type to new data element

Go back to your ABAP Dictionary structure `zso_invoice_item` and change the type of field `payment_status` to **`zso_invoice_payment_status`**:

![Image depicting step24-change-field-type](step24-change-field-type.png)
Since the data element `zso_invoice_payment_status` does not exist, you get a syntax error, which you will fix using a quick fix below.


### Choose a Quick Fix


1. Select the data element and display all the available Quick Fixes by choosing **`Ctrl+1`**.

2. Then choose **Create data element `zso_invoice_payment_status`** and choose **Enter**.

    ![Image depicting backup-create-DTEL](backup-create-DTEL.png)


### Create a new data element


Enter a description for the new data element in the field **Description**, then choose **Finish** :

![Image depicting step24b-finish-data-element](step24b-finish-data-element.png)


---
