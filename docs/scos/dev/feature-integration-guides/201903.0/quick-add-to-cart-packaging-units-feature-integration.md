---
title: Quick Add to Cart + Packaging Units feature integration
description: Quick Add to Cart + Packaging Units allow buying products in different packaging units. This guide describes how to integrate this feature into your project.
last_updated: Nov 22, 2019
template: feature-integration-guide-template
originalLink: https://documentation.spryker.com/v2/docs/quick-order-packaging-units-feature-integration
originalArticleId: f2de4075-c61c-46b2-895e-98b641d8936a
redirect_from:
  - /v2/docs/quick-order-packaging-units-feature-integration
  - /v2/docs/en/quick-order-packaging-units-feature-integration
---

## Install feature frontend
### Prerequisites
Install the required features:
|Name|Version|
|---|---|
|Quick Add To Cart|201903.0|
|Packaging units|201903.0|

### 1) Set up Behavior
#### Set up the Additional Functionality

Enable the following behaviors by registering the plugins:

|Plugin|Specification|Prerequisites|Namespace|
|---|---|---|---|
|`QuickOrderItemDefaultPackagingUnitExpanderPlugin`|Expands `ItemTransfer` with packaging unit data if available.|None|`SprykerShop\Yves\ProductPackagingUnitWidget\Plugin\QuickOrder`|

<details open>
<summary markdown='span'>src/Pyz/Yves/QuickOrderPage/QuickOrderPageDependencyProvider.php</summary>

```php
<?php
 
namespace Pyz\Yves\QuickOrderPage;
 
use SprykerShop\Yves\ProductPackagingUnitWidget\Plugin\QuickOrder\QuickOrderItemDefaultPackagingUnitExpanderPlugin;
use SprykerShop\Yves\QuickOrderPage\QuickOrderPageDependencyProvider as SprykerQuickOrderPageDependencyProvider;
 
class QuickOrderPageDependencyProvider extends SprykerQuickOrderPageDependencyProvider
{
	/**
	* @return \SprykerShop\Yves\QuickOrderPageExtension\Dependency\Plugin\QuickOrderItemExpanderPluginInterface[]
	*/
	protected function getQuickOrderItemTransferExpanderPlugins(): array
	{
		return [
			new QuickOrderItemDefaultPackagingUnitExpanderPlugin(),
		];
	}
}
```
<br>
</details>

{% info_block warningBox "Verification" %}
Make the following checks at `https://mysprykershop.com/quick-order`: `QuickOrderItemDefaultPackagingUnitExpanderPlugin` sets default configuration for a product with packaging units:<ol><li>Select a product with packaging units on the **Quick Add To Cart** page and add it to the cart. </li><li>Check `ItemTransfer` in Cart if it has `amount`, `amountSalesUnit`, `amountLeadProduct`, and `productPackagingUnit` properties set.</li></ol>
{% endinfo_block %}
