# MinIO

本目录维护本地开发用的单机版 MinIO 部署。

## 当前固定版本

- `quay.io/minio/minio:RELEASE.2025-09-07T16-13-09Z`
- `quay.io/minio/mc:RELEASE.2025-08-13T08-35-41Z`

## 本地准备

```bash
cd /Users/dingcm/Desktop/dev-infra/services/minio
cp .env.example .env
mkdir -p /Users/dingcm/Desktop/.docker/minio/data
```

然后把 `.env` 里的 `MINIO_ROOT_PASSWORD` 改成你自己的密码。

## 启动

```bash
docker compose --env-file .env up -d
```

## 验证

```bash
curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:9000/minio/health/live
docker compose --env-file .env logs --no-color minio-init
```

## 连接信息

- Endpoint: `http://127.0.0.1:9000`
- Console: `http://127.0.0.1:9001`
- Bucket: `.env` 里的 `MINIO_DEFAULT_BUCKET`
- Region: `us-east-1`
- S3 Path Style: `true`

## Access Key / Secret Key

当前 `.env` 里的这两个值本身就是一组可用的 root 凭证：

- `MINIO_ROOT_USER`: root access key
- `MINIO_ROOT_PASSWORD`: root secret key

如果只是本地临时验证，直接用这组即可。

如果是应用程序接入，建议不要直接使用 root，而是单独创建应用账号或额外 access key。

## 创建应用账号

先进入 `mc` 容器：

```bash
docker compose --env-file .env run --rm --entrypoint /bin/sh minio-init
```

然后执行：

```bash
mc alias set local http://minio:9000 "$MINIO_ROOT_USER" "$MINIO_ROOT_PASSWORD"

# 创建一个新的内置用户
mc admin user add local appuser 'AppUserSecret123!'

# 给它普通读写权限
mc admin policy attach local readwrite --user appuser
```

创建后，这组应用凭证就是：

- Access Key: `appuser`
- Secret Key: `AppUserSecret123!`

如果你想给这个用户控制台管理员权限，可以改成：

```bash
mc admin policy attach local consoleAdmin --user appuser
```

## 为现有用户生成额外 Access Key

如果你已经有一个用户，只是想再生成一组新的 access key / secret key 做轮换，可以执行：

```bash
mc alias set local http://minio:9000 "$MINIO_ROOT_USER" "$MINIO_ROOT_PASSWORD"

mc admin accesskey create local appuser \
  --access-key appuser-ak \
  --secret-key 'AnotherSecret123!'
```

如果不手动指定，MinIO 也可以自动生成：

```bash
mc admin accesskey create local appuser
```

## 说明

- 如果你之前用的是 `bitnami/minio`，UI 入口和官方 `quay.io/minio/minio` 不完全一样。
- 当前这套官方镜像更适合把用户和 access key 管理放在 `mc` CLI 里做。
- 生产或长期使用时，不要继续保留默认的 `minioadmin` / `minioadmin123`。

## 停止

```bash
docker compose --env-file .env down
```
