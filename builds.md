---
tags: dontlink
---

# Builds

Build [artifacts](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts):

<p id="buildArtifactLinks"></p>

<script type="text/javascript">
let url = 'https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts';

fetch(url)
.then(res => res.json())
.then(function (bins) {
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
