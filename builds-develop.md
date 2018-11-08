---
tags: dontlink
sitemap: true
---

# Latest Development Builds

Much like the `master` branch, developmental builds are also built from the `develop` branch by [CircleCI](https://circleci.com/gh/yggdrasil-network/yggdrasil-go) for a number of platforms.

**Warning:** The `develop` branch is used for pre-release changes and features. It's not guaranteed to be 100% compatible with the `master` branch and anything in the `develop` branch is subject to change at any point before release - features merged into the `develop` branch may not be completely finished. Builds from this branch will likely be rougher around the edges and not as well tested. We welcome all testing and feedback of the `develop` branch, but if you value stability, you may prefer to stick to the [master](builds.md) builds instead.

For convenience, the [build artifacts](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts?branch=develop&filter=successful) from the latest `develop` build are linked below:

<h3>yggdrasil</h3>
<p id="buildArtifactLinks"></p>

<h3>yggdrasilctl (admin utility)</h3>
<p id="buildArtifactLinksCtl"></p>

<!-- TODO sort these to a useful order of some kind -->
<script type="text/javascript">
let url = 'https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts?branch=develop&filter=successful';

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
