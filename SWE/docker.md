# Docker

Docker is a platform to build, package, and run applications inside containers. It fixes the "works on my machine" problem caused by inconsistent environments. It solves for environment differences, deployment complexity, scalability and isolation. This is achieved by packaging the app and its dependencies (system/runtime/program) into an image and running the image as a container with its own filesystem, network, and process space. Containers share the host OS kernel, making them lightweight compared to VMs.

### You need it when these hurt:

* “Works on my machine” → Docker kills this. Same environment everywhere. Period.
* Setup time → New laptop? New teammate? docker compose up and you’re running in minutes.
* Dependency hell → Conflicting versions? Docker isolates them. No global mess.
* Prod ≠ dev → Docker lets you run the same stack locally, in CI, and in prod.
* Microservices / multiple services → App + DB + cache + queue? One command, all wired.
* CI/CD → Deterministic builds. No flaky pipelines because “the runner was weird today”.
* Rollback safety → Ship images, not snowflake servers. Roll back by switching versions.

## Image

A container image is a standardized package that includes all of the files, binaries, libraries, and configurations to run a container.

## Container

A container is a runnable instance of an image.

* **Self-contained**: Each container has everything it needs to function with no reliance on any pre-installed dependencies on the host machine.
* **Isolated**: Since containers run in isolation, they have minimal influence on the host and other containers, increasing the security of your applications.
* **Independent**: Each container is independently managed. Deleting one container won't affect any others.
* **Portable**: Containers can run anywhere! The container that runs on your development machine will work the same way in a data center or anywhere in the cloud!
