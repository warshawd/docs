---
title: Send Logs to Tanzu Observability (Beta)
keywords: data, logs
tags: [getting started, logs]
sidebar: doc_sidebar
permalink: logging_send_logs.html
summary: Learn about sending logs to Tanzu Observability.
---

{% include important.html content="Tanzu Observability Logs (Beta) is enabled only for selected customers. To participate, contact your Tanzu Observability account representative or [technical support](wavefront_support_feedback.html#support)."%}


{% include tip.html content="This doc page gets you started. [Try out the demo app tutorial on GitHub](https://github.com/wavefrontHQ/demo-app) to experiment with sending logs to Tanzu Observability."%}

You can send logs to the Wavefront proxy from your log shipper or directly from your application. The Wavefront proxy sends the log data to the Wavefront instance.

![shows how data goes from the log shipper to the wavefront proxy and then to the Wavefront instance](images/logging_send_logs_rev.png)

## Install a Wavefront Proxy

Our logging solution currently requires a Wavefront proxy and does not support direct ingestion. The Wavefront proxy accepts a JSON array payload over HTTP or HTTPS and forwards it to the Wavefront service.

{% include note.html content="For optimal performance, install a standalone proxy cluster that receives only logs payload. Typically two proxy instances behind a load balancer are sufficient." %}

<table style="width: 100;">
  <tr>
    <td width="50%" >
      Proxy System Requirements:
      <ul><li>2 CPUs</li>
      <li>4 GB memory</li>
      <li>Additional proxy configuration settings:
<code>
- name: JAVA_HEAP_USAGE
value:2G
- name: JVM_USE_CONTAINER_OPTS
value: "false"</code>
      </li>
      </ul>
    </td>
    <td width="50%" >
      Proxy Kubernetes Requirements:
      <ul><li>Request resources: 1 CPU and 2 GB memory</li>
      <li>Limit resources: 2 CPUs and 4 GB memory</li>
      <li>1 GB heap memory</li></ul>
    </td>
  </tr>
</table>

To install and configure a new proxy version 11.3 or later:

1. Log in to your Wavefront instance and select **Browse** > **Proxies**.
1. Click **Add Proxy** and follow the instructions on screen.
1. Edit the `wavefront.conf` file to open the `pushListenerPorts` to receive logs from the log shipper.
    <br/>For example:
    * If you installed the proxy on Linux, Mac, or Windows, open the [`wavefront.conf`](proxies_configuring.html#proxy-file-paths) file, uncomment the `pushListenerPorts` configuration property, and save the file. The port is set to 2878 by default.
    * If you installed the proxy on Docker, the command you use opens the `pushListenerPorts` and sets it to 2878.
1. Optionally, uncomment or add other [logs proxy configurations](logging_proxy_configurations.html#proxy-configuration-properties-for-logs) the `wavefront.conf` file.
1. Optionally, configure [preprocessor rules](logging_proxy_configurations.html#proxy-preprocessor-rules-for-logs) for logs in the `preprocessor_rules.yaml` file.
1. [Start the proxy](proxies_installing.html#start-and-stop-a-proxy).

{% include note.html content="To learn more about the proxy configuration properties and preprocessor rules for logs, see [Logs Proxy Configurations and Preprocessor Rules](logging_proxy_configurations.html)." %}

## Configure the Log Shipper

The log shipper sends your data to the Wavefront proxy. During Beta, we support the [Fluentd](https://docs.fluentd.org/) log shipper, which scrapes and buffers your logs before sending them to the Wavefront proxy specified in the `fluent.conf` file.

If you want to use a different log shipper, contact [technical support](https://docs.wavefront.com/wavefront_support_feedback.html#support).

**Prerequisite**:

Add the VMware domain (`*.vmware.com`) to the allowlist in your environment. Because Tanzu Observability uses a VMware log cluster, you need to add the VMware domain to your allowlist to send log data successfully. If you want to narrow down the domain, contact your Tanzu Observability account representative.

Configure your log shipper:
  1. Install the log shipper. For example, [install Fluentd](https://docs.fluentd.org/installation).

  1. Configure the log shipper to send data to the Wavefront proxy.

     a. Add the hostname of the host where the proxy runs.

     b. Add the `pushListenerPorts` that you configured in the proxy.
     <br/>For example, edit the `fluent.conf` file to send data to a proxy as follows:
      ```
      <match wf.**>
        @type copy
        <store>
          @type http
          endpoint http://<proxy url>:<proxy port (example:2878)>/logs/json_array?f=logs_json_arr
          open_timeout 2
          json_array true
          <buffer>
            flush_interval 10s
          </buffer>
        </store>
      </match>
      ```
  1. As part of preprocessing, tag the logs with the application and service name to ensure you can drill down from traces to logs.
  2. (Optional) If you're already using a logging solution, specify alternate strings for required and optional log attributes in the [proxy configuration file](logging_proxy_configurations.html). See also [My Logging Solution Doesn't Use the Default Attributes](logging_faq.html#my-logging-solution-doesnt-use-the-default-attributes).

### Limits for Logs

If logs exceed the maximum character limit for a message, tag, or value, the Wavefront proxy drops the logs. Ensure that your logs are within the given limits. See [How Do I Track Data Blocked by the Wavefront Proxy?](logging_faq.html#how-do-i-track-data-blocked-by-the-wavefront-proxy)

{% include note.html content="To increase the limits, ask your administrator to reach out to [technical support](https://docs.wavefront.com/wavefront_support_feedback.html#support)." %}

<table style="width: 100;">
  <tr>
    <th width="20%">
      Attribute
    </th>
    <th width="80%">
      Default Limit
    </th>
  </tr>
  <tr>
    <td>
      Log message
    </td>
    <td>
      20,000 characters
    </td>
  </tr>
  <tr>
    <td>
      Log tags
    </td>
    <td>
      Low cardinality. Many of the recommendations in <a href="optimize_data_shape.html">Optimizing Data Shape to Improve Performance</a> apply.<br/>
      128 characters per tag key<br/>
      128 characters per tag value<br/>
      100 tags per log
    </td>
  </tr>
</table>

<!--- Repeat from logging_overview. There are links below already. I recommend we cut this.
## Map the Traces and Metrics to Logs

To get the unified observability experience and drill down from traces to logs and metrics in dashboards or charts to logs, you need to update your settings so that your traces and metrics map to the logs sent from your application. Contact [technical support](wavefront_support_feedback.html#support) to update the settings.

## View Logs

When the data is in Tanzu Observability, you can use the Logs Browser to filter and search logs, and drill into logs from charts, alerts, Application Map page, and the Traces Browser. See [View Logs and Troubleshoot](logging_overview.html#view-logs-and-troubleshoot).
--->

## Learn More!

* [Get started with logs](logging_overview.html).
* [View and browse logs](logging_log_browser.html).
* Learn about the [proxy configurations and proxy preprocessor rules for logs](logging_proxy_configurations.html).
* See [Logs troubleshooting](logging_faq.html).

<!---
[Try out the demo app tutorial on GitHub](https://github.com/wavefrontHQ/demo-app) to send logs to Tanzu Observability.
--->
