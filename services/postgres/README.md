# Postgres

本目录维护本地开发用的单机版 PostgreSQL 部署。

## 当前固定版本

- `postgres:16.13`

## 本地准备

```bash
cd /Users/dnio_bot/Desktop/docker/dev-infra/services/postgres
cp .env.example .env
mkdir -p /Users/dnio_bot/Desktop/.docker/postgres/data
```

然后把 `.env` 里的 `POSTGRES_PASSWORD` 改成你自己的密码。

如果你的桌面路径不是 `/Users/dnio_bot/Desktop`，记得同步修改 `POSTGRES_DATA_DIR`。

## 启动

```bash
docker compose --env-file .env up -d
```

## 验证

```bash
docker compose --env-file .env ps
docker compose --env-file .env logs --no-color postgres
```

如果要做一个最小连接验证，可以执行：

```bash
docker compose --env-file .env exec postgres \
  psql -U appuser -d appdb -c 'select current_database(), current_user;'
```

## 连接信息

- Host: `127.0.0.1`
- Port: `.env` 里的 `POSTGRES_PORT`
- Database: `.env` 里的 `POSTGRES_DB`
- User: `.env` 里的 `POSTGRES_USER`
- Password: `.env` 里的 `POSTGRES_PASSWORD`

## 连接串示例

```text
postgresql://appuser:change-me@127.0.0.1:5432/appdb
```

## 停止

```bash
docker compose --env-file .env down
```
