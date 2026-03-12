# 使用GitHub Action自动构建推送Docker镜像（解决国内依赖拉取问题）
该方案通过GitHub Action自动化打包Docker镜像并推送到任意镜像仓库（阿里云/Harbor/Nexus等），**核心解决国内拉取依赖慢/拉取不到的问题**，免费易用，国内优先推荐阿里云镜像仓库（官方线路速度快）。

## 一、需配置的4个仓库Secret变量
需在GitHub仓库中创建以下4个Action密钥变量（变量名严格区分大小写），用于镜像仓库认证：

| 变量名                  | 变量说明                     | 阿里云示例值                     |
|-------------------------|------------------------------|----------------------------------|
| **ALIYUN_NAME_SPACE**   | 镜像仓库的命名空间           | my-docker-space                  |
| **ALIYUN_REGISTRY_USER** | 镜像仓库登录用户名           | 12345678@aliyun.com              |
| **ALIYUN_REGISTRY_PASSWORD** | 镜像仓库登录密码/访问令牌 | xxxxxxxxxx（阿里云镜像仓库密码）  |
| **ALIYUN_REGISTRY**     | 镜像仓库地址（含域名/端口）  | registry.cn-hangzhou.aliyuncs.com |

## 二、使用步骤
### 1. Fork本项目
将模板项目Fork到你的GitHub账号下，作为自动化构建的基础。

### 2. 启用GitHub Action
进入你Fork后的项目页面 → 点击顶部**Actions** → 首次进入需点击「I understand my workflows, go ahead and enable them」启用Action功能。

### 3. 配置环境变量
1. 进入项目页面 → 点击**Settings**（设置）；
2. 左侧菜单选择「Secrets and variables」→ 「Actions」；
3. 点击「New repository secret」，依次创建上述4个变量（变量名和值一一对应）。

### 4. 配置镜像名/Tag（可选）
仓库根目录下的`docker-build.config`文件用于自定义镜像名称和Tag，不配置则默认`IMAGE_NAME=default-app`、`TAG=latest`：
```ini
# docker-build.config 配置示例（注释：#开头为注释行）
IMAGE_NAME=my-business-app  # 自定义镜像名
IMAGE_TAG=1.0.0             # 自定义镜像版本Tag
# 若仅保留IMAGE_NAME，Tag会默认使用latest
# IMAGE_NAME=my-business-app


5. 复制文件的方法（可选）
在Dockerfile中通过COPY指令将仓库内的文件 / 目录复制到镜像中，常用场景如下：
场景 1：复制单个文件（基础）
dockerfile
# 示例：将仓库根目录的app.jar复制到镜像内/app目录（指定文件名）
COPY app.jar /app/app.jar

# 简化：若WORKDIR已设为/app，可直接写目标文件名
WORKDIR /app
COPY app.jar ./app.jar  # 等价于 COPY app.jar /app/app.jar
COPY app.jar .          # 等价于 COPY app.jar /app/app.jar（省略文件名则沿用源文件名）
场景 2：复制多个文件
dockerfile
# 示例1：复制多个零散文件到镜像同一目录
COPY app.jar start.sh /app/  # 仓库根目录的app.jar、start.sh → 镜像/app目录

# 示例2：复制不同目录的文件到镜像指定目录
COPY config/application.yml scripts/start.sh /app/  # 仓库config/和scripts/下的文件 → 镜像/app目录
场景 3：复制整个目录
dockerfile
# 示例1：复制仓库根目录的config目录 → 镜像/app/config目录（自动创建目标目录）
COPY config/ /app/config/

# 示例2：复制仓库scripts目录下所有内容 → 镜像/app/scripts目录
WORKDIR /app
COPY scripts/ ./scripts/

# 注意：源目录结尾加/表示复制目录内所有内容，不加/则复制目录本身
COPY config /app/  # 仓库config目录 → 镜像/app/config目录
COPY config/ /app/ # 仓库config目录内的所有文件 → 镜像/app目录（无config子目录）
