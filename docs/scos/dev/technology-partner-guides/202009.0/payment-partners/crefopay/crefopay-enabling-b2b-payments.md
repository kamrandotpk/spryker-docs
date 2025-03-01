---
title: CrefoPay—Enabling B2B payments
description: CrefoPay module provides B2B strategy in payments.
last_updated: Aug 27, 2020
template: concept-topic-template
originalLink: https://documentation.spryker.com/v6/docs/crefopay-business-to-business-model
originalArticleId: 8d794f26-8cc9-4b91-9452-5657fcdbe007
redirect_from:
  - /v6/docs/crefopay-business-to-business-model
  - /v6/docs/en/crefopay-business-to-business-model
related:
  - title: Integrating CrefoPay
    link: docs/scos/dev/technology-partner-guides/page.version/payment-partners/crefopay/integrating-crefopay.html
  - title: CrefoPay
    link: docs/scos/user/technology-partners/page.version/payment-partners/crefopay.html
  - title: Installing and configuring CrefoPay
    link: docs/scos/dev/technology-partner-guides/page.version/payment-partners/crefopay/installing-and-configuring-crefopay.html
  - title: CrefoPay payment methods
    link: docs/scos/dev/technology-partner-guides/page.version/payment-partners/crefopay/crefopay-payment-methods.html
  - title: CrefoPay capture and refund Processes
    link: docs/scos/dev/technology-partner-guides/page.version/payment-partners/crefopay/crefopay-capture-and-refund-processes.html
  - title: CrefoPay callbacks
    link: docs/scos/dev/technology-partner-guides/page.version/payment-partners/crefopay/crefopay-callbacks.html
  - title: CrefoPay notifications
    link: docs/scos/dev/technology-partner-guides/page.version/payment-partners/crefopay/crefopay-notifications.html
---

CrefoPay module enables B2B strategy in payments.

To enable the B2B business model for CrefoPay:

1. Set `$config[CrefoPayConstants::IS_BUSINESS_TO_BUSINESS]` to true.
2. Add company data into `QuoteTransfer` on project level. It should be done before customer goes to checkout payment step.

**Company Data**

```php
$quoteTransfer->setCrefoPayCompany(
    (new CrefoPayApiCompanyTransfer())
        ->setCompanyName('Company Name')
        ->setCompanyRegisterType('UNKNOWN')
        ->setCompanyRegistrationID('registration-id')
        ->setCompanyTaxID('tax-id')
        ->setCompanyVatID('vat-id')
        ->setEmail('business.email@company.com')
);
```
