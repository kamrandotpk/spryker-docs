---
title: File details - product_concrete.csv
last_updated: Jun 16, 2021
template: data-import-template
originalLink: https://documentation.spryker.com/2021080/docs/file-details-product-concretecsv
originalArticleId: bff2c63c-1239-4c76-9c74-40fa3c9eb19d
redirect_from:
  - /2021080/docs/file-details-product-concretecsv
  - /2021080/docs/en/file-details-product-concretecsv
  - /docs/file-details-product-concretecsv
  - /docs/en/file-details-product-concretecsv
  - /docs/scos/dev/data-import/202200.0/data-import-categories/catalog-setup/products/file-details-product-concrete.csv.html
  - /docs/scos/dev/data-import/202307.0/data-import-categories/catalog-setup/products/file-details-product-concrete.csv.html
  - /docs/pbc/all/product-information-management/202307.0/import-and-export-data/products-data-import/file-details-product-concrete.csv.html  
related:
  - title: Execution order of data importers in Demo Shop
    link: docs/scos/dev/data-import/page.version/demo-shop-data-import/execution-order-of-data-importers-in-demo-shop.html
---

This document describes the `product_concrete.csv` file to configure [Concrete Product](/docs/pbc/all/product-information-management/{{page.version}}/base-shop/feature-overviews/product-feature-overview/product-feature-overview.html) information in your Spryker Demo Shop.


## Import file dependencies

This file has the following dependency: [ product_abstract.csv](/docs/pbc/all/product-information-management/{{page.version}}/base-shop/import-and-export-data/products-data-import/file-details-product-abstract.csv.html).


## Import file parameters


| PARAMETER | REQUIRED | TYPE | REQUIREMENTS OR COMMENTS | DESCRIPTION |
| --- | --- | --- | --- | --- |
| abstract_sku | &check; | String | | The SKU of the parent abstract product. |
| old_sku |  | String | | Old SKU identifier. |
| concrete_sku | &check;  | String | Must be unique. |  SKU identifier of the concrete product. |
| name.{ANY_LOCALE_NAME}**<br>Example value: *name.en_US* | &check; | String | | Name of the product in the specified location (US for our example). |
| attribute_key_{NUMBER}*<br>Example value: *attribute_key_1*<br> | &check; (if this attribute is defined) | String | | Product attribute key for the attribute. |
| value_{NUMBER}<br>Example value: *value_1*<br>| &check; (if this attribute is defined) | String | | Product value for the attribute. |
| attribute_key_{NUMBER}.{ANY_LOCALE_NAME}**<br>Example value: *attribute_key_1.en_US*<br> |  | String | | Product attribute key, for the first attribute, translated in the specified locale (US for our example). |
| value_{NUMBER}.{ANY_LOCALE_NAME}**<br>Example value: *value_1.en_US*<br>|  | String | | Product value for the attribute, translated in the specified locale (US for our example). |
| icecat_pdp_url |  | String | | Icecat product catalogue URL service. |
| description.{ANY_LOCALE_NAME}<br>Example value: *description.en_US*  |  | String | | Product description, translated in the specified locale (US for our example). |
| is_searchable.{ANY_LOCALE_NAME}<br>Example value: *is_searchable.en_US*|  | Integer |  | Indicates if the product is searchable in the specified locale (US for our example). |
| icecat_license |  | String |  | Icecat product catalogue license code. |
| bundled |  | String | | Products SKUs separated by comas, that are part of the bundle. |
| is_quantity_splittable |  | Boolean |If it is empty, will be *False*.<br>False = 0<br>True = 1 | Defines if the product is [splittable](/docs/pbc/all/cart-and-checkout/202307.0/non-splittable-products-feature-overview.html) or not. |


** ANY_LOCALE_NAME: Locale date is dynamic in data importers. It means that ANY_LOCALE_NAME postfix can be changed, removed, and any number of columns with different locales can be added to the CSV files.
** NUMBER: Any number of  the attribute-value column pair can be added


## Recommendations and other information

Every concrete product is linked to an abstract one via the `abstract_sku` field.

## Import template file and content example

| FILE | DESCRIPTION |
| --- | --- |
| [product_concrete.csv template](https://spryker.s3.eu-central-1.amazonaws.com/docs/Developer+Guide/Back-End/Data+Manipulation/Data+Ingestion/Data+Import/Data+Import+Categories/Catalog+Setup/Products/Template+product_concrete.csv) | Product Exemplary import file with headers only. |
| [product_concrete.csv](https://spryker.s3.eu-central-1.amazonaws.com/docs/Developer+Guide/Back-End/Data+Manipulation/Data+Ingestion/Data+Import/Data+Import+Categories/Catalog+Setup/Products/product_concrete.csv) | Exemplary import file with Demo Shop data. |

## Import command

```bash
data:import:product-concrete
```
