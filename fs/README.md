# Rucio - Scientific Data Management

## (RucioFS Container)

Rucio is a software framework that provides functionality to organize, manage, and access large volumes of scientific data using customisable policies. The data can be spread across globally distributed locations and across heterogeneous data centers, uniting different storage and network technologies as a single federated entity. Rucio offers advanced features such as distributed data recovery or adaptive replication, and is highly scalable, modular, and extensible. Rucio has been originally developed to meet the requirements of the high-energy physics experiment ATLAS, and is continuously extended to support LHC experiments and other diverse scientific communities.

## Documentation

General information and latest documentation about Rucio can be found at [readthedocs](https://rucio.readthedocs.io) or on our [webpage](https://rucio.cern.ch).

## Developers

For information on how to contribute to Rucio, please refer and follow our [CONTRIBUTING](<https://github.com/rucio/rucio/blob/master/CONTRIBUTING.rst>) guidelines.

## Getting Started

This image provides the basic RucioFS mount option that can be used to connect with multiple Rucio servers. If you need to upload/download DIDs please check the experiment specific containers.

To run this container the Rucio server and authentication hosts and the credentials need to be configured. There are two ways to do it: either by using environment variables or by mounting a rucio.cfg file.

Starting the container using the environment with userpass authentication:

    docker run \
      -e RUCIO_CFG_RUCIO_HOST=https://server.rucio:443 \
      -e RUCIO_CFG_AUTH_HOST=https://auth.rucio:443 \
      -e RUCIO_CFG_AUTH_TYPE=userpass \
      -e RUCIO_CFG_USERNAME=ddmlab \
      -e RUCIO_CFG_PASSWORD=secret \
      -e RUCIO_CFG_ACCOUNT=root \
      --cap-add SYS_ADMIN --device /dev/fuse \
      --name=rucio-fs \
      -it -d rucio/rucio-fs

With X509 authentication:

    docker run \
      -e RUCIO_CFG_RUCIO_HOST=https://server.rucio:443 \
      -e RUCIO_CFG_AUTH_HOST=https://auth.rucio:443 \
      -e RUCIO_CFG_AUTH_TYPE=x509 \
      -e RUCIO_CFG_CLIENT_CERT=/opt/rucio/etc/usercert.pem \
      -e RUCIO_CFG_CLIENT_KEY=/opt/rucio/etc/userkey.pem \
      -e RUCIO_CFG_ACCOUNT=root \
      -v /opt/rucio/etc/usercert.pem:/opt/rucio/etc/usercert.pem \
      -v /opt/rucio/etc/userkey.pem:/opt/rucio/etc/userkey.pem \
      --cap-add SYS_ADMIN --device /dev/fuse \
      --name=rucio-fs \
      -it -d rucio/rucio-fs

Using host's proxy:

    docker run \
      -e RUCIO_CFG_RUCIO_HOST=https://server.rucio:443 \
      -e RUCIO_CFG_AUTH_HOST=https://auth.rucio:443 \
      -e RUCIO_CFG_AUTH_TYPE=x509 \
      -e RUCIO_CFG_CLIENT_CERT=/tmp/x509up_u0 \
      -e RUCIO_CFG_CLIENT_KEY=/tmp/x509up_u0 \
      -e RUCIO_CFG_ACCOUNT=root \
      -v $(X509_USER_PROXY):/tmp/x509up_u0
      --cap-add SYS_ADMIN --device /dev/fuse \
      --name=rucio-fs \
      -it -d rucio/rucio-fs

Allowing ruciofs access from host requires adding the following docker run argument:

    --mount type=bind,source=/ruciofs,target=/ruciofs,bind-propagation=rshared

If you already have one or more rucio.cfg files you can also use those all together:

    docker run \
      -v /tmp/rucio.cfg:/ruciofs-settings/rucio.cfg \
      [-v /tmp/rucio1.cfg:/ruciofs-settings/rucio1.cfg] \
      --cap-add SYS_ADMIN --device /dev/fuse \
      --name=rucio-fs \
      -it -d rucio/rucio-fs

In case your RSE require x509 auth we recommend installing the GRID CA certificates.

## `RUCIO_CFG` configuration parameters:

Environment variables can be used to set values for the auto-generated rucio.cfg. The names are derived from the actual names in the configuration file.
The available environment variables are:

* `RUCIO_CFG_ACCOUNT`
* `RUCIO_CFG_AUTH_HOST`
* `RUCIO_CFG_AUTH_TYPE`
* `RUCIO_CFG_CA_CERT`
* `RUCIO_CFG_CLIENT_CERT`
* `RUCIO_CFG_CLIENT_KEY`
* `RUCIO_CFG_CLIENT_VO`
* `RUCIO_CFG_CLIENT_X509_PROXY`
* `RUCIO_CFG_COMMON_MULTI_VO`
* `RUCIO_CFG_PASSWORD`
* `RUCIO_CFG_POLICY_LFN2PFN_ALGORITHM_DEFAULT`
* `RUCIO_CFG_POLICY_PACKAGE`
* `RUCIO_CFG_POLICY_PERMISSION`
* `RUCIO_CFG_POLICY_SCHEMA`
* `RUCIO_CFG_POLICY_SUPPORT`
* `RUCIO_CFG_POLICY_SUPPORT_RUCIO`
* `RUCIO_CFG_REQUEST_RETRIES`
* `RUCIO_CFG_RUCIO_HOST`
* `RUCIO_CFG_USERNAME`

## Getting Support

If you are looking for support, please contact our mailing list rucio-users@googlegroups.com
or join us on our [slack support](<https://rucio.slack.com/messages/#support>) channel.
