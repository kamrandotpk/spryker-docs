---
title: "Glue API: Retrieve companies"
description: Learn how to retrieve company information via Glue API.
last_updated: Jun 16, 2021
template: glue-api-storefront-guide-template
originalLink: https://documentation.spryker.com/2021080/docs/retrieving-companies
originalArticleId: 238c375f-b541-445c-ad9c-ab1b4afb036f
redirect_from:
  - /docs/scos/dev/glue-api-guides/201811.0/managing-b2b-account/retrieving-companies.html
  - /docs/scos/dev/glue-api-guides/201903.0/managing-b2b-account/retrieving-companies.html
  - /docs/scos/dev/glue-api-guides/202212.0/managing-b2b-account/retrieving-companies.html
  - /docs/pbc/all/customer-relationship-management/202212.0/manage-using-glue-api/company-account/glue-api-retrieve-companies.html
related:
  - title: Retrieving company roles
    link: docs/scos/dev/glue-api-guides/page.version/managing-b2b-account/retrieving-company-roles.html
  - title: Retrieving company users
    link: docs/scos/dev/glue-api-guides/page.version/managing-b2b-account/retrieving-company-users.html
  - title: "Glue API: Authenticating as a company user"
    link: docs/pbc/all/identity-access-management/page.version/manage-using-glue-api/glue-api-authenticate-as-a-company-user.html
  - title: Managing company user authentication tokens
    link: docs/pbc/all/identity-access-management/page.version/manage-using-glue-api/glue-api-manage-company-user-authentication-tokens.html
  - title: Searching by company users
    link: docs/scos/dev/glue-api-guides/page.version/managing-b2b-account/searching-by-company-users.html
  - title: Retrieving business units
    link: docs/scos/dev/glue-api-guides/page.version/managing-b2b-account/retrieving-business-units.html
  - title: Retrieving business unit addresses
    link: docs/scos/dev/glue-api-guides/page.version/managing-b2b-account/retrieving-business-unit-addresses.html
  - title: Authenticating as a customer
    link: docs/pbc/all/identity-access-management/page.version/manage-using-glue-api/glue-api-authenticate-as-a-customer.html
  - title: Company Accounts overview
    link: docs/scos/user/features/page.version/company-account-feature-overview/company-accounts-overview.html
---

This endpoint allows retrieving information about companies.

## Installation

For detailed information on the modules that provide the API functionality and related installation instructions, see [Install the Company Account Glue API](/docs/pbc/all/customer-relationship-management/{{page.version}}/base-shop/install-and-upgrade/install-glue-api/install-the-company-account-glue-api.html).

## Retrieve a company

To retrieve information about a company, send the request:

***
`GET` **/companies/*{% raw %}{{{% endraw %}company_id{% raw %}}}{% endraw %}***
***


| PATH PARAMETER | DESCRIPTION |
| --- | --- |
| ***{% raw %}{{{% endraw %}company_id{% raw %}}}{% endraw %}*** | Unique identifier of a company to retrieve information about. Enter `mine` to retrieve information about the company of the currently authenticated company user. |


### Request


| HEADER KEY | TYPE | REQUIRED | DESCRIPTION |
| --- | --- | --- | --- |
| Authorization | string | &check; | String containing digits, letters, and symbols that authorize the company user. [Authenticate as a company user](/docs/pbc/all/identity-access-management/{{page.version}}/manage-using-glue-api/glue-api-authenticate-as-a-company-user.html#authenticate-as-a-company-user) to get the value.  |

| REQUEST | USAGE |
| --- | --- |
| GET https://glue.mysprykershop.com/companies/59b6c025-cc00-54ca-b101-191391adf2af | Retrieve information about the company with id `59b6c025-cc00-54ca-b101-191391adf2af`. |
| GET https://glue.mysprykershop.com/companies/mine | Retrieve information about the company of the currently authenticated company user. |

#### Response

<details>
<summary markdown='span'>Response sample: retrieve information about the companies of the currently authenticated company user</summary>

```json
{
    "data": [
        {
            "type": "companies",
            "id": "88efe8fb-98bd-5423-a041-a8f866c0f913",
            "attributes": {
                "isActive": true,
                "name": "BoB-Hotel Mitte",
                "status": "approved"
            },
            "links": {
                "self": "https://glue.mysprykershop.com/companies/88efe8fb-98bd-5423-a041-a8f866c0f913"
            }
        }
    ],
    "links": {
        "self": "https://glue.mysprykershop.com/companies/mine"
    }
}
```
</details>

| ATTRIBUTE | TYPE | DESCRIPTION |
| --- | --- | --- |
| name | String | Company name. |
| isActive | Boolean | Defines if the company is active. |
| status | String | Company status. Possible values are: `Pending`, `Approved`, or `Denied`.|


## Possible errors

| CODE | REASON |
| --- | --- |
| 001 | Authentication token is invalid. |
| 002 | Authentication token is missing. |
| 1801 | Specified company is not found, or the current authenticated company user does not have access to it. |
| 1803 | Current company account is not set. Select the current company user with `/company-user-access-tokens` to access the resource collection. |

To view generic errors that originate from the Glue Application, see [Reference information: GlueApplication errors](/docs/scos/dev/glue-api-guides/{{page.version}}/old-glue-infrastructure/reference-information-glueapplication-errors.html).

##  Next steps

* [Retrieve business units](/docs/scos/dev/glue-api-guides/{{page.version}}/managing-b2b-account/retrieving-business-units.html)
* [Retrieve company company roles](/docs/scos/dev/glue-api-guides/{{page.version}}/managing-b2b-account/retrieving-company-roles.html)
* [Retrieve business unit addresses](/docs/scos/dev/glue-api-guides/{{page.version}}/managing-b2b-account/retrieving-business-unit-addresses.html)
* [Manage company user authentication tokens](/docs/pbc/all/identity-access-management/{{page.version}}/manage-using-glue-api/glue-api-manage-company-user-authentication-tokens.html)
