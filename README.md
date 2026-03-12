上传dockerfile会使用Github Action自动打包镜像并推送到镜像仓库，解决国内很多拉不动依赖的问题，免费易用
支持任意docker仓库。阿里云、harbor、nexus自建都可以。
国内推荐使用阿里云的官方线路，速度快。

需要创建4个变量

命名空间（ALIYUN_NAME_SPACE）
用户名（ALIYUN_REGISTRY_USER)
密码（ALIYUN_REGISTRY_PASSWORD)
仓库地址（ALIYUN_REGISTRY）
Fork本项目

启动Action
进入您自己的项目，点击Action，启用Github Action功能

配置环境变量
进入Settings->Secret and variables->Actions->New Repository secret
将四个值
ALIYUN_NAME_SPACE,ALIYUN_REGISTRY_USER，ALIYUN_REGISTRY_PASSWORD，ALIYUN_REGISTRY
配置成环境变量
打开docker-build.config文件，配置你想要的镜像名和tag，也可以不用(默认latest)
