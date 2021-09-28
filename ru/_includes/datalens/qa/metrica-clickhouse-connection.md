### Как из {{ datalens-short-name }} подключиться к Яндекс.Метрике? {#metrica-clickhouse-connection}

{{ datalens-short-name }} может подключаться к Яндекс.Метрике одним из способов:
* Напрямую (см. [Веб-аналитика с подключением к Яндекс.Метрике](../../../datalens/solutions/data-from-metrica-visualization.md)). В этом случае будут доступны не все аналитические функции над данными (обращение происходит через API, а оно имеет ограниченную функциональность). Вероятно на больших объемах API будет медленно отвечать на запросы. Если использовать API очень активно, то можно превысить в лимиты API.
* Через Logs API с выгрузкой в {{ CH }} (см. [Веб-аналитика с подключением к Яндекс.Метрике через Logs API](../../../datalens/solutions/data-from-metrica-logsapi-visualization.md)). В этом случае вам нужно создать кластер {{ CH }} в {{ yandex-cloud }}.

Также вы можете самостоятельно настроить экспорт метрики в свою БД с помощью скриптов и подключиться к этой БД с помощью {{ datalens-short-name }}.