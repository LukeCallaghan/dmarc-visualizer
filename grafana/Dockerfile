FROM grafana/grafana:11.6.0

USER root

ADD --chown=grafana:root Grafana-DMARC_Reports.json /var/lib/grafana/dashboards/

## Paths
ENV GF_PATHS_PROVISIONING="/etc/grafana/provisioning"
ENV GF_PATHS_PLUGINS="/var/lib/grafana/plugins"

##################################################################
## Customization depends on the Grafana version
## May work or not work for the version different from the current
## Check GitHub file history for the previous Grafana versions
##################################################################

##################################################################
## Copy Artifacts
## Required for the App plugin
##################################################################

COPY grafana-provisioning/ /etc/grafana/provisioning/
# First set base permissions for directories
RUN find /etc/grafana/provisioning/ -type d -exec chmod 755 {} \;
# Then set permissions for files
RUN find /etc/grafana/provisioning/ -type f -exec chmod 644 {} \;
# Set ownership
RUN chown -R grafana:root /etc/grafana/provisioning/

##################################################################
## VISUAL
##################################################################

## Replace Favicon and Apple Touch
COPY images/favicon-32x32.png /usr/share/grafana/public/img
COPY images/apple-touch-icon.png /usr/share/grafana/public/img/apple-touch-icon.png

## Replace Logo
COPY images/logo.svg /usr/share/grafana/public/img/grafana_icon.svg

## Update Background
COPY images/background.svg /usr/share/grafana/public/img/g8_login_dark.svg
COPY images/background.svg /usr/share/grafana/public/img/g8_login_light.svg

##################################################################
## HANDS-ON
##################################################################

# Update Title
RUN sed -i 's|<title>\[\[.AppTitle\]\]</title>|<title>DMARC-Visualizer</title>|g' /usr/share/grafana/public/views/index.html
RUN sed -i 's|Loading Grafana|Loading DMARC-Visualizer|g' /usr/share/grafana/public/views/index.html


RUN find /usr/share/grafana/public/build/ -name *.js \
## Update Title
    -exec sed -i 's|AppTitle="Grafana"|AppTitle="DMARC-Visualizer"|g' {} \; \
## Update Login Title
    -exec sed -i 's|LoginTitle="Welcome to Grafana"|LoginTitle="Welcome to DMARC-Visualizer"|g' {} \; \
## Remove Documentation, Support, Community in the Footer
    -exec sed -i 's|\[{target:"_blank",id:"documentation".*grafana_footer"}\]|\[\]|g' {} \; \
## Remove Edition in the Footer
    -exec sed -i 's|({target:"_blank",id:"license",.*licenseUrl})|()|g' {} \; \
## Remove Version in the Footer
    -exec sed -i 's|({target:"_blank",id:"version",text:..versionString,url:D?"https://github.com/grafana/grafana/blob/main/CHANGELOG.md":void 0})|()|g' {} \; \
## Remove News icon
    -exec sed -i 's|(0,t.jsx)(...,{className:ge,onClick:.*,iconOnly:!0,icon:"rss","aria-label":"News"})|null|g' {} \; \
## Remove Old Dashboard page icon
    -exec sed -i 's|(0,t.jsx)(u.I,{tooltip:"Switch to old dashboard page",icon:"apps",onClick:()=>{s.Ny.partial({scenes:!1})}},"view-in-old-dashboard-button")|null|g' {} \; \
## Remove Open Source icon
    -exec sed -i 's|.push({target:"_blank",id:"version",text:`${..edition}${.}`,url:..licenseUrl,icon:"external-link-alt"})||g' {} \;

##################################################################
## CLEANING Remove Native Data Sources
##################################################################

##################################################################
## CLEANING
## Remove Native Data Sources
##################################################################

RUN rm -rf /usr/share/grafana/public/app/plugins/datasource/graphite /usr/share/grafana/public/build/graphite* \
    /usr/share/grafana/public/app/plugins/datasource/opentsdb /usr/share/grafana/public/build/opentsdb* \
    /usr/share/grafana/public/app/plugins/datasource/influxdb /usr/share/grafana/public/build/influxdb* \
    /usr/share/grafana/public/app/plugins/datasource/mssql /usr/share/grafana/public/build/mssql* \
    /usr/share/grafana/public/app/plugins/datasource/mysql /usr/share/grafana/public/build/mysql* \
    /usr/share/grafana/public/app/plugins/datasource/tempo /usr/share/grafana/public/build/tempo* \
    /usr/share/grafana/public/app/plugins/datasource/jaeger /usr/share/grafana/public/build/jaeger* \
    /usr/share/grafana/public/app/plugins/datasource/zipkin /usr/share/grafana/public/build/zipkin* \
    /usr/share/grafana/public/app/plugins/datasource/azuremonitor /usr/share/grafana/public/build/azureMonitor* \
    /usr/share/grafana/public/app/plugins/datasource/cloudwatch /usr/share/grafana/public/build/cloudwatch* \
    /usr/share/grafana/public/app/plugins/datasource/cloud-monitoring /usr/share/grafana/public/build/cloudMonitoring* \
    /usr/share/grafana/public/app/plugins/datasource/parca /usr/share/grafana/public/build/parca* \
    /usr/share/grafana/public/app/plugins/datasource/phlare /usr/share/grafana/public/build/phlare* \
    /usr/share/grafana/public/app/plugins/datasource/grafana-pyroscope-datasource /usr/share/grafana/public/build/pyroscope*

## Remove Cloud and Enterprise categories
RUN find /usr/share/grafana/public/build/ -name *.js \
    -exec sed -i 's|.id==="enterprise"|.id==="notanenterprise"|g' {} \; \
    -exec sed -i 's|.id==="cloud"|.id==="notacloud"|g' {} \;

##################################################################
## CLEANING Remove Native Panels
##################################################################

RUN rm -rf /usr/share/grafana/public/app/plugins/panel/alertlist \
    /usr/share/grafana/public/app/plugins/panel/annolist \
    /usr/share/grafana/public/app/plugins/panel/dashlist \
    /usr/share/grafana/public/app/plugins/panel/news \
    /usr/share/grafana/public/app/plugins/panel/geomap \
    /usr/share/grafana/public/app/plugins/panel/table-old \
    /usr/share/grafana/public/app/plugins/panel/traces \
    /usr/share/grafana/public/app/plugins/panel/flamegraph

##################################################################

USER grafana

EXPOSE 3000