---
tags: dontlink
sitemap: true
---

# Latest Builds

After every push to the `master` branch of the [GitHub repository](https://github.com/yggdrasil-network/yggdrasil-go), the code is automatically built by [CircleCI](https://circleci.com/gh/yggdrasil-network/yggdrasil-go) for a number of platforms.

If you prefer to live dangerously and want to test bleeding-edge features or changes, development builds are [available from the `develop` branch](builds-develop.md), although these may not be as stable.

For convenience, the [build artifacts](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts?branch=master&filter=successful) from the latest build are linked below:

<p id="buildArtifactLinks"></p>
<p id="buildArtifactLinksCtl"></p>

<!-- TODO sort these to a useful order of some kind -->
<script type="text/javascript">
let url = 'https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts?branch=master&filter=successful';
let json = fetch(url)

json.then(function(res) {
  return res.json();
}).then(function (bins) {
  var links = document.createElement('p')
  for (var idx in bins) {
    if (bin.path.includes("yggdrasilctl")) {
      continue
    }
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

json.then(function(res) {
  return res.json();
}).then(function (bins) {
  var links = document.createElement('p')
  for (var idx in bins) {
    if (!bin.path.includes("yggdrasilctl")) {
      continue
    }
    var bin = bins[idx]
    var link = document.createElement('a');
    link.appendChild(document.createTextNode(bin.path));
    link.title = bin.path;
    link.href = bin.url;
    links.appendChild(link);
    links.appendChild(document.createElement('br'));
  }
  document.getElementById("buildArtifactLinksCtl").appendChild(links)
})
.catch(err => { throw err });
</script>
