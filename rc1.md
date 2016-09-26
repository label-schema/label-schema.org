---
layout: page
permalink: /rc1/
---

<h1 style="font-weight: 800">Label Schema Specification DRAFT (1.0.0-rc.1)</h1>

* Canonical URL: `http://label-schema.org/rc1/`
* Version: 1.0.0-rc.1
* Status: DRAFT
* Maintainers:
  * Liz Rice <liz@microscaling.com>
  * Michael Hausenblas <michael.hausenblas@dcos.io>
  * Gareth Rushgrove <gareth@morethanseven.net>
  * Adrian Mouat <adrian.mouat@container-solutions.com>

* TOC
{:toc}

### A shared namespace for container labels

[Docker Inc. express a preference](https://docs.docker.com/engine/userguide/labels-custom-metadata/) that container labels should be namespaced. Label Schema is a community project to provide a shared namespace for use by multiple tools, specifically **org.label-schema**.

By providing a shared and community owned namespace we aim to:

* Avoid duplication in cases where the same information is needed in multiple labels

* Encourage the use of labels, both by image creators and by tool builders which might consume them

* Codify good community practice in a way that is easy to consume and keep up-to-date with

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

Label Schema labels MUST comply with Docker’s guidelines: 

* Keys should only consist of lowercase alphanumeric characters, dots and dashes (for example, [a-z0-9-.]).

* Keys should start and end with an alphanumeric character.

* Keys may not contain consecutive dots or dashes.

Where applicable we suggest compatibility with the [generic labels suggested by Project Atomic](https://github.com/projectatomic/ContainerApplicationGenericLabels), except that rather than namespacing to a domain controlled by the author we suggest using the shared **org.label-schema** namespace, so that multiple tools can take advantage of a common understanding of the meaning of the label.

In addition all values for Label Schema compatible labels MUST use UTF-8 encoding.

### Label semantics

All labels are OPTIONAL, although `org.label-schema.schema-version` SHOULD be present. 

Consumers should not make assumptions based on the absence of one of these labels. For example, the absence of a `vcs-url` label means that a tool using this label schema does not know the URL for this image’s source code. From the absence of the label one can not infere there is no such source.

Labels apply only to the image on which they are set. They do not apply to any base images used to build the image.

In general a label has a single value. Where a list of values is meaningful, it is indicated in the table below, and the values are given as a comma-separated list. 

Where a label is semantically meaningful to a tool that claims compatibility with the spec, it MUST support the key name and value defined in this specification. 

### Build-time labels

These are immutable, and can only have one sensible meaning that is defined at build time. 

All labels are OPTIONAL, however if present MUST be prefixed with the namespace `org.label-schema`.

| Label | Example | Meaning |
|-------|---------|---------|
| `build-date` | `org.label-schema.build-date="2016-04-12T23:20:50.52Z"` | This label contains the Date/Time the image was built. The value SHOULD be formatted according to [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| `name` | `org.label-schema.name = "myname"` | A human friendly name for the image. For example, this could be the name of a microservice in a microservice architecture. |
| `description` | `org.label-schema.description = "This service does awesome things with other things"` | Text description of the image. May contain up to 300 characters. |
| `usage` | `org.label-schema.usage= "/usr/doc/app-usage.txt"` | Link to a file in the container or alternatively a URL that provides usage instructions. If a URL is given it SHOULD be specific to this version of the image e.g. `http://docs.example.com/v1.2/usage` rather than `http://docs.example.com/usage` |
| `url` | `org.label-schema.url="http://postgresql.org"` | URL of website with more information about the product or service provided by the container. |
| `vcs-url` | `org.label-schema.vcs-url = "https://github.com/nginx/nginx"` | URL for the source code under version control from which this container image was built. |
| `vcs-ref` | `org.label-schema.vcs-ref = "279FA63"` | Identifier for the version of the source code from which this image was built. For example if the version control system is git this is the SHA. |
| `vendor` | `org.label-schema.vendor = "Stark Industries"` | The organization that produces this image. |
| `version` | `org.label-schema.version = "1.2.3"` `org.label-schema.version = "Beta4.2"` `org.label-schema.version = "1.2.2-dirty"` `org.label-schema.version = "my-test"` | Release identifier for the contents of the image. This is entirely up to the user and could be a numeric version number like 1.2.3, or a text label.<br> The version MAY match a label or tag in the source code repository.<br> You should make sure that only images that exactly reflect a version of code should have that version label. If Julia finds a version 0.7.1 in a repository she SHOULD be able to infer this matches version 0.7.1 of the associated code (and in particular, not 0.7.1 plus some later commits).<br> You SHOULD omit the version label, or use a marker like "dirty" or "test" to indicate when a container image does not match a labelled / tagged version of the code. |
| `schema-version` | `org.label-schema.schema-version = "1.0"` | This label SHOULD be present to indicate the version of Label Schema in use. |
| `docker.cmd` | `org.label-schema.docker.cmd= "docker run -d -p 5000:5000 -v config.json:/etc/config.json myapp"` | How to run a container based on the image under the Docker runtime. |
| `docker.cmd.devel` | `org.label-schema.docker.cmd.devel = "docker run -d -p 5050:5050 -e ENV=DEV myapp"` | How to run the container in development mode under the Docker runtime e.g. with debug tooling or more verbose output. |
| `docker.cmd.test` | `org.label-schema.docker.cmd.test = "docker run myapp runtests"` | How to run the bundled test-suite for the image under the Docker runtime. MUST execute tests then exit, returning output on stdout and exit with a non-zero exit code on failure. |
| `docker.cmd.debug` | `org.label-schema.docker.debug-shell = "docker exec -it $CONTAINER /bin/redis-cli"` | How to get an appropriate interactive shell for debugging on the container under Docker. |
| `docker.cmd.help` | `org.label-schema.docker.cmd.help = "docker exec -it $CONTAINER /bin/app --help"` | How to output help from the image under the docker runtime. The container MUST print this information to stdout and then exit. |
| `docker.params` | `org.label-schema.docker.param = "NO_THREADS=integer number of threads to launch"` | Applicable environment variables for the Docker runtime. Multiple environment variables can be specified by separating with commas. |
| `rkt.cmd` | `org.label-schema.rkt.cmd= "rkt run --port=5000-tcp:5000 myapp.aci"` | How to run a container based on the image under the rkt runtime. |
| `rkt.cmd.devel` | `org.label-schema.rkt.cmd.devel = "rkt run --port=5000-tcp:5000 --set-env=ENV=DEV myapp.aci"` | How to run the container in development mode under the rkt runtime e.g. with debug tooling or more verbose output. |
| `rkt.cmd.test` | `org.label-schema.rkt.cmd.test = "rkt run --port=5000-tcp:5000 myapp.aci -- runtests"` | How to run the bundled test-suite for the image under the rkt runtime. MUST execute tests then exit, returning output on stdout and exit with a non-zero exit code on failure. |
| `rkt.cmd.debug` | `org.label-schema.rkt.debug-shell = "rkt enter $CONTAINER --app=/bin/redis-cli"` | How to get an appropriate interactive shell for debugging on the container under rkt. |
| `rkt.cmd.help` | `org.label-schema.rkt.cmd.help = "rkt enter $CONTAINER --app=/bin/help"` | How to output help from the image under the rkt runtime. The container MUST print this information to stdout and then exit. |
| `rkt.params` | `org.label-schema.rkt.param = "NO_THREADS=integer number of threads to launch"` | Applicable environment variables for the rkt runtime. Multiple environment variables can be specified by separating with commas. |


### Background

Many tools need some information about a container image, or about the consumer's use of that container image. Metadata built into container images can enable even more powerful container management tooling. 

By agreeing on a standardized set of labels, everyone can use tools from different vendors without the need to add vendor-specific labels to all their images, especially where those labels carry the same information or serve the the same purpose. Duplication leads to the risk of error. 

Examples of the kind of information commonly found in image labels today are:

* What source code a container was built from

* Version number 

* Information on how to run the code within the container

* Where to find additional information about the container

* Who to contact if this container causes issues in production

* Test status of this container

In some cases the information is immutable for the container image, and can (and even should) be defined at build time - for example the version number and source code. Building this  additional information into the container image acts like a ‘shipping manifest’ to describe the contents of the container image. 

In other cases it is only possible or desirable to define labels after the container is built - for example labels indicating whether the container has been tested, or who is the current support engineer for a deployed container. We describe this as ‘run time’ information, though in some cases (like test status) it is useful even when a container is not currently running. 

Many tools and orchestration systems already support the use of container labels or annotations. This spec defines the semantics of a set of standardized labels so that ecosystem tools can have a shared understanding of them. The intention is for this specification to be fully compatible with existing container runtime specifications and implementations.

Here are a small number of examples illustrating cases where it’s useful for different tool vendors to support the same label semantics:

* A continuous integration tool automatically add labels for source code and version number during the build process. An orchestration system’s UI could use the same labels to show which versions of code are running, and a logging tool could automatically add versioning information to its output. If the tools use the standard label definition, the user doesn’t need to do anything to see information about the version of code that is running.

* Monitoring / alarm systems can use standardized labels for identifying who to contact in the event of a critical error being generated from within a given container. 

* Visualization and monitoring tools can use container metadata to display useful information in human-readable form, such as what code is running within a deployment, and how the containers are related. If different tools use a standard schema, users don’t need to add different labels whenever they want to experiment with a new tool, or if they want to use multiple tools in parallel. 

This is just the tip of the iceberg; the key concept is that if tool vendors support a common set of semantics it will be much easier for users to add and maintain metadata that those tools might utilize. 

