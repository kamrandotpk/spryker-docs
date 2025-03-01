---
title: Product Lists feature integration
description: The Product Lists Feature allows defining access to particular items. The guide walks you through the process of integrating this feature into your project.
last_updated: Mar 6, 2020
template: feature-integration-guide-template
originalLink: https://documentation.spryker.com/v4/docs/product-lists-feature-integration
originalArticleId: 21db3294-96a7-4125-854d-fb21d9b745b1
redirect_from:
  - /v4/docs/product-lists-feature-integration
  - /v4/docs/en/product-lists-feature-integration
related:
  - title: Product Lists + Catalog feature integration
    link: docs/scos/dev/feature-integration-guides/page.version/product-lists-catalog-feature-integration.html
  - title: CMS + Product Lists + Catalog feature integration
    link: docs/scos/dev/feature-integration-guides/page.version/cms-product-lists-catalog-feature-integration.html
  - title: Configurable Bundle feature integration
    link: docs/scos/dev/feature-integration-guides/page.version/configurable-bundle-feature-integration.html
  - title: Product feature integration
    link: docs/scos/dev/feature-integration-guides/page.version/product-feature-integration.html
  - title: Prices feature integration
    link: docs/scos/dev/feature-integration-guides/page.version/prices-feature-integration.html
  - title: Merchant Product Restrictions feature integration
    link: docs/scos/dev/feature-integration-guides/page.version/merchant-product-restrictions-feature-integration.html
  - title: Product Images + Configurable Bundle feature integration
    link: docs/scos/dev/feature-integration-guides/page.version/product-images-configurable-bundle-feature-integration.html
---

## Install Feature Core
### Prerequisites
Install the required features:

| Name | Version |
| --- | --- |
|Spryker Core|{{page.version}}|
| Product |{{page.version}} |
| Category Management |{{page.version}} |

### 1) Install the required modules using Composer
Run the following command(s) to install the required modules:

```bash
composer require spryker-feature/product-lists:"^{{page.version}}" --update-with-dependencies
```
{% info_block warningBox "Verification" %}

Make sure that the following modules have been installed:
    
| Module | Expected Directory |
| --- | --- |
| `ProductList` | `vendor/spryker/product-list` |
| `ProductListDataImport` | `vendor/spryker/product-list-data-import` |
| `ProductListGui` | `vendor/spryker/product-list-gui` |
| `ProductListGuiExtension` | `vendor/spryker/product-list-gui-extension` |
| `ProductListSearch` | `vendor/spryker/product-list-search` |
| `ProductListStorage` | `vendor/spryker/product-list-storage` |
| `ProductStorageExtension` | `vendor/spryker/product-storage-extension` |

{% endinfo_block %}

### 2) Set up Database Schema and Transfer Objects

Define the following transfer objects:

src/Pyz/Shared/ProductList/Transfer/product_list.transfer.xml

```xml
<?xml version="1.0"?>
<transfers xmlns="spryker:transfer-01"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="spryker:transfer-01 http://static.spryker.com/transfer-01.xsd">
 
    <transfer name="ProductListCollection">
        <property name="productLists" type="ProductList[]" singular="productList"/>
    </transfer>
 
    <transfer name="Customer">
        <property name="customerProductListCollection" type="CustomerProductListCollection"/>
        <property name="companyUserTransfer" type="CompanyUser"/>
    </transfer>
 
    <transfer name="CustomerProductListCollection">
        <property name="blacklistIds" type="int[]" singular="blacklistId"/>
        <property name="whitelistIds" type="int[]" singular="whitelistId"/>
    </transfer>
 
</transfers>
 ```

Adjust the schema definition so that entity changes trigger events.

| Affected entity | Triggered events |
| --- | --- |
| `spy_product_list` | `Entity.spy_product_list.create`<br>`Entity.spy_product_list.update`<br>`Entity.spy_product_list.delete` |
| `spy_product_list_product_concrete` | `Entity.spy_product_list_product_concrete.create`<br>`Entity.spy_product_list_product_concrete.update`<br>`Entity.spy_product_list_product_concrete.delete` |
| `spy_product_list_category` | `Entity.spy_product_list_category.create`<br>`Entity.spy_product_list_category.update`<br>`Entity.spy_product_list_category.delete` |

src/Pyz/Zed/ProductList/Persistence/Propel/Schema/spy_product_list.schema.xml

``` xml
<?xml version="1.0"?>
<database xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        name="zed"
        xsi:noNamespaceSchemaLocation="http://static.spryker.com/schema-01.xsd"
        namespace="Orm\Zed\ProductList\Persistence"
        package="src.Orm.Zed.ProductList.Persistence">
    <table name="spy_product_list" identifierQuoting="true">
        <behavior name="event">
            <parameter name="spy_product_list_all" column="*"/>
        </behavior>
    </table>
    <table name="spy_product_list_product_concrete" isCrossRef="true">
        <behavior name="event">
            <parameter name="spy_product_list_product_concrete_all" column="*"/>
        </behavior>
    </table>
    <table name="spy_product_list_category" isCrossRef="true">
        <behavior name="event">
            <parameter name="spy_product_list_category_all" column="*"/>
        </behavior>
    </table>
 </database>
```

Set up synchronization queue pools so  that non-multi-store entities (not store-specific entities) are synchronized among stores:

src/Pyz/Zed/ProductListStorage/Persistence/Propel/Schema/spy_product_list_storage.schema.xml

```xml
<?xml version="1.0"?>
<database xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        name="zed"
        xsi:noNamespaceSchemaLocation="http://static.spryker.com/schema-01.xsd"
        namespace="Orm\Zed\ProductListStorage\Persistence"
        package="src.Orm.Zed.ProductListStorage.Persistence">
     <table name="spy_product_abstract_product_list_storage">
        <behavior name="synchronization">
            <parameter name="queue_pool" value="synchronizationPool"/>
        </behavior>
     </table>
     <table name="spy_product_concrete_product_list_storage">
        <behavior name="synchronization">
            <parameter name="queue_pool" value="synchronizationPool"/>
        </behavior>
    </table>
 </database>
```

Run the following commands to apply database changes and generate entity and transfer changes:

```bash
console transfer:generate
console propel:install
console transfer:generate
```
{% info_block warningBox "Verification" %}

Make sure that the following changes have been applied in the transfer objects:
    
| Transfer | Type | Event | Path |
| --- | --- | --- | --- |
| `ProductList` | class | created | `src/Generated/Shared/Transfer/ProductListTransfer` |
| `ProductListCollection` | class | created | `src/Generated/Shared/Transfer/ProductListCollectionTransfer` |
| `ProductListCategoryRelation` | class | created | `src/Generated/Shared/Transfer/ProductListCategoryRelationTransfer` |
| `ProductListProductConcreteRelation` | class | created | `src/Generated/Shared/Transfer/ProductListProductConcreteRelationTransfer` |
| `CartChange` | class | created | `src/Generated/Shared/Transfer/CartChangeTransfer` |
| `ProductListAggregateForm` | class | created | `src/Generated/Shared/Transfer/ProductListAggregateFormTransfer` |
| `QueryCriteria` | class | created | `src/Generated/Shared/Transfer/QueryCriteriaTransfer` |
| `QueryJoin` | class | created | `src/Generated/Shared/Transfer/QueryJoinTransfer` |
| `PageMap` | class | created | `src/Generated/Shared/Transfer/PageMapTransfer` |
| `ProductListMap` | class | created | `src/Generated/Shared/Transfer/ProductListMapTransfer` |
| `ProductPageSearch` | class | created | `src/Generated/Shared/Transfer/ProductPageSearchTransfer` |
| `ProductConcretePageSearch` | class | created | `src/Generated/Shared/Transfer/ProductConcretePageSearchTransfer` |
| `ProductPayload` | class | created | `src/Generated/Shared/Transfer/ProductPayloadTransfer` |
| `ProductAbstractProductListStorage` | class | created | `src/Generated/Shared/Transfer/ProductAbstractProductListStorageTransfer` |
| `ProductConcreteProductListStorage` | class | created | `src/Generated/Shared/Transfer/ProductConcreteProductListStorageTransfer` |
| `CustomerProductListCollection` | class | created | `src/Generated/Shared/Transfer/CustomerProductListCollectionTransfer` |
| `Customer.companyUserTransfer` | property | created | `src/Generated/Shared/Transfer/CustomerTransfer` |
| `Customer.customerProductListCollection` | property | created | `src/Generated/Shared/Transfer/CustomerTransfer` |

{% endinfo_block %}

{% info_block warningBox "Verification" %}

Make sure that the following changes have been applied by checking your database:
    
| Database Entity | Type | Event |
| --- | --- | --- |
| `spy_product_list` | table | created |
| `spy_product_list_product_concrete` | table | created |
| `spy_product_list_category` | table | created |
| `spy_product_abstract_product_list_storage` | table |  created |
| `spy_product_concrete_product_list_storage` | table | created |

{% endinfo_block %}

{% info_block warningBox "Verification" %}

Make sure that propel entities have been generated successfully by checking their existence. Also, change the generated entity classes to extend from Spryker core classes.
    
| Class Path | Extends |
| --- | --- |
| `src/Orm/Zed/ProductList/Persistence/Base/SpyProductList.php` | `Spryker\Zed\ProductList\Persistence\Propel\AbstractSpyProductList` |
| `src/Orm/Zed/ProductList/Persistence/Base/SpyProductListQuery.php` | `Spryker\Zed\ProductList\Persistence\Propel\AbstractSpyProductListQuery` |
| `src/Orm/Zed/ProductList/Persistence/Base/SpyProductListProductConcrete.php` | `Spryker\Zed\ProductList\Persistence\Propel\AbstractSpyProductListProductConcrete` |
| `src/Orm/Zed/ProductList/Persistence/Base/SpyProductListProductConcreteQuery.php` | `Spryker\Zed\ProductList\Persistence\Propel\AbstractSpyProductListProductConcreteQuery` |
| `src/Orm/Zed/ProductList/Persistence/Base/SpyProductListCategory.php` | `Spryker\Zed\ProductList\Persistence\Propel\AbstractSpyProductListCategory` |
| `src/Orm/Zed/ProductList/Persistence/Base/SpyProductListCategoryQuery.php` | `Spryker\Zed\ProductList\Persistence\Propel\AbstractSpyProductListCategoryQuery` |    
| `src/Orm/Zed/ProductListStorage/Persistence/Base/SpyProductAbstractProductListStorage` | `Spryker\Zed\ProductListStorage\Persistence\Propel\AbstractSpyProductAbstractProductListStorage` |
| `src/Orm/Zed/ProductListStorage/Persistence/Base/SpyProductAbstractProductListStorageQuery` | `Spryker\Zed\ProductListStorage\Persistence\Propel\AbstractSpyProductAbstractProductListStorageQuery` |    
| `src/Orm/Zed/ProductListStorage/Persistence/Base/SpyProductConcreteProductListStorage` | `Spryker\Zed\ProductListStorage\Persistence\Propel\AbstractSpyProductConcreteProductListStorage` | 
| `src/Orm/Zed/ProductListStorage/Persistence/Base/SpyProductConcreteProductListStorageQuery` | `Spryker\Zed\ProductListStorage\Persistence\Propel\AbstractSpyProductConcreteProductListStorageQuery` | 

{% endinfo_block %}

{% info_block warningBox "Verification" %}

Make sure that the changes have been implemented successfully. For this purpose, trigger the following methods and make sure that the above events have been triggered:
    
| Path | Method Name |
| --- | --- |
| `src/Orm/Zed/ProductList/Persistence/Base/SpyProductList.php` | `prepareSaveEventName()`<br>`addSaveEventToMemory()`<br>`addDeleteEventToMemory()` |
| `src/Orm/Zed/ProductList/Persistence/Base/SpyProductListProductConcrete.php` | `prepareSaveEventName()`<br>`addSaveEventToMemory()`<br>`addDeleteEventToMemory()` |
| `src/Orm/Zed/ProductList/Persistence/Base/SpyProductListCategory.php` | `prepareSaveEventName()`<br>`addSaveEventToMemory()`<br>`addDeleteEventToMemory()` |
| `src/Orm/Zed/ProductListStorage/Persistence/Base/SpyProductAbstractProductListStorage.php` | `sendToQueue()` |
| `src/Orm/Zed/ProductListStorage/Persistence/Base/SpyProductConcreteProductListStorage.php` | `sendToQueue()` |

{% endinfo_block %}

### 3) Add Translations

Append glossary according to your language configuration:

src/data/import/glossary.csv

```yaml
product-cart.info.restricted-product.removed,"Unavailable item %sku% was removed from your shopping cart.",en_US
product-cart.info.restricted-product.removed,"Der nicht verfügbare Artikel% sku% wurde aus Ihrem Einkaufswagen entfernt.",de_DE
```

Run the following console command to import data:

```bash
console data:import glossary
```

{% info_block warningBox "Verification" %}
Make sure that the configured data has been added to the `spy_glossary` table  in the database.
{% endinfo_block %}


### 4) Configure Export to Redis and Elasticsearch
#### Set up Event Listeners
With this step, you will be able to publish tables on change (create, edit, delete) to the `spy_product_abstract_product_list_storage`, `spy_product_concrete_product_list_storage` and synchronize the data to Storage and Search.

| Plugin | Specification |Prerequisites  | Namespace |
| --- | --- | --- | --- |
| `ProductListStorageEventSubscriber` | Registers listeners that are responsible for publishing product list information to storage when a related entity changes. | None | `Spryker\Zed\ProductListStorage\Communication\Plugin\Event\Subscriber` |
|`ProductListSearchEventSubscriber`|Registers listeners that are responsible for publishing product list information to search when a related entity changes.|None|`Spryker\Zed\ProductListSearch\Communication\Plugin\Event\Subscriber`|

src/Pyz/Zed/Event/EventDependencyProvider.php

```php
<?php
  
namespace Pyz\Zed\Event;
  
use Spryker\Zed\Event\EventDependencyProvider as SprykerEventDependencyProvider;
use Spryker\Zed\ProductListSearch\Communication\Plugin\Event\Subscriber\ProductListSearchEventSubscriber;
use Spryker\Zed\ProductListStorage\Communication\Plugin\Event\Subscriber\ProductListStorageEventSubscriber;
  
class EventDependencyProvider extends SprykerEventDependencyProvider
{
    public function getEventSubscriberCollection()
    {
        $eventSubscriberCollection = parent::getEventSubscriberCollection();
        $eventSubscriberCollection->add(new ProductListStorageEventSubscriber());
        $eventSubscriberCollection->add(new ProductListSearchEventSubscriber());
  
        return $eventSubscriberCollection;
    }
}
```

{% info_block warningBox "Verification" %}

Make sure that when a product list is created, updated or deleted, they are exported (or removed) to Redis and Elasticsearch accordingly.

| Storage type | Target Entity | Example Expected Data Identifier | 
| --- | --- | --- |
| Redis | Product Abstract Product List | `kv:product_abstract_product_lists:1` |
| Redis | Product Concrete Product List | `kv:product_concrete_product_list:1` |
| Elasticsearch | Product Abstract | `product_abstract:de:en_us:1` |
| Elasticsearch | Product Concrete | `product_concrete:de:en_us:1` |

{% endinfo_block %}

**Example expected data fragment for Product Abstract Product List**

```json
{
    "id_product_abstract": 1,
    "id_whitelists": [1,2],
    "id_blacklists": [3]
}
```

**Example expected data fragment for Product Concrete Product List**

```json
{
    "id_product_concrete": 1,
    "id_whitelists": [1,2],
    "id_blacklists": [3]
}
```

**Example expected data fragment for Product Abstract**

```json
{
    "product-lists": {
      "whitelists": [1,2],
      "blacklists": [3]
    }
}
```

**Example expected data fragment for Product Concrete**

```json
{
    "product-lists": {
      "whitelists": [1,2],
      "blacklists": [3]
    }
}
```

### Prepare Search Data for Export
With this step, we are extending Elasticsearch documents with product list data. Add the following plugins in your project:

| Plugin | Specification |Prerequisites  |Namespace  |
| --- | --- | --- | --- |
| `ProductListDataLoaderPlugin` |Loads product list data as payload for the publishing process.  | None|`Spryker\Zed\ProductListSearch\Communication\Plugin\ProductPageSearch\DataLoader`  |
|`ProductListDataLoadExpanderPlugin`|Expands product page data with all its product lists for publishing based on the previously collected product information.|Product list data should be available in the product payload. Suggestion: use `ProductListDataLoaderPlugin` (see above).|`Spryker\Zed\ProductListSearch\Communication\Plugin\ProductPageSearch\DataExpander`|
|`ProductListMapExpanderPlugin`|Maps product list data to Elasticsearch document structure.|Product list data should be available. Suggestion: use `ProductListDataLoadExpanderPlugin` (see above).|`Spryker\Zed\ProductListSearch\Communication\Plugin\ProductPageSearch`|
|`ProductAbstractProductListSynchronizationDataPlugin`|Can be executed to synchronize all product_abstract_product_list entries from database to Redis.|None|`Spryker\Zed\ProductListStorage\Communication\Plugin\Synchronization`|
|`ProductConcreteProductListSynchronizationDataPlugin`|Can be executed to synchronize all product_concrete_product_list entries from database to Redis.|None|`Spryker\Zed\ProductListStorage\Communication\Plugin\Synchronization`|
|`ProductConcreteProductListPageDataExpanderPlugin`|Expands `ProductConcretePageSearchTransfer` with product lists data and returns the modified object.|None|`Spryker\Zed\ProductListSearch\Communication\Plugin\ProductPageSearch`|
|`ProductConcreteProductListPageMapExpanderPlugin`|Maps product list data related to concrete products to Elasticsearch document structure.|Product list data should be available. Suggestion: use `ProductConcreteProductListPageDataExpanderPlugin` (see above).|`Spryker\Zed\ProductListSearch\Communication\Plugin\ProductPageSearch`|

src/Pyz/Zed/ProductPageSearch/ProductPageSearchDependencyProvider.php

```php
<?php
 
namespace Pyz\Zed\ProductPageSearch;
 
use Spryker\Shared\ProductListSearch\ProductListSearchConfig;
use Spryker\Zed\ProductListSearch\Communication\Plugin\ProductPageSearch\DataExpander\ProductListDataLoadExpanderPlugin;
use Spryker\Zed\ProductListSearch\Communication\Plugin\ProductPageSearch\DataLoader\ProductListDataLoaderPlugin;
use Spryker\Zed\ProductListSearch\Communication\Plugin\ProductPageSearch\ProductConcreteProductListPageDataExpanderPlugin;
use Spryker\Zed\ProductListSearch\Communication\Plugin\ProductPageSearch\ProductConcreteProductListPageMapExpanderPlugin;
use Spryker\Zed\ProductListSearch\Communication\Plugin\ProductPageSearch\ProductListMapExpanderPlugin;
use Spryker\Zed\ProductPageSearch\ProductPageSearchDependencyProvider as SprykerProductPageSearchDependencyProvider;
 
class ProductPageSearchDependencyProvider extends SprykerProductPageSearchDependencyProvider
{
    public const PLUGIN_PRODUCT_LABEL_DATA = 'PLUGIN_PRODUCT_LABEL_DATA';
 
    /**
     * @return \Spryker\Zed\ProductPageSearch\Dependency\Plugin\ProductPageDataExpanderInterface[]
     */
    protected function getDataExpanderPlugins()
    {
        return [
            ProductListSearchConfig::PLUGIN_PRODUCT_LIST_DATA => new ProductListDataLoadExpanderPlugin(),
        ];
    }
 
    /**
     * @return \Spryker\Zed\ProductPageSearch\Dependency\Plugin\ProductPageMapExpanderInterface[]
     */
    protected function getMapExpanderPlugins()
    {
        return [
            new ProductListMapExpanderPlugin(),
        ];
    }
 
    /**
     * @return \Spryker\Zed\ProductPageSearchExtension\Dependency\Plugin\ProductPageDataLoaderPluginInterface[]
     */
    protected function getDataLoaderPlugins()
    {
        return [
            new ProductListDataLoaderPlugin(),
        ];
    }
 
    /**
     * @return \Spryker\Zed\ProductPageSearchExtension\Dependency\Plugin\ProductConcretePageMapExpanderPluginInterface[]
     */
    protected function getConcreteProductPageMapExpanderPlugins(): array
    {
        return [
            new ProductConcreteProductListPageMapExpanderPlugin(),
        ];
    }
 
    /**
     * @return \Spryker\Zed\ProductPageSearchExtension\Dependency\Plugin\ProductConcretePageDataExpanderPluginInterface[]
     */
    protected function getProductConcretePageDataExpanderPlugins(): array
    {
        return [
            new ProductConcreteProductListPageDataExpanderPlugin(),
        ];
    }
}
```

src/Pyz/Zed/Synchronization/SynchronizationDependencyProvider.php

```php
<?php
 
namespace Pyz\Zed\Synchronization;
 
use Spryker\Zed\ProductListStorage\Communication\Plugin\Synchronization\ProductAbstractProductListSynchronizationDataPlugin;
use Spryker\Zed\ProductListStorage\Communication\Plugin\Synchronization\ProductConcreteProductListSynchronizationDataPlugin;
use Spryker\Zed\Synchronization\SynchronizationDependencyProvider as SprykerSynchronizationDependencyProvider;
 
class SynchronizationDependencyProvider extends SprykerSynchronizationDependencyProvider
{
    /**
     * @return \Spryker\Zed\SynchronizationExtension\Dependency\Plugin\SynchronizationDataPluginInterface[]
     */
    protected function getSynchronizationDataPlugins(): array
    {
        return [
            new ProductAbstractProductListSynchronizationDataPlugin(),
            new ProductConcreteProductListSynchronizationDataPlugin(),
        ];
    }
}
```

## 5) Import Data
### Import Product Lists
Prepare your data according to your requirements using our demo data:

vendor/spryker/product-list-data-import/data/import/product_list.csv

```yaml
"product_list_key","name","type"
"pl-001","All computers","whitelist"
"pl-002","No ASUS","blacklist"
"pl-003","All tablets","blacklist"
"pl-004","Cameras, Wearables & Phones","whitelist"
"pl-005","Camcorders over 400€","blacklist"
"pl-006","All cameras","whitelist"
"pl-007","Tablets with enough storage","whitelist"
"pl-008","No Smartwatches","blacklist"
```

| Column | Is obligatory? | Data type | Data example | Data explanation |
| --- | --- | --- | --- | --- |
| `product_list_key` | mandatory | string (unique) | pl-001 | Unique identifier used to identify a product list. |
|`name`|mandatory|string|All computers|Custom product list name used to provide a readable title or sentence of what the list contains. Used only for internal representation.|
|`type`|mandatory|string ("blacklist"/"whitelist")|whitelist|Defines whether the list is a blacklist or a whitelist.|

Register the following plugin to enable data import:

| Plugin | Specification | Prerequisites | Namespace |
| --- | --- | --- | --- |
| `ProductListDataImportPlugin` | Imports basic product list data into the database. | None | `Spryker\Zed\ProductListDataImport\Communication\Plugin` |

src/Pyz/Zed/DataImport/DataImportDependencyProvider.php

```php
<?php
 
namespace Pyz\Zed\DataImport;
 
use Spryker\Zed\DataImport\DataImportDependencyProvider as SprykerDataImportDependencyProvider;
use Spryker\Zed\ProductListDataImport\Communication\Plugin\ProductListDataImportPlugin;
 
class DataImportDependencyProvider extends SprykerDataImportDependencyProvider
{
    /**
     * @return array
     */
    protected function getDataImporterPlugins(): array
    {
        return [
            //...
            new ProductListDataImportPlugin(),
        ];
    }
}
```

Run the following console command to import data:

```bash
console data:import product-list
```

{% info_block warningBox "Verification" %}
Make sure that the configured data has been added to the `spy_product_list `table in the database.
{% endinfo_block %}

#### Import Product List Category Assignments
Prepare your data according to your requirements using our demo data:

vendor/spryker/product-list-data-import/data/import/product_list_to_category.csv

```yaml
product_list_key,category_key
pl-001,computer
pl-003,tablets
pl-004,cameras-and-camcorder
pl-004,smart-wearables
pl-004,smartphones
pl-008,smartwatches
```

| Column | Is obligatory? | Data type | Data example | Data explanation |
| --- | --- | --- | --- | --- |
| `product_list_key` | mandatory | string | pl-001 | An existing product list identifier for the assignment. |
|`category_key`|mandatory|string|computer|An existing category identifier to be assigned to the product list.|

Register the following plugin to enable data import:

| Plugin | Specification | Prerequisites | Namespace |
| --- | --- | --- | --- |
| `ProductListCategoryDataImportPlugin` | Imports category assignments for product lists. | Product list data and category data must exist before this installer plugin runs. | `Spryker\Zed\ProductListDataImport\Communication\Plugin` |

src/Pyz/Zed/DataImport/DataImportDependencyProvider.php

```php
<?php
 
namespace Pyz\Zed\DataImport;
 
use Spryker\Zed\DataImport\DataImportDependencyProvider as SprykerDataImportDependencyProvider;
use Spryker\Zed\ProductListDataImport\Communication\Plugin\ProductListCategoryDataImportPlugin;
 
class DataImportDependencyProvider extends SprykerDataImportDependencyProvider
{
    /**
     * @return array
     */
    protected function getDataImporterPlugins(): array
    {
        return [
            new ProductListCategoryDataImportPlugin(),
        ];
    }
}
```

Run the following console command to import data:

```bash
console data:import product-list-category
```
{% info_block warningBox "Verification" %}
Make sure that the configured data has been added to the `spy_product_list_category` table in the database.
{% endinfo_block %}

#### Import Product List Concrete Product Assignments

Prepare your data according to your requirements using our demo data:

vendor/spryker/product-list-data-import/data/import/product_list_to_concrete_product.csv

```yaml
product_list_key,concrete_sku
pl-002,166_30230575
pl-002,166_29565389
pl-002,165_29879507
pl-002,165_29879528
pl-002,099_27207215
pl-002,114_29911081
pl-002,114_30580483
pl-002,139_24699831
pl-002,140_22766487
pl-002,141_29380410
pl-002,142_30943081
pl-002,143_31035196
pl-002,144_29804740
pl-002,144_30312874
pl-002,144_29804741
pl-002,157_29525342
pl-002,158_29885222
pl-002,159_29885260
pl-002,159_29885269
pl-002,160_29533301
pl-002,161_29533300
pl-002,162_29533299
pl-002,163_29728850
pl-002,164_29565390
pl-002,165_29879507
pl-002,165_29879528
pl-002,166_30230575
pl-002,166_29565389
pl-002,215_123
pl-002,215_124
pl-005,204_29851280
pl-005,187_26306352
pl-005,188_26306353
pl-005,194_25904145
pl-005,195_25904159
pl-007,161_29533300
pl-007,177_24867659
pl-007,177_25913296
```

| Column | Is obligatory? | Data type | Data example | Data explanation |
| --- | --- | --- | --- | --- |
| `product_list_key` | mandatory | string | pl-002 | An existing product list identifier for the assignment. |
|`concrete_sku`|mandatory|string|166_30230575|An existing concrete product SKU to assign to the product list.|

Register the following plugin to enable data import:

| Plugin | Specification | Prerequisites | Namespace |
| --- | --- | --- | --- |
| `ProductListProductConcreteDataImportPlugin` | Imports concrete product assignments for product lists. | Product list data and concrete product data must exist before this importer plugin runs. | `vendor/spryker/product-list-data-import/src/Spryker/Zed/ProductListDataImport/Communication/Plugin/ProductListProductConcreteDataImportPlugin.php` |

src/Pyz/Zed/DataImport/DataImportDependencyProvider.php

```php
<?php
 
namespace Pyz\Zed\DataImport;
 
use Spryker\Zed\DataImport\DataImportDependencyProvider as SprykerDataImportDependencyProvider;
use Spryker\Zed\ProductListDataImport\Communication\Plugin\ProductListCategoryDataImportPlugin;
 
class DataImportDependencyProvider extends SprykerDataImportDependencyProvider
{
    /**
     * @return array
     */
    protected function getDataImporterPlugins(): array
    {
        return [
            new ProductListProductConcreteDataImportPlugin(),
        ];
    }
}
```

Run the following console command to import data:

```bash
console data:import product-list-product-concrete
```

{% info_block warningBox "Verification" %}
Make sure that the configured data has been added to the `spy_product_list_product_concrete` table in the database.
{% endinfo_block %}

## 6) Set up Behavior
### Reading from Product Storage
Add the following plugins to your project:

| Plugin | Specification | Prerequisites | Namespace |
| --- | --- | --- | --- |
| `ProductAbstractRestrictionPlugin` | This plugin is responsible for determining if an abstract product is restricted for the current customer or not. | None | `Spryker\Client\ProductListStorage\Plugin\ProductStorageExtension` |
|`ProductConcreteRestrictionPlugin`|This plugin is responsible for determining if a concrete product is restricted for the current customer or not.|None|`Spryker\Client\ProductListStorage\Plugin\ProductStorageExtension`|
|`ProductViewVariantRestrictionPlugin`|This plugin is responsible for filtering out product variants of a product view object.|The product view object should contain all available variant information.<br>Suggestion: use `ProductViewVariantExpanderPlugin`before, to collect variant data.|`Spryker\Client\ProductListStorage\Plugin\ProductStorageExtension`|
| `ProductAbstractListStorageRestrictionFilterPlugin` | The plugin is responsible for filtering abstract product ids based on white and blacklists. | None | `Spryker\Client\ProductListStorage\Plugin\ProductStorageExtension` |
| `ProductConcreteListStorageRestrictionFilterPlugin`| The plugin is responsible for filtering concrete product ids based on white and blacklists. | None | `Spryker\Client\ProductListStorage\Plugin\ProductStorageExtension`|

src/Pyz/Client/ProductStorage/ProductStorageDependencyProvider.php

```php
<?php
 
namespace Pyz\Client\ProductStorage;
 
use Spryker\Client\ProductListStorage\Plugin\ProductStorageExtension\ProductAbstractRestrictionPlugin;
use Spryker\Client\ProductListStorage\Plugin\ProductStorageExtension\ProductConcreteRestrictionPlugin;
use Spryker\Client\ProductListStorage\Plugin\ProductStorageExtension\ProductViewVariantRestrictionPlugin;
use Spryker\Client\ProductStorage\ProductStorageDependencyProvider as SprykerProductStorageDependencyProvider;
 
class ProductStorageDependencyProvider extends SprykerProductStorageDependencyProvider
{
    /**
     * @return \Spryker\Client\ProductStorage\Dependency\Plugin\ProductViewExpanderPluginInterface[]
     */
    protected function getProductViewExpanderPlugins()
    {
        return [
            new ProductViewVariantRestrictionPlugin(),
        ];
    }
 
    /**
     * @return \Spryker\Client\ProductStorageExtension\Dependency\Plugin\ProductAbstractRestrictionPluginInterface[]
     */
    protected function getProductAbstractRestrictionPlugins(): array
    {
        return [
            new ProductAbstractRestrictionPlugin(),
        ];
    }
 
    /**
     * @return \Spryker\Client\ProductStorageExtension\Dependency\Plugin\ProductConcreteRestrictionPluginInterface[]
     */
    protected function getProductConcreteRestrictionPlugins(): array
    {
        return [
            new ProductConcreteRestrictionPlugin(),
        ];
    }
 
    /**
     * @return \Spryker\Client\ProductStorageExtension\Dependency\Plugin\ProductAbstractRestrictionFilterPluginInterface[]
     */
    protected function getProductAbstractRestrictionFilterPlugins(): array
    {
        return [
            new ProductAbstractListStorageRestrictionFilterPlugin(),
        ];
    }
 
    /**
     * @return \Spryker\Client\ProductStorageExtension\Dependency\Plugin\ProductConcreteRestrictionFilterPluginInterface[]
     */
    protected function getProductConcreteRestrictionFilterPlugins(): array
    {
        return [
            new ProductConcreteListStorageRestrictionFilterPlugin(),
        ];
    }
}
```

{% info_block warningBox "Verification" %}
Make sure that features which use Redis to read product data (for example, Product Details Page, Product relations, etc.
{% endinfo_block %} don't show it when a product is restricted for the customer.)

#### Product Restrictions in the Cart
Add the following plugins to handle product restrictions for cart items:

| Plugin |Specification  |Prerequisites  |Namespace  |
| --- | --- | --- | --- |
| `ProductListRestrictedItemsPreCheckPlugin` | Checks all cart items and adds violations to the cart precheck response when they are restricted for the current customer. | None | `Spryker\Zed\ProductList\Communication\Plugin\CartExtension` |
|`RemoveRestrictedItemsPreReloadPlugin`|Checks and removes restricted cart items from the quote and adds a message for each removed item.|None|`Spryker\Zed\ProductList\Communication\Plugin\CartExtension`|

src/Pyz/Zed/Cart/CartDependencyProvider.php

```php
<?php
 
namespace Pyz\Zed\Cart;
 
use Spryker\Zed\Cart\CartDependencyProvider as SprykerCartDependencyProvider;
use Spryker\Zed\Kernel\Container;
use Spryker\Zed\ProductList\Communication\Plugin\CartExtension\ProductListRestrictedItemsPreCheckPlugin;
use Spryker\Zed\ProductList\Communication\Plugin\CartExtension\RemoveRestrictedItemsPreReloadPlugin;
 
class CartDependencyProvider extends SprykerCartDependencyProvider
{
    /**
     * @param \Spryker\Zed\Kernel\Container $container
     *
     * @return \Spryker\Zed\CartExtension\Dependency\Plugin\CartPreCheckPluginInterface[]
     */
    protected function getCartPreCheckPlugins(Container $container)
    {
        return [
            new ProductListRestrictedItemsPreCheckPlugin(),
        ];
    }
 
    /**
     * @param \Spryker\Zed\Kernel\Container $container
     *
     * @return \Spryker\Zed\CartExtension\Dependency\Plugin\PreReloadItemsPluginInterface[]
     */
    protected function getPreReloadPlugins(Container $container)
    {
        return [
            new RemoveRestrictedItemsPreReloadPlugin(),
        ];
    }
}
```

{% info_block warningBox "Verification" %}
Make sure that no restricted products can be added to a cart and if they were already in a cart, they get removed properly once a product became restricted for the customer.
{% endinfo_block %}

{% info_block infoBox "Info" %}
After completing the integration of this feature, you need to extend it further to provide one or many owner types for product lists to be able to assign them. A product list can only be fully functional when a user who browses the catalog gets product lists assigned, and this can be fulfilled by providing owners for product lists.<br>Check out our [Merchant Relationship Product Restrictions integration guide](/docs/scos/dev/feature-integration-guides/{{page.version}}/merchant-product-restrictions-feature-integration.html
{% endinfo_block %} that adds this functionality for merchant relationships.)
