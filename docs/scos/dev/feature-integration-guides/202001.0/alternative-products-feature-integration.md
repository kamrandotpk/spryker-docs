---
title: Alternative Products feature integration
description: This guide walks you through the process of installing the Alternative Products feature into your project.
last_updated: Mar 20, 2020
template: feature-integration-guide-template
originalLink: https://documentation.spryker.com/v4/docs/alternative-products-feature-integration
originalArticleId: cfe7055d-d805-4977-8f09-6dd73925fd33
redirect_from:
  - /v4/docs/alternative-products-feature-integration
  - /v4/docs/en/alternative-products-feature-integration
---

## Install Feature Core

### Prerequisites

Install the required features:

| Name | Version |
|---|---|
| Product | 202001.0 |
| Spryker Core | 202001.0 |

### 1) Install the required modules using Composer

Run the following command(s) to install the required modules:
```bash
composer require spryker-feature/alternative-products: "^202001.0" --update-with-dependencies 
```

{% info_block warningBox "Verification" %}

Make sure that the following modules were installed:

|Module|Expected Directory|
|--- |--- |
|`ProductAlternative`|`vendor/spryker/product-alternative`|
|`ProductAlternativeDataImport`|`vendor/spryker/product-alternative-data-import`|
|`ProductAlternativeGui`|`vendor/spryker/product-alternative-gui`|
|`ProductAlternativeStorage`|`vendor/spryker/product-alternative-storage`|

{% endinfo_block %}

### 2) Set up Database Schema and Transfer Objects

Adjust the schema definition so that entity changes trigger the events.

| Affected Entity | Triggered Events |
|---|---|
|  `spy_product_alternative` |  `Entity.spy_product_alternative.create` `Entity.spy_product_alternative.update` `Entity.spy_product_alternative.delete` |

**src/Pyz/Zed/ProductAlternative/Persistence/Propel/Schema/spy_product_alternative.schema.xml**
    
```xml
<?xml version="1.0"?>
<database xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 name="zed"
 xsi:noNamespaceSchemaLocation="http://static.spryker.com/schema-01.xsd"
 namespace="Orm\Zed\ProductAlternative\Persistence"
 package="src.Orm.Zed.ProductAlternative.Persistence">

<table name="spy_product_alternative" idMethod="native" allowPkInsert="true" phpName="SpyProductAlternative">
 <behavior name="event">
 <parameter name="spy_product_alternative_all" column="*"/>
 </behavior>
 </table>
 </database>
 ```
    
Set up synchronization queue pools so that non-multi-store entities (not store specific entities) get synchronized among stores:

**src/Pyz/Zed/ProductAlternativeStorage/Persistence/Propel/Schema/spy_product_alternative_storage.schema.xml**

```xml
 <?xml version="1.0"?>
<database xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 name="zed"
 xsi:noNamespaceSchemaLocation="http://static.spryker.com/schema-01.xsd"
 namespace="Orm\Zed\ProductAlternativeStorage\Persistence"
 package="src.Orm.Zed.ProductAlternativeStorage.Persistence">

 <table name="spy_product_alternative_storage">
 <behavior name="synchronization">
 <parameter name="queue_pool" value="synchronizationPool" />
 </behavior>
 </table>

 <table name="spy_product_replacement_for_storage">
 <behavior name="synchronization">
 <parameter name="queue_pool" value="synchronizationPool" />
 </behavior>
 </table>

</database> 
```

Run the following commands to apply the database changes and generate entity and transfer changes:

```bash
console transfer:generate
console propel:install
console transfer:generate
```

{% info_block warningBox "Verification" %}

Make sure that the following changes have been applied by checking your database:

|Database Entity|Type|Event|
|--- |--- |--- |
|`spy_product_alternative`|table|created|
|`spy_product_alternative_storage`|table|created|
|`spy_product_replacement_for_storage`|table|created|

{% endinfo_block %}

{% info_block warningBox "Verification" %}

Make sure that the following changes have been applied in transfer objects:

|Transfer|Type|Event|Path|
|--- |--- |--- |--- |
|`SpyProductAlternativeEntityTransfer`|class|created|`src/Generated/Shared/Transfer/SpyProductAlternativeEntityTransfer`|
|`SpyProductAlternativeStorageEntityTransfer`|class|created|`src/Generated/Shared/Transfer/SpyProductAlternativeStorageEntityTransfer`|
|`SpyProductReplacementForStorageEntityTransfer`|class|created|`src/Generated/Shared/Transfer/SpyProductReplacementForStorageEntityTransfer`|
|`ProductAlternative`|class|created|`src/Generated/Shared/Transfer/ProductAlternative`|
|`ProductAlternativeResponse`|class|created|`src/Generated/Shared/Transfer/ProductAlternativeResponse`|
|`ResponseMessage`|class|created|`src/Generated/Shared/Transfer/ResponseMessage`|
|`ProductAlternativeCollection`|class|created|`src/Generated/Shared/Transfer/ProductAlternativeCollection`|
|`ProductAlternativeCreateRequest`|class|created|`src/Generated/Shared/Transfer/ProductAlternativeCreateRequest`|
|`ProductAlternativeListItem`|class|created|`src/Generated/Shared/Transfer/ProductAlternativeListItem`|
|`ProductAlternativeList`|class|created|`src/Generated/Shared/Transfer/ProductAlternativeList`|
|`ProductAlternativeStorage`|class|created|`src/Generated/Shared/Transfer/ProductAlternativeStorage`|
|`ProductReplacementStorage`|class|created|`src/Generated/Shared/Transfer/ProductReplacementStorage`|

{% endinfo_block %}

{% info_block warningBox "Verification" %}

Make sure that the changes have been implemented successfully. For this purpose, trigger the following methods and make sure that the above events have been triggered:

|Path|Method Name|
|--- |--- |
|`src/Orm/Zed/ProductAlternative/Persistence/Base/SpyProductAlternative.php`|`prepareSaveEventName()``addSaveEventToMemory()``addDeleteEventToMemory()`|

{% endinfo_block %}

### 3) Configure Export to Redis
    
{% info_block infoBox "Info" %}

This step will publish tables on change (create, edit, delete to the `spy_product_alternative_storage`, `spy_product_replacement_for_storage`  and synchronize the data to Storage.

{% endinfo_block %}

#### Set up Event Listeners

| Plugin | Specification | Prerequisites | Namespace |
|---|---|---|---|
|  `ProductAlternativeStorageEventSubscriber` | Registers listeners that are responsible to publish alternative products storage entity changes when a related entity change event occurs. | None |  `Spryker\Zed\ProductAlternativeStorage\Communication\Plugin\Event\Subscriber` |

**src/Pyz/Zed/Event/EventDependencyProvider.php**
 
```php
<?php

namespace Pyz\Zed\Event;

use Spryker\Zed\Event\EventDependencyProvider as SprykerEventDependencyProvider;
use Spryker\Zed\ProductAlternativeStorage\Communication\Plugin\Event\Subscriber\ProductAlternativeStorageEventSubscriber;

class EventDependencyProvider extends SprykerEventDependencyProvider
{
 public function getEventSubscriberCollection()
 {
 $eventSubscriberCollection = parent::getEventSubscriberCollection();
 $eventSubscriberCollection->add(new ProductAlternativeStorageEventSubscriber());

 return $eventSubscriberCollection;
 }
}
```

#### Set up Re-Generate and Re-Sync Features

| Plugin | Specification | Prerequisites | Namespace |
|---|---|---|---|
|  `ProductAlternativeSynchronizationDataPlugin` | Allows synchronizing the whole storage table content into Storage. | None |  `Spryker\Zed\ProductAlternativeStorage\Communication\Plugin\Synchronization` |
|  `ProductReplacementForSynchronizationDataPlugin` | Allows synchronizing the whole storage table content into Storage. | None |  `Spryker\Zed\ProductAlternativeStorage\Communication\Plugin\Synchronization` |

**src/Pyz/Zed/ProductAlternativeStorage/ProductAlternativeStorageConfig.php**

```php
<?php

namespace Pyz\Zed\ProductAlternativeStorage;

use Pyz\Zed\Synchronization\SynchronizationConfig;
use Spryker\Zed\ProductAlternativeStorage\ProductAlternativeStorageConfig as SprykerProductAlternativeStorageConfig;

class ProductAlternativeStorageConfig extends SprykerProductAlternativeStorageConfig
{
 /**
 * @ return string|null
 */
 public function getProductAlternativeSynchronizationPoolName(): ?string
 {
 return SynchronizationConfig::DEFAULT_SYNCHRONIZATION_POOL_NAME;
 }

 /**
 * @return string|null
 */
 public function getProductReplacementForSynchronizationPoolName(): ?string
 {
 return SynchronizationConfig::DEFAULT_SYNCHRONIZATION_POOL_NAME;
 }
} 
```

**src/Pyz/Zed/Synchronization/SynchronizationDependencyProvider.php**

```php
<?php

namespace Pyz\Zed\Synchronization;

use Spryker\Zed\ProductAlternativeStorage\Communication\Plugin\Synchronization\ProductAlternativeSynchronizationDataPlugin;
use Spryker\Zed\ProductAlternativeStorage\Communication\Plugin\Synchronization\ProductReplacementForSynchronizationDataPlugin;
use Spryker\Zed\Synchronization\SynchronizationDependencyProvider as SprykerSynchronizationDependencyProvider;

class SynchronizationDependencyProvider extends SprykerSynchronizationDependencyProvider
{
 /**
 * @return \Spryker\Zed\SynchronizationExtension\Dependency\Plugin\SynchronizationDataPluginInterface[]
 */
 protected function getSynchronizationDataPlugins(): array
 {
 return [
 new ProductAlternativeSynchronizationDataPlugin(),
 new ProductReplacementForSynchronizationDataPlugin(),
 ];
 }
}
```

### 4) Import Data

#### Import Product Alternatives

{% info_block infoBox "Info" %}

The following imported entities will be used as alternative products in the Spryker OS.

{% endinfo_block %}

Prepare your data according to your requirements using our demo data:

**vendor/spryker/spryker/Bundles/ProductAlternativeDataImport/data/import/product_alternative.csv**

```yaml
concrete_sku,alternative_product_concrete_sku,alternative_product_abstract_sku
145_29885470,134_26145012,
145_29885471,134_29759322,
145_29885473,,134
138_30046855,142_30943081,
138_30046855,140_22766487,
138_30046855,,155
155_30149933,142_30943081,
155_30149933,140_22766487,
155_30149933,134_26145012,
```

| Column | REQUIRED | Data Type | Data Example | Data Explanation |
|---|---|---|---|---|
|  `concrete_sku` | mandatory | string | 420566 | SKU of concrete product which will have alternative products. |
|  `alternative_product_concrete_sku` | optional | string | 420565 | SKU of the concrete alternative product. |
|  `alternative_product_abstract_sku` | optional | string | M1000785 | SKU of the abstract alternative product. |

Register the following plugin to enable data import:

| Plugin | Specification | Prerequisites | Namespace |
|---|---|---|---|
|  `ProductAlternativeDataImportPlugin` | Imports alternative product data into the database. | None |  `Spryker\Zed\ProductAlternativeDataImport\Communication\Plugin` |

src/Pyz/Zed/DataImport/DataImportDependencyProvider.php

```php
<?php

namespace Pyz\Zed\DataImport;

use Spryker\Zed\DataImport\DataImportDependencyProvider as SprykerDataImportDependencyProvider;
use Spryker\Zed\ProductAlternativeDataImport\Communication\Plugin\ProductAlternativeDataImportPlugin;

class DataImportDependencyProvider extends SprykerDataImportDependencyProvider
{
 protected function getDataImporterPlugins(): array
 {
 return [
 new ProductAlternativeDataImportPlugin(),
 ];
 }
} 
```

Run the following console command to import data:

```bash
console data:import product-alternative
```

{% info_block warningBox "Verification" %}

Make sure that, in the database, the configured data has been added to the `spy_product_alternative` table.

{% endinfo_block %}

### 5) Set up Behavior

#### Set up Alternative Products Workflow

Enable the following behaviors by registering the plugins:

| Plugin | Specification | Prerequisites | Namespace |
|---|---|---|---|
|  `ProductConcretePluginUpdate` | Saves product alternatives on product concrete save. | None |  `Spryker\Zed\ProductAlternativeGui\Communication\Plugin\Product` |
|  `ProductConcreteFormEditTabsExpanderPlugin` | Expands form tabs for `ProductConcreteEditForm` with `Product Alternatives` section. | None |  `Spryker\Zed\ProductAlternativeGui\Communication\Plugin\ProductManagement` |
|  `ProductConcreteEditFormExpanderPlugin` | Expands `ProductConcreteEditForm` with `AddProductAlternativeForm` form. | None |  `Spryker\Zed\ProductAlternativeGui\Communication\Plugin\ProductManagement` |
|  `ProductConcreteFormEditDataProviderExpanderPlugin` | Adds alternative product information to `ProductConcreteEditForm` data. | Expected `idProductConcrete` set for `ProductConcreteTransfer`. |  `Spryker\Zed\ProductAlternativeGui\Communication\Plugin\ProductManagement` |
|  `ProductFormTransferMapperExpanderPlugin` | Adds product alternative create requests to product concrete transfer. | None |  `Spryker\Zed\ProductAlternativeGui\Communication\Plugin\ProductManagement` |

**src/Pyz/Zed/Product/ProductDependencyProvider**

```php
<?php

namespace Pyz\Zed\Product;

use Spryker\Zed\Product\ProductDependencyProvider as SprykerProductDependencyProvider;
use Spryker\Zed\ProductAlternativeGui\Communication\Plugin\Product\ProductConcretePluginUpdate as ProductAlternativeGuiProductConcretePluginUpdate;

class ProductDependencyProvider extends SprykerProductDependencyProvider
{
 /**
 * @param \Spryker\Zed\Kernel\Container $container
 *
 * @return \Spryker\Zed\Product\Dependency\Plugin\ProductConcretePluginUpdateInterface[]
 */
 protected function getProductConcreteBeforeUpdatePlugins(Container $container)
 {
 return [
 new ProductAlternativeGuiProductConcretePluginUpdate(), #ProductAlternativeFeature
 ];
 }
} 
```

**src/Pyz/Zed/ProductManagement/ProductManagementDependencyProvider.php**

```php
<?php

namespace Pyz\Zed\ProductManagement;

use Spryker\Zed\ProductAlternativeGui\Communication\Plugin\ProductManagement\ProductConcreteEditFormExpanderPlugin;
use Spryker\Zed\ProductAlternativeGui\Communication\Plugin\ProductManagement\ProductConcreteFormEditDataProviderExpanderPlugin;
use Spryker\Zed\ProductAlternativeGui\Communication\Plugin\ProductManagement\ProductConcreteFormEditTabsExpanderPlugin;
use Spryker\Zed\ProductAlternativeGui\Communication\Plugin\ProductManagement\ProductFormTransferMapperExpanderPlugin;
use Spryker\Zed\ProductManagement\ProductManagementDependencyProvider as SprykerProductManagementDependencyProvider;

class ProductManagementDependencyProvider extends SprykerProductManagementDependencyProvider
{
 /**
 * @return \Spryker\Zed\ProductManagementExtension\Dependency\Plugin\ProductConcreteFormEditTabsExpanderPluginInterface[]
 */
 protected function getProductConcreteFormEditTabsExpanderPlugins(): array
 {
 return [
 new ProductConcreteFormEditTabsExpanderPlugin(), #ProductAlternativeFeature
 ];
 }

 /**
 * @return \Spryker\Zed\ProductManagementExtension\Dependency\Plugin\ProductConcreteEditFormExpanderPluginInterface[]
 */
 protected function getProductConcreteEditFormExpanderPlugins(): array
 {
 return [
 new ProductConcreteEditFormExpanderPlugin(), #ProductAlternativeFeature
 ];
 }

 /**
 * @return \Spryker\Zed\ProductManagementExtension\Dependency\Plugin\ProductConcreteFormEditDataProviderExpanderPluginInterface[]
 */
 protected function getProductConcreteFormEditDataProviderExpanderPlugins(): array
 {
 return [
 new ProductConcreteFormEditDataProviderExpanderPlugin(), #ProductAlternativeFeature
 ];
 }

 /**
 * @return \Spryker\Zed\ProductManagementExtension\Dependency\Plugin\ProductConcreteFormEditDataProviderExpanderPluginInterface[]
 */
 protected function getProductFormTransferMapperExpanderPlugins(): array
 {
 return [
 new ProductFormTransferMapperExpanderPlugin(), #ProductAlternativeFeature
 ];
 }
}
```

{% info_block warningBox "Verification" %}

Make sure that when you edit any product variant in Zed you have "Product Alternatives" tab, and you can add some product SKU's as alternatives.

{% endinfo_block %}

## Install feature frontend

### Prerequisites

Please overview and install the necessary features before beginning the integration step.

| Name | Version |
|---|---|
| Product | 201903.0 |
| Spryker Core | 201903.0 |

### 1) Install the required modules using Composer

Run the following command(s) to install the required modules:

```bash
composer require spryker-feature/alternative-products: "^201903.0" --update-with-dependencies 
```
{% info_block warningBox "Verification" %}

Make sure that the following modules have been installed:

|Module|Expected Directory|
|--- |--- |
|`ProductAlternativeWidget`|`vendor/spryker-shop/product-alternative-widget`|
|`ProductReplacementForWidget`|`vendor/spryker-shop/product-replacement-for-widget`|


{% endinfo_block %}

### 2) Add Translations

Append glossary according to your configuration:

**src/data/import/glossary.csv**

```yaml
replacement_for_widget.replacement_for,Replacement for,en_US
replacement_for_widget.replacement_for,Ersatz für,de_DE
product_alternative_widget.product_alternative,Alternative products,en_US
product_alternative_widget.product_alternative,Alternative Produkte,de_DE
product_alternative_widget.add_to_shopping_list,Add to shopping list,en_USproduct_alternative_widget.add_to_shopping_list,Auf die Merkliste,de_DE
product_alternative_widget.alternative_for,Alternative for name,en_US
product_alternative_widget.alternative_for,Alternative für name,de_DE
product_alternative_widget.show_all,Show all,en_US
product_alternative_widget.show_all,Zeige alles,de_DE 
```

Run the following console command to import data:

```bash
console data:import glossary
```

{% info_block warningBox "Verification" %}

Make sure that in the database the configured data are added to the `spy_glossary` table.

{% endinfo_block %}

### 3) Set up Widgets

Register the following plugins to enable widgets:

| Plugin | Description | Prerequisites | Namespace |
|---|---|---|---|
|  `ProductAlternativeWidget` | Displays alternative product. | None |  `SprykerShop\Yves\ProductWidget\Widget` |
|  `PdpProductReplacementForListWidget` | Displays list of products for replacement. | None |  `SprykerShop\Yves\ProductWidget\Widget` |
|  `ProductReplacementForListWidget` | Displays product for replacement. | None |  `SprykerShop\Yves\ProductReplacementForWidget\Widget` |
|  `ProductAlternativeListWidget` | Display list of alternative products for the provided product. | None |  `SprykerShop\Yves\ProductAlternativeWidget\Widget` |

**src/Pyz/Yves/ShopApplication/ShopApplicationDependencyProvider.php**

```php
<?php

namespace Pyz\Yves\ShopApplication;

use SprykerShop\Yves\ProductWidget\Widget\ProductAlternativeWidget;
use SprykerShop\Yves\ProductWidget\Widget\PdpProductReplacementForListWidget;
use SprykerShop\Yves\ProductReplacementForWidget\Widget\ProductReplacementForListWidget;
use SprykerShop\Yves\ProductAlternativeWidget\Widget\ProductAlternativeListWidget;
use SprykerShop\Yves\ShopApplication\ShopApplicationDependencyProvider as SprykerShopApplicationDependencyProvider;

class ShopApplicationDependencyProvider extends SprykerShopApplicationDependencyProvider
{
 /**
 * @return string[]
 */
 protected function getGlobalWidgets(): array
 {
 return [
 ProductAlternativeWidget::class,
 PdpProductReplacementForListWidget::class,
 ProductReplacementForListWidget::class,
 ProductAlternativeListWidget::class,
 ];
 }
}
```

Run the following command to enable Javascript and CSS changes:
```bash
console frontend:yves:build 
```

{% info_block warningBox "Verification" %}

Make sure that the following widgets were registered:

|Module|Test|
|--- |--- |
|`ProductAlternativeWidget`|Assign some alternative products in Zed, and make sure that they are displayed on the detail page of the product to which they were assigned.|
|`ProductReplacementForListWidget`|Make that after you've assigned some product as an alternative for another you can see "Replacement for" section on its product detail page.|
|`PdpProductReplacementForListWidget`|Make that after you've assigned some product as an alternative for another you can see "Replacement for" section on its product detail page.|
|`ProductAlternativeListWidget`|Assign some alternative products in Zed, and make sure that they are displayed on the PDP of the product to which they were assigned.|

{% endinfo_block %}
