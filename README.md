# Установка и настройка Marzban с поддержкой XRay

## 📦 Установка Marzban

Для быстрой установки выполните следующую команду:

```bash
sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ install
```

## 🔐 Генерация ключей X25519 и shortId

Для настройки протокола `REALITY` необходимо сгенерировать приватный ключ и короткий идентификатор:

```bash
sudo docker exec marzban-marzban-1 xray x25519
openssl rand -hex 8
```

## 👤 Создание администратора

После установки создайте администратора командой:

```bash
sudo marzban cli admin create --sudo
```

## 🌐 Настройка обратного прокси Nginx

Создайте файл `/etc/nginx/sites-available/marzban`:

```nginx
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://127.0.0.1:8000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /dashboard/ {
        proxy_pass http://127.0.0.1:8000/dashboard/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Активируйте конфигурацию:

```bash
sudo ln -s /etc/nginx/sites-available/marzban /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl restart nginx
```

## ⚙️ Пример `nginx.conf`

Если вы редактируете напрямую `nginx.conf`, вставьте тот же блок:

```nginx
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://127.0.0.1:8000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /dashboard/ {
        proxy_pass http://127.0.0.1:8000/dashboard/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## 🛡️ Пример конфигурации `xray.conf`

Для добавления поддержки протоколов `VLESS`, `VMess`, `Shadowsocks` с использованием `REALITY` и `grpc`, используйте следующий шаблон:

<details>
<summary>Нажмите, чтобы развернуть конфигурацию</summary>

```json
{...}
```

</details>

## 📁 Используемые внешние репозитории

Данный проект использует готовые конфигурации из публичного репозитория:

👉 [Epodonios/v2ray-configs](https://github.com/Epodonios/v2ray-configs.git)

## 📌 Примечания

- Значения `<privateKey>` и `<shortId>` необходимо заменить на результаты генерации.
- В поле `dest` и `serverNames` можно указать любое существующее доменное имя с открытым портом 443.
- При необходимости настройте firewall/ufw для разрешения нужных портов.

---

Разработка и поддержка: [Gozargah/Marzban](https://github.com/Gozargah/Marzban)  
Дополнительно: [Документация XRay-core](https://xtls.github.io/ru/)
