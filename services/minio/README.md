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

## 停止

```bash
docker compose --env-file .env down
```
