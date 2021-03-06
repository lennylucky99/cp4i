<h2>Useful Command</h2>:
<ul>
<li>Merge mirror-registry-pullsecret.json & redhat-pullsecret.json:</li>
<pre><code>jq -s &#39;{&quot;auths&quot;: ( .[0].auths + .[1].auths ) }&#39; mirror-registry-pullsecret.json redhat-pullsecret.json &gt; pullsecret.json</code></pre>
  <li>Show Openshift marketplace image SHA </li>
 <pre><code>oc -n openshift-marketplace get pod -o yaml $(oc -n openshift-marketplace get pods | grep opencloud-operators | cut -d " " -f 1) | grep imageID</code></pre><br>
  The SHAs for this build are as follows:<br>
Catalog digest: sha256:6069dabd079438019990ca437648f609d939a59745fb092f9b783cc379942350<br>
amd64 digest: sha256:4f12dc9ca504f69e149ea4c6c897c33b3de7f53087f7fa318aaae075fbe6d75c<br>
  <li>Adding ephemeral storaste to the image registry can be done with the following command:</li>
<pre><code>
  oc patch configs.imageregistry.operator.openshift.io cluster --type merge --patch '{"spec":{"storage":{"emptyDir":{}}}}'
</code></pre>
  <li>Assigning persistent storage for image-registry:</li>
  <pre><code>oc patch configs.imageregistry.operator.openshift.io cluster --type merge --patch '{"spec":{"storage":{"pvc":{"claim":"{<changeme>}"}}}}'  </code></pre>
  <li>Disable default operator source. This only needs to be done once for a cluster.</li>
  <pre><code>oc patch OperatorHub cluster --type json -p '[{"op": "add", "path": "/spec/disableAllDefaultSources", "value": true}]'</code></pre>
  <li>Approve Pending CSR.</li>
  <pre><code>oc get csr -o name | xargs oc adm certificate approve
oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | xargs oc adm certificate approve</code></pre>
  <li>Install cephtool, run the following cmd</li>
  <pre><code>oc patch OCSInitialization ocsinit -n openshift-storage --type json --patch  '[{ "op": "replace", "path": "/spec/enableCephTools", "value": true }]'</code></pre>
after this cmd, under project openshift-storage there is a rook-ceph-tools pod，bash into the pod, could execute ceph cmd and check ceph cluster status.
  <li>Out-of-the-box CP4I platform admin user id/password</li><pre><code>oc extract secret/platform-auth-idp-credentials -n ibm-common-services --to=-</code></pre>
   <li>Add default router log level</li><pre><code>oc patch dc -n default router -p '[{"op": "add", "path": "/spec/template/spec/containers/0/args", "value":["--loglevel=4"]}]' --type=json</code></pre>
   <li>Remove default router log level</li><pre><codeoc patch dc -n default router -p '[{"op": "remove", "path": "/spec/template/spec/containers/0/args", "value":["--loglevel=4"]}]' --type=json</code></pre>

