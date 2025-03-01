---
title: Commerce Setup
last_updated: Jun 17, 2021
template: data-import-template
originalLink: https://documentation.spryker.com/2021080/docs/commerce-setup
originalArticleId: ec34f53d-6830-4b9e-b238-0a9e9958c238
redirect_from:
  - /2021080/docs/commerce-setup
  - /2021080/docs/en/commerce-setup
  - /docs/commerce-setup
  - /docs/en/commerce-setup
---

The *Commerce Setup* category contains data required to set up the multi-store environment, warehouses, tax levels, shipping and payment methods, etc., so you could start selling products and services online.
The table below provides details on Commerce Setup data importers, their purpose, CSV files, dependencies, and other details. Each data importer contains links to CSV files used to import the corresponding data, including specifications of mandatory and unique fields, dependencies, detailed explanations, recommendations, templates, and content examples.


| DATA IMPORTER | PURPOSE | CONSOLE COMMAND | FILES | DEPENDENCIES |
| --- | --- | --- | --- |--- |
| Store  | Imports basic information about the stores. |`data:import:store` | There is no CSV file to import the store setup information.<br>Store names and other setup information is set in the `stores.php` configuration file in the demo shop PHP project.  | `stores.php` configuration file of Demo Shop|
| Currency | Imports information about currencies used in the store(s). The `currency.csv` file provides an easy way to load information about currencies used in Spryker Demo Shop. It allows to load information like: ISO code, currency symbol, and the name of the currency.|`data:import:currency` | [currency.csv](/docs/pbc/all/price-management/{{page.version}}/base-shop/import-and-export-data/file-details-currency.csv.html) | None|
| Customer | Imports information about customers.|`data:import:customer` | [customer.csv](/docs/scos/dev/data-import/{{page.version}}/data-import-categories/commerce-setup/file-details-customer.csv.html) | None|
| Glossary | Imports information relative to the several locales.|`data:import:glossary` | [glossary.csv](/docs/scos/dev/data-import/{{page.version}}/data-import-categories/commerce-setup/file-details-glossary.csv.html) | None|
| Tax |Imports information about taxes.|`data:import:tax` | [tax.csv](/docs/pbc/all/tax-management/{{page.version}}/spryker-tax/base-shop/import-and-export-data/import-file-details-tax-sets.csv.html) | None|
| Shipment |Imports information about shipment methods.|`data:import:shipment` | [shipment.csv](/docs/pbc/all/carrier-management/{{site.version}}/base-shop/import-and-export-data/file-details-shipment.csv.html) | None|
| Shipment Price |Imports information about the price of each shipment method, applied in each store.|`data:import:shipment-price` | [shipment_price.csv](/docs/scos/dev/data-import/{{page.version}}/data-import-categories/commerce-setup/file-details-shipment-price.csv.html) | <ul><li>[shipment.cs](/docs/pbc/all/carrier-management/{{site.version}}/base-shop/import-and-export-data/file-details-shipment.csv.html)v</li><li>[currency.csv](/docs/pbc/all/price-management/{{page.version}}/base-shop/import-and-export-data/file-details-currency.csv.html)</li><li>`stores.php` configuration file of demo shop PHP project</li></ul>|
| Shipment Method Store  | Links a *shipment method* to a specific *store*.|`data:import:shipment-method-store` | [shipment_method_store.csv](/docs/pbc/all/carrier-management/{{site.version}}/base-shop/import-and-export-data/file-details-shipment-method-store.csv.html) | <ul><li>[shipment.csv](/docs/pbc/all/carrier-management/{{site.version}}/base-shop/import-and-export-data/file-details-shipment.csv.html)</li><li>`stores.php` configuration file of demo shop PHP project</li></ul>|
| Sales Order Threshold  | Imports information about sales order thresholds. The CSV file content defines certain thresholds for specific stores and currencies.|`data:import:sales-order-threshold` | [sales_order_threshold.csv](/docs/pbc/all/cart-and-checkout/{{page.version}}/import-and-export-data/file-details-sales-order-threshold.csv.html) | <ul><li>[currency.csv](/docs/scos/dev/data-import/{{page.version}}/data-import-categories/commerce-setup/file-details-currency.csv.html)</li><li>[glossary.csv](/docs/scos/dev/data-import/{{page.version}}/data-import-categories/commerce-setup/file-details-glossary.csv.html)</li><li>`stores.php` configuration file of demo shop PHP project</li></ul>|
| Warehouse | Imports information about warehouses (for example, name of the warehouse, and if it is available or not).|`data:import:stock` | [warehouse.csv](/docs/pbc/all/warehouse-management-system/{{page.version}}/base-shop/import-and-export-data/file-details-warehouse.csv.html) | None|
| Warehouse Store  | Imports information about the Warehouse / Store relation configuration indicating which warehouse serves which store(s).|` data:import:stock-store`| [warehouse_store.csv](/docs/pbc/all/warehouse-management-system/{{page.version}}/base-shop/import-and-export-data/file-details-warehouse-store.csv.html) | <ul><li>[warehouse.csv](/docs/pbc/all/warehouse-management-system/{{page.version}}/base-shop/import-and-export-data/file-details-warehouse.csv.html)</li><li>`stores.php` configuration file of demo shop PHP project</li></ul>|
| Payment Method | Imports information about *payment methods* as well as *payment providers*.|`data:import:payment-method` | [payment_method.csv](/docs/pbc/all/payment-service-provider/{{page.version}}/import-and-export-data/file-details-payment-method.csv.html) | None|
| Payment Method Store  |Imports information about payment methods per store. The CSV file links the *payment method* with each *store*.|`data:import:payment-method-store`| [payment_method_store.csv](/docs/pbc/all/payment-service-provider/{{page.version}}/import-and-export-data/file-details-payment-method-store.csv.html) | <ul><li>[payment_method.csv](/docs/pbc/all/payment-service-provider/{{page.version}}/import-and-export-data/file-details-payment-method.csv.html)</li><li>`stores.php` configuration file of demo shop PHP project</li></ul>|
| Warehouse address  |Imports warehouse address information to be used as shipping origin address.|`data:import:stock-address`| [warehouse_address.csv](/docs/pbc/all/warehouse-management-system/{{page.version}}/base-shop/import-and-export-data/file-details-warehouse-address.csv.html) |[warehouse.csv](/docs/pbc/all/warehouse-management-system/{{page.version}}/base-shop/import-and-export-data/file-details-warehouse-store.csv.html)|
| Order |Updates the status of the order item.|`order-oms:status-import order-status`| [order-status.csv](/docs/scos/dev/data-import/{{page.version}}/data-import-categories/commerce-setup/file-details-order-status.csv.html) | |
