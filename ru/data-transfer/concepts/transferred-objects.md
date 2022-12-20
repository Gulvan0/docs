---
title: "Объекты, переносимые трансфером"
description: "{{ data-transfer-full-name }} позволяет легко переносить данные таблиц, пустые объекты и представления."
---
# Объекты, переносимые трансфером

Основными объектами, которые переносятся во время трансфера, являются таблицы и схемы данных. 

Помимо этого, для определённых типов эндпоинтов могут переноситься [пустые объекты](#features-common-processing-of-empty-objects) или [представления](#features-common-processing-of-views).

## Обработка пустых объектов {#features-common-processing-of-empty-objects}

Трансферы _между эндпоинтами разных типов_ (например, из {{ PG }} в {{ CH }}) переносят только непустые объекты. Например, таблицы (в реляционных базах данных), не содержащие кортежей или содержащие нулевое число полей, не переносятся.

Трансферы _между эндпоинтами одного типа_ (например, из {{ PG }} в {{ PG }}) переносят пустые объекты как часть схемы.

## Обработка представлений (`VIEW`) {#features-common-processing-of-views}

В общем случае {{ data-transfer-full-name }} переносит `VIEW` (из баз, где такие объекты могут существовать) с некоторыми ограничениями:

* Трансферы типа {{ dt-type-repl }} не реплицируют изменения над данными `VIEW`;
* Трансферы типов {{ dt-type-copy }} и {{ dt-type-copy-repl }} (на стадии копирования) _между эндпоинтами одного типа_ (например, из {{ PG }} в {{ PG }}) переносят `VIEW` только как часть схемы. Данные (строки) в `VIEW` не переносятся. Перенос схемы регулируется настройкой "Перенос схемы" и смежными настройками, доступными в некоторых эндпоинтах-источниках;
* Трансферы типов {{ dt-type-copy }} и {{ dt-type-copy-repl }} (на стадии копирования) _между эндпоинтами разных типов_ (например, из {{ PG }} в {{ CH }}) переносят `VIEW` как обыкновенные таблицы (не как представления). Эта функция позволяет трансформировать и экспортировать данные во внешние базы данных и может быть особенно полезна в регулярных трансферах типа {{ dt-type-copy }}.

Отдельные источники могут налагать дополнительные ограничения на перенос `VIEW` и аналогичных объектов. Дополнительную информацию о работе с представлениями конкретных источников см. в разделе [Работа {{ data-transfer-full-name }} с источниками и приемниками](work-with-endpoints.md).