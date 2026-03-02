# Practical lesson pz-MQTT
# Розгортання та налаштування MQTT-брокера

> У цьому занятті студенти отримують практичні навички роботи з MQTT-брокером.  
> Мета — навчитися розгортати брокер та обмінюватися повідомленнями за допомогою публікації/підписки.

---

## Структура проєкту

```
├── stt-pz-3
│   ├── broker
│   │   ├── mosquitto.conf        # конфігурація MQTT-брокера Mosquitto
│   │   ├── docker-compose.yml    # варіант розгортання брокера у Docker
│   ├── screenshots               # докази роботи Publish/Subscribe
│   ├── .editorconfig
│   ├── .gitignore
│   └── README.md
```

---

## Основні поняття MQTT

| Поняття | Опис |
|---------|------|
| **Topic** | Канал передачі повідомлень. Наприклад: `home/temperature`. Може мати ієрархічну структуру через `/`. |
| **Publish** | Відправка повідомлення на певний топік. |
| **Subscribe** | Підписка на топік для отримання повідомлень. |
| **QoS 0** | At most once — повідомлення відправляється один раз без підтвердження. |
| **QoS 1** | At least once — гарантована доставка, але можливі дублікати. |
| **QoS 2** | Exactly once — гарантована доставка рівно один раз. |

---

## Розгортання брокера

### Передумови
- Docker та Docker Compose встановлені на машині

### Запуск

```bash
cd stt-pz-3/broker
docker compose up -d
```

### Перевірка статусу

```bash
docker ps
docker logs mqtt-broker
```

Очікуваний вивід логів:
```
1748000000: mosquitto version 2.0.x starting
1748000000: Opening ipv4 listen socket on port 1883.
1748000000: Opening websockets listen socket on port 9001.
1748000000: mosquitto version 2.0.x running
```

### Зупинка

```bash
docker compose down
```

---

## Конфігурація брокера (`mosquitto.conf`)

```conf
# MQTT порт
listener 1883
protocol mqtt

# WebSocket порт (для Postman)
listener 9001
protocol websockets

# Дозвіл анонімних підключень
allow_anonymous true

# Логування
log_type all
log_dest stdout

# Персистентність повідомлень
persistence true
persistence_location /mosquitto/data/
```

---

## Тестування через Postman

### Підключення

1. Відкрийте Postman → **New** → **MQTT**
2. Вкажіть URL підключення: `mqtt://localhost:1883`
3. Натисніть **Connect**

### Subscribe (підписка на топік)

1. У вкладці **Subscribe** введіть топік: `test/topic`
2. Виберіть **QoS 1**
3. Натисніть **Subscribe**

### Publish (публікація повідомлення)

1. У вкладці **Publish** введіть:
   - **Topic:** `test/topic`
   - **Message:** `Hello MQTT!`
   - **QoS:** 1
2. Натисніть **Send**
3. Повідомлення має з'явитися у вікні підписки

---

## Тестування через CLI (опційно)

Якщо встановлено `mosquitto-clients`:

```bash
# Subscribe (у першому терміналі)
mosquitto_sub -h localhost -p 1883 -t "test/topic" -v

# Publish (у другому терміналі)
mosquitto_pub -h localhost -p 1883 -t "test/topic" -m "Hello from CLI"
```

Або через Docker без встановлення клієнта:

```bash
# Subscribe
docker exec -it mqtt-broker mosquitto_sub -h localhost -t "test/topic" -v

# Publish (в іншому терміналі)
docker exec -it mqtt-broker mosquitto_pub -h localhost -t "test/topic" -m "Hello MQTT!"
```

---

## Скріншоти

Скріншоти роботи Publish/Subscribe знаходяться у папці [`screenshots/`](./screenshots/).

---

## Useful links

- [MQTT Essentials](https://www.hivemq.com/mqtt-essentials/)
- [Eclipse Mosquitto](https://mosquitto.org/)
- [MQTT with Postman](https://learning.postman.com/docs/sending-mqtt-requests/)
- [EMQX Documentation](https://www.emqx.io/docs/en/latest/)
