---
title: Glue API - Glue application feature integration
description: This guide will navigate you through the process of installing and configuring the Glue Application feature in Spryker OS.
last_updated: May 14, 2020
template: feature-integration-guide-template
originalLink: https://documentation.spryker.com/v4/docs/glue-api-glue-application-feature-integration
originalArticleId: 808326dc-e0f5-43a9-859a-8bc2db5b1f8c
redirect_from:
  - /v4/docs/glue-api-glue-application-feature-integration
  - /v4/docs/en/glue-api-glue-application-feature-integration
---

Follow the steps below to install Glue application feature API.


### Prerequisites

Install the required features:

| Name | Type | Version |
| --- | --- | --- |
| Spryker Core | Feature | 202001.0 |

### 1) Install the required modules using Composer

Run the following command(s) to install the required modules:

```bash
composer require spryker/glue-application:"^1.0.0" spryker/entity-tags-rest-api:"^1.0.0" spryker/stores-rest-api:"^1.0.0" --update-with-dependencies
```

{% info_block warningBox “Verification” %}

Make sure that the following modules are installed:

| Module | Expected Directory |
| --- | --- |
| `GlueApplication` | `vendor/spryker/glue-application` |
| `EntityTagsRestApi` | `vendor/spryker/entity-tag-rest-api` |
| `StoresRestApi` | `vendor/spryker/stores-rest-api` |

{% endinfo_block %}

### 2) Set Up Configuration

Add the necessary parameters to `config/Shared/config_default.php`:

**config/Shared/config_default.php<**
    
```bash
$config[GlueApplicationConstants::GLUE_APPLICATION_DOMAIN] = 'https://glue.mysprykershop.com';
$config[GlueApplicationConstants::GLUE_APPLICATION_CORS_ALLOW_ORIGIN] = 'https://glue.mysprykershop.com';
$config[GlueApplicationConstants::GLUE_APPLICATION_REST_DEBUG] = false;
```

#### Add Global CORS policy

{% info_block infoBox "Info" %}

`GLUE_APPLICATION_CORS_ALLOW_ORIGIN` should be configured for every domain used in the project.

{% endinfo_block %}

Adjust `config/Shared/config_default.php`:

**config/Shared/config_default.php**
    
```bash
$config[GlueApplicationConstants::GLUE_APPLICATION_CORS_ALLOW_ORIGIN] = 'https://glue.mysprykershop.com';
```

#### Allow CORS requests to any domain

Adjust `config/Shared/config_default.php`:

**config/Shared/config_default.php**

```bash
$config[GlueApplicationConstants::GLUE_APPLICATION_CORS_ALLOW_ORIGIN] = '*';
```

{% info_block warningBox “Verification” %}

To make sure the CORS headers are set up correctly, send an `OPTIONS` request to any valid GLUE resource with Origin header `https://glue.mysprykershop.com/` and see the correct JSON response:
- Verify that the access-control-allow-origin header exists and is the same as set in config
- Verify that the access-control-allow-methods header exists and contains all available methods
- Make POST, PATCH or DELETE request (can choose any of available one and verify that response headers are the same
  
{% endinfo_block %} 

{% info_block infoBox %}

- When the `GlueApplicationConfig::isEagerRelationshipsLoadingEnabled()` option is set to "false", no relationship will be loaded, unless they are explicitly specified in the "included" query parameter (e.g. `/abstract-products?include=abstract-product-prices`).
- When the `GlueApplicationConfig::isEagerRelationshipsLoadingEnabled()` option is set to "true", all resource relationships will be loaded by default unless you pass empty "include" query parameter (e.g. `/abstract-products?include=`). If you specify needed relationships in the "included" query parameter, only required relationships will be added to response data.
  
{% endinfo_block %}

### 3) Set Up Transfer Objects

Run the following command to generate transfer objects:

```bash
console transfer:generate
```

{% info_block warningBox “Verification” %}

Make sure that the following changes have occurred:

| Transfer | Type | Event | Path |
| --- | --- | --- | --- |
| `RestPageOffsetsTransfer` | class | created | `src/Generated/Shared/Transfer/RestPageOffsetsTransfer.php` |
| `RestErrorMessageTransfer` | class | created | `src/Generated/Shared/Transfer/RestErrorMessageTransfer.php` |
| `RestErrorCollectionTransfer` | class | created | `src/Generated/Shared/Transfer/RestErrorCollectionTransfer.php` |
| `RestVersionTransfer` | class | created | `src/Generated/Shared/Transfer/RestVersionTransfer.php` |
| `RestUserTransfer` | class | created | `src/Generated/Shared/Transfer/RestUserTransfer.php` |
| `StoresRestAttributesTransfer` | class | created | `src/Generated/Shared/Transfer/StoresRestAttributesTransfer.php` |
| `StoreCountryRestAttributesTransfer` | class | created | `src/Generated/Shared/Transfer/StoreCountryRestAttributesTransfer.php` |
| `StoreRegionRestAttributesTransfer` | class | created | `src/Generated/Shared/Transfer/StoreRegionRestAttributesTransfer.php` |
| `StoreLocaleRestAttributesTransfer` | class | created | `src/Generated/Shared/Transfer/StoreLocaleRestAttributesTransfer.php` |
| `StoreCurrencyRestAttributesTransfer` | class | created | `src/Generated/Shared/Transfer/StoreCurrencyRestAttributesTransfer.php` |

{% endinfo_block %}

### 4) Set Up Behavior

Activate the following plugins:

| Plugin | Specification | Prerequisites | Namespace |
| --- | --- | --- | --- |
| `GlueResourceBuilderService` | Registers the resource builder service in Glue Application. | None | `Spryker\Glue\GlueApplication\Plugin\Rest\ServiceProvider` |
| `GlueApplicationServiceProvider` | Registers the pimple plugin, the controller resolver and configures the debug mode in Glue Application. | None | `Spryker\Glue\GlueApplication\Plugin\Rest\ServiceProvider` |
| `SessionServiceProvider` | Registers session services in Glue Application. | None | `Silex\Provider` |
| `ServiceControllerServiceProvider` | Registers the controller resolver service in Glue Application. | None | `Silex\Provider` |
| `GlueServiceProviderPlugin` | Registers the `onKernelController` event listeners in Glue Application. | None | `Spryker\Glue\GlueApplication\Plugin\Rest` |
| `GlueRoutingServiceProvider` | Registers the URL matcher and router services in Glue Application. | None | `Spryker\Glue\GlueApplication\Plugin\Rest\ServiceProvider` |
| `SetStoreCurrentLocaleBeforeActionPlugin` | Sets a locale for the whole current store. | None | `Spryker\Glue\GlueApplication\Plugin\Rest\SetStoreCurrentLocaleBeforeActionPlugin` |
| `EntityTagFormatResponseHeadersPlugin` | Adds the ETag header to response if applicable. | None | `Spryker\Glue\EntityTagsRestApi\Plugin\GlueApplication\EntityTagFormatResponseHeadersPlugin` |
| `EntityTagRestRequestValidatorPlugin` | Verifies that the `If-Match` header is equal to entity tag. | None | `Spryker\Glue\EntityTagsRestApi\Plugin\GlueApplication\EntityTagRestRequestValidatorPlugin` |
| `StoresResourceRoutePlugin` | Registers the `stores` resource. | None | `Spryker\Glue\StoresRestApi\Plugin` |

**src/Pyz/Glue/GlueApplication/Bootstrap/GlueBootstrap.php**
    
```php
<?php
 
namespace Pyz\Glue\GlueApplication\Bootstrap;
 
use Silex\Provider\ServiceControllerServiceProvider;
use Silex\Provider\SessionServiceProvider;
use Spryker\Glue\GlueApplication\Bootstrap\AbstractGlueBootstrap;
use Spryker\Glue\GlueApplication\Plugin\Rest\GlueServiceProviderPlugin;
use Spryker\Glue\GlueApplication\Plugin\Rest\ServiceProvider\GlueApplicationServiceProvider;
use Spryker\Glue\GlueApplication\Plugin\Rest\ServiceProvider\GlueResourceBuilderService;
use Spryker\Glue\GlueApplication\Plugin\Rest\ServiceProvider\GlueRoutingServiceProvider;
 
class GlueBootstrap extends AbstractGlueBootstrap
{
	/**
	* @return void
	*/
	protected function registerServiceProviders(): void
	{
		$this->application
			->register(new GlueResourceBuilderService())
			->register(new GlueApplicationServiceProvider())
			->register(new SessionServiceProvider())
			->register(new ServiceControllerServiceProvider())
			->register(new GlueServiceProviderPlugin())
			->register(new GlueRoutingServiceProvider());
	}
}
```

Create a new entry point for Glue Application:

**public/Glue/index.php**

```php
<?php
 
use Pyz\Glue\GlueApplication\Bootstrap\GlueBootstrap;
use Spryker\Shared\Config\Application\Environment;
use Spryker\Shared\ErrorHandler\ErrorHandlerEnvironment;
 
define('APPLICATION', 'GLUE');
defined('APPLICATION_ROOT_DIR') || define('APPLICATION_ROOT_DIR', realpath(__DIR__ . '/../..'));
 
require_once APPLICATION_ROOT_DIR . '/vendor/autoload.php';
 
Environment::initialize();
 
$errorHandlerEnvironment = new ErrorHandlerEnvironment();
$errorHandlerEnvironment->initialize();
 
$bootstrap = new GlueBootstrap();
$bootstrap
	->boot()
	->run();
```

#### Configure web server

Create Nginx VHOST configuration:

**/etc/nginx/sites-enabled/DE_development_glue**

```php
server {
	# Listener for production/staging - requires external LoadBalancer directing traffic to this port
	listen 10001;
 
	# Listener for testing/development - one host only, doesn't require external LoadBalancer
	listen 80;
 
	server_name ~^glue\\..+\\.com$;
 
	keepalive_timeout 0;
	access_log  /data/logs/development/glue-access.log extended;
 
	# entry point for Glue Application
	root /data/shop/development/current/public/Glue;
 
	set $application_env development;
	# Binding store
	set $application_store DE;
	include "spryker/zed.conf";
}
```

Update hosts configuration by adding the following line (replace IP with your server's IP address):

**/etc/hosts**

```
ip glue.mysprykershop.com
```

{% info_block warningBox “Verification” %}

If everything is set up correctly, you should be able to access `https://glue.mysprykershop.com` and get a correct JSON response as follows:

**Default JSON Response**

```json
{
	"errors": [
		{
			"status": 404,
			"detail": "Not Found"
		}
	]
}
```

{% endinfo_block %}

**\Pyz\Glue\GlueApplication\GlueApplicationDependencyProvider.php**

```php
<?php
 
namespace Pyz\Glue\GlueApplication;
 
use Spryker\Glue\GlueApplication\GlueApplicationDependencyProvider as SprykerGlueApplicationDependencyProvider;
use Spryker\Glue\GlueApplication\Plugin\Rest\SetStoreCurrentLocaleBeforeActionPlugin;
use Spryker\Glue\StoresRestApi\Plugin\StoresResourceRoutePlugin;
 
class GlueApplicationDependencyProvider extends SprykerGlueApplicationDependencyProvider
{
	/**
	* @return \Spryker\Glue\GlueApplicationExtension\Dependency\Plugin\ResourceRoutePluginInterface[]
	*/
	protected function getResourceRoutePlugins(): array
	{
		return [
			new StoresResourceRoutePlugin(),
		];
	}
 
	/**
	* @return \Spryker\Glue\GlueApplicationExtension\Dependency\Plugin\ControllerBeforeActionPluginInterface[]
	*/
	protected function getControllerBeforeActionPlugins(): array
	{
		return [
			new SetStoreCurrentLocaleBeforeActionPlugin(),
		];
	}
     
	/**
	* @return \Spryker\Glue\GlueApplicationExtension\Dependency\Plugin\FormatResponseHeadersPluginInterface[]
	*/
	protected function getFormatResponseHeadersPlugins(): array
	{
		return [
			new EntityTagFormatResponseHeadersPlugin(),
		];
	}
 
	/**
	* @return \Spryker\Glue\GlueApplicationExtension\Dependency\Plugin\RestRequestValidatorPluginInterface[]
	*/
	protected function getRestRequestValidatorPlugins(): array
	{
		return [
			new EntityTagRestRequestValidatorPlugin(),
		];
	}
}
```

{% info_block infoBox "Hint" %}

Use constant of resource name instead of plain string.

{% endinfo_block %}

**src/Pyz/Glue/EntityTagsRestApi/EntityTagsRestApiConfig.php**

```php
<?php
 
namespace Pyz\Glue\EntityTagsRestApi;
 
use Spryker\Glue\CartsRestApi\CartsRestApiConfig;
use Spryker\Glue\EntityTagsRestApi\EntityTagsRestApiConfig as SprykerEntityTagsRestApiConfig;
 
class EntityTagsRestApiConfig extends SprykerEntityTagsRestApiConfig
{
	/**
	* @return string[]
	*/
	public function getEntityTagRequiredResources(): array
	{
		return array_merge(
			parent::getEntityTagRequiredResources(),
			[PyzRestApiConfig::RESOURCE_NAME]
		);
	}
}
```

{% info_block warningBox “Verification” %}

If everything is set up correctly, a request to `https://glue.mysprykershop.com` with the header `[{"key":"Accept-Language","value":"de_DE, de;q=0.9"}]` should result in a response that contains the content-language header set to de_DE.

{% endinfo_block %}

{% info_block warningBox “Verification” %}

Send a GET request to `https://glue.mysprykershop.com/{% raw %}{{{% endraw %}RESOURCE_NAME{% raw %}}}{% endraw %}/{% raw %}{{{% endraw %}identifier{% raw %}}}{% endraw %}`. Make sure that the response contains the 'ETag' header.

Prepare a PATCH request to `https://glue.mysprykershop.com/{% raw %}{{{% endraw %}RESOURCE_NAME{% raw %}}}{% endraw %}/{% raw %}{{{% endraw %}identitifer{% raw %}}}{% endraw %}`.

Add the 'If-Match' header with the value of ETag from the GET response header.

Add the request body.

```json
{
	"data": {
		"type": "RESOURCE_NAME",
		"attributes": {
			"name": "{% raw %}{{{% endraw %}new_name{% raw %}}}{% endraw %}"
		}
	}
}
```

{% endinfo_block %}

{% info_block warningBox “Verification” %}

Send a request with the specified header and body.<br>Make sure that the returned resource contains updated 'ETag'.

{% endinfo_block %}

{% info_block warningBox “Verification” %}

Send a GET request to `https://glue.mysprykershop.com/{% raw %}{{{% endraw %}RESOURCE_NAME{% raw %}}}{% endraw %}/{% raw %}{{{% endraw %}identifier{% raw %}}}{% endraw %}`.

Make sure that the response contains the 'ETag' header.

Prepare a PATCH request to `https://glue.mysprykershop.com/{% raw %}{{{% endraw %}RESOURCE_NAME{% raw %}}}{% endraw %}/{% raw %}{{{% endraw %}identifier{% raw %}}}{% endraw %}`.

Add the 'If-Match' header with some random value.

Add a request body.

```json
{
	"data": {
		"type": "RESOURCE_NAME",
		"attributes": {
			"name": "{% raw %}{{{% endraw %}new_name{% raw %}}}{% endraw %}"
		}
	}
}
```

Send a request with the specified header and body.

Make sure that the response contains the ETag validation error.

{% endinfo_block %}
		
{% info_block warningBox “Verification” %}

Make sure that the following endpoint is available: `http://mysprykershop.com/stores`

{% endinfo_block %}

