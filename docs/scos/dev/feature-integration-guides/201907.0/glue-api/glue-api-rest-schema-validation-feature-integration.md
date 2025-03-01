---
title: Glue API - REST Schema Validation feature integration
description: This guide will navigate you through the process of installing and configuring the REST Schema Validation feature in Spryker OS.
last_updated: May 14, 2020
template: feature-integration-guide-template
originalLink: https://documentation.spryker.com/v3/docs/glue-api-rest-schema-validation-feature-integrationglue-api-rest-schema-validation-feature-integration
originalArticleId: 2a29f1d2-6139-4889-b987-0dbab0e27b85
redirect_from:
  - /v3/docs/glue-api-rest-schema-validation-feature-integrationglue-api-rest-schema-validation-feature-integration
  - /v3/docs/en/glue-api-rest-schema-validation-feature-integrationglue-api-rest-schema-validation-feature-integration
---

Follow the steps below to install Rest schema validation feature API.

## Prerequisites

Install the required features:



| Name | Version | Integration guide | 
| --- | --- | --- |
| Spryker Core | 201907.0 | [Glue Application feature integration](/docs/scos/dev/feature-integration-guides/{{page.version}}/glue-api/glue-api-glue-application-feature-integration.html) |


## 1)  Install the required modules using Composer

Run the following command(s) to install the required modules:

```bash	
composer require spryker/rest-request-validator:"^1.0.0" --update-with-dependencies
```

{% info_block warningBox "Verification" %}

Make sure that the following modules have been installed:

| Module | Expected Directory |
| --- | --- |
| RestRequestValidator | vendor/spryker/rest-request-validator |
	

{% endinfo_block %}


## 2) Set up Behavior

Set up the following behaviors.

### Enable Console Command

Activate the console command provided by the module:


| Class | Specification | Prerequisites | Namespace |
| --- | --- | --- | --- |
| BuildValidationCacheConsole | Generates a validation cache that is required for request validation | None | 	Spryker\Zed\RestRequestValidator\Communication\Console |


**src/Pyz/Zed/Console/ConsoleDependencyProvider.php**

```php
<?php
 
namespace Pyz\Zed\Console;
 
use Spryker\Zed\Kernel\Container;
use Spryker\Zed\RestRequestValidator\Communication\Console\BuildValidationCacheConsole;
use Spryker\Zed\Console\ConsoleDependencyProvider as SprykerConsoleDependencyProvider;
 
class ConsoleDependencyProvider extends SprykerConsoleDependencyProvider
{
    /**
     * @param \Spryker\Zed\Kernel\Container $container
     *
     * @return \Symfony\Component\Console\Command\Command[]
     */
    protected function getConsoleCommands(Container $container)
    {
        $commands = [
            new BuildValidationCacheConsole(),
        ];
 
        return $commands;
    }
}
```

{% info_block warningBox "Verification" %}

To verify that `BuildValidationCacheConsole` is activated, make sure that the following command exists: `console | grep glue:rest:build-request-validation-cache`.

{% endinfo_block %}


### Generate Validation Cache

Run the following command to generate the validation cache:

```bash
console glue:rest:build-request-validation-cache
```


{% info_block warningBox "Verification" %}

Make sure that cache has been generated successfully. To do so, verify the contents of the `src/Generated/Glue/Validator` directory. It should contain a directory structure according to your store setup. Each directory should contain a `validation.cache` file with a Yaml of default core validation rules.
```yaml
- AT
    - validation.cache
- DE
    - validation.cache
...
```

{% endinfo_block %}

### Enable Request Validation

Activate the following plugin(s):



| Plugin | Specification | Prerequisites | Namespace |
| --- | --- | --- | --- |
| ValidateRestRequestAttributesPlugin | Validates the `request attributes` section of `POST` and `PATCH` methods. | None | Spryker\Glue\RestRequestValidator\Plugin |
	


		
**src/Pyz/Glue/GlueApplication/GlueApplicationDependencyProvider.php**

```php
<?php
 
namespace Pyz\Glue\GlueApplication;
 
use Spryker\Glue\RestRequestValidator\Plugin\ValidateRestRequestAttributesPlugin;
use Spryker\Glue\GlueApplication\GlueApplicationDependencyProvider as SprykerGlueApplicationDependencyProvider;
 
class GlueApplicationDependencyProvider extends SprykerGlueApplicationDependencyProvider
{
    /**
     * @return \Spryker\Glue\GlueApplicationExtension\Dependency\Plugin\RestRequestValidatorPluginInterface[]
     */
    protected function getRestRequestValidatorPlugins(): array
    {
        return [
            new ValidateRestRequestAttributesPlugin(),
        ];
    }
}
```

{% info_block warningBox "Verification" %}


Make sure that `ValidateRestRequestAttributesPlugin` has been activated:
1. Make sure that there is a Glue API feature that uses validation configuration in your project.
2. Create validation configuration:
    a. Create `src/Pyz/Glue/YourModuleRestApi/Validation/{module}.validation.yaml`.
    
    b. In the file, describe validation rules for endpoints. See see [Validate REST request format](/docs/scos/dev/tutorials-and-howtos/introduction-tutorials/glue-api/validating-rest-request-format.html) for more details. Example:

    ```yaml
    access-tokens:
      post:
        username:
          - NotBlank
          - Email
        password:
          - NotBlank
    ```


    c. Collect the validation cache:

    ```bash
    console glue:rest:build-request-validation-cache
    ```

2. Make a call to the endpoint you described in the validation file with invalid data. Request sample:

`POST http://mysprykershop.com/access-tokens`

```json
{
    "data":
        {
            "type":"access-tokens",
            "attributes":
                {
                    "username":"tester",
                    "password": ""
                }
        }
}
```

You should get a validation error similar to the following response sample:

```json
{
    "errors": [
        {
            "code": "901",
            "status": 422,
            "detail": "username => This value is not a valid email address."
        },
        {
            "code": "901",
            "status": 422,
            "detail": "password => This value should not be blank."
        }
    ]
}


{% endinfo_block %}
