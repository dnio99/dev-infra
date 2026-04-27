# Bitnami MinIO

本目录维护本地开发用的 Bitnami 变体 MinIO 部署，和 `services/minio/` 的官方镜像方案彼此独立。

## 当前固定版本

- `bitnamilegacy/minio:2025.7.23-debian-12-r5`

## 为什么默认不是 `bitnami/minio`

- Bitnami 当前公开仓库的 `bitnami/minio` 不再提供可见的 immutable tags。
- 本仓库要求镜像固定到明确版本，不使用 `:latest`。
- 因此这里默认钉到仍然公开可拉取、可固定版本的 `bitnamilegacy/minio`。

如果你本地有 Bitnami Secure Images 访问权限，也可以把 `.env` 里的 `MINIO_IMAGE` 改成你自己的 `bitnami/minio` 镜像引用。

## 本地准备

```bash
cd /Users/dingcm/Desktop/dnio/dev-infra/services/minio-bitnami
cp .env.example .env
mkdir -p /Users/dingcm/Desktop/.docker/minio-bitnami/data
```

然后把 `.env` 里的 `MINIO_ROOT_PASSWORD` 改成你自己的密码。

如果你在 Linux 上遇到写入权限问题，再执行：

```bash
sudo chown -R 1001:1001 /Users/dingcm/Desktop/.docker/minio-bitnami/data
```

## 启动

```bash
docker compose --env-file .env up -d
```

## 验证

```bash
curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:9000/minio/health/live
docker compose --env-file .env logs --no-color minio
```

## 连接信息

- Endpoint: `http://127.0.0.1:9000`
- Console: `http://127.0.0.1:9001`
- Bucket: `.env` 里的 `MINIO_DEFAULT_BUCKETS`
- Region: `us-east-1`
- S3 Path Style: `true`

## Access Key / Secret Key

当前 `.env` 里的这两个值本身就是一组可用的 root 凭证：

- `MINIO_ROOT_USER`: root access key
- `MINIO_ROOT_PASSWORD`: root secret key

如果只是本地临时验证，直接用这组即可。

## Console 说明

- `MINIO_BROWSER=on` 时会启用 Bitnami 打包的 Web Console。
- 这套 UI 和官方 `quay.io/minio/minio` 的 Console 不完全一样，但日常本地查看 bucket、对象和用户配置会更直观。

## 使用 `mc`

Bitnami 镜像里自带 `mc`，可以直接进入容器执行：

```bash
docker compose --env-file .env exec minio /opt/bitnami/minio-client/bin/mc alias set local http://127.0.0.1:9000 "$MINIO_ROOT_USER" "$MINIO_ROOT_PASSWORD"
docker compose --env-file .env exec minio /opt/bitnami/minio-client/bin/mc ls local
```

如果你想创建额外 bucket，可以执行：

```bash
docker compose --env-file .env exec minio /opt/bitnami/minio-client/bin/mc mb --ignore-existing local/another-bucket
```

## 停止

```bash
docker compose --env-file .env down
```
