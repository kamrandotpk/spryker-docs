---
title: Product lists + Catalog feature integration
description: The guide walks you through the process of installing the Product Lists as a catalog  feature in the project.
last_updated: Jun 16, 2021
template: feature-integration-guide-template
originalLink: https://documentation.spryker.com/2021080/docs/product-lists-catalog-feature-integration
originalArticleId: 14356b81-71a8-46ba-93b0-ef5d376fc290
redirect_from:
  - /2021080/docs/product-lists-catalog-feature-integration
  - /2021080/docs/en/product-lists-catalog-feature-integration
  - /docs/product-lists-catalog-feature-integration
  - /docs/en/product-lists-catalog-feature-integration
related:
  - title: Merchant Product Restrictions feature integration
    link: docs/scos/dev/feature-integration-guides/page.version/merchant-product-restrictions-feature-integration.html
---

## Install feature core

### Prerequisites

Install the required features:

| NAME | VERSION |
| --- | --- |
| Product Lists | {{page.version}} |
| Catalog | {{page.version}} |
| Customer | {{page.version}} |

### 1) Install the required modules using Composer

Run the following command(s) to install the required modules:

```
composer require spryker/customer-catalog:"^1.0.0" --update-with-dependencies
```

{% info_block warningBox "Verification" %}

Make sure that the following modules were installed:

| MODULE | EXPECTED DIRECTORY |
| --- | --- |
| CustomerCatalog | vendor/spryker/customer-catalog |

{% endinfo_block %}

### 2) Configure Elasticsearch querying

#### Prepare the search queries

Once the Product List data is exported to Elasticsearch, make sure to extend your search queries to filter out Restricted Products by adding the following query expander plugin to all your search queries where necessary.

| PLUGIN | SPECIFICATION | PREREQUISITES | NAMESPACE |
| --- | --- | --- | --- |
| ProductListQueryExpanderPlugin | Expands an Elasticsearch query for Abstract and Concrete Products with Blacklist and Whitelist filters based on the Customer session.<br>The result of the query will contain only Products that were on the given Whitelists, but not on the given Blacklists.| The Customer session must contain Product List information. See [Merchant Product Restrictions feature integration](/docs/scos/dev/feature-integration-guides/{{page.version}}/merchant-product-restrictions-feature-integration.html) for an example implementation. | Spryker\Client\CustomerCatalog\Plugin\Search |
| ProductListQueryExpanderPlugin | Expands search query with filtering by product list ID. | None | Spryker\Client\ProductListSearch\Plugin\Search |

{% info_block infoBox "Info" %}

The order of the query expander plugins matters for the search result. Make sure that your query expanders are in the appropriate order. I.e., the `FacetQueryExpanderPlugin` needs to be placed after all the other plugins that filter down the result; otherwise, it can't generate the proper query fragment for itself.

{% endinfo_block %}

**src/Pyz/Client/Catalog/CatalogDependencyProvider.php**

```php
<?php

namespace Pyz\Client\Catalog;

use Spryker\Client\Catalog\CatalogDependencyProvider as SprykerCatalogDependencyProvider;
use Spryker\Client\CustomerCatalog\Plugin\Search\ProductListQueryExpanderPlugin as CustomerCatalogProductListQueryExpanderPlugin;
use Spryker\Client\ProductListSearch\Plugin\Search\ProductListQueryExpanderPlugin as ProductListSearchProductListQueryExpanderPlugin;

class CatalogDependencyProvider extends SprykerCatalogDependencyProvider
{
    /**
     * @return \Spryker\Client\Search\Dependency\Plugin\QueryExpanderPluginInterface[]
     */
    protected function createCatalogSearchQueryExpanderPlugins()
    {
        return [
            new CustomerCatalogProductListQueryExpanderPlugin(),
        ];
    }

    /**
     * @return \Spryker\Client\Search\Dependency\Plugin\QueryExpanderPluginInterface[]
     */
    protected function createSuggestionQueryExpanderPlugins()
    {
        return [
            new CustomerCatalogProductListQueryExpanderPlugin(),
        ];
    }

    /**
     * @return \Spryker\Client\Search\Dependency\Plugin\QueryExpanderPluginInterface[]
     */
    protected function getProductConcreteCatalogSearchQueryExpanderPlugins(): array
    {
        return [
            new CustomerCatalogProductListQueryExpanderPlugin(),
            new ProductListSearchProductListQueryExpanderPlugin(),
        ];
    }
}
```

{% info_block infoBox "Info" %}

Make sure you haven't missed the expansion of any product search queries in your project where you need to consider Product Lists.

{% endinfo_block %}

{% info_block warningBox "Verification" %}

Once you are done with this step, you should only be able to see those Products in your search results, which are on the Product Lists of your Customer's session.<br>You should also be able to filter product concrete search results by product list ID.

{% endinfo_block %}
