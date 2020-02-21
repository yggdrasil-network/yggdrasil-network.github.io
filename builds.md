---
tags: dontlink
sitemap: true
---

# Latest Builds

After every push to the `master` branch of the [GitHub repository](https://github.com/yggdrasil-network/yggdrasil-go), the code is automatically built by [CircleCI](https://circleci.com/gh/yggdrasil-network/yggdrasil-go) for a number of platforms.

If you prefer to live dangerously and want to test bleeding-edge features or changes, development builds are [available from the `develop` branch](builds-develop.md), although these may not be as stable.

Looking for Windows installers? [Click here to go to AppVeyor](https://ci.appveyor.com/project/neilalexander/yggdrasil-go/build/artifacts).

For convenience, the latest builds are linked below:

<h3>yggdrasil</h3>
<p id="buildArtifactLinks"></p>

<h3>yggdrasilctl (admin utility)</h3>
<p id="buildArtifactLinksCtl"></p>

<!-- TODO sort these to a useful order of some kind -->
<script type="text/javascript">
let url = 'https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts?branch=master&filter=successful';
fetch(url).then(function(res) {
  return res.json();
}).then(function (bins) {
  var links = document.createElement('p')
  var ctllinks = document.createElement('p')
  for (var idx in bins) {
    var bin = bins[idx]
    var link = document.createElement('a');
    link.appendChild(document.createTextNode(bin.path));
    link.title = bin.path;
    link.href = bin.url;
    if (bin.path.includes("yggdrasilctl")) {
      ctllinks.appendChild(link);
      ctllinks.appendChild(document.createElement('br'));
    } else {
      links.appendChild(link);
      links.appendChild(document.createElement('br'));
    }
  }
  document.getElementById("buildArtifactLinks").appendChild(links);
  document.getElementById("buildArtifactLinksCtl").appendChild(ctllinks);
})
.catch(err => { throw err });
</script>
