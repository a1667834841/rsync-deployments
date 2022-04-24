# rsync deployments

远程同步文件且执行命令Action

借助 [rsync-deployments-action](https://github.com/marketplace/actions/rsync-deployments-action) 的CD功能，简单添加了下远程执行命令
自己的博客一直使用它的，但是有一天 我需要同步一个go应用到我的服务器，我想可以提交完代码后，将编译好的go的二进制文件同步到服务器上并启动（CD）,所以借助它做了下改动，希望也能帮助其他人吧

---

## Inputs

- `switches`* - The first is for any initial/required rsync flags, eg: `-avzr --delete` （rsync 中的命令 --delete：删除那些DST中SRC没有的文件）

- `rsh` - Remote shell commands（这个以为是远程命令，但是试了下，报错了）

- `path` - The source path. Defaults to GITHUB_WORKSPACE （需要传输的文件目录）

- `remote_path`* - The deployment target path（远程目录）

- `remote_host`* - The remote host（服务器端口）

- `remote_port` - The remote port. Defaults to 22（服务器ip）

- `remote_user`* - The remote user（服务器用户）

- `remote_key`* - The remote ssh key（服务器私钥）

- `remote_key_pass` - The remote ssh key passphrase (if any)
- 
- `cmd` - The remote ssh key （传输完之后需要执行的命令）


## Required secret(s)

This action needs secret variables for the ssh private key of your key pair. The public key part should be added to the authorized_keys file on the server that receives the deployment. The secret variable should be set in the Github secrets section of your org/repo and then referenced as the  `remote_key` input.

> Always use secrets when dealing with sensitive inputs!

For simplicity, we are using `DEPLOY_*` as the secret variables throughout the examples.

## Example usage

Simple:

```
name: DEPLOY
on:
  push:
    branches:
    - master

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: rsync deployments
      uses: burnett01/rsync-deployments@5.2
      with:
        switches: -avzr --delete
        path: src/
        remote_path: /var/www/html/
        remote_host: example.com
        remote_user: debian
        remote_key: ${{ secrets.DEPLOY_KEY }}
        cmd: df -h
```

Advanced:

```
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: rsync deployments
      uses: burnett01/rsync-deployments@5.2
      with:
        switches: -avzr --delete --exclude="" --include="" --filter=""
        path: src/
        remote_path: /var/www/html/
        remote_host: example.com
        remote_port: 5555
        remote_user: debian
        remote_key: ${{ secrets.DEPLOY_KEY }}
        cmd: df -h
```


