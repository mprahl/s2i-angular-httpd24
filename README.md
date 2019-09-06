
# S2I Builder for Angular Apps

This repository contains the source for building Angular applications based on
the official httpd24 CentOS and Red Hat images using source-to-image (S2I).
This image supports incremental builds by reusing the `node_modules` folder
between builds. To use this, you must enable incremental builds in your build
configuration.

For more information about using these images with OpenShift, please see the
official OpenShift documentation.

## Configuration

The following configuration environment variables are specific to this image:

* **NG_CONFIG** - the Angular configuration to pass to `ng build`. This
    defaults to `production`.
* **NODE_ENV** - this defaults to `production`.
* **NPM_CONFIG_LOGLEVEL** - this defaults to `info`.
* **NPM_MIRROR** - an NPM registry mirror to use instead of the official NPM
    registry. Please note that if the mirror uses a certificate signed by an
    untrusted CA, you'll need to mount the certificate of the untrusted CA in
    the build container, and use the `npm_config_cafile` environment variable
    to point to the mounted CA certificate.
* **DISCARD_NODE_MODULES** - set to `true` to discard the `node_modules`
    directory containing node.js dependencies for smaller container images.
    Otherwise the directory will be retained to allow for incremental builds.

All other environment variables from the base image can also be overriden.

Additionally, you can mount Angular environment files in `/tmp/ng-environments/`
and they will be copied to `./src/environments/`. This is useful if you'd like
to override the environment files stored in source.

## Installation

To build an Angular Docker image, choose either the CentOS or RHEL based image.

### CentOS Based Image

This image is available on DockerHub. To download it, run:

```bash
$ sudo docker pull docker pull mprahl/s2i-angular-httpd24
```

To build your Angular app image with S2I, run:

```bash
$ sudo s2i build <git_url> mprahl/s2i-angular-httpd24 <app_image_name>
```

### RHEL Based Image

This image is not on any public registries, so you must build it from scratch.
To build it, run:

```bash
$ sudo docker build -t mprahl/s2i-angular-httpd24-rhel7 https://raw.githubusercontent.com/mprahl/s2i-angular-httpd24/master/Dockerfile.rhel7
```

To build your Angular app image with S2I, run:

```bash
$ sudo s2i build <git_url> mprahl/s2i-angular-httpd24-rhel7 <app_image_name>
```

## Testing

This repository contains an Angular demo app produced from
`ng new test-app --minimal` in the directory `test/test-app`. This can be used
for testing purposes. To build a Docker image with this demo app, run:

```bash
$ sudo s2i build https://github.com/mprahl/s2i-angular-httpd24 \
    --context-dir=test/test-app/ mprahl/s2i-angular-httpd24 angular-demo-app
$ sudo docker run -p 8080:8080 angular-demo-app
```

You can also run the test script with the following:
```bash
$ sudo docker build -t angular-httpd24-candidate .
$ sudo IMAGE_NAME=angular-httpd24-candidate test/run
```

## OpenShift

To use the S2I image within OpenShift, you must create an image stream
in your project. To do this, run:

```bash
$ oc create -f s2i-angular-httpd24.yml
```

Alternatively, if you'd prefer to have the OpenShift image stream available
globally, you can create the image stream in the `openshift` namespace. To do
this, run:

```bash
$ oc create -f s2i-angular-httpd24.yml -n openshift
```

To view the newly created image stream, run:

```bash
$ oc describe imagestream s2i-angular-httpd24
```

The builder image will now appear as "Angular" under the category "JavaScript"
in the OpenShift UI.
