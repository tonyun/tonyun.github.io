---
title: Install NodeJS 16.17
---


```sh
sudo yum install -y gcc-c++ make curl
curl -sL https://rpm.nodesource.com/setup_16.x | sudo -E bash -

sudo yum list available nodejs
sudo yum install -y nodejs
node -v
npm -v
```

## Node.js LTS
```sh
sudo yum install -y gcc-c++ make curl
curl -sL https://rpm.nodesource.com/setup_lts.x | bash -
# Remove old version
yum remove -y nodejs npm
# Clear cache
yum clean all
# Install new Node.js
yum install -y nodejs
```

## Remove NodeJS
```sh
sudo yum remove -y nodejs
sudo rm -rf /var/cache/yum
sudo rm /etc/yum.repos.d/nodesource*
sudo yum clean all
```

## Remove residual files
```sh
whereis node
sudo rm -rfv /usr/bin/node /usr/local/bin/node /usr/share/man/man1/node.1.gz
sudo rm -rfv /usr/bin/npm /usr/local/bin/npm /usr/share/man/man1/npm.1.gz
sudo rm -rfv /usr/local/bin/npx
sudo rm -rfv /usr/local/lib/node*
sudo rm -rfv /usr/local/include/node*
sudo rm -rfv /usr/lib/node_modules/
```