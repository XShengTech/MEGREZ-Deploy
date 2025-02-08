# MEGREZ 部署教程

## 目录

* [主程序](#主程序)
* [被控端](#被控端)

## 主程序

> [!NOTE]
> 即后端服务

### 1. 安装依赖

#### 1.1. 安装

```bash
sudo apt update && sudo apt install git
```

#### 1.2. 安装 docker

```bash
sudo curl -sSL get.docker.com | sh

# 国内用户可以使用以下命令
sudo curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

### 2. 下载部署配置

```bash
git clone https://github.com/XShengTech/MEGREZ-Deploy.git

# 国内用户可以使用以下命令
git clone https://openi.pcl.ac.cn/XShengTech/MEGREZ-Deploy.git

cd MEGREZ-Deploy/megrez
```

### 3. 启动主程序

```bash
docker compose up -d
```


## 被控端

> [!NOTE]
> 即被控制的机器

### 1. 安装依赖

#### 1.1. 安装依赖

```bash
sudo apt update && sudo apt install git lxcfs
```

#### 1.2. 安装 docker

```bash
sudo curl -sSL get.docker.com | sh

# 国内用户可以使用以下命令
sudo curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

#### 1.3 安装 NVIDIA 驱动

[NVIDIA Linux Driver](https://www.nvidia.com/en-us/drivers/unix/)

#### 1.4 安装 NVIDIA Container Toolkit

```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

```bash
sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
```

```bash
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

### 2. 配置环境

#### 2.1 配置 XFS 分区

```bash
sudo mkfs.xfs /dev/sdb    # 例如 /dev/sdb
```

修改 `/etc/fstab` 添加 XFS 分区

> [!IMPORTANT]
> 一定要添加 `prjquota` 参数

```bash
/dev/sdb /path/to/docker/data xfs defaults,prjquota 0 0
```

挂载 XFS 分区

```bash
sudo mount -a
```

#### 2.2 配置 Docker 存储路径至 XFS 文件系统分区

修改 `/etc/docker/daemon.json` 添加 `data-root` 字段

```json
{
    "data-root": "/path/to/docker/data" // 例如 "/data/docker"
}
```

#### 2.3 配置 Docker 镜像加速

修改 `/etc/docker/daemon.json` 添加 `registry-mirrors` 字段

```json
{
    "registry-mirrors": ["https://docker.1panelproxy.com"]
}
```

#### 2.4 重启 Docker

```bash
sudo systemctl restart docker
```

查看 Docker 状态

```bash
docker info | grep 'Docker Root Dir'
```

返回 `Docker Root Dir: /path/to/docker/data`

```bash
docker info
```

返回 `Registry Mirrors: https://docker.1panelproxy.com` 即可


#### 2.5 配置 CDI 设备

启用 Docker 的 CDI 特性

```bash
sudo nvidia-ctk runtime configure --runtime=docker --cdi-enabled
systemctl restart docker
```

生成 CDI 设备配置

```bash
sudo nvidia-ctk cdi generate --output=/var/run/cdi/nvidia.yaml
```

查看 CDI 设备配置

```bash
nvidia-ctk cdi list
```

有如下返回即可

```bash
INFO[0000] Found 17 CDI devices                         
nvidia.com/gpu=0
nvidia.com/gpu=1
nvidia.com/gpu=2
nvidia.com/gpu=3
nvidia.com/gpu=4
nvidia.com/gpu=5
nvidia.com/gpu=6
nvidia.com/gpu=7
nvidia.com/gpu=GPU-23bb08b6-****-****-****-************
nvidia.com/gpu=GPU-5f996fb2-****-****-****-************
nvidia.com/gpu=GPU-a55f05cc-****-****-****-************
nvidia.com/gpu=GPU-b3d3f52f-****-****-****-************
nvidia.com/gpu=GPU-c8d9b1fb-****-****-****-************
nvidia.com/gpu=GPU-c94df367-****-****-****-************
nvidia.com/gpu=GPU-cddc6468-****-****-****-************
nvidia.com/gpu=GPU-ea606b9e-****-****-****-************
nvidia.com/gpu=all
```

### 3. 下载部署配置

```bash
git clone https://github.com/XShengTech/MEGREZ-Deploy.git

# 国内用户可以使用以下命令
git clone https://openi.pcl.ac.cn/XShengTech/MEGREZ-Deploy.git

cd MEGREZ-Deploy/controler
```

修改 `docker-compose.yml` 文件中的 `gpu-docker-api` 的 `environment` 字段的 `APIKEY` 为任意字符串

```yaml
    environment:
      - APIKEY=CHANGETHIS # 修改为任意字符串
```
修改 `docker-compose.yml` 文件中的 `gpu-docker-api` 的 `volumes` 字段的 `PATH_TO_DOCKER_STORAGE` 为 Docker 存储路径

```yaml
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /path/to/docker/data:/path/to/docker/data
```


### 4. 启动被控端

```bash
docker compose up -d
```