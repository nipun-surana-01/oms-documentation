# BOPIS Check Inventory API 

## Overview
The BOPIS (Buy Online, Pick-up In Store) Check Inventory API allows you to retrieve stock details for products at specific locations. By calling the `/checkBopisInventory` endpoint with the POST method, you can check the availability of BOPIS inventory.


# Request

### Endpoint

**URL:** `https://<host>/rest/s1/ofbiz-oms-usl/checkBopisInventory`  

This API uses our next generation OMS instance. All traditional instances have access to this next generation OMS by replacing "oms" with "maarg" in the host name.

**Production**

Current Name: `demo-oms`

Maarg Name: `demo-maarg`

**UAT**

Current Name: `demo-uat`

Maarg Name: `demo-maarg-uat`


**Example:** `https://demo-maarg.hotwax.io/rest/s1/ofbiz-oms-usl/checkBopisInventory` 

### Method
GET

### Headers

Content-Type: application/json


### Sample Request Params
  
#### Single product and facility

```https://demo-maarg.hotwax.io/rest/s1/ofbiz-oms-usl/checkBopisInventory?productStoreId=STORE&inventoryGroupId=SHOPIFY_FAC_GRP&internalNames=XS-BLACK&facilityIds=114&productIds=11855```


#### Multiple products and facilities

To handle multiple values for any key in a URL request, each value must be associated with a separate instance of the key. For example, if you have multiple productIds or internalNames, you need to create a separate key for each value in the URL, like this:

```https://demo-maarg.hotwax.io/rest/s1/ofbiz-oms-usl/checkBopisInventory?productStoreId=STORE&inventoryGroupId=SHOPIFY_FAC_GRP&internalNames=XS-BLACK&internalNames=S-BLACK&internalNames=M-BLACK&facilityIds=114&facilityIds=115&facilityIds=116&productIds=11855&productIds=11856&productIds=11857```

### Query Parameters

| Parameter        | Description                                             | Example             |
|------------------|---------------------------------------------------------|---------------------|
| `productStoreId` | The ID of the product store                             | `"STORE"`           |
| `productIds`      | HotWax Commerce internal product ID                     | `"11855"`           |
| `internalNames`   | The well known ID of a product, usually UPC or SKU based on configuration| `"XS-BLACK"`           |
| `facilityIds`     | The HotWax Commerce facility ID where product inventory is located | `"114"`             |
| `inventoryGroupId` | HotWax Commerce inventory group ID                     | `"SHOPIFY_FAC_GRP"` |

If both productIds and internalNames are passed, the value of productIds will be honored.

**Select Products**

Products can be queried in the inventory API by both their HotWax ID or their internalName. The internal name refers to the UPC or SKU, whichever one is selected as a primary identifier during product configuration. While both fields are optional, one is requried to obtain a result.

**productStoreId**

Product Store represents a brand in HotWax Commerce. To obtain possible values, log into HotWax Commerce and view the product store list page for possible values.

**inventoryGroupId**
Inventory groups are used to segregate inventory for different channels of sales. Obtain the inventory group ID by logging into HotWax Commerce Facilities App and browsing inventory groups.


## Response

### Status Code

HTTP/1.1 200 OK

### Headers

Content-Type: application/json


### Body

#### ATP zeroed out due to the inventory checks

```json
{
  "atp": 0.0,
  "computedAtp": 0.0,
  "decisionReason": "AllowPickupFacility"
  "decisionReasonDesc": "Pickup is not allowed for product 11855 at facility 115."
}
```

### Possible `decisionReason` explanation

| Decision Reason             | Description                                                                 |
|-----------------------------|-----------------------------------------------------------------------------|
| `ProductStore`              | The facility is not associated with the product store.                     |
| `PickUpFacility`          | The facility does not have PickUp enabled. Add it to the PickUp facility group to enable pickup.        |
| `InventoryGroup`            | The facility where the product is located does not cater its inventory to that channel. |
| `AllowPickupInventoryGroup` | Pickup is not allowed globally for the inventory group.                     |
| `AllowPickupFacility`       | The facility where the product inventory is located is disabled for catering to BOPIS orders. 

Each Decision Reason also comes with a detailed log of why the given check failed.

1. ProductStore - Facility ${facilityId} is not associated with Product Store ${productStoreId}.
2. PickUp Facility - Facility ${facilityId} is not a member of a PICKUP Facility Group.
3. Inventory Group - Facility ${facilityId} is not a member of ${inventoryGroupId} channel.
4. Product Inventory Group allowPickup - Pickup is not allowed for product ${productId} ${inventoryGroupId} inventory group.
5. Product Facility allowPickup - Pickup is not allowed for product ${productId} at facility ${facilityId}.

### ATP

```json
{
  "atp": 50.0,
  "safetyStock": 5.0,
  "computedAtp": 45.0,
}
```
