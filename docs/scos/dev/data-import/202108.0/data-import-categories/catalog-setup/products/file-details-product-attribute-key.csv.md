---
title: File details - product_attribute_key.csv
last_updated: Jul 7, 2021
template: data-import-template
originalLink: https://documentation.spryker.com/2021080/docs/file-details-product-attribute-keycsv
originalArticleId: 141aa68e-c752-4021-b6cb-df6fa4803d72
redirect_from:
  - /2021080/docs/file-details-product-attribute-keycsv
  - /2021080/docs/en/file-details-product-attribute-keycsv
  - /docs/file-details-product-attribute-keycsv
  - /docs/en/file-details-product-attribute-keycsv
related:
  - title: Execution order of data importers in Demo Shop
    link: docs/scos/dev/data-import/page.version/demo-shop-data-import/execution-order-of-data-importers-in-demo-shop.html
---

This document describes the `product_attribute_key.csv` file to configure [Product Attribute](/docs/scos/user/features/{{page.version}}/product-feature-overview/product-attributes-overview.html) information on your Spryker Demo Shop.

To import the file, run:

```bash
data:import:product-attribute-key
```

## Import file parameters

The file should have the following parameters:

| PARAMETER | REQUIRED | TYPE | REQUIREMENTS OR COMMENTS | DESCRIPTION |
| --- | --- | --- | --- | --- |
| attribute_key | &check;  | String | Must be unique. | Product attribute key name. |
| is_super |  | Boolean | If empty it will be imported as *False* (0).<br>False = 0 = It is not a super attribute.<br>True = 1 = It is a super attribute. | Indicates whether it is a super attribute or not.  |


## Import file dependencies

This file has no dependencies.

## Import template file and content example

Find the template and an example of the file below:

| FILE | DESCRIPTION |
| --- | --- |
| [product_attribute_key.csv template](https://spryker.s3.eu-central-1.amazonaws.com/docs/Developer+Guide/Back-End/Data+Manipulation/Data+Ingestion/Data+Import/Data+Import+Categories/Catalog+Setup/Products/Template+product_attribute_key.csv) | Exemplary import file with headers only. |
| [product_attribute_key.csv](https://spryker.s3.eu-central-1.amazonaws.com/docs/Developer+Guide/Back-End/Data+Manipulation/Data+Ingestion/Data+Import/Data+Import+Categories/Catalog+Setup/Products/product_attribute_key.csv) | Product Exemplary import file with Demo Shop data. |
