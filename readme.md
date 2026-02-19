1.
```bash
sudo su
apt update && apt upgrade -y

apt install docker-compose-v2

mkdir -p /opt/mtproto
cd /opt/mtproto

cat > config.toml << 'EOF'
secret = "ТОТ САМЫЙ SECRET"
bind-to = "0.0.0.0:3128"
EOF
```

2.
В /opt/mtproto создаем docker-compose.yml
```yaml
services:
  mtg:
    image: nineseconds/mtg:master
    restart: unless-stopped
    ports:
      - "8443:3128"
    volumes:
      - ./config.toml:/config.toml:ro
    command: run /config.toml
```

3.
```bash
docker compose up -d
docker compose logs -n 50 --no-color
```

4.
Проверяем:
```bash
ss -tulnp | grep 8443 || true
```

должно быть примерно так:
```bash
root@mini-shu:/opt/mtproto# ss -tulnp | grep 8443 || true
tcp     LISTEN   0        4096             0.0.0.0:8443           0.0.0.0:*      users:(("docker-proxy",pid=1337357,fd=4))
tcp     LISTEN   0        4096                [::]:8443              [::]:*      users:(("docker-proxy",pid=1337363,fd=4))

```

5.
В телеге:
Настройки -> Данные и память -> Настройки прокси -> добавить -> тип MTProto
ставим туда IP, порт, secret

Готово
