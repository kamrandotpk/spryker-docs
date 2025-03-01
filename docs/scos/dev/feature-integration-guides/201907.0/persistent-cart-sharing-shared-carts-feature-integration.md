---
title: Persistent Cart Sharing + Shared Carts feature integration
description: The guide walks you through the process of installing the Shared Carts and Persistent Cart Sharing features in the project.
last_updated: Jan 28, 2020
template: feature-integration-guide-template
originalLink: https://documentation.spryker.com/v3/docs/persisitent-cart-sharing-shared-carts-integration
originalArticleId: cc1c8614-51dd-464d-a93b-002b47724c06
redirect_from:
  - /v3/docs/persisitent-cart-sharing-shared-carts-integration
  - /v3/docs/en/persisitent-cart-sharing-shared-carts-integration
---

## Install Feature Core
### Prerequisites
Install the required features:

| Name  | Version |
| --- | --- |
| Shared Carts | 201907.0 |
| Persistent Cart Sharing | 201907.0 |

### 1) Set up Behavior
Enable the following behaviors by registering the plugins:

| Plugin | Specification | Prerequisites| Namespace |
| --- | --- | --- | --- |
| `ShareCartByResourceShareZedActivatorStrategyPlugin` | Responsible for sharing a cart to company users within the same business unit. Updates company user access level if he proceeded the cart share link with higher permission.  | Expects the Company User to be logged in. | `Spryker\Zed\SharedCart\Communication\Plugin\ResourceShare` |
| `CartShareLoginRequiredResourceShareClientActivatorStrategyPlugin` | Provides a redirect route for resource share link when share option requires Customer to be logged in. | None | `Spryker\Client\SharedCart\Plugin\ResourceShare` |
| `SwitchDefaultCartResourceShareClientActivatorStrategyPlugin` | Switches default cart for company user after he proceeded cart share link with Read-only or Full Access access. | None | `Spryker\Client\SharedCart\Plugin\ResourceShare` |

<details open>
<summary markdown='span'>src/Pyz/Zed/ResourceShare/ResourceShareDependencyProvider.php</summary>

```php
<?php
 
namespace Pyz\Zed\ResourceShare;
 
use Spryker\Zed\ResourceShare\ResourceShareDependencyProvider as SprykerResourceShareDependencyProvider;
use Spryker\Zed\SharedCart\Communication\Plugin\ResourceShare\ShareCartByResourceShareZedActivatorStrategyPlugin;
 
class ResourceShareDependencyProvider extends SprykerResourceShareDependencyProvider
{
    /**
     * @return \Spryker\Zed\ResourceShareExtension\Dependency\Plugin\ResourceShareZedActivatorStrategyPluginInterface[]
     */
    protected function getResourceShareActivatorStrategyPlugins(): array
    {
        return [
            new ShareCartByResourceShareZedActivatorStrategyPlugin(),
        ];
    }
}
```
<br>
</details>

<details open>
<summary markdown='span'>src/Pyz/Client/ResourceShare/ResourceShareDependencyProvider.php</summary>

```php
<?php
 
namespace Pyz\Client\ResourceShare;
 
use Spryker\Client\ResourceShare\ResourceShareDependencyProvider as SprykerResourceShareDependencyProvider;
use Spryker\Client\SharedCart\Plugin\ResourceShare\CartShareLoginRequiredResourceShareClientActivatorStrategyPlugin;
use Spryker\Client\SharedCart\Plugin\ResourceShare\SwitchDefaultCartResourceShareClientActivatorStrategyPlugin;
 
class ResourceShareDependencyProvider extends SprykerResourceShareDependencyProvider
{
    /**
     * @return \Spryker\Client\ResourceShareExtension\Dependency\Plugin\ResourceShareClientActivatorStrategyPluginInterface[]
     */
    protected function getBeforeZedResourceShareActivatorStrategyPlugins(): array
    {
        return [
            new CartShareLoginRequiredResourceShareClientActivatorStrategyPlugin(),
        ];
    }
 
    /**
     * @return \Spryker\Client\ResourceShareExtension\Dependency\Plugin\ResourceShareClientActivatorStrategyPluginInterface[]
     */
    protected function getAfterZedResourceShareActivatorStrategyPlugins(): array
    {
        return [
            new SwitchDefaultCartResourceShareClientActivatorStrategyPlugin(),
        ];
    }
}
```
<br>
</details>

{% info_block infoBox "Note" %}
All verification steps for plugins mentioned above require feature frontend steps to be processed.
{% endinfo_block %}

{% info_block warningBox "Verification" %}
Make sure, that you're able to share a cart with the "Internal Access" links (both Read-only and Full Acess
{% endinfo_block %} to other company users from the same business unit.<br>Make sure, that after you shared a cart with Read-only access to another company user and he followed a "Full Access" link - his cart access was updated to "Full Access".)

{% info_block warningBox "Verification" %}
Make sure, that when you have a cart share link with Read-only or Full Access and you're not logged in - you can see the "Please login to access requested shared resource." error message.
{% endinfo_block %}

{% info_block warningBox "Verification" %}
Make sure, that when you have a cart share link with Read-only or Full Access, you are logged in and you follow the link - your default cart will be changed to shared one before you get redirected to the cart page.
{% endinfo_block %}

## Install feature frontend
### Prerequisites
Install the required features:

| Name | Version|
| --- | --- |
| Shared Carts | 201907.0 |
| Persistent Cart Sharing | 201907.0 |

## 1) Add Translations
Append glossary according to your configuration:

<details open>
<summary markdown='span'>src/data/import/glossary.csv</summary>

```html
shared_cart.resource_share.strategy.error.cart_access_denied,The cart URL is wrong or try to login with another company user account.,en_US
shared_cart.resource_share.strategy.error.cart_access_denied,"Die URL des Einkaufswagens ist falsch, oder versuchen Sie sich mit einem anderen Konto anzumelden.",de_DE
shared_cart.resource_share.strategy.error.unable_to_share_cart,Unable to share a cart with provided customer.,en_US
shared_cart.resource_share.strategy.error.unable_to_share_cart,Der Einkaufswagen kann nicht mit dem angegebenen Kunden geteilt werden.,de_DE
shared_cart.resource_share.strategy.success.cart_share_access_updated,Access level was updated.,en_US
shared_cart.resource_share.strategy.success.cart_share_access_updated,Zugriffsebene wurde aktualisiert.,de_DE
```
<br>
</details>

Run the following console command to import data:

```bash
console data:import glossary
```
{% info_block warningBox "Verification" %}
Make sure that in the database the configured data are added to the `spy_glossary` table.
{% endinfo_block %}

##  2) Set up Behavior
Enable the following behaviors by registering the plugins:

| Plugin | Specification | Prerequisites | Namespace |
| --- | --- | --- | --- |
| `FullAccessCartShareOptionPlugin` | Provides an internal (Full Access) share option for the Share Cart via link widget. | None | `Spryker\Client\SharedCart\Plugin`|
| `ReadOnlyCartShareOptionPlugin` | Provides an internal (Read-Only) share option for the Share Cart via link widget. | None | `Spryker\Client\SharedCart\Plugin` |
| `SharedCartRouterStrategyPlugin` | Provides a route to the page, where the customer will be redirected to, after successful cart share with Read-only or Full Access access. | None | `SprykerShop\Yves\SharedCartPage\Plugin\ResourceShare` |

<details open>
<summary markdown='span'>src/Pyz/Client/PersistentCartShare/PersistentCartShareDependencyProvider.php</summary>

```php
<?php
 
namespace Pyz\Client\PersistentCartShare;
 
use Spryker\Client\PersistentCartShare\PersistentCartShareDependencyProvider as SprykerPersistentCartShareDependencyProvider;
use Spryker\Client\SharedCart\Plugin\PersistentCartShare\FullAccessCartShareOptionPlugin;
use Spryker\Client\SharedCart\Plugin\PersistentCartShare\ReadOnlyCartShareOptionPlugin;
 
class PersistentCartShareDependencyProvider extends SprykerPersistentCartShareDependencyProvider
{
    /**
     * @return \Spryker\Client\PersistentCartShareExtension\Dependency\Plugin\CartShareOptionPluginInterface[]
     */
    protected function getCartShareOptionPlugins(): array
    {
        return [
            new ReadOnlyCartShareOptionPlugin(),
            new FullAccessCartShareOptionPlugin(),
        ];
    }
}
```
<br>
</details>

<details open>
<summary markdown='span'>src/Pyz/Yves/ResourceSharePage/ResourceSharePageDependencyProvider.php</summary>

```php
<?php
 
namespace Pyz\Yves\ResourceSharePage;
 
use SprykerShop\Yves\ResourceSharePage\ResourceSharePageDependencyProvider as SprykerResourceSharePageDependencyProvider;
use SprykerShop\Yves\SharedCartPage\Plugin\ResourceShare\SharedCartRouterStrategyPlugin;
 
class ResourceSharePageDependencyProvider extends SprykerResourceSharePageDependencyProvider
{
    /**
     * @return \SprykerShop\Yves\ResourceSharePageExtension\Dependency\Plugin\ResourceShareRouterStrategyPluginInterface[]
     */
    protected function getResourceShareRouterStrategyPlugins(): array
    {
        return [
            new SharedCartRouterStrategyPlugin(),
        ];
    }
}
```
<br>
</details>

Run the following command to enable Javascript and CSS changes:

```bash
console frontend:yves:build
```

{% info_block warningBox "Verification" %}
Make sure, that "Share Cart by Link" widget on the Cart page generates a cart share link with Read-only access (which can be used by other company users from the same business unit only).
{% endinfo_block %}

{% info_block warningBox "Verification" %}
Make sure, that "Share Cart by Link" widget on the Cart page generates a cart share link with Full Access access (which can be used by other company users from the same business unit only).
{% endinfo_block %}

{% info_block warningBox "Verification" %}
Make sure, that when you proceed with the cart share link with Read-only or Full Access access, your default cart will be switched to shared one, and you'll be redirected to the cart page.
{% endinfo_block %}

{% info_block warningBox "Verification" %}
Login to Yves as Company User, add some product to the cart and go to the cart page.<br>Make sure, that you can see the "Share Cart via Link" widget on a cart page.<br>Make sure you can see an "Internal Users" radio button. Click on it.<br>Make sure, that you can see two generated links - one for Read-only access and another - for Full Access access.<br>Login as another Company User from the same business unit.<br>Proceed with the link for Read-only access. Make sure, that the cart was shared to you with Read-only access.<br>Proceed with the link for Full Access access. Make sure, that the "Access level was updated." message appeared, and now a cart is shared with you with Full Access access.
{% endinfo_block %}

