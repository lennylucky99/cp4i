<h1># cp4i<h1>
<h2>Container Related Tools</h2>:
  <ul>
    <li>cloudctl --	Running CASE commands https://github.com/IBM/cloud-pak-cli/ </li>
    <pre><code>tar -xf cloudctl-linux-amd64.tar.gz</code></pre>
    <li>Install Docker </li>
    <pre><code>yum check-update
yum install docker</code></pre>
    <li>Install Podman. https://podman.io/getting-started/installation.html</li>
    <pre><code>sudo yum -y install podman</code></pre>
    <li>Install httpd-tools.</li>
    <pre><code>sudo yum install httpd-tools</code></pre>
    <li>Install skopeo.</li>
    <pre><code>sudo yum -y install skopeo</code></pre>
  <li>Buildah -- building OCI images: https://github.com/containers/buildah</li>
  <li>grpctl: https://github.com/fullstorydev/grpcurl/releases/download/v1.8.5/grpcurl_1.8.5_linux_x86_64.tar.gz</li>
  <li>opm and openshift client: https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/latest-4.6/?extIdCarryOver=true&sc_cid=701f2000001Css5AAC</li>
  <li>Installing OPM CLI from image</li>
  <pre><code>
    oc image extract registry.redhat.io/openshift4/ose-operator-registry:v4.6 --registry-config='~/openshift/pull-secret-full.json' --path /usr/bin/opm:. --confirm
    sudo chmod +x opm
    sudo mv opm /usr/local/bin  
 </code></pre>
    <li>Useful network utilities and tools</li>
    <ul>
      <li>Ping -- ICMP protocol to test connectivity, Option	Description</li>
      <ul>
        <li> -b   Broadcast to the network specified as an argumen </li>
        <li> -t   Set the IP Time to Live </li>
        <li> -n	Display host information numerically </li>
        <li> -i INTERVAL	Specify the echo request interval in seconds (default is 1) </li>
        <li> -I INTERFACE	Send echo requests out INTERFACE </li>
        <li> -c COUNT	Send only COUNT echo requests </li>
        <li> -W TIMEOUT	Wait TIMEOUT seconds before quitting </li>
      </ul>
      <li>Nmap -- Network discovery and security auditing. could find open port</li>
      <pre><code>      nmap -n 192.168.137.0/24
      nmap -n -sU 192.168.137.130
      nmap 127.0.0.1
      </code></pre>
      <li>iptraf-ng -- is a console-based network monitoring program for Linux that
displays information about IP traffic. https://github.com/iptraf-ng/iptraf-ng </li>
      <li>Wireshark -- widely-used network protocol analyzer. https://www.wireshark.org/</li>
        <pre><code># yum -y install wireshark-gnome</code></pre>
      <li>tcpdump -- a powerful command-line packet analyzer. https://www.tcpdump.org/</li>
      <pre><code>      tcpdump -i eth1
      tcpdump host 210.27.48.1 
      tcpdump host helios and \( hot or ace \)
      tcpdump host 210.27.48.1 and \ (210.27.48.2 or 210.27.48.3 \) 
      tcpdump ip host ace and not helios
      tcpdump ip host 210.27.48.1 and ! 210.27.48.2
      tcpdump -i eth0 src host hostname
      tcpdump -i eth0 dst host hostname
      tcpdump net ucb-ether
      tcpdump 'gateway snup and (port ftp or ftp-data)'
      tcpdump ip and not net localnet
      tcpdump 'tcp[tcpflags] & (tcp-syn|tcp-fin) != 0 and not src and dst net localnet'
      tcpdump 'tcp port 80 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'
      tcpdump tcp -i eth1 -t -s 0 -c 100 and dst port ! 22 and src net 192.168.1.0/24 -w ./target.cap
      tcpdump  -XvvennSs 0 -i eth0 tcp[20:2]=0x4745 or tcp[20:2]=0x4854 -- get http packate</code></pre>
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
    <li>Deploy GPU Operators in a disconnected or airgapped environment:<br>
https://docs.nvidia.com/datacenter/cloud-native/openshift/mirror-gpu-ocp-disconnected.html
    </li>
  </ul>
