

This document describes how to integrate the Marketplace Product Offer + Service Points feature into a Spryker project.

## Install feature core

Follow the steps below to install the Marketplace Product Offer + Service Points feature core.

### Prerequisites

Install the required features:

| NAME                         | VERSION          | INTEGRATION GUIDE                                                                                                                                                                               |
|------------------------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Marketplace Product Offer    | {{page.version}} | [Marketplace Product Offer feature integration](/docs/pbc/all/offer-management/{{page.version}}/marketplace/install-and-upgrade/marketplace-product-offer-feature-integration.html) |
| Product Offer + Service Points | {{page.version}} | [Install the Product Offer + Service Points feature](/docs/scos/dev/feature-integration-guides/{{page.version}}/install-the-product-offer-service-points-feature.html)                            |

### 1) Set up behavior

Enable the following behaviors by registering the plugins:

| PLUGIN                                          | DESCRIPTION                                                                 | PREREQUISITES | NAMESPACE                                                                                    |
|-------------------------------------------------|-----------------------------------------------------------------------------|---------------|----------------------------------------------------------------------------------------------|
| MerchantProductOfferServiceStorageFilterPlugin  | Filters product offer services collection by active and approved merchants. |               | Spryker\Zed\MerchantProductOfferStorage\Communication\Plugin\ProductOfferServicePointStorage |

**src/Pyz/Client/Catalog/CatalogDependencyProvider.php**

```php
<?php

namespace Pyz\Zed\ProductOfferServicePointStorage;

use Spryker\Zed\MerchantProductOfferStorage\Communication\Plugin\ProductOfferServicePointStorage\MerchantProductOfferServiceStorageFilterPlugin;
use Spryker\Zed\ProductOfferServicePointStorage\ProductOfferServicePointStorageDependencyProvider as SprykerProductOfferServicePointStorageDependencyProvider;

class ProductOfferServicePointStorageDependencyProvider extends SprykerProductOfferServicePointStorageDependencyProvider
{
    /**
     * @return list<\Spryker\Zed\ProductOfferServicePointStorageExtension\Dependeency\Plugin\ProductOfferServiceStorageFilterPluginInterface>
     */
    protected function getProductOfferServiceStorageFilterPlugins(): array
    {
        return [
            new MerchantProductOfferServiceStorageFilterPlugin(),
        ];
    }
}
```

{% info_block warningBox "Verification" %}

Make sure that the only product offer service with active and approved merchant are published into Redis.

{% endinfo_block %}