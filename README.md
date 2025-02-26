# SVODD Monitoring

Проект для мониторинга сервисов SVODD с использованием Prometheus и Grafana.

## Описание

Этот проект развертывает стек мониторинга в Docker Swarm, включающий:
- **Prometheus** — система сбора и хранения метрик.
- **Grafana** — платформа для визуализации метрик.

Сервисы доступны через Traefik по следующим адресам:
- Prometheus: `https://prometheus.svodd.ru`
- Grafana: `https://grafana.svodd.ru`

## Требования

- Docker Swarm с настроенным Traefik.
- Узел с меткой `frontend-1` для размещения сервисов.

## Установка

1. Склонируйте репозиторий:
  
```bash
git clone https://github.com/terratensor/svodd-monitoring.git
cd svodd-monitoring
```
2. Разверните стек в Docker Swarm:

```bash
docker stack deploy -c docker-compose.yml svodd-monitoring
```

3. Проверьте, что сервисы запущены:

```bash
docker service ls
```

## Настройка Prometheus

Конфигурация Prometheus находится в файле prometheus/prometheus.yml. Добавьте целевые сервисы для сбора метрик:

```yaml
scrape_configs:
  - job_name: 'svodd-services'
    static_configs:
      - targets: ['svodd-service1:8000', 'svodd-service2:8000']
```      

### Настройка Grafana

1. Войдите в Grafana по адресу https://grafana.svodd.ru.

    - Логин: admin
    - Пароль: admin

2. Настройте источник данных (Data Source):

    - Перейдите в Configuration > Data Sources.

    - Добавьте новый источник данных с типом Prometheus.

    - Укажите URL: http://prometheus:9090.

3. Создайте дашборды для визуализации метрик.

### Локальная разработка

Для локальной разработки используйте `docker-compose`:

```bash
docker-compose up -d
```