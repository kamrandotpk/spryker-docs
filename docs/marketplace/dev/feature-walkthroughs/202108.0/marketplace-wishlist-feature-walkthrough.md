---
title: Marketplace Wishlist feature walkthrough
description: This document provides technical details about the Marketplace Wishlist feature.
template: feature-walkthrough-template
---

With the *Marketplace Wishlist* feature, the customers can track and save merchant offers and products they wish to purchase through the wishlist. The customers can create multiple wishlists and customize their names.

{% info_block warningBox "User documentation" %}

To learn more about the feature and to find out how end users use it, see [Marketplace Wishlist feature overview](/docs/marketplace/user/features/{{page.version}}/marketplace-wishlist-feature-overview.html) for business users.

{% endinfo_block %}

## Module dependency graph

The following diagram illustrates the dependencies between the modules for the *Marketplace Wishlist* feature.

![Entity diagram](https://confluence-connect.gliffy.net/embed/image/e7a2ef43-7eb8-435a-870b-d8012fe8bd07.png?utm_medium=live&utm_source=confluence)

| MODULE     | DESCRIPTION                |
|------------|----------------------------|
| MerchantProductWishlist | Provides ability to work with merchant product in the wishlist. |
| MerchantProductOfferWishlist | Provides ability to work with the product offers in the wishlist. |
| MerchantProductOfferWishlistRestApi | Provides ability to manage product offers for wishlist Rest API. |
| Wishlist | Provides infrastructure and functionality to handle multiple wishlists for customer account. |
| WishlistExtension | Provides plugin interfaces for extending Wishlist module functionality. |

## Domain model

The following diagram illustrates the domain model of the *Marketplace Wishlist* feature:

![Entity diagram](https://confluence-connect.gliffy.net/embed/image/6d5e9f9f-f841-4877-bf65-7fdd38d6d49b.png?utm_medium=live&utm_source=confluence)

## Related Developer documents

| INSTALLATION GUIDES | GLUE API GUIDES  |
| ------------- | -------------- |
| [Marketplace Wishlist feature integration](/docs/marketplace/dev/feature-integration-guides/{{page.version}}/marketplace-wishlist-feature-integration.html) | [Managing wishlists](/docs/marketplace/dev/glue-api-guides/{{page.version}}/wishlists/managing-wishlists.html)
| [Glue API: Marketplace Product Offer + Wishlist feature integration](/docs/marketplace/dev/feature-integration-guides/{{page.version}}/glue/marketplace-product-offer-wishlist-feature-integration.html) | [Managing wishlist items](/docs/marketplace/dev/glue-api-guides/{{page.version}}/wishlists/managing-wishlist-items.html) |
