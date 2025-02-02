<div align="center">
<img alt="Docker Image Version (tag)" src="https://img.shields.io/docker/v/maxldwg/snort/latest?style=for-the-badge&logo=docker&label=Latest%20Version&link=https%3A%2F%2Fhub.docker.com%2Fr%2Fmaxldwg%2Fbicep-snort">
<img alt="Docker Pulls" src="https://img.shields.io/docker/pulls/maxldwg/snort?style=for-the-badge&logo=docker&logoColor=blue&link=https%3A%2F%2Fhub.docker.com%2Fr%2Fmaxldwg%2Fbicep-snort">
<img alt="Dynamic JSON Badge" src="https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fapi.github.com%2Frepos%2Fmaldwg%2Fsnort-docker-image%2Factions%2Fworkflows%2Fon_schedule.yaml%2Fruns%3Fstatus%3Dcompleted%26per_page%3D1&query=workflow_runs%5B0%5D.run_started_at&style=for-the-badge&label=Last%20Pipeline%20Run">
</div>

# snort-docker-image

This project offers an automatically updated docker image of snort.
The installation is conducted as stated by the official documentation.
Automated pipelines take care so that the most recent available snort version is containerized and available on dockerhub

## Usage

The image starts a container using no special command. In order to use the snort functionalities properly, start a container with the respective snort command, as documented in the official [snort documentation](https://docs.snort.org/start/installation). 


It is recommended to use the container in host network mode to allow it to run in promiscuous mode. 

```
docker run -it --name snort \
                --network host \
                --cap-add NET_ADMIN \
                maxldwg/snort "snort --help"
```