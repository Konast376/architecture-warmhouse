# Project_template

# Задание 1. Анализ и планирование

<aside>

Чтобы составить документ с описанием текущей архитектуры приложения, можно часть информации взять из описания компании и условия задания. Это нормально.

</aside>

### 1. Описание функциональности монолитного приложения

**Управление отоплением:**

- Пользователи могут удалённо включать/выключать отопление в своих домах.

**Мониторинг температуры:**

- Система получает данные о температуре с датчиков, установленных в домах. 
- Пользователи могут просматривать текущую температуру в своих домах через веб-интерфейс.

### 2. Анализ архитектуры монолитного приложения

Перечислите здесь основные особенности текущего приложения: какой язык программирования используется, какая база данных, как организовано взаимодействие между компонентами и так далее.

Язык программирования: Go
База данных: PostgreSQL (взаимодействие с БД реализовано с помощью библиотеки pgxpool)
Архитектура: Монолитная, все компоненты системы (обработка запросов, бизнес-логика, работа с данными) находятся в рамках одного приложения.
Взаимодействие: Синхронное, запросы обрабатываются последовательно.


### 3. Определение доменов и границы контекстов

- Управление отоплением (управление отоплением в доме: включение/выключение, изменение режимов и т.д.)
- Мониторинг температуры (сбор, хранение и предоставление данных о температуре в доме)

### **4. Проблемы монолитного решения**

Масштабируемость: Ограничена, так как монолит сложно масштабировать по частям.
Развертывание: Требует остановки всего приложения.

### 5. Визуализация контекста системы — диаграмма С4

Добавьте сюда диаграмму контекста в модели C4.

[Диаграмма контекста монолита](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/context/monolith_context.puml)

# Задание 2. Проектирование микросервисной архитектуры

В этом задании вам нужно предоставить только диаграммы в модели C4. Мы не просим вас отдельно описывать получившиеся микросервисы и то, как вы определили взаимодействия между компонентами To-Be системы. Если вы правильно подготовите диаграммы C4, они и так это покажут.

**Диаграмма контейнеров (Containers)**

[Диаграмма контейнеров в микросервисной архитектуре](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/containers/microservices_containers.puml)

**Диаграмма компонентов (Components)**

[Диаграмма компонента api-gateway-service](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/components/api_gateway_service_components.puml)
[Диаграмма компонента auth-service](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/components/auth_service_components.puml)
[Диаграмма компонента cctv-monitoring](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/components/cctv_monitoring_components.puml)
[Диаграмма компонента device-registry-service](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/components/device_registry_components.puml)
[Диаграмма компонента real-estate-service](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/components/real_estate_service_components.puml)
[Диаграмма компонента gate-control](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/components/gate_control_components.puml)
[Диаграмма компонента heating-control](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/components/heating_control_components.puml)
[Диаграмма компонента lighting-control](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/components/lighting_control_components.puml)

**Диаграмма кода (Code)**

[Диаграмма кода gate-control](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/code/gate_control_code.puml)

# Задание 3. Разработка ER-диаграммы

[ER диаграмма](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/diagrams/ER/ER_diagram.puml)

# Задание 4. Создание и документирование API

### 1. Тип API

Укажите, какой тип API вы будете использовать для взаимодействия микросервисов. Объясните своё решение.

Для взаимодействия пользователя с элементами приложения выбрал REST API, т.к. пользователю требуется управление устройствами в реальном времени.
Для сбора метрик умных устройств выбран AsyncAPI. Это позволит обновлять данные (например, изменение температуры) в фоновом режиме.

### 2. Документация API

Здесь приложите ссылки на документацию API для микросервисов, которые вы спроектировали в первой части проектной работы. Для документирования используйте Swagger/OpenAPI или AsyncAPI.

[SWAGGER example](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/documentation/swagger_example.yaml)

[AsyncApi example](https://github.com/Konast376/architecture-warmhouse/blob/warmhouse/apps/documentation/async-api-example.json)

# Задание 5. Работа с docker и docker-compose

Перейдите в apps.

Там находится приложение-монолит для работы с датчиками температуры. В README.md описано как запустить решение.

Вам нужно:

1) сделать простое приложение temperature-api на любом удобном для вас языке программирования, которое при запросе /temperature?location= будет отдавать рандомное значение температуры.

Locations - название комнаты, sensorId - идентификатор названия комнаты

```
	// If no location is provided, use a default based on sensor ID
	if location == "" {
		switch sensorID {
		case "1":
			location = "Living Room"
		case "2":
			location = "Bedroom"
		case "3":
			location = "Kitchen"
		default:
			location = "Unknown"
		}
	}

	// If no sensor ID is provided, generate one based on location
	if sensorID == "" {
		switch location {
		case "Living Room":
			sensorID = "1"
		case "Bedroom":
			sensorID = "2"
		case "Kitchen":
			sensorID = "3"
		default:
			sensorID = "0"
		}
	}
```

2) Приложение следует упаковать в Docker и добавить в docker-compose. Порт по умолчанию должен быть 8081

3) Кроме того для smart_home приложения требуется база данных - добавьте в docker-compose файл настройки для запуска postgres с указанием скрипта инициализации ./smart_home/init.sql

Для проверки можно использовать Postman коллекцию smarthome-api.postman_collection.json и вызвать:

- Create Sensor
- Get All Sensors

Должно при каждом вызове отображаться разное значение температуры

Ревьюер будет проверять точно так же.


