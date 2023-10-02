## Configure Istio Service Mesh on Prometheus and Monitor using Grafana

Gain the expertise to set up Prometheus metrics within the Istio service mesh for a comprehensive grasp of Kubernetes application performance, and then utilize Grafana to create visual representations of metric data.

**An Overview of Leveraging Prometheus for Istio Observability**

The Istio service mesh abstracts network intricacies from the application layers by employing sidecar proxies. It empowers you to enforce robust security and advanced networking policies for all communication throughout your infrastructure.

However, an equally vital facet of Istio is its observability capabilities. Utilizing Istio, you can gain comprehensive insights into the performance and behavior of all your microservices within your infrastructure (as depicted in the image below). This functionality is especially crucial for Site Reliability Engineers (SREs) in large organizations, as it enables them to diligently monitor essential application metrics like CPU utilization, memory usage, latency, and throughput.

![Istio2](https://github.com/vamseekrishnaudatha/performance-engineering.github.io/assets/144172718/0634f33a-5bfc-4936-a148-5e169f6e9e6b)

In this article, we will explore the advantages that Site Reliability Engineers (SREs) can derive from the integration of three prominent open-source tools: Istio, Prometheus, and Grafana. Istio stands out as a renowned service management software, Prometheus enjoys widespread popularity as a monitoring solution, and Grafana is celebrated as a leading visualization tool in the field.

**Please watch the video tutorial demonstrating the configuration of Istio, Prometheus, and Grafana**

[![Video](https://img.youtube.com/vi/jZvKKvgirpM/maxresdefault.jpg)](https://www.youtube.com/watch?v=jZvKKvgirpM)

**Step 1: Go to Istio Add-Ons and Apply Prometheus and Grafana YAML File**

To configure Prometheus, start by navigating to the add-on folder within your Istio directory. Assuming you are using Istio version 1.17.1, the path for you would be istio-1.17.1/samples/addons.

In this directory, you'll find pre-existing YAML files provided by Istio for configuring various components, including Grafana, Prometheus, Jaeger, Kiali, and more. To set up Prometheus, you can use the following command:

Shell

```1 kubectl apply -f prometheus.yaml```

```1 kubectl apply -f grafana.yaml```

![Step1-1](https://github.com/vamseekrishnaudatha/performance-engineering.github.io/assets/144172718/72c87d96-8176-43d3-aaf9-2c0a1b5d90d7)

It's important to note that these add-on YAML files are applied to the istio-system namespace by default.

![Step1-2](https://github.com/vamseekrishnaudatha/performance-engineering.github.io/assets/144172718/9d05f4ca-cd94-4a8a-8ae2-3e296d8b61c3)

**Step 2: Deploy New Service and Port-Forward Istio Ingress Gateway**

To test the operational setup, we'll deploy the httpbin service in an Istio-enabled namespace. Additionally, we'll configure an Istio Ingress Gateway object to handle incoming traffic from the public for this service.

![Step2-1](https://github.com/vamseekrishnaudatha/performance-engineering.github.io/assets/144172718/99f21ee9-acce-4954-99b5-1bb46eae1456)

Furthermore, we will set up port-forwarding for the Istio Ingress Gateway to a specific port, which in this case is port 7777. This allows us to manage and monitor incoming traffic effectively.

![Step2-2](https://github.com/vamseekrishnaudatha/performance-engineering.github.io/assets/144172718/fbb8b67d-ad53-4e1f-9890-30da7b2d41e4)

Upon completing the previous steps, you should be able to access the following screen at localhost:7777:

![Step2-3](https://github.com/vamseekrishnaudatha/performance-engineering.github.io/assets/144172718/2dcd9591-ce25-4643-8e5d-8ab5bd0abd7c)


**Step 3: Access the Prometheus and Grafana dashboards**

To access the Prometheus dashboard, you can utilize the following command:

Shell

```istioctl dashboard prometheus```

```istioctl dashboard grafana```

Both Grafana and Prometheus will open locally on your machine, typically accessible at localhost for further configuration and monitoring.

![Step3-1](https://github.com/vamseekrishnaudatha/performance-engineering.github.io/assets/144172718/8efc8d8d-21a1-4f96-a116-3c5edc1a2e07)

**Step 4: Make HTTP Requests From Postman**

In this step, we will observe how the httpbin service utilizes CPU and memory resources under varying traffic loads. To do so, we will generate a series of GET and POST requests to localhost:7777 using the Postman application. This will help us assess the service's performance and resource utilization.

![Step4-1](https://github.com/vamseekrishnaudatha/performance-engineering.github.io/assets/144172718/9d819b19-5a72-4f3f-a6d2-9c24e876647e)

After issuing multiple GET or POST requests to the httpbin service, resource utilization will occur, and these metrics can be visualized in Grafana. However, before this, it's essential to configure the metrics for the httpbin service within Prometheus and Grafana to effectively monitor and analyze the service's performance.

Step 5: Configure metrics in Prometheus

To configure metrics in Prometheus for monitoring purposes, we can focus on specific Kubernetes resources, such as the API server, applications, workloads, or Envoy. In this context, we will select the container_memory_working_set_bytes metric. Here's how you can set it up:

In the Prometheus application, initiate the metric configuration.
Specify the namespace from which you want to scrape metrics. In this case, you can use the following search term: ```container_memory_working_set_bytes{namespace="istio-telemetry"}```. (Here, ```"istio-telemetry"``` represents the Istio-enabled namespace where the httpbin service is deployed.)
Please note that running this query will provide you with memory statistics for your namespace. However, if you intend to analyze the memory usage of individual pods and calculate the total memory consumed across pods, you can use the following query:

```sum(container_memory_working_set_bytes{namespace="istio-telemetry"}) by (pod)``` 

This query will aggregate memory usage data by pods, allowing you to monitor and analyze the total memory consumed by the pods in the specified namespace. 

![Step5-1](https://github.com/vamseekrishnaudatha/performance-engineering.github.io/assets/144172718/cbd04cc5-d151-4c33-a7c8-6294cc32591b)

Note: Prometheus provides a lot of flexibility to filter, slice, and dice the metric data. The central idea of this article was to showcase the ability of Istio to emit and send metrics to Prometheus for collection.

**Step 6: Configuring Istio Metrics Graphs in Grafana**

Creating a time-series graph for the ```sum(container_memory_working_set_bytes{namespace="istio-telemetry"}) by (pod)``` query in Grafana is a straightforward process:

1. Create a new dashboard in Grafana.
2. In the metrics browser, paste the query ```(sum(container_memory_working_set_bytes{namespace="istio-telemetry"}) by (pod))```.
3. Grafana will automatically generate a time-series graph based on this query.
4. You can further enhance the graph by customizing the names, legends, and titles to make it more informative and suitable for sharing with other stakeholders in the Operations (Ops) team.
   
This approach allows for clear and effective visualization of memory usage over time, providing valuable insights for monitoring and decision-making.

![Step6-1](https://github.com/vamseekrishnaudatha/performance-engineering.github.io/assets/144172718/5a040000-dcad-4570-a4fb-4bd3c21064cb)

Certainly, customization options in Grafana are extensive and can be tailored to suit specific enterprise requirements. Your video experiments are likely to offer valuable insights into how Prometheus metrics can be effectively visualized and customized in Grafana. Exploring these customization possibilities can significantly enhance the monitoring and reporting capabilities of your observability stack. Thank you for the information, and I encourage others to check out the video for hands-on examples.

**Conclusion**

Istio's service mesh capabilities indeed offer a powerful foundation for achieving comprehensive observability across your infrastructure. This article has provided a valuable example of metrics scraping and visualization using Istio in combination with Prometheus and Grafana.
