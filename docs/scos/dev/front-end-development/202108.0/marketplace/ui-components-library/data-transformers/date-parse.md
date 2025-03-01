---
title: Data Transformer Date-parse
description: This document provides details about the Data Transformer Date-parse service in the Components Library.
template: concept-topic-template
related:
  - title: Data Transformers
    link: docs/scos/dev/front-end-development/page.version/marketplace/ui-components-library/data-transformers/data-transformers.html
  - title: Data Transformer Array-map
    link: docs/scos/dev/front-end-development/page.version/marketplace/ui-components-library/data-transformers/data-transformer-array-map.html
  - title: Data Transformer Chain
    link: docs/scos/dev/front-end-development/page.version/marketplace/ui-components-library/data-transformers/data-transformer-chain.html
  - title: Data Transformer Date-serialize
    link: docs/scos/dev/front-end-development/page.version/marketplace/ui-components-library/data-transformers/data-transformer-date-serialize.html
  - title: Data Transformer Lens
    link: docs/scos/dev/front-end-development/page.version/marketplace/ui-components-library/data-transformers/data-transformer-lens.html
  - title: Data Transformer Object-map
    link: docs/scos/dev/front-end-development/page.version/marketplace/ui-components-library/data-transformers/data-transformer-object-map.html
  - title: Data Transformer Pluck
    link: docs/scos/dev/front-end-development/page.version/marketplace/ui-components-library/data-transformers/data-transformer-pluck.html
---

This document explains the Data Transformer Date-parse service in the Components Library.

## Overview

Data Transformer Date-parse is an Angular Service that parses the string value as a Date ISO into the JS Date Object.

In the following example, the `datasource` transforms the `date` string into the parsed `date` object.

```html
<spy-select
    [datasource]="{
        type: 'inline',
        data: '2020-09-24T15:20:08+02:00',
        transform: {
            type: 'date-parse'
        },
    }"
>
</spy-select>
```

## Service registration

Register the service:

```ts
declare module '@spryker/data-transformer' {
    interface DataTransformerRegistry {
        'date-parse': DateParseDataTransformerConfig;
    }
}

@NgModule({
    imports: [
        DataTransformerModule.withTransformers({
            'date-parse': DateParseDataTransformerService,
        }),
    ],
})
export class RootModule {}
```

## Interfaces

Below you can find interfaces for the Data Transformer Date-parse:

```ts
export interface DateParseDataTransformerConfig extends DataTransformerConfig {}
```
