# Мониторинг K3s с Prometheus и Grafana
Этот репозиторий содержит конфигурацию Docker Compose для развертывания стека мониторинга с использованием Prometheus, Grafana и Node Exporter для наблюдения за кластером K3s. Стек собирает метрики с узлов K3s, cAdvisor, kube-state-metrics и API K3s, отображая их в дашбордах Grafana.

## Обзор
Этот проект разворачивает стек мониторинга, включающий:

**Prometheus**: База данных временных рядов для сбора и запроса метрик.  
**Grafana**: Платформа для визуализации данных и создания дашбордов.  
**Node Exporter**: Собирает системные метрики (ЦПУ, память, диск и т.д.).  
**cAdvisor и kube-state-metrics**: Мониторинг контейнеров и состояния кластера K3s.  
**K3s API**: Мониторинг API-сервера Kubernetes.  

Стек настроен для сбора метрик из кластера K3s и их визуализации в Grafana.  

## Инструкции по установке

1. Клонируйте репозиторий:
    - 'git clone https://github.com/your-username/your-repo.git'
    - 'cd your-repo'

2. Поместите ваш токен K3s в файл secrets/k3s_token  

3. Настройте prometheus.yaml:
    - Откройте prometheus.yaml и замените <node-ip> на реальные IP-адреса ваших узлов.
    - Убедитесь, что путь /metrics/cadvisor и /metrics для K3s API доступен.

4. Запустите стек:
    - 'docker-compose up -d'

## Детали конфигурации
Файл docker-compose.yaml определяет три сервиса:
- Prometheus: Слушает на порту 9090, использует конфигурацию из prometheus.yaml и токен K3s.
- Grafana: Слушает на порту 3000, использует постоянное хранилище для данных и проверяет здоровье Prometheus.
- Node Exporter: Слушает на порту 9100, собирает системные метрики хоста, исключая определенные точки монтирования.

Файл prometheus.yaml настраивает сбор метрик с интервалом 15 секунд. Он включает:  

- Мониторинг мастер-узла и слейв-узла K3s через Node Exporter.
- Мониторинг локального сервера Prometheus.
- Мониторинг cAdvisor и K3s API через HTTPS с токеном аутентификации.
- Мониторинг kube-state-metrics на порту 30080.

## Доступ к сервисам  

- Prometheus: 'http://<host-ip>:9090'  
- Grafana: 'http://<host-ip>:3000' (логин: admin, пароль: admin)  
- Node Exporter: 'http://<host-ip>:9100/metrics'  

В Grafana добавьте Prometheus как источник данных:
URL: 'http://prometheus:9090'