---
title: Quick Add to Cart + Shopping Lists feature integration
description: Quick Add to Cart + Shopping Lists allow creating a shopping list to buy products. This guide describes how to integrate this feature into your project.
last_updated: Nov 22, 2019
template: feature-integration-guide-template
originalLink: https://documentation.spryker.com/v2/docs/quick-order-shopping-lists-feature-integration
originalArticleId: 96c7203d-ce4c-40da-b8f0-ad4d738a11e1
redirect_from:
  - /v2/docs/quick-order-shopping-lists-feature-integration
  - /v2/docs/en/quick-order-shopping-lists-feature-integration
---

## Install feature frontend
### Prerequisites

Install the required features:
|Name |	Version|
|---|---|
|Quick Add To Cart|201903.0|
|Shopping Lists|201903.0|

### 1) Set up Widgets

Register the following global widget:

|Widget|Description|Namespace|
|---|---|---|
|`AddItemsToShoppingListWidget`|Adds another submit button and a drop-down list with the shopping lists available for the logged-in customer. Note: You don't need it if you don't use Shopping List functionality or just don't want it to be displayed on the Quick Order page.|`SprykerShop\Yves\ShoppingListWidget\Widget`|

<details open>
<summary markdown='span'>src\Pyz\Yves\ShopApplication\ShopApplicationDependencyProvider.php</summary>

```php
<?php
 
namespace Pyz\Yves\ShopApplication;
 
use SprykerShop\Yves\ShopApplication\ShopApplicationDependencyProvider as SprykerShopApplicationDependencyProvider;
use SprykerShop\Yves\ShoppingListWidget\Widget\AddItemsToShoppingListWidget;
 
class ShopApplicationDependencyProvider extends SprykerShopApplicationDependencyProvider
{
	/**
	 * @return string[]
	 */
	protected function getGlobalWidgets(): array
	{
		return [
			AddItemsToShoppingListWidget::class,
		];
	}
}		
```
<br>
</details>

{% info_block warningBox "Verification" %}
Make sure that the following elements are available on the **Quick Order** page for logged-in customers (`https://mysprykershop.com/quick-order`
{% endinfo_block %}:<ul><li>**Submit** button under the form called *Add to shopping list*</li><li>**Drop-down box** containing shopping list names.</li></ul>)

### 2) Set up Behavior

#### Set up the Additional Functionality

Enable the following behaviors by registering the plugins:

|Plugin|Specification|Prerequisites|Namespace|
|---|---|---|---|
|`ShoppingListQuickOrderFormHandlerStrategyPlugin`|Send items to Shopping list instead of Cart if **Add to shopping list** has been selected.|None|`SprykerShop\Yves\ShoppingListWidget\Plugin\QuickOrderPage`|

<details open>
<summary markdown='span'>src/Pyz/Yves/QuickOrderPage/QuickOrderPageDependencyProvider.php</summary>

```php
<?php
 
namespace Pyz\Yves\QuickOrderPage;
 
use SprykerShop\Yves\QuickOrderPage\QuickOrderPageDependencyProvider as SprykerQuickOrderPageDependencyProvider;
use SprykerShop\Yves\ShoppingListWidget\Plugin\QuickOrderPage\ShoppingListQuickOrderFormHandlerStrategyPlugin;
 
class QuickOrderPageDependencyProvider extends SprykerQuickOrderPageDependencyProvider
{
	/**
	* @return \SprykerShop\Yves\QuickOrderPageExtension\Dependency\Plugin\QuickOrderFormHandlerStrategyPluginInterface[]
	*/
	protected function getQuickOrderFormHandlerStrategyPlugins(): array
	{
		return [
			new ShoppingListQuickOrderFormHandlerStrategyPlugin(), #ShoppingListFeature
		];
	}
}	
```
<br>
</details>

{% info_block warningBox "Verification" %}
Make the following checks at https://mysprykershop.com/quick-order: `ShoppingListQuickOrderFormHandlerStrategyPlugin` takes care about storing quick order as a shopping list. Click **Add To Shopping List** on the **Quick Order** page and check if products were successfully added to the shopping list.
{% endinfo_block %}
