---
title: API Gateway and API Manager 7.7 May 2023 Release Notes
linkTitle: API Gateway and API Manager May 2023
weight: 95
date: 2023-03-20
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

### placeholder 1

placeholder 1

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update, which may impact your current installation.

### placeholder 2

placeholder 2

<!-- RDAPI-28755 -->

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

### Sun Access Manager has been retired

Sun Access Manager has been retired in this release. This includes the following Sun Access Manager filters:

* Authorization
* Log out session
* Retrieve attributes
* SSO Token Validation
* X.509 Certificate Authentication
  
These filters will no longer be available to use when creating policies in Policy Studio, so we strongly recommend that you replace them with the [Oracle Access Manager](/docs/apim_policydev/apigw_polref/connector_oam/) filters, or remove them entirely.

This retirement also includes the following:

* The **Access Manager** settings, found in **Server Settings > Security**.
* The **Sun Access Manager** Repositories, found in **External Connections > Authentication Repositories**. Sun Access Manager repositories can be replaced by Oracle Access Manager repositories.

Existing configurations that use these filters and settings will cause a critical warning when upgraded in Policy Studio or with an upgrade tool such as `projupgrade`.

After this repository is removed, you will still be able to deploy upgraded configuration containing Sun Access Manager; and, updated gateways will continue to run existing deployed configuration, but as a consequence, a warning will be logged in API Gateway trace files and the deploy will always return a fail response.

### Localization in Policy Studio

This is notice that we will remove localization in Policy Studio from February 2024.

## Fixed issues

This version of API Gateway and API Manager includes:

* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [Release note](/docs/apim_relnotes/) for each 7.7 update.
* Additional fixes might be delivered as patches up to 15 months after the release date. You can find the list of patches available on top of this update on [Axway Support](https://support.axway.com/en/search/index/type/Downloads/q/20230228/ipp/100/product/324/product/464/version/3034/version/3035/subtype/8). If no patches were created for this release, the link will return "No search results found".

### Fixed security vulnerabilities

placeholder

### Other fixed issues

placeholder

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
