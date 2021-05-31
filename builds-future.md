---
tags: dontlink
sitemap: true
---

# Future builds

**WARNING!** This branch contains a protocol break and WILL NOT work with earlier versions of Yggdrasil from either the `master` or `develop` branches at this time.

<h3>yggdrasil</h3>
<p id="buildArtifactLinks"></p>

<h3>yggdrasilctl (admin utility)</h3>
<p id="buildArtifactLinksCtl"></p>

<!-- TODO sort these to a useful order of some kind -->
<script type="text/javascript">
let url = 'https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts?branch=future&filter=successful';

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
