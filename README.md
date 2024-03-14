# DMARC Visualizer

Visualize DMARC results using open-source tools.

* [parsedmarc](https://github.com/domainaware/parsedmarc) for parsing DMARC reports,
* [Elasticsearch](https://www.elastic.co/) to store aggregated data.
* [Grafana](https://grafana.com/) to visualize the aggregated reports.

See the full blog post with the original instructions at [Debricked](https://debricked.com/blog/2020/05/14/analyse-and-visualize-dmarc-results-using-open-source-tools/).

## Updates from original repository

* parsedmarc
  * Running from python 3.11
  * Built in the geoip functions with cron running updates.
  * Runs in verbose mode by default.
  * Delayed start by waiting for Elasticsearch to report service healthy.

* Grafana
  * Updated to recent release version - 10.1.5 (Upgrades to 10.2.x are causing errors that are yet to be resolved).
  * Uses a docker volume for data persistence.
  * Delayed start by waiting for Elasticsearch to report service healthy.
  * grafana-piechart-panel plugin is deprecated so has been removed and panel converted to the new builtin Piechart.
  * grafana-worldmap-panel plugin is deprecated so has been removed and panel converted to the new builtin Geomap.

* Elasticsearch
  * Updated to current release version - 8.11.0.
  * Uses a docker volume for data persistence.
  * Runs health check to report service healthy, forcing other containers to wait until initialised.

* Other
  * Containers are networked with manual IP addressing.

## KNOWN ISSUES

* Some of the Dockerfile steps to add customisations to the Grafana interface break the build process when using Synology versions of Docker (called Container Manager). I have not been able to replicate this issue on macOS or Ubuntu using the same versions of Docker, and I do not have Synology to test on. To resolve this you will need to comment out lines 27-29, 72-77 and 89-91 in `grafana/Dockerfile`. (Issue reported, and workaround found, by supaeasy @ [#1](https://github.com/LukeCallaghan/dmarc-visualizer/issues/1#issuecomment-1872215466), cheers!)

## Instructions

```shell
git clone https://github.com/LukeCallaghan/dmarc-visualizer.git
cd dmarc-visualizer
docker-compose build
docker-compose up -d
```

The containers will start and can take a while to get running.

Visit `http://localhost:3000/` in your browser. Default username and password is admin. This can be edited and will persist.

## Configuration

Configuration options are available for each of the services.

### parsedmarc

parsedmarc can be configured in the parsedmarc.ini file. There are common options included in the file already.

For a full list of options and information about their use, please visit the [config section of the parsedmarc documentation](https://domainaware.github.io/parsedmarc/usage.html#configuration-file)

#### Geo IP

To enable Geo IP you will need to complete the requirements outlined in the [geoipupdate section of the parsedmarc documentation](https://domainaware.github.io/parsedmarc/installation.html#geoipupdate-setup)

Once you have your license key file, you can update the GeoIP.conf file with your license details and remove the comments so the geoip functionality will work.

### Elasticseach

If you are getting errors in your dashboard regarding memory issues, then you need to increase the memory available to the Elasticsearch instance. In the docker-compose file, you can change the line below to increase the memory:

`- "ES_JAVA_OPTS=-Xms512m -Xms512m"`

For example, to use 1Gb of memory you would use:

`- "ES_JAVA_OPTS=-Xms1024m -Xms1024m"`

### Grafana

Customisation of the Grafana interface is done by following the guide from [Volkov Labs](https://volkovlabs.io/blog/how-to-customize-the-grafana-user-interface-8d70a42dc2b6/)

## Screenshot

![Screenshot of Grafana dashboard](/big_screenshot.png)
