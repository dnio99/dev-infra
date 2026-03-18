# dev-infra

本仓库用于维护本地开发和自托管基础设施的部署文件，不存放运行时数据，也不提交真实密钥。

## 目录约定

```text
dev-infra/
├── services/
│   └── minio/
│       ├── docker-compose.yml
│       ├── .env.example
│       └── README.md
└── README.md
```

## 约定

- 所有镜像固定到明确版本，不使用 `:latest`
- 真实环境变量只保存在本地 `.env`
- 持久化数据统一落到 Desktop 下的 `.docker/`
- 每个服务目录都可以独立 `docker compose up -d`
