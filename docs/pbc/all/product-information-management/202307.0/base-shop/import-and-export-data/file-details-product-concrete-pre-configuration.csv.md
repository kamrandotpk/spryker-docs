---
title: File details- product_concrete_pre_configuration.csv
description: Description of the `product_concrete_pre_configuration.csv` import file.
last_updated: Jun 25, 2021
template: data-import-template
originalLink: https://documentation.spryker.com/2021080/docs/file-details-product-concrete-pre-configurationcsv
originalArticleId: 81e75f7f-b1bd-4707-870a-dbea4a001273
redirect_from:
  - /2021080/docs/file-details-product-concrete-pre-configurationcsv
  - /2021080/docs/en/file-details-product-concrete-pre-configurationcsv
  - /docs/file-details-product-concrete-pre-configurationcsv
  - /docs/en/file-details-product-concrete-pre-configurationcsv
  - /docs/scos/dev/data-import/202200.0/data-import-categories/special-product-types/configurable-product-import-category/file-details-product-concrete-pre-configuration.csv.html
  - /docs/scos/dev/data-import/202307.0/data-import-categories/special-product-types/configurable-product-import-category/file-details-product-concrete-pre-configuration.csv.html
  - /2021080/docs/configurable-product-data-import
  - /2021080/docs/en/configurable-product-data-import
  - /docs/configurable-product-data-import
  - /docs/en/configurable-product-data-import  
related:
  - title: Execution order of data importers in Demo Shop
    link: docs/scos/dev/data-import/page.version/demo-shop-data-import/execution-order-of-data-importers-in-demo-shop.html
---

This document describes the `product_concrete_pre_configuration.csv` file to configure [configurable product](/docs/pbc/all/product-information-management/{{page.version}}/base-shop/configurable-product-feature-overview/configurable-product-feature-overview.html) information in your Spryker shop.

## Import file dependencies

[File details: product_concrete.csv](/docs/pbc/all/product-information-management/{{page.version}}/base-shop/import-and-export-data/products-data-import/file-details-product-concrete.csv.html#import-file-parameters)

## Import file parameters

| PARAMETER | REQUIRED | TYPE | DEFAULT VALUE | REQUIREMENTS AND COMMENTS | DESCRIPTION |
| --- | --- | --- | --- | --- | --- |
| concrete_sku | ✓ | String | | Must be an SKU of an existing product. | Unique product identifier. |
| configurator_key | ✓ | String | | Multiple products can use the same `configurator_key`. | Unique identifier of a product configurator to be used for this product. |
| is_complete | ✓ | Boolean | | 0 | True = `1` <br> False = `0` | Defines if product configuration is complete by default.
| default_configuration | | String |  | Accepts JSON. | Defines the configuration customers start configuring the product with. |
| default_display_data | | String |  | Accepts JSON. | Defines the configuration to be displayed to customers when they start configuring the product. The parameters are taken from `default_configuration`. |

## Import template file and content example

| FILE | DESCRIPTION |
| --- | --- |
| [Template product_concrete_pre_configuration.csv](https://spryker.s3.eu-central-1.amazonaws.com/docs/Developer+Guide/Back-End/Data+Manipulation/Data+Ingestion/Data+Import/Data+Import+Categories/Catalog+Setup/Products/Template+product_concrete_pre_configuration.csv) | Import file template with headers only. |
| [product_concrete_pre_configuration.csv](https://spryker.s3.eu-central-1.amazonaws.com/docs/Developer+Guide/Back-End/Data+Manipulation/Data+Ingestion/Data+Import/Data+Import+Categories/Catalog+Setup/Products/product_concrete_pre_configuration.csv) | Exemplary import file with Demo Shop data. |

## Import command

```bash
data:import:product-configuration
```
