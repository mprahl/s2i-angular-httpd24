
# S2I Builder for Angular Apps

This repository contains the source for building Angular applications based on
the official httpd24 CentOS and Red Hat images using source-to-image (S2I).

For more information about using these images with OpenShift, please see the
official OpenShift documentation.

## Configuration

The following configuration environment variables are specific to this image:

* **NG_ENVIRONMENT** - the Angular environment to pass to `ng build`. This
    defaults to `prod`.
* **NODE_ENV** - this defaults to `production`.
* **NPM_CONFIG_LOGLEVEL** - this defaults to `info`.

All other environment variables from the base image can also be overriden.

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
