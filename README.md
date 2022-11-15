# CloudBees-Monitoring-Prometheus

## How to monitor the CloudBees CI with Prometheus and Grafana:

1. Install Cloudbees prometheus plugin for oc and Jenkins Prometheus plugins for controllers and remember to RESTART after installation
2. Pull the Prometheus/Grafana helm chart: kube-prometheus-stack (https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)
3. Portforward(or expose through loadbalancer) the Prometheus and Grafana for access: kubectl port-forward [grafana-pod] 3000; kubectl port-forward [prometheus-pod] 9090.  Grafana username: admin   password: prom-operator

5. Configuration of kube-prometheus-stack: 
    1. Set serviceMonitorSelectorNilUsesHelmValues=false in values.yaml
    2. Add serviceMonitors for operation center and each controller respectively, the path is: /cjoc/prometheus and /[controller-name]/prometheus: 
    3. Install CloudBees CI Dashboard in Grafana: https://grafana.com/grafana/dashboards/12571
    4. Sample Dashboard for controllers: https://grafana.com/grafana/dashboards/?search=jenkins
6. The metrics explanation: https://plugins.jenkins.io/metrics/#documentation

## NOTE 
The health check API: https://docs.cloudbees.com/docs/cloudbees-ci-api/latest/metrics-api#_healthcheck 

## Cjoc ServiceMonitor sample code:

additionalServiceMonitors: 
- name: "servicemonitor-cloudbees-oc"
   selector: 
       matchLabels:
            app.kubernetes.io/component: cjoc
            app.kubernetes.io/instance: cloudbees-core
            app.kubernetes.io/name: cloudbees-core
       namespaceSelector:
           any: true
       endpoints:
       - port: http
          path: /cjoc/prometheus
          scheme: http
