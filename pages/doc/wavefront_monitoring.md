---
title: Monitor Your Wavefront Service with the Wavefront Usage Integration
keywords: administration
tags: [administration, dashboards]
sidebar: doc_sidebar
permalink: wavefront_monitoring.html
summary: Monitor and troubleshoot your Wavefront instance and see points per second information.
---

Tanzu Observability by Wavefront includes the Wavefront Usage integration. Use can use the Wavefront Usage integration dashboards to:
* Get usage information for your Wavefront instance and Wavefront proxy.
* Drill down into the metrics namespaces to discover trends.
* Examine the points per second (PPS) based on predefined ingestion policies.
* See whether the ingested metrics are at 95% of committed rate. Optionally, get alerts if that happens.

In addition, you can create your own dashboards, charts, and alerts by using the [internal metrics](wavefront-internal-metrics.html) to investigate a problem.

See [Monitoring Wavefront Proxies](monitoring_proxies.html) for details on investigating proxy usage.

## Learn About Your Usage with Dashboards

The Wavefront Usage integration includes the following dashboards:

<table style="width: 100%;">
<tbody>
<thead>
<tr><th width="30%">Dashboard</th><th width="30%">Focus</th><th width="40%">Description</th></tr>
</thead>
<tr>
<td><strong>Wavefront Service and Proxy Data</strong></td>
<td>Examine <strong>usage data</strong>.</td>
<td>Provides visibility into your use of the Wavefront service via internal metrics that Tanzu Observability collects automatically. Preconfigured charts monitor the data ingestion rate for points, spans, and distributions, the data scan rate, and different proxy metrics.</td></tr>
<tr>
<td><strong>Wavefront Namespace Usage Explorer</strong></td>
<td>Explore metrics namespaces to see the <strong>trend</strong> of your metrics ingestion rate.</td>
<td>Tracks the number of time series metrics received for the first 3 levels of your metric namespace. Also tracks the breakdown of histograms, spans, and delta counters.</td></tr>
<tr>
<td><strong>Wavefront Ingestion Policy Explorer</strong></td>
<td>In environments where ingestion policies are defined, investigate usage for <strong>each account and ingestion policy</strong>.</td>
<td markdown="span">Provides a granular breakdown of the ingestion across your organization by [ingestion policies](ingestion_policies.html), accounts, sources, and types. Use this dashboard to identify who is contributing the most to your usage and manage your overall usage of the Wavefront service.</td></tr>
<tr>
<td><strong>Committed Rate vs Monthly Usage (PPS P95) for Billable</strong></td>
<td>Avoid <strong>exceeding the monthly commitment</strong> for your instance by exploring dashboards and creating alerts.
</td>
<td><strong>Important:</strong> Use only if you have a billable commit contract with Tanzu Observability.
<p>Provides a detailed breakdown of your monthly usage against your monthly commitment. Enables you to take appropriate action when usage reaches around 95% of your monthly commitment.</p></td></tr>
<tr>
<td><strong>Usage (PPS) vs Remaining Balance (PPS P95) for Burndown</strong></td>
<td>Avoid <strong>exceeding the burndown commitment</strong> for your instance by exploring dashboards and creating alerts.
</td>
<td><strong>Important:</strong> Use only if you have a burndown commit contract with Tanzu Observability.
<p>Provides details about your usage against your remaining burndown balance and a breakdown of your usage per billing period. Enables you to take appropriate action when usage reaches around 95% of your burndown commitment.</p></td></tr>
</tbody>
</table>


### Wavefront Service and Proxy Data Dashboard

The **Wavefront Service and Proxy Data** dashboard helps you find reasons for system slowdown.

The charts show internal metrics information and allow you to examine many aspects of your Wavefront instance. See [Monitoring Wavefront Proxies](monitoring_proxies.html) for details on the **Proxy Overview** and **Proxy Troubleshooting** sections.


#### Overall Data Rate

The **Overall Data Rate** section shows the overall point rate being processed by the Wavefront servers.

![Overall data rate section. Contains charts for data ingestion rate per points, spans and distributions, as well as data scan rate charts.](images/overall_section.png)

These charts use the following metrics:

- **Data Ingestion Rate**
   - `~collector.points.reported` -- Points coming from the proxy.
   - `~collector.direct-ingestion.points.reported` -- Points coming through direct ingestion.
   - `~collector.delta_points.reported` -- Delta counter points.
   - `~externalservices.<*>.points` -- Per-second rate at which cloud integrations, such as AWS, GCP, and Azure, ingest new points.

   For example, use `~externalservices.ec2.points` for the EC2 points.
   - `externalservices.points.reported` -- Shows how you get billed for external services.

- **Data Scan Rate**
  - `~query.metrics_scanned` -- The per-second rate at which metrics are being queried through dashboards, custom charts, derived metrics, or API calls.
  - `~query.spans_scanned` -- The per-second rate at which spans are being queried through dashboards, custom charts, or API calls.
  - `~query.histograms_scanned` -- The per-second rate at which histograms are being queried through dashboards, custom charts, derived metrics, or API calls.

#### Logs Stats

The **Logs Stats** section contains charts that track the amount of logs that are successfully delivered and successfully queried by the Wavefront service. Also, the section shows charts that track the amount of logs that are received, queued, and blocked by the Wavefront proxy.

{% include important.html content="Tanzu Observability Logs (Beta) is enabled only for selected customers. To participate, contact your Tanzu Observability account representative."%}

These charts use the following metrics:

- `~agent.logs.*.delivered` -- Number of log bytes successfully delivered.
- `~wavefront.logservice.api.bytesQueried.total.bytes` -- Number of log bytes successfully queried.
- `~proxy.logs.*.received.bytes` -- Number of log bytes received by the proxy.
- `~agent.logs.*.received.max-burst-rate` -- Maximum burst rate of incoming logs.
- `~proxy.buffer.logs-count` -- Number of delayed log bytes stored on disk. Logs can be queued for the following reasons:
    - Intermittent failures in communication with the backend.
    - A surge of incoming data in excess of thread buffer size.
    - Memory pressure in the proxy.
    - Rate limiting.
- `~proxy.logs.*.blocked` -- Number of log objects blocked by the preprocessor.
- `~proxy.logs.*.queued` -- Number of queued log bytes.

#### Wavefront Stats and Alert Stats

Charts that track the number of users during various time windows, the number of dashboards and alerts, and also provide information about the state and types of alerts.

These charts use the following metrics:

- `~wavefront.alerts.*` -- Count, states, and types of the alerts.
- `~wavefront.dashboard.*`  -- Metrics related to the number of dashboard views.
- `~wavefront.dashboards.*` -- Total count of the dashboards, number of the custom dashboards, and number of deleted dashboards.
- `~wavefront.maintenancewindows.*` -- Number of the active and future maintenance windows, as well as the total number of maintenance windows.
- `~wavefront.serviceAccounts.*` -- Number of the active, inactive, and deleted service accounts.
- `~wavefront.users.*` -- Metrics related to the user accounts and their activities.


<!--#### AWS Integration

If you have an [AWS integration](integrations_aws_metrics.html) and are ingesting AWS CloudWatch, CloudTrail, and AWS Metrics+ metrics into Wavefront, this section monitors the count of CloudWatch requests, API requests, the point rate, and events coming in from your integration.

![aws_metric_sections](images/aws_metric_sections.png)

The available metrics for the AWS integration are:

- `~externalservices.cloudwatch.api-requests` - number of CloudWatch API requests.
- `~externalservices.cloudwatch.points`- number of CloudWatch metrics returned.
- `~externalservices.ec2.points` - number of AWS Metrics+ metrics returned.
- `~externalservices.cloudtrail.events` - number of CloudTrail events returned.
- `~externalservices.cloudwatch-cycle-timer` - time in milliseconds CloudWatch requests take to complete.

-->

#### Ingest Rate by Source

The **Ingest Rate by Source** section gives insight into the shape of your data. It shows the total number of sources reporting. It also monitors the rate of metrics creation and breaks it down by source.

![Ingestion rate by source showing a chart with the number of reporting sources and the top 20 sources.](images/point_rate_breakdown.png)

The metrics used in this section are:

- `~metric.counter` -- Number of metrics being collected. Does not include internal metrics.

   If you're interested in histogram ingestion by source, clone this dashboard and add a chart that uses the `~histogram.counter` metric.

- `~histogram.counter` -- Number of histograms being collected. Does not include internal histogram data.

#### Rate of New Data Creations

The **Rate of New Data Creations** section gives an insight into the newly created data, such as metrics, sources, point tags, delta counters, histograms and spans.

![Rate of new data creations section of the dashboard.](images/new_data_creations.png)


### Wavefront Namespace Usage Explorer Dashboard

This dashboard helps you drill down into the metrics namespace and explore the **trend** of your metrics ingestion rate.

Tanzu Observability automatically tracks the number of metrics received for the first 3 levels of your metric namespace as delta counters, which can be queried with `cs(~metric.global.namespace.*)`. The period (`.`) character separates the levels. For example for a metric named `disk.space.total.bytes`, the first level is disk, the second is space, and the third is total. This dashboard includes chart to explore those metrics and trends.

![Screenshot of the Overview section of the Wavefront Namespace Usage Explorer dashboard.](/images/metrics_breakdown.png)

### Wavefront Ingestion Policy Explorer Dashboard

This dashboard helps you investigate the usage of the Wavefront service for each user and ingestion policy.

Tanzu Observability supports creation of ingestion policies. You create policies and assign accounts (user or service accounts) or groups to each policy and examine which teams use which part of total ingestion in this Wavefront Ingestion Policy Explorer dashboard. You can even drill down and examine usage of individual users.

The dashboard includes a link to the **Ingestion Policies** page so if you are a Super Admin, you can create, examine, or modify [ingestion policies](ingestion_policies.html).

![Screenshot of part of the Ingestion Policy Explorer dashboard](/images/ingestion_pps_usage_breakdown.png)

{% include note.html content="The information contained in the **Wavefront Ingestion Policy Explorer** dashboard has a 24-hour latency."%}

### PPS P95 Dashboards for Billable and Burndown

{% include important.html content="Use only the dashboard for your contract type:<br/>
    - If you have a billable commit contract, use the **Committed Rate vs Monthly Usage (PPS P95) for Billable** dashboard.<br/>
    - If you have a burndown commit contract, use the **Usage (PPS) vs Remaining Balance (PPS P95) for Burndown** dashboard.<br/>
"%}

The dashboard for your contract type helps you monitor your usage and ensure that you're not ingesting more PPS than your contracted rate allows.

* The **Committed Rate vs Monthly Usage (PPS P95) for Billable** dashboard provides a detailed breakdown of your Tanzu Observability monthly usage against commitment.

    ![Screenshot of part of the Committed Rate vs Monthly Usage (PPS P95) for Billable dashboard](/images/Billable.png)

* The **Usage (PPS) vs Remaining Balance (PPS P95) for Burndown** dashboard provides visibility into your Tanzu Observability usage against burndown commitment and a detailed breakdown of your usage per billing period.

    ![Screenshot of part of the Usage (PPS) vs Remaining Balance (PPS P95) for Burndown dashboard](/images/Burndown.png)

When your usage reaches around 95% of your committed rate, you can then take appropriate action. For example:

* Examine who is using a high percentage of the PPS in the  **Wavefront Ingestion Policy Explorer** dashboard.
* Implement [ingestion policies](ingestion_policies.html) and examine who is using a high percentage of the PPS.

## Scenario: Avoid Exceeding the Committed Rate

Customers often tell us that they want to make sure they don't exceed their committed monthly PPS (points per second). Follow these steps to monitor usage and take corrective action.

1. The **Committed Rate vs Monthly Usage (PPS P95) for Billable** dashboard includes charts that show how close you are to consuming 95% of your contracted rate. You can add alerts to charts in this dashboard to get notifications.
2. If you need to reduce usage, you have several options:
   * Start examining ingestion from the **Wavefront Service and Proxy Data** dashboard.

     The [internal metrics](wavefront-internal-metrics.html) shown in this dashboard highlight.
   * Use the **Wavefront Namespace Usage Explorer** dashboard to drill down into the metrics.

     Tanzu Observability automatically tracks the number of metrics received for the first 3 levels of your metric namespace as delta counters, and this dashboard presents the metrics in an easy-to-use way.
   * [Examine the overall usage of your Wavefront service](examine_usage.html).
   * Finally, if you suspect that much of your usage comes from certain accounts (user or service accounts), consider setting up one or more [ingestion policies](ingestion_policies.html).


## Learn More!

* [Find Actionable Usage Information](wavefront_usage_info.html) explains how to use tools and dashboards to learn how much data is coming in, who is sending the data, how to get alerted if ingested data get close to monthly contracted usage, and how to optimize your ingestion rate.
* [Metadata (Label Manipulation) Functions](query_language_metadata_functions.html) explains how to rename metrics and sources and create point tags with `aliasSource`, `aliasMetric`, and `taggify`.]
