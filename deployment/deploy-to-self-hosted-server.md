# 自托管服务器自动化部署方案

将独立开发项目自动化部署到自己服务器的方案。

采用 `Github` +  `Github Actions` + `Docker (image + compose)` + `腾讯云镜像服务TCR`： 
- Github 托管代码
- Docker 打包镜像，使用 Docker Compose 容器部署服务
- 腾讯云镜像服务管理 Docker 镜像
- Github Actions 实现自动化部署流程（统筹实现）
	- 监听 main 分支源码和配置变更
	- 打包镜像并推送到腾讯云镜像服务
	- 登录腾讯云服务器实现容器更新

具体流程：
```
本地开发  
↓  
Push 到 Github  
↓  
Github Actions  
↓  
Docker Build  
↓  
Push 腾讯云 TCR  
↓  
SSH 登录服务器  
↓  
docker compose pull  
↓  
docker compose up -d  
↓  
零停机更新
```

