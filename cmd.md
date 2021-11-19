<h2>Useful Command</h2>:
<ul>
<li>Merge mirror-registry-pullsecret.json & redhat-pullsecret.json:</li>
<table class="highlighttable"><tr><td class="linenos"><div class="linenodiv"><pre><span></span><span class="normal">1</span></pre></div></td><td class="code"><div class="highlight"><pre><span></span><code>jq -s &#39;{&quot;auths&quot;: ( .[0].auths + .[1].auths ) }&#39; mirror-registry-pullsecret.json redhat-pullsecret.json &gt; pullsecret.json
</code></pre></div>
</td></tr></table>
