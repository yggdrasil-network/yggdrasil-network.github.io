---
tags: dontlink
---

# Builds

Build [artifacts](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts):

<script type="text/javascript">
let url = 'https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts';

fetch(url)
.then(res => res.json())
.then((bins) => {
  for (var bin in bins) {
    console.log("<a href=\"" + bin.url + "\">" + bin.path + "</a><br/>");
  }
})
.catch(err => { throw err });
</script>
