# Задание 2. HPA: по памяти и по RPS


## Часть 1. HPA по памяти

- `01-deployment.yaml` — Deployment `scaletestapp`: 1 реплика, memory limit 30Mi.
- `02-service.yaml` — Service NodePort 30080 (порт `http`).
- `03-hpa-memory.yaml` — HPA v2: память, averageUtilization 80%, maxReplicas 10.
- `locustfile.py` — нагрузочный тест: GET `/`, пауза между запросами 1–5 с.
- `part1-HPA-память-логи.txt` — логи смены числа реплик под нагрузкой 500 users:
  под нагрузкой количество подов росло 1, затем 2, 3, 4.

## Часть 2. HPA по RPS через Prometheus

- `04-prometheus-scrapejob-values.yaml` — values для Prometheus
  (helm release `prom`, ns `monitoring`): `extraScrapeConfigs` со scrape-джобом подов
  приложения, интервал 15 с, relabel меток `namespace` и `pod`.
- `06-prometheus-adapter-values.yaml` — values для prometheus-adapter
  (helm release `adapter`): кастомная метрика `rps`, считается как
  `sum(rate(http_requests_total[2m])) by (namespace, pod)`.
- `05-hpa-rps.yaml` — итоговая версия HPA: тип метрики `Pods`, метрика `rps`,
  averageValue «100» (цель 100 rps на под), maxReplicas 10.
- `part2-HPA-RPS-логи.txt` — логи: целевое значение выросло до 126076m/100,
  HPA добавил вторую реплику, затем метрика стабилизировалась около 66.5k/100.
- `prometheus-graph-rps.png` — скриншот Prometheus UI, вкладка Graph: RPS по подам.
- `prometheus-targets.png` — скриншот Prometheus UI, вкладка Targets:
  scrape-джоб `scaletestapp` работает (UP).
