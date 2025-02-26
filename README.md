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



3. Пример использования метрики
Теперь в Prometheus вы сможете видеть метрику rss_parser_error_requests_total с метками:

url: URL RSS-ленты.

error_type: Тип ошибки (например, connection reset by peer).

attempt: Номер попытки (например, 1, 2, 3).

Пример запроса в Prometheus:

promql
Copy
rate(rss_parser_error_requests_total{url="http://example.com/rss"}[1m])
4. Визуализация в Grafana
Создайте дашборд в Grafana для анализа повторных попыток. Например:

a) Количество ошибок по попыткам
Запрос:

promql
Copy
sum(rate(rss_parser_error_requests_total{url="http://example.com/rss"}[1m])) by (attempt)
График покажет, на каких попытках чаще всего возникают ошибки.

b) Средний номер попытки для успешного запроса
Запрос:

promql
Copy
avg(attempt) by (url)
Этот график покажет, на какой попытке запросы чаще всего завершаются успешно.

c) Алерт на высокое количество повторных попыток
Настройте алерт, если средний номер попытки превышает определенный порог:

promql
Copy
avg(attempt) by (url) > 3
5. Дополнительные идеи для метрик
Количество успешных запросов по попыткам:

Добавьте метку attempt в SuccessRequests, чтобы отслеживать, на какой попытке запросы завершаются успешно. — реализовано

Время между попытками:

Добавьте метрику для отслеживания времени между повторными запросами.

Общее количество попыток:

Добавьте метрику, которая считает общее количество попыток для каждого URL.

Статус последней попытки:

Добавьте метрику, которая показывает, завершилась ли последняя попытка успешно или с ошибкой.