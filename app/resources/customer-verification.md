---
layout: twoColumn
section: resources
type: article
title:  "Customer verification"
description: "Build bank transfers programmatically into your site or app. Learn about account verification and how to respond when additional information is required."
---

# Customer verification

This article will walk through the Customer verification workflow for white label integrations. For more information about white label, please [contact sales](https://www.dwolla.com/contact).

A “customer” represents an individual or business that you intend to transact with. In any transfer, at least one party—either the sender or the recipient—must complete the identity verification process described below. In cases where a Customer is only sending funds to or receiving funds from your full Dwolla account, the Customer does not need to complete the process set out below because you have already completed it. Note that some activities require a Customer to complete the verification process, regardless of the verification status of the other party.

However, if your application will allow your Customers to transfer funds between each other, at least one party will need to be verified using the process below.

First, you should have [an active webhook subscription](/guides/webhooks/).  Information about a Customer’s progress in the verification process is sent asynchronously to your application.

## Quick overview

 1. Create a verified personal Customer.
 2. Check the status of the personal Customer.
 3. If necessary, retry verification.
 4. If necessary, upload a photo document.

### Create a verified personal Customer

To create a verified personal Customer, use the [Create Customer](https://docsv2.dwolla.com/#new-customer) endpoint:

```rawnoselect
POST /customers
Content-Type: application/vnd.dwolla.v1.hal+json
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY

{
  "firstName": "Gordon",
  "lastName": "Zheng",
  "email": "gordon+15@dwolla.com",
  "ipAddress": "10.10.10.10",
  "type": "personal",
  "address1": "6680 Forest Ave.",
  "address2": "Apt 4F",
  "city": "Ridgewood",
  "state": "NY",
  "postalCode": "11385",
  "dateOfBirth": "1990-07-11",
  "tin": "1516"
}
```

You’ll need to provide the Customer’s full name, email address, home address, date of birth, and the last four digits of their taxpayer identification number (for individuals, this is their Social Security Number).

Once you submit this request, Dwolla will perform some initial validation to check for formatting issues such as an invalid date of birth, invalid email format, etc. If successful, the response will be a HTTP 201/Created with the URL of the new Customer resource contained in the `Location` header.

```rawnoselect
HTTP/1.1 201 Created
Location: https://api.dwolla.com/customers/FC451A7A-AE30-4404-AB95-E3553FCD733F
```

### Check the status of the personal Customer

The successful creation of a Customer doesn’t necessarily mean the Customer is verified. When a Customer has been successfully verified by Dwolla, their status will be set to `verified`.

Let’s check to see if the Customer was successfully verified or not:

```rawnoselect
GET https://api.dwolla.com/customers/FC451A7A-AE30-4404-AB95-E3553FCD733F
Content-Type: application/vnd.dwolla.v1.hal+json
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY
```

Response: 

```rawnoselect
{
  "_links": {
    "self": {
      "href": "https://api-uat.dwolla.com/customers/132681FA-1B4D-4181-8FF2-619CA46235B1"
    },
    "receive": {
      "href": "https://api-uat.dwolla.com/transfers"
    },
    "funding-sources": {
      "href": "https://api-uat.dwolla.com/customers/132681FA-1B4D-4181-8FF2-619CA46235B1/funding-sources"
    },
    "transfers": {
      "href": "https://api-uat.dwolla.com/customers/132681FA-1B4D-4181-8FF2-619CA46235B1/transfers"
    },
    "send": {
      "href": "https://api-uat.dwolla.com/transfers"
    }
  },
  "id": "132681FA-1B4D-4181-8FF2-619CA46235B1",
  "firstName": "Gordon",
  "lastName": "Zheng",
  "email": "gordon+15@dwolla.com",
  "type": "personal",
  "status": "verified",
  "created": "2015-09-29T19:47:28.920Z"
}
```

Our Customer was successfully verified! Other Customers, however, may require additional verification. Continue reading for instructions on providing additional information to verify these Customers.

### Handling status: `retry`

If the Customer has a status of `retry`, some information may have been miskeyed. You have one more opportunity to correct any mistakes. This time, you’ll need to provide the customer’s full SSN.

```rawnoselect
POST /customers/132681FA-1B4D-4181-8FF2-619CA46235B1
Content-Type: application/vnd.dwolla.v1.hal+json
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY

{
  "firstName": "Gordon",
  "lastName": "Zheng",
  "email": "gordon+15@dwolla.com",
  "ipAddress": "10.10.10.10",
  "type": "personal",
  "address1": "221 Corrected Address St..",
  "address2": "Fl 8",
  "city": "Ridgewood",
  "state": "NY",
  "postalCode": "11385",
  "dateOfBirth": "1990-07-11",
  "tin": "202-99-1516"
}
```

Check the Customer’s status again. The Customer will either be verified or in the `document` or `suspended` state.

### Handling status: `document`

If the customer has a status of `document`, then you'll need to upload a photo of the customer's U.S. passport, state driver's license, or other U.S. government-issued photo ID. Use the Create a document endpoint for that. The document will then be reviewed by Dwolla.  

```rawnoselect
curl -X POST 
\ -H "Authorization: Bearer tJlyMNW6e3QVbzHjeJ9JvAPsRglFjwnba4NdfCzsYJm7XbckcR" 
\ -H "Accept: application/vnd.dwolla.v1.hal+json" 
\ -H "Cache-Control: no-cache" 
\ -H "Content-Type: multipart/form-data" 
\ -F "documentType=passport" 
\ -F "file=@foo.png" 
\ 'https://api-uat.dwolla.com/customers/132681FA-1B4D-4181-8FF2-619CA46235B1/documents'
```

If the document was successfully uploaded, the response will be a HTTP 201/Created with the URL of the new document resource contained in the Location header.

```rawnoselect
HTTP/1.1 201 Created
Location: https://api-uat.dwolla.com/documents/11fe0bab-39bd-42ee-bb39-275afcc050d0
```

You’ll also get a webhook with a `customer_verification_document_uploaded` event to let you know the document was successfully uploaded.

Once created, the document will be reviewed by Dwolla. When we’ve made a decision, we’ll create either a `customer_verification_document_approved` or `customer_verification_document_failed` event (possibly followed by another `customer_verification_document_needed`).

If you receive a `customer_verification_document_failed` webhook, you’ll need to upload another document.

If the document was sufficient, the Customer will be verified. Otherwise, we may need additional documentation.

If the document was found to be fraudulent or doesn’t match the identity of the customer, they will be suspended.

### Handling status: `suspended`

If the Customer is `suspended`, there’s no further action you can take to correct this using the API. You’ll need to contact support@dwolla.com for assistance.