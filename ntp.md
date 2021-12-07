<h1> Mannually configure chrony service at each node:</h1>

<ul>
<li>default installed, if not</li>
<pre><code>yum -y install chrony</code></pre>
<li>enable and start</li>
<pre><code>systemctl enable chronyd.service
systemctl restart chronyd.service
systemctl status chronyd.service</code></pre>
<li>configure ntp source server in /etc/chrony.conf, add server like:</li>
<pre><code>server 10.62.16.166 iburst</code></pre>
<li>enable ntp service and set timezone</li>
<pre><code>timedatectl set-ntp true
timedatectl set-timezone</code></pre> 
<li>check time date setting</li>
<pre><code>timedatectl
timedatectl status</code></pre> 
<li>check ntp sources</li>
<pre><code>chronyc sources -v
chronyc sourcestats -v<pre><code>
</ul>

<h1>Leverage machineconfigure operator, ref https://docs.openshift.com/container-platform/4.8/installing/installing_bare_metal_ipi/ipi-install-post-installation-configuration.html</h1>
<ul>
<li>set ntp source</li>
 <pre><code>$ CHRONY_HOST=chrony.example.internal</code></pre>
<li>generate master conf file</li> 
<pre><code>$ cat << EOF >> chroney_master.conf 
server ${CHRONY_HOST} iburst
stratumweight 0
driftfile /var/lib/chrony/drift
rtcsync
makestep 10 3
bindcmdaddress 127.0.0.1
bindcmdaddress ::1
keyfile /etc/chrony.keys
commandkey 1
generatecommandkey
noclientlog
logchange 0.5
logdir /var/log/chrony
# Configure the control plane nodes to serve as local NTP servers
# for all worker nodes, even if they are not in sync with an
# upstream NTP server.
# Allow NTP client access from the local network.
allow all
# Serve time even if not synchronized to a time source.
local stratum 3 orphan
EOF</code></pre>
<li>base64 the file</li>
<pre><code>$ CHRONEY_MASTER_BASE64=$(base64 -w 0 chroney_master.conf)
$ echo $CHRONEY_MASTER_BASE64</code></pre>
<li>generate master configure</li>
<pre><code>$ cat << EOF > ./99_masters-chrony-configuration.yaml
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: master
  name: masters-chrony-configuration
spec:
  config:
    ignition:
      config: {}
      security:
        tls: {}
      timeouts: {}
      version: 3.1.0
    networkd: {}
    passwd: {}
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,${CHRONEY_MASTER_BASE64}
        mode: 420
        overwrite: true
        path: /etc/chrony.conf
  osImageURL: ""
EOF</code></pre>
<li>generate worker conf file</li> 
<pre><code>$ cat << EOF >> chroney_worker.conf 
server ${CHRONY_HOST} iburst
stratumweight 0
driftfile /var/lib/chrony/drift
rtcsync
makestep 10 3
bindcmdaddress 127.0.0.1
bindcmdaddress ::1
keyfile /etc/chrony.keys
commandkey 1
generatecommandkey
noclientlog
logchange 0.5
logdir /var/log/chrony
EOF</code></pre>
<li>base64 the file</li>
<pre><code>$ CHRONEY_WORKER_BASE64=$(base64 -w 0 chroney_worker.conf)
$ echo $CHRONEY_WORKER_BASE64</code></pre>
<li>generate worker confiure file:</li>
<pre><code>$ cat << EOF > ./99_workers-chrony-configuration.yaml
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: workers-chrony-configuration
spec:
  config:
    ignition:
      config: {}
      security:
        tls: {}
      timeouts: {}
      version: 3.1.0
    networkd: {}
    passwd: {}
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,${CHRONEY_WORKER_BASE64}
        mode: 420
        overwrite: true
        path: /etc/chrony.conf
  osImageURL: ""
EOF</code></pre>
<li>apply worker and masters machine configuration  or pre installation use  masters-chrony-configuration.yaml and 99_workers-chrony-configuration.yaml to replace openshift-install create manifests generated files</li>
<pre><code>$ oc apply -f 99_masters-chrony-configuration.yaml
$ oc apply -f 99_workers-chrony-configuration.yaml</code></pre>
<li>check node status, it will restart</li>
<pre><code>$ oc get nodes -l node-role.kubernetes.io/worker -w</code></pre>
</ul>

<h1>mannually set in the node:</h1>
<pre><code>$ oc debug node/<WORKER_NODE>
sh-4.4# chroot /host
sh-4.4# chronyc -4 -n sources</code></pre>
