---
title: Glue API - Catalog Search feature integration
description: This guide will navigate you through the process of installing and configuring the Search API feature in Spryker OS.
last_updated: Nov 22, 2019
template: feature-integration-guide-template
originalLink: https://documentation.spryker.com/v3/docs/catalog-search-api-feature-integration-201903
originalArticleId: be58cbc9-8ade-4300-93d7-07da2bf4d8b9
redirect_from:
  - /v3/docs/catalog-search-api-feature-integration-201903
  - /v3/docs/en/catalog-search-api-feature-integration-201903
---

## Install feature API
### Prerequisites
Install the required features:
|Name|Version|Integration guide|
|---|---|---|
| Glue API: Glue Application |201903.0| [Glue Application feature integration](/docs/scos/dev/feature-integration-guides/{{page.version}}/glue-api/glue-api-glue-application-feature-integration.html) |
|Search|201903.0||
|Catalog2|01903.0||
|Product|201903.0|[Product API feature integration](/docs/scos/dev/feature-integration-guides/{{page.version}}/glue-api/product-api-feature-integration.html) <!-- Replace by v201903 once available-->|
|Catalog + Price|201903.0||

### 1) Install the required modules using Composer
Run the following command to install the required modules:

```yaml
composer require spryker/catalog-search-rest-api:"^2.1.0" spryker/catalog-search-products-resource-relationship:"^1.1.0" --update-with-dependencies
```
{% info_block infoBox "Verification" %}
Make sure that the following modules are installed:
{% endinfo_block %}
|Module|Expected Directory|
|---|---|
|`CatalogSearchRestApi`|`vendor/spryker/catalog-search-rest-api`|
|`CatalogSearchProductsResourceRelationship`|`vendor/spryker/catalog-search-products-resource-relationship`|

### 2) Set up Transfer objects
Run the following command to generate transfer changes:
```yaml
console transfer:generate
```
{% info_block infoBox "Verification" %}
Make sure that the following changes in transfer objects have occurred:
{% endinfo_block %}
|Transfer|Type|Event|Path|
|--- |--- |--- |--- |
|`RestCatalogSearchAttributesTransfer`|class|created|`src/Generated/Shared/Transfer/RestCatalogSearchAttributesTransfer`|
|`RestCatalogSearchSortTransfer`|class|created|`src/Generated/Shared/Transfer/RestCatalogSearchSortTransfer`|
|`RestCatalogSearchPaginationTransfer`|class|created|`src/Generated/Shared/Transfer/RestCatalogSearchPaginationTransfer`|
|`RestCatalogSearchAbstractProductsTransfer`|class|created|`src/Generated/Shared/Transfer/RestCatalogSearchAbstractProductsTransfer`|
|`RestCatalogSearchProductImageTransfer`|class|created|`src/Generated/Shared/Transfer/RestCatalogSearchProductImageTransfer`|
|`RestRangeSearchResultTransfer`|class|created|`src/Generated/Shared/Transfer/RestRangeSearchResultTransfer`|
|`RestFacetSearchResultTransfer`|class|created|`src/Generated/Shared/Transfer/RestFacetSearchResultTransfer`|
|`RestCatalogSearchSuggestionsAttributesTransfer`|class|created|`src/Generated/Shared/Transfer/RestCatalogSearchSuggestionsAttributesTransfer`|
|`RestCatalogSearchSuggestionAbstractProductsTransfer`|class|created|`src/Generated/Shared/Transfer/RestCatalogSearchSuggestionAbstractProductsTransfer`|
|`RestCatalogSearchSuggestionProductImageTransfer`|class|created|`src/Generated/Shared/Transfer/RestCatalogSearchSuggestionProductImageTransfer`|
|`RestPriceProductTransfer`|class|created|`src/Generated/Shared/Transfer/RestPriceProductTransfer`|
|`PriceModeConfigurationTransfer`|class|created|`src/Generated/Shared/Transfer/PriceModeConfigurationTransfer`|
|`RestCurrencyTransfer`|class|created|`src/Generated/Shared/Transfer/RestCurrencyTransfer`|
|`RestFacetConfigTransfer`|class|created|`src/Generated/Shared/Transfer/RestFacetConfigTransfer`|

### 3) Set Up Behavior
#### Enable resources and relationships
Activate the following plugins:
|Plugin|Specification|Prerequisites|Namespace|
|--- |--- |--- |--- |
|`CatalogSearchAbstractProductsResourceRelationshipPlugin`|Adds the `abstract-products` resource relationship to search results.|None|`Spryker\Glue\CatalogSearchProductsResourceRelationship\Plugin`|
|`CatalogSearchSuggestionsAbstractProductsResourceRelationshipPlugin`|Adds the `abstract-products` resource relationship to search suggestions results.|None|`Spryker\Glue\CatalogSearchProductsResourceRelationship\Plugin`|
|`CatalogSearchResourceRoutePlugin`|Registers the `search` resource.|None|`Spryker\Glue\CatalogSearchRestApi\Plugin`|
|`CatalogSearchSuggestionsResourceRoutePlugin`|Registers the `search-suggestions` resource.|None|`Spryker\Glue\CatalogSearchRestApi\Plugin`|
<details open>
<summary markdown='span'>src/Pyz/Glue/GlueApplication/GlueApplicationDependencyProvider.php</summary>

```php
<?php

namespace Pyz\Glue\GlueApplication;

use Spryker\Glue\CatalogSearchProductsResourceRelationship\Plugin\CatalogSearchAbstractProductsResourceRelationshipPlugin;
use Spryker\Glue\CatalogSearchProductsResourceRelationship\Plugin\CatalogSearchSuggestionsAbstractProductsResourceRelationshipPlugin;
use Spryker\Glue\CatalogSearchRestApi\CatalogSearchRestApiConfig;
use Spryker\Glue\CatalogSearchRestApi\Plugin\CatalogSearchResourceRoutePlugin;
use Spryker\Glue\CatalogSearchRestApi\Plugin\CatalogSearchSuggestionsResourceRoutePlugin;
use Spryker\Glue\GlueApplication\GlueApplicationDependencyProvider as SprykerGlueApplicationDependencyProvider;


class GlueApplicationDependencyProvider extends SprykerGlueApplicationDependencyProvider
{
    /**
    * @return \Spryker\Glue\GlueApplicationExtension\Dependency\Plugin\ResourceRoutePluginInterface[]
    */
    protected function getResourceRoutePlugins(): array
    {       
        return [
            new CatalogSearchResourceRoutePlugin(),
            new CatalogSearchSuggestionsResourceRoutePlugin(),
        ];
    }

    /**
    * @param \Spryker\Glue\GlueApplicationExtension\Dependency\Plugin\ResourceRelationshipCollectionInterface $resourceRelationshipCollection
    *
    * @return \Spryker\Glue\GlueApplicationExtension\Dependency\Plugin\ResourceRelationshipCollectionInterface
    */
    protected function getResourceRelationshipPlugins(
        ResourceRelationshipCollectionInterface $resourceRelationshipCollection
    ): ResourceRelationshipCollectionInterface {
        $resourceRelationshipCollection-&gt;addRelationship(
            CatalogSearchRestApiConfig::RESOURCE_CATALOG_SEARCH,
            new CatalogSearchAbstractProductsResourceRelationshipPlugin()
        );
        $resourceRelationshipCollection-&gt;addRelationship(
            CatalogSearchRestApiConfig::RESOURCE_CATALOG_SEARCH_SUGGESTIONS,
            new CatalogSearchSuggestionsAbstractProductsResourceRelationshipPlugin()
        );

        return $resourceRelationshipCollection;
    }
}
```
<br>
</details>

**Verification**
{% info_block infoBox %}
If CatalogSearchResourceRoutePlugin and CatalogSearchSuggestionsResourceRoutePlugin are installed correctly, the following endpoints should now be available:
{% endinfo_block %}

* https://glue.mysprykershop.comm/catalog-search?q={% raw %}{{{% endraw %}q_term{% raw %}}}{% endraw %}
* https://glue.mysprykershop.comm/catalog-search-suggestions?q={% raw %}{{{% endraw %}q_term{% raw %}}}{% endraw %}

{% info_block infoBox %}
To make sure that `CatalogSearchAbstractProductsResourceRelationshipPlugin` and `CatalogSearchSuggestionsAbstractProductsResourceRelationshipPlugin` are functioning correctly, do the following:
{% endinfo_block %}
{% info_block infoBox %}
Make a request to https://glue.mysprykershop.comm/catalog-search?q={% raw %}{{{% endraw %}q_term{% raw %}}}{% endraw %}&amp;include=abstract-products and verify that the response includes relationships to abstract-products resources.Make a request tohttps://glue.mysprykershop.comom/catalog-search-suggestions?q={% raw %}{{{% endraw %}q_term{% raw %}}}{% endraw %}&amp;include=abstract-products and verify that the response includes relationships to abstract-products resources.
{% endinfo_block %}
