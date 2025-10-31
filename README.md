# 🛒 Groceries Demo (Magento 2)

This is a **Magento 2 demo store** built with [Warden](https://warden.dev/), designed for local development using Docker and Traefik.

---

## 🚀 Tech Stack

- **Magento**: 2.4.x
- **PHP**: 8.3 (FPM & Xdebug)
- **MariaDB**: 10.6
- **Redis**, **RabbitMQ**, **OpenSearch**
- **Varnish** for full-page caching
- **Traefik** for HTTPS routing
- **Warden** for environment orchestration

---

## 🧩 Requirements

Make sure you have these installed before setup:

- Docker Desktop (with WSL2 backend)
- Warden (`brew install wardenenv/warden/warden`)
- PHP CLI (optional, for manual commands)
- Composer

---

## ⚙️ Setup Instructions

```bash
git clone https://github.com/nhantt71/groceries-demo.git
cd groceries-demo

# Start environment
warden svc up

# Create Magento project (if not yet created)
META_PACKAGE=magento/project-community-edition META_VERSION=2.4.x
composer create-project --repository-url=https://repo.magento.com/ \
  "${META_PACKAGE}" /tmp/exampleproject "${META_VERSION}"

rsync -a /tmp/exampleproject/ /var/www/html/
rm -rf /tmp/exampleproject/

# Install Magento
bin/magento setup:install \
  --backend-frontname=admin \
  --db-host=db \
  --db-name=magento \
  --db-user=magento \
  --db-password=magento \
  --search-engine=opensearch \
  --opensearch-host=opensearch \
  --opensearch-port=9200 \
  --session-save=redis \
  --session-save-redis-host=redis

# Reindex & flush cache
bin/magento indexer:reindex
bin/magento cache:flush
