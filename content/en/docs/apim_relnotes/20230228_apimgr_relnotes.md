---
title: API Gateway and API Manager 7.7 February 2023 Release Notes
linkTitle: API Gateway and API Manager February 2023
weight: 95
date: 2023-01-09
description: null
---
API Gateway and API Manager updates are cumulative, comprising new features and changes delivered in previous updates unless specifically indicated otherwise in the Release notes.

In this update, we have progressed the work started in API Management previous updated (November 2022) around the deployment methodology for containerised deployment, which uses a Helm chart and premade images in order to offer the most simple way to deploy the API Management solution, including the externalisation of the configuration of the API Gateway.

This new development and enhancement will allow customers to deploy and maintain the API Management solution at a lower cost.

In addition, we have made a significant update and restructuring of our [Deploy API Gateway in containers](/docs/apim_installation/apigw_containers/) documentation to reflect these changes; and, a detailed reference for customers wishing to deploy the API Management solution on the Openshift platform has been added to our [Reference Architecture](/docs/apim-reference-architectures/container-openshift/) section.

This update also introduces an upgrade to Cassandra to version 4 of the product, which will deliver stability and performance improvements.

It should be noted that we are working on some upgrades (for example Java), which will take a number of updates to complete, and this work has started during this update cycle.

## Installation

* To **update** your API Gateway, see [Update from API Gateway One Version](/docs/apim_installation/apigw_upgrade/upgrade_steps_oneversion/).
* To **upgrade** from an older version, see [Upgrade from API Gateway 7.5.x or 7.6.x](/docs/apim_installation/apigw_upgrade/upgrade_steps_extcass/).
* For more details on supported platforms for software installation, see [System requirements](/docs/apim_installation/apigtw_install/system_requirements/).
* For a summary of the system requirements for a Docker deployment, see [Set up Docker environment](/docs/apim_installation/apigw_containers/docker_scripts_prereqs/).

### Update a container deployment

Any custom `.fed` files deployed to a container must be upgraded using [upgradeconfig](/docs/apim_installation/apigw_upgrade/upgrade_analytics#upgradeconfig-options) or [projupgrade](/docs/apim_reference/devopstools_ref#projupgrade-command-options). They must be upgraded the same way, regardless of whether they are API Manager enabled or not. The `.fed` files contain the updates for the API Manager configuration and can be used to build containers.

## New features and enhancements

The following new features and enhancements are available in this update.

### Support for Cassandra 4.0.7

API Gateway now supports Apache Cassandra 4.0.7. For details on how to upgrade existing Apache Cassandra environments, see [Upgrade Apache Cassandra](/docs/apim_installation/apigw_upgrade/upgrade_cassandra/upgrade_cassandra_v4/).

{{< alert title="Note" >}}Apache Cassandra version 3.11 onwards will no longer be supported by Apache Cassandra after their end of service (EOS) date, ETA May-July 2023.

Axway will continue to provide support for these versions of Cassandra on a best efforts basis post this date, however, no critical updates will be available from Apache Cassandra from that time. Therefore, we recommend you to update your Cassandra installation to version 4.0.7 before version 3.11 is EOS.

API Gateway and API Manager February 2023 version will remain compatible with Apache Cassandra 3.11, but not with Cassandra 2.2.{{< /alert >}}

### FIPS mode supported in the API Gateway Docker image

FIPS mode can now be used with the API Gateway Docker image. The runtime parameter `EMT_FIPS_MODE` has been added. For more information about FIPS mode, see [Run API Gateway in FIPS mode](/docs/apim_administration/apigtw_admin/admin_fips/).

### API Gateway containerization documentation restructure

With the release of Axway images in the November 2022 release, API Management documentation has now been enhanced to include an outline of containerised deployments for Administrators who are utilizing these images and the Axway Helm chart. For more information, see [Deploy API Gateway in containers](/docs/apim_installation/apigw_containers/).

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update, which may impact your current installation.

### Charset value configured in the Set Message filter is preserved

API Gateway now preserves the configured `charset` property value, with or without quotes, in the `Content-Type` field of the **Set Message** filter. When quotes are present in the `charset` property value, they are preserved, and if quotes are not present, then they are not added by API Gateway.

For backwards compatibility, set `com.axway.apigw.http.contenttype.charset.legacy` Java system property to `true` to always enclose the `charset` property value in quotes for the `Content-Type` header.

For more information, see [Set message filter](/docs/apim_policydev/apigw_polref/conversion_common/#set-message-filter) and [System property changes, 7.7 February 2023](/docs/apim_reference/system_props/#77-february-2023).

### Via header received-protocol version set to the remote peer version

API Gateway now sets the `Via` header received-protocol version to the client request protocol version, that is, the version used by the upstream sender of the message in conformance with [RFC 9110 Section 7.6.3](https://www.rfc-editor.org/rfc/rfc9110#name-via).

For backwards compatibility, set `com.axway.apigw.http.headers.via.legacy` Java system property to `true` to use the remote peer version in the `Via` header as the received-protocol version.

For more information, see [System property changes, 7.7 February 2023](/docs/apim_reference/system_props/#77-february-2023).

### Docker Compose security configuration changes in AAOI

The default Amplify Analytics Operational Insights (AAOI) docker compose files have been updated to add resource limits for both CPU and memory. For more information, see [Configure a production setup for Docker compose](/docs/operational_insights/production_setup/op_insights_setup_prod_docker/).

<!-- RDAPI-28755 -->

### Rate limiter added to unauthenticated APIs in API Manager

The API Manager rate limiter has been improved to now limit unauthenticated sessions for both `/forgotpassword` and `/register` endpoints. For more information, see [Configure API Manager request rate limiter](/docs/apim_administration/apimgr_admin/api_mgmt_config#configure-api-manager-request-rate-limiter).

<!-- RDAPI-28548 -->

### URIs removed from MIME threatening content filter

A defect was fixed where URIs were appearing in the MIME threatening content filter. URIs are now removed.

If your configuration includes a URI in this filter, you must use the new system property `com.axway.apigw.filter.threateningContent.mimeType.ignoreList`. When set, this system property allows you to still use URIs in the threatening content filter.

<!-- RDAPI-26275 -->

### Link to Axway General Terms and Conditions has been updated

The link to the Axway General Terms and Conditions document has been updated. An updated link is now provided upon installation of the product in Policy Studio.

The link is also updated in the Install API Gateway, [Unattended installation](/docs/apim_installation/apigtw_install/installation_unattended#unattended-mode-options) section of our documentation.

### API Gateway SDK libraries updates

API Gateway SDK libraries have been updated as follows:

| Class name | Update |
|------------|--------|
| `com/vordel/mime/FormURLEncodedBody` | Javadoc enhancements |
| `com/vordel/mime/MimeTypeUtils` | Utility class, made `abstract` to avoid unnecessary instantiation |
| `com/vordel/mime/Multipart` | Javadoc enhancements |
| `com/vordel/mime/Body` | Interface and Javadoc enhancements |
| `com/vordel/mime/JSONBody` | Javadoc enhancements |
| `com/vordel/mime/RawBody` | Javadoc enhancements |
| `com/vordel/mime/XMLBody` | Javadoc enhancements |

### Serialization of XML payload

XML bodies will only be serialized when the XML body has been modified. Previously, XML bodies would be re-serialized when they were parsed, and this would result in header changes. A new system property `com.axway.data.serialize.xmlbody.always` has been defined to allow you to switch back to the old behavior. To enable the re-serialization of an XML body, add `<SystemProperty name="com.axway.data.serialize.xmlbody.always" value="true" />` to your jvm.xml file. For more information, see [System property changes, 7.7 February 2023](/docs/apim_reference/system_props/#77-february-2023).

### XMLBody, JSONBody, and RawBody source is now private

Body classes have changed in this release to prevent double buffering of the body source. The contract of these classes have changed, and the scripts that use these classes should be reviewed.

## Deprecated features

As part of our software development life cycle, we constantly review our API Management offering. As part of this update, please see notice of deprecation of the following capabilities.

### Axway PassPort

Axway PassPort is due for end of life (EOL) February 28th 2024. This is notice that we will remove support for use of PassPort with API Gateway from February 2024.

### Sentinel

This is notice that we will remove support for use of Sentinel with with API Gateway from February 2024.

### Client Application Registry

This is notice that we will remove support for use of the Client Application Registry (CAR) from February 2024.

The API Gateway can still act as an OAuth server without CAR, but API Manager will be required when CAR-only mode is no longer available.

## End of support notices

There are no end of support notices in this update.

## Removed features

To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities. As part of this update, the following features have been removed:

<!-- No features have been removed in this update. -->

### Localization in Policy Studio

This is notice that we will remove localization in Policy Studio from February 2024.

## Fixed issues

This version of API Gateway and API Manager includes:

* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [Release note](/docs/apim_relnotes/) for each 7.7 update.
* Additional fixes might be delivered as patches up to 15 months after the release date. You can find the list of patches available on top of this update on [Axway Support](https://support.axway.com/en/search/index/type/Downloads/q/20230228/ipp/100/product/324/product/464/version/3034/version/3035/subtype/8). If no patches were created for this release, the link will return "No search results found".

### Fixed security vulnerabilities

| Internal ID | Case ID                                | Cve Identifier | Description                                                                                                                                                                                                                                                                                     |
| ----------- | -------------------------------------- | -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|RDAPI-27940|01384003||**Issue**: The Try method in API Manager UI renders HTML code passed as a URL parameter. **Resolution**: Add a safe scape before displaying the URL parameter.|
|RDAPI-28516|01408537|CVE-2019-16728|**Issue**: DOMPurify version 1.0.9 has security vulnerabilities. CWE-1104: Use of Unmaintained Third Party Components. **Resolution**: DOMPurify has been updated to its latest version 2.4.1.|
|RDAPI-28548|01404906||**Issue**: An unauthenticated user might send unlimited requests to the /register endpoint of API Manager. **Resolution**: Add the /register endpoint to the unauthenticated request rate limiter, which limits the amount of requests a user can send on that endpoint during a configurable time window.|
|RDAPI-29002|01432899||**Issue**: Password policy is not applicable for backend, a user is able to set weak password bypassing the UI. **Resolution**: The new 'com.axway.apimanager.user.password.regex' system property can be used to configure the regular expression to enforce the user password syntax in API Manager either in the API Manager setup script or the change password API. Defaults to none. When configured, the `<regex>` system property will be enclosed in between `^<regex>$` to match the complete password. |
|RDAPI-29189|01444551|CVE-2023-0286  CVE-2022-4304  CVE-2022-4203  CVE-2023-0215  CVE-2022-4450  CVE-2023-0216  CVE-2023-0217  CVE-2023-0401|**Issue**: OpenSSL 3.0.7 has multiple CVE's published. **Resolution**: Updated to OpenSSL 3.0.8|

### Other fixed issues

| Internal ID | Case ID                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ----------- | ----------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|RDAPI-19217|01138458|**Issue**: Changes to the API Manager Application Developer user page did not trigger an automatic update of the Account Setting page and vice versa.  **Resolution**: When a change is applied to the API Manager Application Developer user page, the Account Settings page is updated automatically and vice versa.|
|RDAPI-20593|01134661|**Issue**: An API Manager front-end API cannot be saved if an Authentication Profile used by an Outbound Per-Method Override is updated or deleted in the UI. **Resolution**: The updates to an Authentication Profile are now propagated to any Outbound Per-Method Override that may be using the profile, allowing the API Manager front-end API to be saved.|
|RDAPI-23500|01140662  01139356|**Issue**: A trial on an organization would not start when a user logged in, if that organization was disabled for API Development. **Resolution**: A trial on an organization now starts when a user logs in for both organizations which are API Development enabled and disabled.|
|RDAPI-24596|01275405  01268346|**Issue**: API Manager runtime fails to forward request bodies with a Content-Type of 'application/x-www-form-urlencoded' or 'multipart/form-data' when the underlying schema of the request body contains 'allOf', 'anyOf' or 'oneOf'. This is due to API Manager not generating form parameters for request bodies containing compound schemas ('allOf', 'anyOf' or 'oneOf'). This only affects runtime requests relating to virtualized OAS3 backend APIs. **Resolution**: API Manager now correctly generates form parameters for request bodies that have a Content-Type of 'application/x-www-form-urlencoded' or 'multipart/form-data' and have an underlying compound schema containing 'allOf', 'anyOf' or 'oneOf'.|
|RDAPI-24766|01265984|**Issue**: APIs assigned to an application could not be sorted by column. **Resolution**: APIs can now be sorted.|
|RDAPI-25689|01318842  01297348|**Issue**: The API Manager queue processing can generate audit log entries with the message "The entity could not be found" but does not give useful information about the process that generated the audit log entry. **Resolution**: The audit log entry metaData attribute is updated with information about the API Manager queue processing that generated the audit log entry with the message "The entity could not be found".|
|RDAPI-25888|01309051|**Issue**: The apimanager-promote script sometimes fails with "404 Not found" error when trying to grant an application access to a newly created API during the promotion process.  **Resolution**: The apimanager-promote script will now wait for the newly created API be published in the API Catalog before continuing the promotion process.|
|RDAPI-26275|01325865|**Issue**: The URI `http://schemas.xmlsoap.org/soap/envelope/` is not a valid  MIME content type. **Resolution**: Remove the content from the list of MIME types.|
|RDAPI-27796|01381709|**Issue**: API Manager REST API v1.4 OAS3 Specification for PUT and POST methods don't show the request body section. **Resolution**: Added the request body annotation for the missing APIs in the API manager specification.|
|RDAPI-28356|01409409  01402848  01403852|**Issue**: When the "polling_interval" property is not defined in file <INSTALL_DIR>/apigateway/webapps/apiportal/vordel/apiportal/app/app.config, the API Manager UI makes multiple calls to /api/portal/v1.4/config (GET/PUT) endpoint. **Resolution**: If the "polling_interval" property is not defined, then API Manager assumes it is disabled with 0.|
|RDAPI-28411|01401559|**Issue**: After upgrading to August 2022 release, an error "AbstractMethodError" is logged when FTP connections are closed. **Resolution**: Apache ftpserver-core has been upgraded to version 1.2.0 and Apache mina-core has been upgraded to version 2.1.6.|
|RDAPI-28529|01409062|**Issue**: Wrong protocol version in Via header updated by API Gateway for non-1.1 remote hosts. **Resolution**: The protocol version in the Via header is now set to the version received in the client request. A new 'com.axway.apigw.http.headers.via.legacy' system property has been added to set the protocol version to the remote peer version as before. Defaults to 'false'.|
|RDAPI-28533|01408704|**Issue**: When Allow HTTP/1.1 is set globally, the first connection to remote host after restart uses HTTP/1.0 **Resolution**: The Allow HTTP/1.1 setting is honored when set either globally or for a remote host for all connections to the remote host.|
|RDAPI-28537|01407108|**Issue**: In Policy Studio, when opening a project that contains broken references, an exception is thrown and no broken references are visible in the Broken Reference View. **Resolution**: Now the exception is handled correctly and broken references are visible in the Broken Reference View.|
|RDAPI-28556|01424043  01410255|**Issue**: An XML body, which was not modified during request operations, was re-serialized and the payload size was different than the one received. **Resolution**: An XML body is re-serialized only in case of modifications applied to the XML body. A new system property "com.axway.data.serialize.xmlbody.always" has been defined to allow customers to switch back to the old behavior. Default value is false. To enable the re-serialization of an XML body, add `<SystemProperty name="com.axway.data.serialize.xmlbody.always" value="true" />` to your jvm.xml file.|
|RDAPI-28560|01410763|**Issue**: In Policy Studio, when attempting to open a project that contains REST APIs, incorrect entity types were selected and an exception was thrown. **Resolution**: Now the incorrect entity types are no longer selected when opening a Policy Studio project that contains REST APIs.|
|RDAPI-28633|01414515|**Issue**: The 'state' of an API was no longer available due to the UI attempting to retrieve the information from the lightweight back-end API endpoint that no longer contained the 'state' of an API, /discovery/apis/light. **Resolution**: Setting the UI to retrieve the 'state' of an API from the front-end API endpoint, /discovery/swagger/apis, resolves the issue, and the 'state' of an API is now visible.|
|RDAPI-28798|01418358|**Issue**: Sending a Multipart/Related MTOM message results in Bad Request error. **Resolution**: Multipart/Related MTOM messages are now correctly processed.|
|RDAPI-28859|01430372  01421190|**Issue**: When using the 'Retrieve attribute from database' filter with a simple SELECT query, the order is not the same as the one displayed. **Resolution**: The filter returns the data respecting the query column order.|
|RDAPI-28914|01425067|**Issue**: Updating the certificate in an existing certificate/private key pair in Policy Studio does not validate that the new certificate matches the existing private key. **Resolution**: Validation will now run in this scenario.|
|RDAPI-28915|01424864|**Issue**: Forms posted to API Manager and filtered through SSO, were failing if they did not have a content type equal to x-www-form-urlencoded. **Resolution**: SSO filter no longer blocks any form posts to API Manager.|
|RDAPI-28923|01423326|**Issue**: API Gateway does not respect the charset value configured in the Set Message filter. **Resolution**: API Gateway now respects the charset value configured in the Set Message filter. For backwards compatibility, If the new 'com.axway.apigw.http.contenttype.charset.legacy' system property is set to 'true' the API Gateway will set the charset value in quotes. Defaults to 'false'.|
|RDAPI-28969|01430125|**Issue**: The help button on the Script filter led to a broken link in the help documentation. **Resolution**: The help button works and the appropriate content is displayed.|
|RDAPI-28996|01432185|**Issue**: Alert filter does not open in Yaml configuration. It cannot be configured after creation. **Resolution**: Alert filter properly opens in Yaml configuration and can be configured after creation.|
|RDAPI-29061|01437533|**Issue**: Docker Images for release built using internal configuration that contains certs and policies specific to test environment, and was not of the same version of the released API Gateway **Resolution**: Docker Images for release are now built using separate "release" configurations that do not contain any internal components and are upgraded to the latest version prior to the release image being built|
|RDAPI-29068|01435756|**Issue**: The apigw-backup-tool does not work on an SSL enabled Cassandra environment. **Resolution**: The apigw-backup-tool now includes configuration parameters to enable SSL support.|
|RDAPI-29184|01444206|**Issue**: KPS encrypted fields can become corrupted when updated via KPS REST API. **Resolution**: KPS REST API updates encrypted fields as expected. An updated object might contain both encrypted field values, which are new values, in clear text, and its original already encrypted values.|

## Known issues

The following are known issues for this update.

### API Analytics PDF reports do not display chart contents

In API Analytics, the PDF reports no longer correctly display the contents of the charts. This issue has arisen due to a security upgrade of the `Highcharts.js` charting library. We are working on the fix of this functionality, to be released in a future update of API Gateway.

Related Issue: RDAPI-27301

### Scripting filter whiteboard attributes not preloaded for Jython scripts

The Scripting filter now uses a Jython 2.7 scripting environment (previously, Jython 2.5) to execute Jython scripts. As a result of this version change, the whiteboard attributes, such as `http.request.uri` and `http.request.verb`, are no longer preloaded for use by Jython scripts. However, you can run a Jython script to load these attributes before they are accessed as follows:

```
from com.vordel.trace import Trace

def invoke(msg):
    msg.forceGenerateAttributes()
    Trace.info("This trace statement was generated in script filter!  [" + str(msg.get("http.request.verb")) + "] [" + str(msg.get("http.request.uri")) + "]")
    return True
```

Related Issue: RDAPI-21363

### Quotes in filter name result in circuit path not being displayed in traffic monitor

In a YAML configuration, when a request is sent through an API Gateway that executes either a policy container or a filter, which names contain a double quotation mark character ("), the resultant transaction cannot be viewed correctly in the API Gateway Manager Traffic Monitor. This issue occurs because the circuit execution path is corrupted due to the presence of the double quotation mark character. The workaround is to load the YAML configuration in Policy Studio, rename the offending filter by removing the quotation marks, and redeploy the configuration.

Note that this issue does not affect runtime filter execution nor when using an XML configuration.

Related Issue: RDAPI-28388

### API Manager runtime does not perform full parameter validation for compound schemas

In API Manager, when you import an OAS definition, which contains a `PathItem` with a content-type of `application/x-www-form-urlencoded` or `multipart/form-data`, and the `content` contains a compound schema of `anyOf` or `oneOf`, the internal model ends up with a list of optional form parameters without a link to the original schema definition. As a result, the API Manager runtime cannot correctly validate inbound requests to the API methods in question because the schema information is missing. This has the potential side-effect of incorrectly allowing certain combinations of form parameters to be sent to the backend service. This is best illustrated with an example.

Consider the following `PathItem` definition:

```json
   "/form-anyOf": {
      "post": {
        "requestBody": {
          "content": {
            "application/x-www-form-urlencoded": {
              "schema": {
                "$ref": "#/components/schemas/TestAnyOf"
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "OK"
          }
        }
      }
    }
  },
```

where the `TestAnyOf` schema is defined as:

```json
      "TestAnyOf": {
        "anyOf": [
          {
            "$ref": "#/components/schemas/Cat"
          },
          {
            "$ref": "#/components/schemas/Dog"
          }
        ]
      },
      "Cat": {
        "additionalProperties": false,
        "type": "object",
        "required": [
          "name"
        ],
        "properties": {
          "name": {
            "type": "string",
            "enum": ["cat","felix"]
          }
        }
      },
      "Dog": {
        "additionalProperties": false,
        "type": "object",
        "required": [
          "age"
        ],
        "properties": {
          "age": {
            "type": "integer"
          },
          "time": {
            "type": "string",
            "format": "date-time"
          }
        }
      }
```

This results in the optional form parameters `name`, `age`, and `time` being generated and stored as part of the internal model for the API method. If the following body is sent as part of a runtime request, parameter validation will succeed and all parameters will be forwarded to the backend service, despite the underlying schema indicating that the request body must contain `anyOf` 'Cat' or 'Dog', but not both.

```json
{code}
name=felix&age=5&time=2022-12-31T22:59:01Z
{code}
```

Related Issue: RDAPI-29098

### Enable Unsafe Legacy Renegotiation on TLS Listeners

Policy Studio contains a setting on Advanced SSL tab of HTTPS Listeners that shows a label error, **NLS Missing Message:...**. This is a UI artifact and does not configure any renegotiation settings for listeners. This option is only relevant for [Connection filters](/docs/apim_policydev/apigw_polref/routing_common/#configure-ssl-settings), where the option is properly labelled as **Enable unsafe legacy renegotiation**.

Related Issue: RDAPI-28853

## Documentation

To find all available documentation for this product version:

1. Go to [Manuals on the Axway Documentation portal](https://docs.axway.com/bundle).
2. In the left pane **Filters** list, select your product or product version.

Customers with active support contracts must log in to access restricted content.

For information on the different operating systems, databases, browsers, and thick client platforms supported by each Axway product, see [Supported Platforms](https://docs.axway.com/bundle/Axway_Products_SupportedPlatforms_allOS_en).

## Support services

The Axway Global Support team provides worldwide 24 x 7 support for customers with active support agreements.

Email [support@axway.com](mailto:support@axway.com) or visit [Axway Support](https://support.axway.com/).

See [Get help with API Gateway](/docs/apim_administration/apigtw_admin/trblshoot_get_help/) for the information that you should be prepared to provide when you contact Axway Support.
