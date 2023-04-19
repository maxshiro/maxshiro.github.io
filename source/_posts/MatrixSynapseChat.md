---
title: Centos安装MatrixSynapseChat简易教程
date: 2022-08-28 21:29:07
cover: img/MatrixSynapseChat/20220920214137.png
tags:
- nextcloud
- 聊天室
- python
---
## 先安装前置
```bash
yum install libtiff-devel libjpeg-devel libzip-devel freetype-devel libwebp-devel libxml2-devel libxslt-devel libpq-devel python3-virtualenv libffi-devel openssl-devel python3-devel
yum groupinstall "Development Tools"
```

## 使用root账户接入您的centos，找到合适的位置开始操作
```bash
mkdir syn
```
### 依次执行以下指令
```bash
virtualenv -p python3 ./env && source ./env/bin/activate
python3 -m pip install --upgrade pip && python3 -m pip install --upgrade setuptools && python3 -m pip install psycopg2-binary
python3 -m pip install matrix-synapse                         //安装synapse
python3 -m pip install -U matrix-synapse                    //更新synapse
```
## 将下列指令拷贝到控制台执行，自动生成homeserver.yaml
```bash
python3 -m synapse.app.homeserver \
    --server-name im.axro.top \
    --config-path homeserver.yaml \
    --generate-config \
    --report-stats=no
```

## 编辑生成`homeserver.yaml`
```bash
enable_registration: true
suppress_key_server_warning: true
bind_addresses: ['::1', '0.0.0.0']
```
## 启动/关闭服务器
#### 注意要先进入虚拟python环境
```bash
source ./env/bin/activate

synctl start
synctl stop
```
## 退出虚拟环境
```bash
deactivate
```

## 配置反代

进入宝塔面板新建静态网站，配置ssl，配置反向代理地址为`https://localhost:8008`,保存即可.
