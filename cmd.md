<h2>Useful Command</h2>:
<ul>
<li>Merge mirror-registry-pullsecret.json & redhat-pullsecret.json:</li>
<code>
  jq -s &#39;{&quot;auths&quot;: ( .[0].auths + .[1].auths ) }&#39; mirror-registry-pullsecret.json redhat-pullsecret.json &gt; pullsecret.json
</code>
