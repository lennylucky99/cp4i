<h1># cp4i<h1>
<h2>Container Related Tools</h2>:
  <ul>
  <li>Buildah -- building OCI images: https://github.com/containers/buildah</li>
  <li>grpctl: https://github.com/fullstorydev/grpcurl/releases/download/v1.8.5/grpcurl_1.8.5_linux_x86_64.tar.gz</li>
  <li>opm and openshift client: https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/latest-4.6/?extIdCarryOver=true&sc_cid=701f2000001Css5AAC</li>
  <li>Installing OPM CLI from image</li>
  <code>
oc image extract registry.redhat.io/openshift4/ose-operator-registry:v4.6 --registry-config='~/openshift/pull-secret-full.json' --path /usr/bin/opm:. --confirm
\nsudo chmod +x opm
\nsudo mv opm /usr/local/bin
\n
 </code>
  </ul>
<h2>OpenShift Related Tools</h2>:
  <ul>
    <li>openshift official github:</li>
    <ul>
      <li>https://github.com/openshift</li>
      <li>https://github.com/RedHatOfficial</li>
    </ul>
    <li>RedHat OpenShift Helper Node(helps set up an "all-in-one" node with tools/service to install OCP4): https://github.com/RedHatOfficial/ocp4-helpernode#quickstarts</li>
  </ul>
  <h5> About Infrascture Node </h5>
  <ul>
    <li>https://tinyexplosions.com/posts/infra-nodes/
  </ul>
 <h5> About Internal Image Registry </h5>
  <ul>
    <li>https://hewlettpackard.github.io/OpenShift-on-SimpliVity/post-deploy/expose-registry.html#add-a-route-for-the-image-registry</li>
  </ul>
   <h5> Airgap Installation OpenShift Operator </h5>
  <ul>
    <li>https://zhimin-wen.medium.com/airgap-installation-for-openshift-operators-fb0a3cad8731</li>
    <li>https://examples.openshift.pub/operators/air-gapped-pre-4.6/</li>
    <li>https://github.com/dwojciec/OLM-disconnected</li>
    <li>https://github.com/operator-framework/</li>
    <li>https://github.com/openshift-telco/ocp4-offline-operator-mirror</li>
  </ul>
