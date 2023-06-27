# Docker

Most engineering projects will be heavily dependant on docker for local development and testing

## Useful Links

- [Docker Hub](/handbook/3rd_party_tools/docker_hub.md)
- [Useful links from internetdevels](https://internetdevels.com/blog/docker-useful-links)
- [Docker cheat sheet](https://i.pinimg.com/736x/22/c5/38/22c538a9973d65ea7eba77ef3102d5c5.jpg)

## Microservices

The engineering team manages a large amount of microservices.  To do this, we embrace the use of docker by ensuring that all software created can build and be stored in docker images.  

## Local Testing

Many of our services rely on third party tools, such a databases and message queues.  It is expected that you build those into your docker-compose settings when creating new environments.  A common pattern we see in many services is to build and service docker images of the minimum required infrastructure to be able to test your software manually, and via proper code testing