{
    "title": "Deploy API Gateway with Axway docker images",
    "linkTitle": "Deploy API Gateway with Axway docker images",
    "weight": "30",
    "date": "2022-02-01",
    "description": "This how-to guide describes a high-level introduction to deploy the API Gateway with mounted volumes utilizing Axway images or custom-built images. It describes where to download the images and sample configuration files from, and how to configure an API Gateway, Admin Node Manager, or Analytics docker container to use a persisted volume for customized configuration."
}

The primary documentation for [API Gateway in containers](/docs/apim_installation/apigw_containers) outlines the creation of images and their deployment as containers. Another deployment methodology is to download Axway images from [Axway repository](http://repository.axway.com/), then deploy the same images by updating the configuration using externalization in both development and production environments.

The downloaded Admin Node Manager, API Gateway, and Analytics images are built with a default configuration, which can be updated at runtime by externalizing the customized configuration files.

The API Gateway image is built with the YAML configuration by default but it can be updated with *.fed, or vice-versa, by externalizing the deployment package (fed or YAML) as outlined in [Sample container configurations](/docs/apim_howto_guides/configuring_apigw_container#sample-container-configurations).

You can also deploy API Gateway using a helm chart. For more information see, [Deploy API Gateway using Helm](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment/).

The following sections describe how to deploy API Gateway using Axway images.

## Download Axway docker images

Download Axway docker images (Admin Node Manager, API Gateway, and API Gateway Analytics) from [Axway repository](http://repository.axway.com/). The example tag for images looks like `7.7.0.20221130-1-ubi7`.

## Run API Gateway images using docker volumes

This section describes how during the creation of a container, you can use Docker volumes with a persisted store to update the configuration within the container. The main advantage of using Docker volumes is to allow you to update your configuration without having to rebake the Docker image.

The major steps to create and configure your API Gateway or Admin Node Manager in containers with Docker volumes are:

1. Mount the new configuration.
2. Start the docker container.
3. Verify the container configuration.

This process applies to API Gateway or Admin Node Manager configurations only, such as policy management, system property changes, environment settings, logging configurations, and son on.

### Mount the configuration

Docker volumes facilitate mounting configuration files to the Docker container so that they are available to the system at runtime. These configuration files are stored in the host file system, which can be across any hardware or cloud service offering the client is utilizing. See [API Gateway configuration files](/docs/apim_reference/config_files_reference/) for a full list of configurable files.

The expected mounted directory (mount point location) structure is as follows:

| Docker volume mount | Description |
| ------------------- | ----------- |
| /merge/fed | For a policy configuration stored as a deployment package (.fed file).|
| /merge/yaml | For YAML based API Gateway policy configuration stored either as a deployment package (.tar.gz file) or as a directory. This option is applicable for API Gateway only.|
| /merge/apigateway | For all other API Gateway or Admin Node Manager configurations, for example, jvm.xml, envSettings.props, and so on. |
| /merge/mandatoryFiles | For the verification of mandatory configuration files. |
| /merge/analytics | For all Analytics configurations, for example, jvm.xml, envSettings.props, and so on. |

### Start the container

After you create a persisted store, you must add the Docker volume configuration to the Docker `run` command to start the container with the new runtime configuration. For example:

```bash
# API Gateway
docker run -d --name=apimgr --network=api-gateway-domain \
           -p 8075:8075 -p 8065:8065 -p 8080:8080 \
           -v /home/user/apigw/fed/newFed.fed:/merge/fed \
           -v /home/user/apigw/mandatoryFiles.yaml:/merge/mandatoryFiles \
           -v /home/user/apigw/apigateway:/merge/apigateway \
           -e ACCEPT_GENERAL_CONDITIONS=yes -e EMT_ANM_HOSTS=anm:8090 -e CASS_HOST=casshost1 \
           -e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false -e METRICS_DB_USERNAME=db_user1 -e METRICS_DB_PASS=my_db_pwd \
           api-gateway-my-group:1.0

# Admin Node Manager
docker run -d -p 8090:8090 --name=anm --network=api-gateway-domain \
           -v /home/user/anm/emt-nm.fed:/merge/fed \
           -v /home/user/anm/mandatoryFiles.yaml:/merge/mandatoryFiles \
           -v /home/user/anm/apigateway:/merge/apigateway \
           -e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false -e METRICS_DB_USERNAME=db_user1 -e METRICS_DB_PASS=my_db_pwd \
           -e ACCEPT_GENERAL_CONDITIONS=yes admin-node-manager:0.0.1

# Analytics
docker run -d -p 8040:8040 --name=analytics --network=api-gateway-domain \
           -v /home/user/aga/analytics:/merge/analytics \
           -e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false -e METRICS_DB_USERNAME=db_user1 -e METRICS_DB_PASS=my_db_pwd \
           -e ACCEPT_GENERAL_CONDITIONS=yes analytics:0.0.1
```

{{< alert title="Note" >}}By default, the Axway docker images do not contain the `mysql jar`. To run the API Gateway Analytics successfully, the MySQL *jar must be mounted in the `/merge/analytics` mount point.{{< /alert >}}

After the container is running, the files mounted within the Docker volumes are copied to the file system within the running container in its `/merge` directory. For example:

```bash
.
├── merge
│   ├── fed
│   ├──apigateway
│   │  └── groups
│   │      └── emt-group
│   │          └── emt-service
│   │              └── conf
│   │                  ├── envSettings.props
│   │                  └── jvm.xml
│   └── mandatoryFiles
└── opt
    └── Axway
        └── apigateway
```

The original factory configuration in the `/opt/Axway/apigateway` directory is then replaced with the hosted configuration in the `/merge` directory, and the new configuration is reflected in the running instance.

### Verify the configuration

Docker containers use the configuration specified during the image creation process by default. Only files that require to be configured again in the container need to be mounted.

To verify that reconfigured files, mounted on Docker volumes, have been successfully found by the Docker container at runtime, you must add the `mandatoryFiles.yaml` configuration file to the `/merge/mandatoryFiles` Docker volume. This file lists the external configuration files, which must be mounted to the `/merge` directory in the running container. For example:

```bash
required:
    - /merge/apigateway/groups/emt-group/emt-service/conf/envSettings.props
    - /merge/apigateway/groups/emt-group/emt-service/conf/jvm.xml
    - /merge/apigateway/system/conf/log4j2.yaml2
    - /merge/fed
```

If a file listed in the `mandatoryFiles.yaml` is not available to the file system of the container, the container will fail to start and it will log an appropriate error message, which can be seen by using the `docker logs <container name>` command.

## Sample container configurations

This section provides examples of how to configure an API Gateway, Admin Node Manager, or Analytics Docker container for different configuration types.

### Get the sample configuration files

The sample configuration files can be retrieved from the running container. To get the sample files:

1. Copy the sample configuration you wish to update from a running container on to your local machine. For example, Admin Node Manager `envSettings.props` file is available at `apigateway/conf/envSettings.props`.
2. Use `docker cp` to copy files to your local machine.
3. Update the file and mount it using docker volume.

### Policy configuration

Policy configuration stored as a deployment package (.fed file) can be added to the Docker container runtime configuration by adding the `fed` file to the `/merge/fed` Docker volume:

```bash
# API Gateway
docker run -d --name=apimgr --network=api-gateway-domain \
           -p 8075:8075 -p 8065:8065 -p 8080:8080 \
           -v /home/user/apigw/fed/newFed.fed:/merge/fed \
           -e ACCEPT_GENERAL_CONDITIONS=yes -e EMT_ANM_HOSTS=anm:8090 -e CASS_HOST=casshost1 \
           -e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false -e METRICS_DB_USERNAME=db_user1 -e METRICS_DB_PASS=my_db_pwd \
           api-gateway-my-group:1.0

# Admin Node Manager
docker run -d -p 8090:8090 --name=anm --network=api-gateway-domain \
           -v /home/user/anm/emt-nm.fed:/merge/fed \
           -e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false -e METRICS_DB_USERNAME=db_user1 -e METRICS_DB_PASS=my_db_pwd \
           -e ACCEPT_GENERAL_CONDITIONS=yes admin-node-manager:0.0.1
```

### YAML Entity Store configuration

YAML-based API Gateway policy configuration can be stored either as a deployment package (.tar.gz file) or as a directory. The following examples show how to add them to the Docker container runtime configuration.

{{< alert title="Note" >}}YAML-based policy configuration is only applicable for API Gateway.{{< /alert >}}

#### Deployment package

For configuration stored in a deployment package (.tar.gz file), mount the deployment package to the `/merge/yaml` Docker volume:

```bash
docker run -d --name=apimgr --network=api-gateway-domain \
           -p 8075:8075 -p 8065:8065 -p 8080:8080 \
           -v /home/user/apigw/yaml.tar.gz:/merge/yaml \
           -e ACCEPT_GENERAL_CONDITIONS=yes -e EMT_ANM_HOSTS=anm:8090 -e CASS_HOST=casshost1 \
           -e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false -e METRICS_DB_USERNAME=db_user1 -e METRICS_DB_PASS=my_db_pwd \
           api-gateway-my-group:1.0
```

#### Directory

For configuration stored in a directory, mount the source directory to the `/merge/yaml` Docker volume:

```bash
docker run -d --name=apimgr --network=api-gateway-domain \
           -p 8075:8075 -p 8065:8065 -p 8080:8080 \
           -v /home/user/apigw/yaml:/merge/yaml \
           -e ACCEPT_GENERAL_CONDITIONS=yes -e EMT_ANM_HOSTS=anm:8090 -e CASS_HOST=casshost1 \
           -e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false -e METRICS_DB_USERNAME=db_user1 -e METRICS_DB_PASS=my_db_pwd \
           api-gateway-my-group:1.0
```

### All other configuration

All other configuration, such as `jvm.xml` and `envSettings.props`, can be added to the Docker container runtime configuration by way of the `/merge/apigateway` or `/merge/analytics` Docker volumes. You must store these configurations locally, in a directory structure which mirrors `/opt/Axway/apigateway` or `/opt/Axway/analytics` subfolder structure inside the Docker container. For example:

```bash
apigateway
├── groups
│   └── emt-group
│       └── emt-service
│           └── conf
│               ├── envSettings.props
│               └── jvm.xml
└── system
    └── conf
        └── log4j2.yaml
```

This folder structure is then mounted to the `/merge/apigateway` or `/merge/analytics` directory of the container.

After that, you can start API Gateway, Admin Node Manager, and Analytics Docker container as follows:

```bash
# API Gateway
docker run -d --name=apimgr --network=api-gateway-domain \
           -p 8075:8075 -p 8065:8065 -p 8080:8080 \
           -v /home/user/apigw/apigateway:/merge/apigateway \
           -e ACCEPT_GENERAL_CONDITIONS=yes -e EMT_ANM_HOSTS=anm:8090 -e CASS_HOST=casshost1 \
           -e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false -e METRICS_DB_USERNAME=db_user1 -e METRICS_DB_PASS=my_db_pwd \
           api-gateway-my-group:1.0

# Admin Node Manager
docker run -d -p 8090:8090 --name=anm --network=api-gateway-domain \
           -v /home/user/anm/apigateway:/merge/apigateway \
           -e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false -e METRICS_DB_USERNAME=db_user1 -e METRICS_DB_PASS=my_db_pwd \
           -e ACCEPT_GENERAL_CONDITIONS=yes admin-node-manager:0.0.1

# Analytics
docker run -d -p 8040:8040 --name=analytics --network=api-gateway-domain \
           -v /home/user/aga/analytics:/merge/analytics \
           -e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false -e METRICS_DB_USERNAME=db_user1 -e METRICS_DB_PASS=my_db_pwd \
           -e ACCEPT_GENERAL_CONDITIONS=yes analytics:0.0.1
```

### Update domain certificates at runtime

By default, Axway images contain the default domain certs. To update the domain certs, like other configurations such as jvm.xml, envSettings.props, and so on, domain certs can be added to the Docker container at runtime by way of the `/merge/apigateway` Docker volume. You must store these configurations locally, in a directory structure which mirrors `/opt/Axway/apigateway` subfolder structure inside the Docker container.

```bash
apigateway
├── groups
   └── certs
       └── domaincert.pem
       └── private
           └── domainkey.pem
```

This folder structure is then mounted to the `/merge/apigateway` directory of the container.

{{< alert title="Note" >}}When updating the domain certs at runtime, the ES_PASSPHRASE and DOMAIN_KEY_PASSPHRASE must be provided if present, otherwise it takes the default passphrases.{{< /alert >}}

During this process the Admin Node Manager (ANM) and API Gateway topology SSL certs get created and signed by the domain certificates provided at runtime. To secure the communication between the ANM and API Gateways, both the SSL certificates must be signed with the same domain certificate, otherwise there will not be any communication.

### Set Group ID at runtime

Group ID can be set at runtime as an environment variable. All the containers in the group are built with same image, which means they contain the same configuration. For example:

```bash
docker run -d --name=apimgr --network=api-gateway-domain \
           -p 8075:8075 -p 8065:8065 -p 8080:8080 \
           -v /tmp/emt/events:/opt/Axway/apigateway/events \
           -e EMT_DEPLOYMENT_ENABLED=true -e EMT_ANM_HOSTS=anm:8090 \
           -e CASS_HOST=casshost1 \
           -e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false \
           -e METRICS_DB_USERNAME=root -e METRICS_DB_PASS=root01 \
           -e ACCEPT_GENERAL_CONDITIONS=yes \
           -e GROUP_ID=CustomEmtGroup \
           api-gateway-defaultgroup
```

## Further Information

For more information on how to build API Gateway, Admin Node Manager, or Analytics Docker images and run in a Docker container, see [Deploy API Gateway in containers](/docs/apim_installation/apigw_containers).
