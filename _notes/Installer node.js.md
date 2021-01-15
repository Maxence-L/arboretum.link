---
title : Installer node.js
tags : Réseau
etat : hiver
---

Un échantillon simple pour installer une [version à jour ](https://github.com/nodesource/distributions/blob/master/README.md) de `node.js` sur Ubuntu/Debian, la version proposée par anaconda étant dépassée :

```bash
# Using Ubuntu
curl -sL https://deb.nodesource.com/setup\_15.x | sudo -E bash -
sudo apt-get install -y nodejs

# Using Debian, as root
curl -sL https://deb.nodesource.com/setup\_15.x | bash -
apt-get install -y nodejs
````


