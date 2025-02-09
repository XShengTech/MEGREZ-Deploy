# 主程序

> [!NOTE]
> 即后端服务

## 1. 安装依赖

### 1.1. 安装

```bash
sudo apt update && sudo apt install git
```

### 1.2. 安装 docker

```bash
sudo curl -sSL get.docker.com | sh

# 国内用户可以使用以下命令
sudo curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

## 2. 下载部署配置

```bash
git clone https://github.com/XShengTech/MEGREZ-Deploy.git

# 国内用户可以使用以下命令
git clone https://openi.pcl.ac.cn/XShengTech/MEGREZ-Deploy.git

cd MEGREZ-Deploy/megrez
```

## 3. 启动主程序

```bash
docker compose up -d
```
