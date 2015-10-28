---
layout: twoColumn
section: guides
type: guide
guide: 
    name: transfer-money-between-users
    step: overview
title:  Transfer money between customers in your application
description: Transfer money between customers within your application by utilizing our open API with no per transaction fees.
---

# Overview: Transfer money between your users

The most common scenario for this guide is to facilitate marketplace payments between your customers--to allow your buyers to send money to your sellers. 

In this guide, we’ll cover the key points of transferring money:

 - Create a verified customer who will receive the transfer
 - Create an unverified customer who will send the transfer
 - Associate a verified funding source (bank or credit union account) with the  sender
 - Associate an unverified funding source (bank or credit union account) with  recipient
 - Transfer funds from the sender’s funding source to the recipient’s funding  source


### Get set up with sandbox accounts

Before you begin, be sure your [Sandbox accounts](/guides/sandbox-setup) are already set up. 

### Verified and unverified customers
Here are some rules to keep in mind:

1. With a transfer of money, at least one party must be identity-verified, either the sender or the receiver. In a marketplace, it’s best to have the seller go through the identity verification process to allow the buyer to complete a transaction with minimal friction. However, it’s your decision about which party you require identity verification from, either the sender or the receiver.
2. The sender must have a verified funding source. Unverified funding sources can only receive money, not send.

In this guide, we’ll create two customers: one to represent a seller and one to represent a buyer. In this scenario, the seller, Jane Merchant, is a verified customer with an unverified funding source. The buyer, Joe Consumer, is an unverified customer with a verified funding source.

Please note that this is a suggested approach, but that there are other ways you can implement your marketplace transfers. For instance, both the sender and the receiver (or buyer and seller) could be verified customers, and both could have verified funding sources. Or, you could have the sender undergo identity verification but not the recipient.  

<nav class="pager-nav">
<a href="" style="display:none;"></a>
<a href="01-access-token.html">Next step: Generate an access token</a>
</nav>
