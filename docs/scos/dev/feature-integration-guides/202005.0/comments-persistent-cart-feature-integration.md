---
title: Comments + Persistent Cart feature integration
description: The guide walks you through the process of integrating the Comments + Persistent Cart feature into the project.
last_updated: Apr 24, 2020
template: feature-integration-guide-template
originalLink: https://documentation.spryker.com/v5/docs/comments-persistent-cart-feature-integration
originalArticleId: 36dd5afb-c74c-45b0-8728-4464c62d0b35
redirect_from:
  - /v5/docs/comments-persistent-cart-feature-integration
  - /v5/docs/en/comments-persistent-cart-feature-integration
---

## Install Feature Core
### Prerequisites
Install the required features:

| Name | Version |
| --- | --- |
| Comments | master |
| Persistent Cart | master |

### 1) Set up Behavior
Register the following plugins:

| Plugin | Specification | Prerequisites | Namespace |
| --- | --- | --- | --- |
| `CommentThreadQuoteExpanderPlugin` | Expands quote transfer with `CommentThread`. | None | `Spryker\Zed\Comment\Communication\Plugin\Quote` |

**Pyz\Zed\Quote\QuoteDependencyProvider.php**

```php
<?php

namespace Pyz\Zed\Quote;
 
use Spryker\Zed\Comment\Communication\Plugin\Quote\CommentThreadQuoteExpanderPlugin;
use Spryker\Zed\Quote\QuoteDependencyProvider as SprykerQuoteDependencyProvider;
 
class QuoteDependencyProvider extends SprykerQuoteDependencyProvider
{
	/**
	 * @return \Spryker\Zed\QuoteExtension\Dependency\Plugin\QuoteExpanderPluginInterface[]
	 */
	protected function getQuoteExpanderPlugins(): array
	{
		return [
			new CommentThreadQuoteExpanderPlugin(),
		];
	}
}
```

{% info_block warningBox "Verification" %}
Make sure that `QuoteTransfer::commentThread` contains information about comments when you retrieve a quote from the database.
{% endinfo_block %}

## Install feature frontend
### Prerequisites
Please overview and install the necessary features before beginning the integration step.

| Name | Version |
| --- | --- |
| Comments | 201907.0 |
| Cart | 201907.0 |

### 1) Set up Behavior
Register the following plugins:

| Plugin | Specification | Prerequisites | Namespace |
| --- | --- | --- | --- |
| `CartCommentThreadAfterOperationStrategyPlugin` | Updates a session quote with the comment thread. | None | `SprykerShop\Yves\CartPage\Plugin\CommentWidget` |

**Pyz\Yves\CommentWidget\CommentWidgetDependencyProvider.php**

```php
<?php
 
namespace Pyz\Yves\CommentWidget;
 
use SprykerShop\Yves\CartPage\Plugin\CommentWidget\CartCommentThreadAfterOperationStrategyPlugin;
use SprykerShop\Yves\CommentWidget\CommentWidgetDependencyProvider as SprykerShopCommentDependencyProvider;
 
class CommentWidgetDependencyProvider extends SprykerShopCommentDependencyProvider
{
	/**
	 * @return \SprykerShop\Yves\CommentWidgetExtension\Dependency\Plugin\CommentThreadAfterOperationStrategyPluginInterface[]
	 */
	protected function getCommentThreadAfterOperationStrategyPlugins(): array
	{
		return [
			new CartCommentThreadAfterOperationStrategyPlugin(),
		];
	}
}
```

{% info_block warningBox "Verification" %}
Make sure that add/update/remove actions update a session quote with the latest comment thread.
{% endinfo_block %}
