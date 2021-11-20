<h2> Set up Infrastructure Node and move workload</h2>
<h3>Set up Infrastrcture Node and you will have the following benefits:</h3>
  <ul>
    <li>Prevent incurring billing costs against subscription counts</li>
    <li>Separate maintenance and management. </li>
    <li>Application workload and OpenShift infrastructure workload not affect each other.</li>
    <li>Logging could be deployed on infrastructure node and will not affect application performance.</li>
  </ul>
<br>Procedure:
  <ul>
    <li>Define a infra MCP definition below will find all MachineConfigs labeled both "worker" and "infra" and it will apply them to any Machines or Nodes that have the "infra" role label. In this manner, you will ensure that your infra nodes can upgrade without the "worker" role label.</li>
    <pre><code>apiVersion: machineconfiguration.openshift.io/v1
    kind: MachineConfigPool
metadata:
  name: infra
spec:
  machineConfigSelector:
    matchExpressions:
      - {key: machineconfiguration.openshift.io/role, operator: In, values: [worker,infra]}
  nodeSelector:
    matchLabels:
      node-role.kubernetes.io/infra: ""</code></pre>
  <li>Without MCP, you could define app and infr node </li>
    <ul>
      <li>Add a label to the worker node(s) you wish to act as app node(s):</li>
      <pre><code>$ oc label node <node-name> node-role.kubernetes.io/app=""</code></pre>
      <li>Add a label to the worker node(s) you wish to act as infra node(s):</li>
      <pre><code>$ oc label node <node-name> node-role.kubernetes.io/infra=""</code></pre>
      <li>Add a label to machine set to act as infra node(s) :</li>
      <pre><code>$ oc patch machineset $MACHINESET_INFRA  --type=merge -p '{"spec":{"template":{"spec":{"metadata":{"labels":{"node-role.kubernetes.io/infra":""}}}}}}'</code></pre>
    </ul>
  <li>An example MachineSet with the required nodeSelector and taints applied might look like this:</li>
  <pre><code>spec:
  replicas: 1
  selector:
    matchLabels:
      machine.openshift.io/cluster-api-cluster: <infrastructureID> 
      machine.openshift.io/cluster-api-machineset: <infrastructureID>-infra-<zone> 
  template:
    metadata:
      labels:
        machine.openshift.io/cluster-api-cluster: <infrastructureID> 
        machine.openshift.io/cluster-api-machine-role: infra 
        machine.openshift.io/cluster-api-machine-type: worker 
        machine.openshift.io/cluster-api-machineset: <infrastructureID>-infra-<zone> 
    spec:
      metadata:
        labels:
          node-role.kubernetes.io/infra: ""
      taints:
      - effect: NoSchedule
        key: infra
        value: reserved
      - effect: NoExecute
        key: infra
        value: reserved</code></pre>
    
 <li>You could move Router, image registry, monitoring and logging etc to the infr node. </li>
  <ul>
    <li>Specify the defaultNodeSelector to deploy pods on worker nodes by default would look like:</li>
    <pre><code>$ oc patch scheduler cluster --type=merge -p '{"spec":{"defaultNodeSelector":"node-role.kubernetes.io/app="}}'</code></pre>
    <li>You could use following spec to	move workload to the infr node:</li>
	<pre><code>spec:
  nodePlacement:
    nodeSelector:
      matchLabels:
        node-role.kubernetes.io/infra: ""
    tolerations:
    - effect: NoSchedule
      key: infra
      value: reserved
    - effect: NoExecute
      key: infra
      value: reserved</code></pre>
    <li>move ingresscontroller/default</li>
    <pre><code>oc patch ingresscontroller/default -n  openshift-ingress-operator  --type=merge -p '{"spec":{"nodePlacement": {"nodeSelector": {"matchLabels": {"node-role.kubernetes.io/infra": ""}},"tolerations": [{"effect":"NoSchedule","key": "infra","value": "reserved"},{"effect":"NoExecute","key": "infra","value": "reserved"}]}}}'
    oc patch ingresscontroller/default -n openshift-ingress-operator --type=merge -p '{"spec":{"replicas": 3}}'</code></pre>
	  <li>move internal registry:</li>
    <pre><code>oc patch configs.imageregistry.operator.openshift.io/cluster --type=merge -p '{"spec":{"nodeSelector": {"node-role.kubernetes.io/infra": ""},"tolerations": [{"effect":"NoSchedule","key": "infra","value": "reserved"},{"effect":"NoExecute","key": "infra","value": "reserved"}]}}'</code></pre>
	  <li>move monitoring:</li>
    <pre><code>apiVersion: v1
kind: ConfigMap
metadata:
  name: cluster-monitoring-config
  namespace: openshift-monitoring
data:
  config.yaml: |+
    alertmanagerMain:
      nodeSelector:
        node-role.kubernetes.io/infra: ""
      tolerations:
      - key: infra
        value: reserved
        effect: NoSchedule
      - key: infra
        value: reserved
        effect: NoExecute
    prometheusK8s:
      nodeSelector:
        node-role.kubernetes.io/infra: ""
      tolerations:
      - key: infra
        value: reserved
        effect: NoSchedule
      - key: infra
        value: reserved
        effect: NoExecute
    prometheusOperator:
      nodeSelector:
        node-role.kubernetes.io/infra: ""
      tolerations:
      - key: infra
        value: reserved
        effect: NoSchedule
      - key: infra
        value: reserved
        effect: NoExecute
    grafana:
      nodeSelector:
        node-role.kubernetes.io/infra: ""
      tolerations:
      - key: infra
        value: reserved
        effect: NoSchedule
      - key: infra
        value: reserved
        effect: NoExecute
    k8sPrometheusAdapter:
      nodeSelector:
        node-role.kubernetes.io/infra: ""
      tolerations:
      - key: infra
        value: reserved
        effect: NoSchedule
      - key: infra
        value: reserved
        effect: NoExecute
    kubeStateMetrics:
      nodeSelector:
        node-role.kubernetes.io/infra: ""
      tolerations:
      - key: infra
        value: reserved
        effect: NoSchedule
      - key: infra
        value: reserved
        effect: NoExecute
    telemeterClient:
      nodeSelector:
        node-role.kubernetes.io/infra: ""
      tolerations:
      - key: infra
        value: reserved
        effect: NoSchedule
      - key: infra
        value: reserved
        effect: NoExecute
    openshiftStateMetrics:
      nodeSelector:
        node-role.kubernetes.io/infra: ""
      tolerations:
      - key: infra
        value: reserved
        effect: NoSchedule
      - key: infra
        value: reserved
        effect: NoExecut</code></pre>
	<li>Moving the cluster logging resources, Edit the Cluster Logging Custom Resource in the openshift-logging project:</li>
	<pre><code>$oc edit ClusterLogging instance
apiVersion: logging.openshift.io/v1
kind: ClusterLogging
...
spec:
  collection:
    logs:
      fluentd:
        resources: null
      type: fluentd
  curation:
    curator:
	  tolerations:
        key: infra
        value: reserved
        effect: NoSchedule
        key: infra
        value: reserved
        effect: NoExecut
      nodeSelector: 
          node-role.kubernetes.io/infra: ''
      resources: null
      schedule: 30 3 * * *
    type: curator
  logStore:
    elasticsearch:
      nodeCount: 3
	  tolerations:
        key: infra
        value: reserved
        effect: NoSchedule
        key: infra
        value: reserved
        effect: NoExecut
      nodeSelector: 
        node-role.kubernetes.io/infra: ''
      redundancyPolicy: SingleRedundancy
      resources:
        limits:
          cpu: 500m
          memory: 16Gi
        requests:
          cpu: 500m
          memory: 16Gi
      storage: {}
    type: elasticsearch
  managementState: Managed
  visualization:
    kibana:
	  tolerations:
        key: infra
        value: reserved
        effect: NoSchedule
        key: infra
        value: reserved
        effect: NoExecut
      nodeSelector: 
        node-role.kubernetes.io/infra: ''
      proxy:
        resources: null
      replicas: 1
      resources: null
    type: kibana
...
</code></pre>
</ul>
</ul>

<h3>Ref:</h3>
 		<li>https://access.redhat.com/solutions/5034771</li>
    <li>https://mp.weixin.qq.com/s/uTnDJ7-6JVTWz-mlyULnqg</li>
