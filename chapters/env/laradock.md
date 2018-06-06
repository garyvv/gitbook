# laradock

[Laradock](http://laradock.io/) 努力简化创建开发环境过程。 它包含预包装 Docker 镜像，提供你一个美妙的开发环境而不需要安装 PHP, NGINX, MySQL 和其他任何软件在你本地机器上。

<extoc></extoc>

## 使用概览

1.克隆源代码：

```bash
> git clone https://github.com/Laradock/laradock.git ~/code/laradock

> cd ~/code/laradock
```


2.进入 Laradock 目录

```bash
> cp env-example .env
```

3.运行这些容器。

```bash
> docker-compose up -d nginx mysql redis workspace
```

4.进入容器。

```bash
> docker-compose exec workspace bash
```

---

## Docker 常用命令

### 构建容器

如果你改了任何 `Dockerfile` 文件，运行下面语句

```bash
> docker-compose build
```

或者指定重新构建哪一个容器

```bash
> docker-compose build {container-name}

# 例如 workspace
> docker-compose build workspace
```

使用 `--no-cache` 参数来完全重建

```bash
> docker-compose build --no-cache {container-name...}

# 例如 workspace
> docker-compose build --no-cache workspace
```

### 启动容器

```bash
> docker-compose up -d {container-name...} 

# 例如启动 nginx mysql redis workspace
> docker-compose up -d nginx mysql redis workspace
```

### 重启容器

```bash
> docker-compose restart {container-name...} 

# 例如启动 nginx mysql redis workspace
> docker-compose restart nginx mysql redis workspace
```

### 列出容器

```bash
> docker-compose ps
```

### 停止容器

```bash
# 停止全部
> docker-compose stop

# 停止指定容器
> docker-compose stop {container-name...}

```

### 删除所有存在的容器

```bash
docker-compose down
```

### 删除指定容器

```bash
> docker-compose rm -s -v {container-name...}
```

### 查看运行进程

```bash
> docker-compose top

# 查看指定容器进程
> docker-compose top {container-name...}
```

### 查看日志

```bash
> docker-compose logs

# 查看指定容器进程
> docker-compose logs -f {container-name...}
```

---

## 注意

容器间服务共享填写容器名称

例如要在 `nginx` 容器中 反向代理 `workspace` 中的 `query_service`

```nginx
// query.maka.com.conf

proxy_pass http://workspace:9502;

```

又或者在项目中用到 `mysql` 或者 `redis` 等服务 `host` 填写容器名称

例如在 `nodejs` 项目中

```js
  
  "redis": {
    host: 'redis',// 设置为 docker 容器
    'port': 6379
  },

```

在 `laravel` 中

```
// file .env

REDIS_HOST=redis
CACHE_DRIVER=redis
SESSION_DRIVER=redis

DB_HOST=mysql
```


## 开发步骤

## 开机运行环境

```bash
> docker-compose up -d nginx mysql redis workspace
```

## 进入容器操作

```bash
> docker-compose exec workspace bash
```


