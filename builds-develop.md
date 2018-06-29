---
tags: dontlink
---

# Latest Development Builds

Much like the `master` branch, developmental builds are also built from the `develop` branch by [CircleCI](https://circleci.com/gh/yggdrasil-network/yggdrasil-go) for a number of platforms.

Builds from the `develop` branch will contain prerelease features and changes, although they may be rougher around the edges and not be as well tested! If you value stability, stick to the [master](builds.md) builds.

For convenience, the [build artifacts](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts?branch=develop&filter=successful) from the latest `develop` build are linked below:

<p id="buildArtifactLinks"></p>

<!-- TODO sort these to a useful order of some kind -->
<script type="text/javascript">
let url = 'https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts?branch=develop&filter=successful';

fetch(url)
.then(function(res) {
  return res.json();
}).then(function (bins) {
  var links = document.createElement('p')
  for (var idx in bins) {
    var bin = bins[idx]
    var link = document.createElement('a');
    link.appendChild(document.createTextNode(bin.path));
    link.title = bin.path;
    link.href = bin.url;
    links.appendChild(link);
    links.appendChild(document.createElement('br'));
  }
  document.getElementById("buildArtifactLinks").appendChild(links)
})
.catch(err => { throw err });
</script>
