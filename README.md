# EAP7 with JSF 1.2 for running on OpenShift

See [Configuring the JBoss EAP for OpenShift Image For Your Java Application](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/getting_started_with_jboss_eap_for_openshift_online/configuring_eap_openshift_image) in the Red Hat documentation.

This repository contains configuration of EAP 7.2 with JSF 1.2 enabled which can be used for running applications on OpenShift.

The directory `eap/modules` contains the custom modules for JSF 1.2 built following:
- the Red Hat documentation: https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/javaserver_faces_jsf_configuration 
- the knowledge base solution https://access.redhat.com/solutions/2773121
The name of the slot is `mojarra-1.2`.

The directory `eap/configuration` contains the file `standalone-openshift.xml` which is exactly the same as the default `standalone.xml` with the only change:

```
<subsystem xmlns="urn:jboss:domain:jsf:1.1" default-jsf-impl-slot="mojarra-1.2"/>
```

## Use image for binary builds

To build a custom EAP image with the provided configuration run:

```
oc new-build \
  openshift/jboss-eap72-openshift:1.0~https://github.com/mayorova/eap7-with-jsf12-on-openshift \
  --name custom-eap-image \
  --context-dir eap
```
Then the resulting image can be used to create binary builds, for example:
```
oc new-build custom-eap-image --binary=true --name=<app-name>

oc start-build <app-name> --from-file=<app-name>.ear --wait
```

## Use Git source for deployment

You can also place the artifact `<app-name>.ear` to `eap/deployments` (and check it into the repository), and deploy the application directly using the `eap72-basic-s2i` template, for example:

```
oc new-app --template=eap72-basic-s2i \
 -p SOURCE_REPOSITORY_URL=https://github.com/mayorova/eap7-with-jsf12-on-openshift \
 -p SOURCE_REPOSITORY_REF=master \
 -p CONTEXT_DIR=eap \
 -e ADMIN_USERNAME=<user> \
 -e ADMIN_PASSWORD=<password>
```
