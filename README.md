# 📦 Zabbix в Docker + TLS (PSK)

Настройка `Zabbix Server`, `Frontend`, `Agent2` с шифрованием по PSK 🔐

---

## 🚀 Быстрый старт

1. 📁 Склонируй проект и перейди в директорию

```
git clone <репозиторий>
cd zabbix
```

2. 🔑 Создай ключ для шифрования (PSK)

```
openssl rand -hex 32 > zabbix_agent2.psk
```

3. ✅ Убедись, что `zabbix_agent2.psk` добавлен в `docker-compose.yml`:

```
secrets:
  zabbix-agent2-psk:
    file: ./zabbix_agent2.psk
```

4. ⚙️ Убедись, что `zabbix-server` и `zabbix-agent2` используют TLS настройки:

В `zabbix-server`:

```
    environment:
      ...
      TLSCONNECT: psk
      TLSACCEPT: psk
      TLSPSKIDENTITY: agent2-psk
      TLSPSKFILE: /run/secrets/zabbix-agent2-psk
```

В `zabbix-agent2`:

```
    environment:
      ...
      TLSCONNECT: psk
      TLSACCEPT: psk
      TLSPSKIDENTITY: agent2-psk
      TLSPSKFILE: /run/secrets/zabbix-agent2-psk
```

5. 📡 Настрой хост в Zabbix UI:
   - **Hostname**: `agent-on-host`
   - **Connection**: `Zabbix agent (active)`
   - **Encryption**:
     - PSK identity: `agent2-psk`
     - PSK value: вставь содержимое файла `zabbix_agent2.psk`

---

## 🔁 Запуск

```
docker compose up -d
```

---

## ✅ Проверка

Проверь лог агента:

```
docker logs -f zabbix-agent2
```

Увидишь:

```
successfully connected to server over PSK TLS
```

---

## 🧹 Остановка и очистка

```
docker compose down -v
```

---

## 🧠 Дополнительно

- Убедись, что порт `64343` проброшен (если сервер слушает не `10051`)
- Используй `network_mode: host` для агента, если надо собирать метрики хоста
- Можно расширить `agent2` с кастомными `UserParameter`

---
