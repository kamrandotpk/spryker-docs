---
title: Product Groups feature overview
description: Product Groups feature allows product catalog managers to group products by attributes.
last_updated: Nov 22, 2019
template: concept-topic-template
originalLink: https://documentation.spryker.com/v2/docs/product-group
originalArticleId: 0ec0eec5-7e08-4486-943a-463e60f9f8ec
redirect_from:
  - /v2/docs/product-group
  - /v2/docs/en/product-group
---

The *Product Groups* feature allows product catalog managers to group products by attributes, like color or size. A typical use case is combining the same product in different colors into a product group (not to be confused with [product variant](/docs/scos/user/features/{{page.version}}/product/product-feature-overview/products-overview.html)). The feature changes the way shop users interact with products by improving accessibility and navigation.

## Product groups on the Storefront

{% info_block warningBox "Examplary content" %}

By default, there is no way to display product groups on Storefront. This section describes an examplary implementation which you can add to your project. See [HowTo - Display product groups by color on Storefront](/docs/scos/dev/tutorials-and-howtos/howtos/feature-howtos/howto-display-product-groups-by-color-on-the-storefront.html) for more details.

{% endinfo_block %}


On the Storefront, the product group is not displayed as a group. Instead, the behavior of all the UI elements related to the products in the group changes. It affects product abstract card and *Product details* page.

Product abstract card:

1. Hovering over the product abstract card opens a pop-up menu with the colors of all the products included into the group.
2. Hovering over or clicking a color circle changes the abstract product image, title, rating, label, and the price.
3. Having hovered over the desired color, a shop user clicks on the product abstract card to be redirected to the *Product details* page of the corresponding product.

![Product group - product abstract card](https://spryker.s3.eu-central-1.amazonaws.com/docs/Features/Product+Management/Product+Groups/Product+Groups+Feature+Overview/product-group-product-abstract-card.gif)


*Product details* page:

* Hovering over a color circle changes the abstract product image.
* Once you stop hovering over a color circle, the image changes back to the image of the product you are viewing.
* Clicking on a color circle redirects the shop user to the page of the corresponding abstract product.


![Product group - product details page](https://spryker.s3.eu-central-1.amazonaws.com/docs/Features/Product+Management/Product+Groups/Product+Groups+Feature+Overview/product-group-product-details-page.gif)

## Product groups in the Back Office

In the Back Office, a product catalog manager can view what product group an abstract product belongs to. See [Viewing a Product](/docs/scos/user/back-office-user-guides/{{page.version}}/catalog/products/managing-products/managing-products.html#viewing-products) to learn more.   

Also, they can insert product groups into CMS pages via content widgets in the [WYSIWYG editor](/docs/scos/user/features/{{page.version}}/content-items-feature-overview.html#content-item-widget).

## Creating product groups

Currently, only a developer can create product groups by [importing them](/docs/scos/dev/data-import/{{page.version}}/data-import-categories/merchandising-setup/product-merchandising/file-details-product-group.csv.html) or modifying the database. Only abstract products can be added to product groups.


## Current constraints

Currently, the feature has the following functional constraints which are going to be resolved in the future:

* There is no user interface to manage product groups in the Back Office.
* Products can only be grouped by the color attribute.

## Video tutorial

Check out this video tutorial on product groups:

{% wistia r5l2kit2c1 960 720 %}

## Related Business User documents

|BACK OFFICE USER GUIDES|
|---|
| [Check what product group a product belongs to by viewing a product](/docs/scos/user/back-office-user-guides/{{page.version}}/catalog/products/managing-products/managing-products.html#viewing-products)  |

{% info_block warningBox "Developer guides" %}

Are you a developer? See [Product Groups feature walkthrough](/docs/scos/dev/feature-walkthroughs/{{page.version}}/product-groups-feature-walkthrough.html) for developers.

{% endinfo_block %}
