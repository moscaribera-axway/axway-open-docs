---
title: API Portal 7.7 May 2023 Release Notes
linkTitle: API Portal May 2023
draft: false
weight: 95
date: 2023-03-20
description: null
---
API Portal updates are cumulative, comprising new features and changes delivered in previous updates unless specifically indicated otherwise in the Release notes.

In this update, we have delivered some performance improvements in the display of data in the user interface, specifically the applications page display when trying to show large volumes of data. We have also completed some security fixes and addressed some defects.

## Installation

API Portal is available as a software installation or a virtualized deployment in a Docker container. For more information, see the following options:

* If you are installing API Portal for the first time using this update, see [Install API Portal](/docs/apim_installation/apiportal_install/).
* If you are already using API Portal (7.5.x, 7.6.x, 7.7.x) and want to install this update, see [Upgrade API Portal](/docs/apim_installation/apiportal_install/upgrade_automatic/).

### Docker containers

* To deploy API Portal in Docker containers, see [Deploy API Portal in containers](/docs/apim_installation/apiportal_docker/docker_portal_upgrade/).
* If you are already using API Portal in Docker containers and you want to install this update, see [Upgrade API Portal in Docker containers](/docs/apim_installation/apiportal_docker/docker_portal_upgrade/).

## Limitations of this update

This update has the following limitations:

* API Portal 7.7.20230228 is compatible with API Gateway and API Manager 7.7.20230228 only.
* To update to API Portal February 2023 (which is based on Joomla 4) from versions older than February 2022, we recommend you to upgrade to API Portal February 2022 first, then follow the [Upgrade API Portal May 2022 or latest](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-to-api-portal-may-2022-or-latest-releases) section.
* Direct update to API Portal February 2023 can be done only from API Portal May 2022 and later by following the [Upgrade from the Joomla! Administrator Interface](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-from-the-joomla-administrator-interface) section.
* This update is not available as a virtual appliance or as a managed service on Axway Cloud.

## New features and enhancements

The following new features and enhancements are available in this update.

### placeholder 1

placeholder

<!-- ticket reference: IAP-5622 -->

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update.

### placeholder 2

placeholder

<!-- IAP-5663 breaking change -->

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

placeholder

### Other fixed issues

placeholder

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
