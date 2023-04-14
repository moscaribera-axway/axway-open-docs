---
title: API Portal 7.7 February 2023 Release Notes
linkTitle: API Portal February 2023
draft: false
weight: 95
date: 2023-01-09
description: null
---
API Portal updates are cumulative, comprising new features and changes delivered in previous updates unless specifically indicated otherwise in the Release notes.

In this update, we have delivered some performance improvements in the display of data in the user interface, specifically the applications page display when trying to show large volumes of data. We have also completed some security fixes and addressed some defects.

## Installation

API Portal is available as a software installation or a virtualized deployment in a Docker container. For more information, see the following options:

* If you are installing API Portal for the first time using this update, see [Install API Portal](/docs/apim_installation/apiportal_install/).
* If you are already using API Portal (7.5.x, 7.6.x, 7.7.x) and want to install this update, see [Upgrade API Portal](/docs/apim_installation/apiportal_install/upgrade_automatic/).

### Docker containers

* To deploy API Portal in Docker containers, see [Deploy API Portal in containers](/docs/apim_installation/apiportal_docker/).
* If you are already using API Portal in Docker containers and you want to install this update, see [Upgrade API Portal in Docker containers](/docs/apim_installation/apiportal_docker/docker_portal_upgrade/).

## New features and enhancements

The following new features and enhancements are available in this update.

### Performance improvements

Significant improvements in the response times of the applications catalog and application details pages to load in under 3 seconds.
Various code optimizations were implemented to achieve these response times.
<!-- ticket reference: IAP-5622 -->

### Security improvements

Various security fixes were implemented to improve compliance with internal security scanning and resolution of docker image CVEs.
<!-- ticket reference: IAP-5700 -->

## Limitations of this update

This update has the following limitations:

* API Portal 7.7.20230228 is compatible with API Gateway and API Manager 7.7.20230228 only.
* To update to API Portal February 2023 (which is based on Joomla 4) from versions older than February 2022, we recommend you to upgrade to API Portal February 2022 first, then follow the [Upgrade API Portal May 2022 or latest](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-to-api-portal-may-2022-or-latest-releases) section.
* Direct update to API Portal February 2023 can be done only from API Portal May 2022 and later by following the [Upgrade from the Joomla! Administrator Interface](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-from-the-joomla-administrator-interface) section.
* This update is not available as a virtual appliance or as a managed service on Axway Cloud.

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update.

### New settings available for conditionally switching to single-column layout if the volume of endpoints are larger than the default

To improve performance when viewing APIs that have a large number of Swagger endpoints, we have added a configuration option in the Joomla! Administrator Interface (JAI) to allow you to define a maximum number of endpoints. When this maximum value is exceeded, the layout is automatically switched to **Single-column** as this view performs better with large volumes of endpoints. For more information, see [Customizing the API catalog and details views](/docs/apim_administration/apiportal_admin/customize_apicatalog_overview).

<!-- IAP-5663 breaking change -->

### New **AuthToMaster** policy is available, which removes warnings previously seen on import

Previously, importing the `AuthoToMasterDynamicOrg.xml` policy (which is shipped with the API Portal install package into Policy Studio to configure multiple API managers) generated several warnings. From this release, an upgraded **AuthToMaster** policy is shipped with the install and upgrade packages for API Portal, which fixes the issue that caused these warnings.

{{< alert title="Note" color="primary" >}}To avail of this fix, you must replace the policy in Policy Studio with the updated **AuthToMaster** policy from this release.{{< /alert >}}

If the policy is not updated, the warnings will continue to be observed. For more information, see Connect API Portal to API Manager, [Import and configure the policies](/docs/apim_installation/apiportal_install/connect_to_apimgr#import-and-configure-the-policies).

<!-- IAP-5873 breaking change -->

### The **Created By** field in Application catalog and details pages is changing in this release

A schema change has been made to add the `Creator Name` property inside the index in Elasticsearch. In order for this property to be populated, the index in Elasticsearch must first be reindexed.

{{< alert title="Note" color="primary" >}}Reindexing can be initiated automatically the next time the cron job is scheduled to run, but we recommend you to manually reindex immediately after updating your product to avoid any data discrepancies.{{< /alert >}}

For more information, see [Configure a schedule to push data to Elasticsearch](/docs/apim_installation/apiportal_install/install_software_elastic#configure-a-schedule-to-push-data-to-Elasticsearch).

<!-- IAP-5946 breaking change -->

## Deprecated features

<!-- As part of our software development life cycle, we constantly review our API Portal offering. As part of this update, the following capabilities have been deprecated. -->

No features have been deprecated in this update.

## End of support notices

There are no end of support notices in this update

## Removed features

<!-- To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities. As part of this review, the following features have been removed. -->

No features have been removed in this update.

## Fixed issues

This version of API Portal includes:

* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [release note](/docs/apim_relnotes/) for each 7.7 update.
* Additional fixes might be delivered as patches up to 15 months after the release date. You can find the list of patches available on top of this update on [Axway Support](https://support.axway.com/en/search/index/type/Downloads/q/20230228/ipp/100/product/545/version/3036/subtype/8). If no patches were created for this release, the link will return "No search results found".

### Fixed security vulnerabilities

| Internal ID  | Case ID | CVE Identifier | Description                                                                                                                                                                                        |
|--------------|---------|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| IAP-5758     | 1423037 |                | **Issue**: XSS vulnerability on sign-in page. **Resolution**: XSS vulnerability was mitigated on sign-in page.                                                                                     |
| IAP-5835     |         |                | **Issue**: Python 3, CURL, SQLite, and Setuptools packages had security vulnerabilities. <br/>**Resolution**: Python 3, CURL, SQLite, and Setuptools were upgraded and are not vulnerable anymore. |
| IAP-5019     |         |                | **Issue**: cron related fields in Elasticsearch configuration page in JAI are vulnerable to command injection. **Resolution**: User input from cron configuration fields are sanitized.            |
| IAP-5836     |         | CVE-2022-23494 | **Issue**: Joomla uses TinyMCE version 5.10.5, which is vulnerable to XSS. **Resolution**: TinyMCE was upgraded to version 5.10.7, which is not vulnerable to XSS.                                 |
| IAP-5653     |         |                | **Issue**: Redirect URL validation doesn't handle properly some scenarios. **Resolution**: Redirect URL validation improved to handle all  scenarios .                                              |

### Other fixed issues

| Internal ID | Case ID | Description                                                                                                                                                                                                                                                 |
|-------------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| IAP-5606    |         | **Issue**: API Portal ships with database-related errors on JAI dashboard. **Resolution**: There are no such errors when installing and upgrading API Portal anymore.                                                                                               |
| IAP-5663    | 1325688 | **Issue**: The browser crashes when an API has too many Swagger endpoints and two-pane layout activated. **Resolution**: An user configuration was added in JAI to define a number of endpoints that when exceeded, the single-pane layout will be rendered. |
| IAP-5827    | 1432895 | **Issue**: API Portal was sending cookie attributes in requests, which caused API Manager to fail when parsing reserved attributes as cookies. **Resolution**: API Portal now uses only the cookie name/value pair and no longer includes cookie attributes in requests.            |

## Known issues

The following are known issues for this update.

<!-- know issues below were added during February 23 release -->

### CSP is not applied in new UI scenario

The Content Security Policy (CSP) sets a policy that instructs the browser to only fetch resources from specified locations. The implementation details of the T4 PageBuilder component prevents the CSP to work, which might cause security concerns because there is no validation whether the browser is allowed to load the resource from a particular location. These are usually Javascript, CSS, and image resources.

Related Issue: IAP-5970

### Edit profile menu item cannot be edited

Users cannot edit the **Profile** menu item because, on save, it shows an error for a missing required field, the **Menu item type**. There is no specific type for this menu item, and it should not be edited.

Related Issue: IAP-5710

### Non-SSO users cannot login if the primary API Manager is configured to use SSO

When API Portal is configured to work with multiple API Managers and the *primary* API Manager is configured to use SSO, a new non-SSO user cannot be automatically created on the *secondary* API Manager on first login because the *primary* API Manager throws an error.

Related Issue: IAP-5869

### List of APIs is not correct on Application create page

This issue is observed only when Elasticsearch is enabled.

When a user that belongs to multiple organizations is creating an application, a drop-down list with the user's organizations is loaded. Selecting a different organization from the drop-down list changes the results in the list of APIs available for subscription for this application. When Elasticsearch is enabled, this list does not show the APIs from the chosen organization.

Related Issue: IAP-5670

### Application View and Edit pages are not accessible from API Details page

This issue is observed only when Elasticsearch is enabled.

When Elasticsearch is enabled, users cannot access the Application View and Edit pages from **API Details page > Application subscription** tab.

Related Issue: IAP-5964

### Organization Administrator is not able to share an application with other users

This issue is observed only when Elasticsearch is enabled.

An Organization Administrator is not able to share or unshare an application with other users when Elasticsearch is enabled.

Related Issue: IAP-5951

<!-- know issues below were preexistent to February 23 release -->

### Data Request feature is not available for SSO users without email in New UI page builder design

[Data Request](/docs/apim_administration/apiportal_admin/manage_privacy_personal_data#manage-data-requests) feature is not able to send request for SSO users without email only for New UI page builder design - T4 and Page Builder. The functionality is available, but the emails are automatically detected from Joomla login details, while for SSO users registered without emails this information is not available and the request generation is not able to proceed. For users upgrading from versions earlier than November 22 and still using T3 and Purity III, the stylization for API Portal Data Request functionality works as before.

Related Issue: IAP-5649

### Two-factor authentication is not available with Joomla 4.2.x in API Portal

Joomla 4.2.0 includes changes around two(multi)-factor authentications. However, this functionality is not yet available in API Portal. For more information, see [Joomla 4.2 release notes](https://www.joomla.org/announcements/release-news/5865-joomla-4-2-release.html).

Related issue: IAP-5504

### Custom properties permissions per role are not respected in some scenarios

Custom properties permissions per role will not be respected for the organization in the following scenarios:

* Permissions will not be respected for the organization the API is assigned to because respecting them will cause significant performance impact on the Try-it page. The custom properties permissions will follow the role of the user's primary organization.
* On creating an application, the App has no organization yet, so the custom properties permissions cannot be respected per role of the organization. They will follow the permissions for the role of the primary organization of the user.

Related issue: IAP-4474

### Page layout and alignment for Arabic language

Changing the API Portal language to Arabic (or any other right to left language) results in issues with page layout and alignment on the API Portal Home and Pricing pages, and some buttons are not visible. As a workaround, you can turn on the development mode in JAI. Follow these steps:

1. Log in to Joomla! Admin Interface (JAI).
2. In the JAI top navigation bar, click **Extensions > Templates**.
3. Click your template style (for example, `purity_III * Default`) to open it.
4. Click the **General** tab.
5. Change **Development Mode** to `ON`.
6. Click **Save** and click **Close** to close the template style.

Related issue: IAP-308

## Documentation

To find all available documentation for this product version:

1. Go to [Manuals on the Axway Documentation portal](https://docs.axway.com/bundle).
2. In the left pane **Filters** list, select your product or product version.

Customers with active support contracts need to log in to access restricted content.

For information on the different operating systems, databases, browsers, and thick client platforms supported by each Axway product, see [Supported Platforms](https://docs.axway.com/bundle/Axway_Products_SupportedPlatforms_allOS_en).

## Support services

The Axway Global Support team provides worldwide 24 x 7 support for customers with active support agreements.

Email [support@axway.com](mailto:support@axway.com) or visit [Axway Support](https://support.axway.com/).

See [Get help with API Gateway](/docs/apim_administration/apigtw_admin/trblshoot_get_help/) for the information that you should be prepared to provide when you contact Axway Support.
