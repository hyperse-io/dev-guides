# Install nodejs on alibaba clound linux

Install multiple versions using `NVM`, `NVM` (Node Version Manager) is the version management software for Node.js, allowing you to easily switch between different versions of Node.js.

## Install steps

### Use Git to clone the source code to your local ~/.nvm directory and check for the latest version.

```shell
yum install git
git clone https://github.com/nvm-sh/nvm.git ~/.nvm && cd ~/.nvm && git checkout `git describe --abbrev=0 --tags`

// if you have network connection issue, you can do like below
git clone https://gitee.com/banjiaojuhao/nvm.git ~/.nvm && cd ~/.nvm && git checkout `git describe --abbrev=0 --tags`
```

### Activate the NVM.

```shell
echo ". ~/.nvm/nvm.sh" >> /etc/profile
source /etc/profile
```

### Lists all versions of Node.js.

```shell
nvm list-remote
```

### `NVM` configures mirroring in china, (If you cannot access list-remote, configure nvm domestic mirroring).

Method 1:

```shell
nvm npm_mirror https://npmmirror.com/mirrors/npm/
nvm node_mirror https://npmmirror.com/mirrors/node/
```

Method 2:

```shell
nvm npm_mirror http://mirrors.cloud.tencent.com/npm/
nvm node_mirror http://mirrors.cloud.tencent.com/nodejs-release/
```

### Install multiple versions of Node.js.

```shell
nvm install v6.9.5
nvm install v7.4.0
```

Run nvm ls to see the installed version of Node.js. This example uses version v7.4.0

```text
[root@iZXXXXZ .nvm]# nvm ls
         v6.9.5
->       v7.4.0
         system
stable -> 7.4 (-> v7.4.0) (default)
unstable -> 6.9 (-> v6.9.5) (default)
```

### Run `nvm use <version number>` to switch the Node.js version. For example, switch the Node.js version to v7.4.0. The following information is displayed.

```text
[root@iZXXXXZ .nvm]# nvm use v7.4.0
Now using node v7.4.0
```

## Note

Note The terminal may be opened again and the old version is gone. In this case, you need to perform the operation

```shell
nvm alias default v18.6.1 (default)
```

# Install `pm2`

```shell
npm i -g pm2
```
