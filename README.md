# Мониторинг стека с Prometheus + Grafana

Развёртывание полноценного стека мониторинга с помощью Docker Compose.
Стек включает Prometheus для сбора метрик, Pushgateway для приёма метрик от внешних источников и Grafana для визуализации.

![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=flat&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?style=flat&logo=grafana&logoColor=white)

---

## Архитектура

```
┌─────────────────────────────────────────────────┐
│           salih-i-my-netology-hw                │
│                (bridge, 10.5.0.0/16)            │
│                                                 │
│  ┌─────────────┐     ┌───────────────────────┐  │
│  │ Pushgateway │────▶│      Prometheus        │  │
│  │  :9091      │     │       :9090            │  │
│  └─────────────┘     └──────────┬────────────┘  │
│                                 │               │
│                       ┌─────────▼──────────┐    │
│                       │      Grafana        │    │
│                       │    :80 (→3000)      │    │
│                       └────────────────────┘    │
└─────────────────────────────────────────────────┘
```

---

## Сервисы

| Сервис | Образ | Порт | Назначение |
|---|---|---|---|
| `prometheus` | `prom/prometheus:latest` | 9090 | Сбор и хранение метрик |
| `pushgateway` | `prom/pushgateway:latest` | 9091 | Приём метрик от batch-задач |
| `grafana` | `grafana/grafana:latest` | 80 | Визуализация и дашборды |

---

## Структура проекта

```
Docker_compose/
├── docker-compose.yml        # Описание сервисов
├── prometheus/
│   └── prometheus.yml        # Конфигурация Prometheus (targets, scrape interval)
└── grafana/
    └── custom.ini            # Кастомная конфигурация Grafana
```

---

## Быстрый старт

### 1. Клонировать репозиторий

```bash
git clone https://github.com/IlyaSalih/Docker_compose.git
cd Docker_compose
```

### 2. Запустить стек

```bash
docker compose up -d
```

### 3. Проверить что контейнеры запущены

```bash
docker compose ps
```

### 4. Открыть веб-интерфейсы

| Сервис | URL |
|---|---|
| Grafana | http://localhost:80 |
| Prometheus | http://localhost:9090 |
| Pushgateway | http://localhost:9091 |

> Логин по умолчанию в Grafana: `admin` / `admin`

---

## Остановка

```bash
# Остановить контейнеры (данные сохраняются)
docker compose down

# Остановить и удалить volumes с данными
docker compose down -v
```

---

## Особенности реализации

- Все сервисы объединены в изолированную bridge-сеть с подсетью `10.5.0.0/16`
- Данные Prometheus и Grafana хранятся в named volumes — не теряются при перезапуске
- Grafana поднимается с кастомным `custom.ini` вместо дефолтной конфигурации
- Все сервисы имеют политику `restart: always` — автоматический старт после перезагрузки хоста
- Grafana проксируется с порта 3000 на стандартный 80

---

## Технологии

- [Docker Compose](https://docs.docker.com/compose/)
- [Prometheus](https://prometheus.io/docs/)
- [Grafana](https://grafana.com/docs/)
- [Pushgateway](https://github.com/prometheus/pushgateway)