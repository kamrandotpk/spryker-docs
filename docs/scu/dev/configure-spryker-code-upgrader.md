---
title: Configure Spryker Code Upgrader
description: Instructions for configuration of Spryker Code Upgrader
template: concept-topic-template
last_updated: Aug 15, 2023
redirect_from:
  - /docs/paas-plus/dev/configure-spryker-code-upgrader.html
---

The [Spryker Code Upgrader](/docs/scu/dev/spryker-code-upgrader.html) offers customizable options to tailor the upgrading process according to your specific project requirements. 
The customization can be done with the help of available predefined variables and the "Change Upgrader config" pipeline.

To change the values of the variables, do the following:

1. In [Spryker CI](/docs/scu/dev/spryker-ci.html), go to **Projects**.
2. On the **Projects** page, select **Spryker Code Upgrader**.

![Spryker CI Projects](https://spryker.s3.eu-central-1.amazonaws.com/docs/paas%2B/dev/onboard-to-spryker-code-upgrader/connect-spryker-code-upgrader-to-a-github-managed-project.md/spryker_ci_projects.png)

3. On the **Pipelines** page, run the **Change Upgrader config** pipeline by clicking **Run** button next to the **Change Upgrader config** pipeline.

![Spryker CI Config Upgrader Pipeline](https://spryker.s3.eu-central-1.amazonaws.com/docs/paas%2B/dev/configure-spryker-code-upgrader.md/config-upgrader-variables.png)

4. On the **Run** page, click the **Run now** pipeline.

![Spryker CI Run Config Upgrader Pipeline](https://spryker.s3.eu-central-1.amazonaws.com/docs/paas%2B/dev/configure-spryker-code-upgrader.md/set-upgrader-variables-run-now.png)

5. On the **Run** page, set the new values for variables and click the **Proceed** button.

![Spryker CI Set Config Upgrader Pipeline](https://spryker.s3.eu-central-1.amazonaws.com/docs/paas%2B/dev/configure-spryker-code-upgrader.md/set-spryker-code-upgrader-variables.png)

## Variables

| VARIABLE              | DESCRIPTION                                                                                                                                                                                                                                                                                          | DEFAULT VALUE |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------|
| CRON_STRING                | Lets you define a cron schedule expression, which dictates the frequency and timing of when the Spryker Code Upgrader pipeline is automatically executed.                                                                                                                     | 0 8 * * MON       |
| MAX_ALLOWED_MINOR_VERSIONS | Lets you define the maximum number of installed *minor* versions of Spryker modules. Note that this serves as a flexible threshold, and there are scenarios where the actual count of installed minor Spryker module versions might exceed the value specified here. | 10                |
| MAX_ALLOWED_PATCH_VERSIONS | Lets you define the maximum number of installed *patch* versions of Spryker modules. Note that this serves as a flexible threshold, and there are scenarios where the actual count of installed patch Spryker module versions might exceed the value specified here. | 30                |

## Support for Spryker CI

* For help with Spryker CI, [contact support](https://spryker.force.com/support/s/).
* To learn more about Buddy, see their [docs](https://buddy.works/docs).
