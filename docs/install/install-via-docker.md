# 使用 Docker 部署本项目（Init via Docker）

## 从 GitHub 仓库地址拉取本项目

在你喜欢的目录下键入：
```shell
git clone https://github.com/Kyomotoi/ATRI.git
```
### 进入该目录，并配置`docker-compose.yml`，您将会看到以下内容：

```yaml
version: "3"

services:
  
  nonebot:
    
    build: .
    
    container_name: nonebot # 容器名称
    
    ports:
      - "20000:20000" # 映射端口到宿主机 宿主机端口:容器端口
    
    network_mode: bridge
    
    restart: unless-stopped
    
    volumes:
      - ~/.ATRI/data:/app/data              # 这几条为挂载ATRI的工作数据目录与配置文件
      - ~/.ATRI/accounts:/app/accounts      # 挂载格式为 宿主机路径:容器路径
      - ~/.ATRI/config.yml:/app/config.yml  # 默认的工作路径为 ~/.ATRI 注意该目录默认状态下是隐藏的
```

### 接下来请编辑 [`config.yml`](configuration-bot.md) 并将其复制到工作目录下

> 默认的工作目录为 `~/.ATRI` 如不存在，请 `mkdir`

### 请按您的处理器架构执行下列步骤

- X86 & AMD64
    
    ```shell
    sudo docker-compose up
    ```
- ARM64 & AARCH64

    ```shell
    cp ./Dockerfile ./Dockerfile_x86 && cp -f ./Dockerfile_aarch64 ./Dockerfile
    
    sudo docker-compose up
    ```

- Windows

    > 请自行修改 `dockerfile` 改为从 `Python:3.9` 开始构筑镜像

**首次部署时因为涉及到扫码登陆，请不要添加 `-d` 这一离屏参数，登陆成功后可以自行添加**

> 就算您使用 `protainer` 等容器管理器也应如此，二维码在 `protainer` 中不会正确显示

部署成功后，账号数据与 go-cqhttp 默认会保存在 `~/.ATRI/accounts` 中，日志等数据默认保存在  `~/.ATRI/data` 中