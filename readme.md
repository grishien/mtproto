# Делаем себе собственный MTProto
# без всяких ебаных пидарасов которые воруют наши метаданные

1.
```bash
sudo su
apt update && apt upgrade -y

apt install docker-compose-v2
apt install nano
```

2. Получаем SECRET
```bash
docker run --rm nineseconds/mtg:master generate-secret 1c.ru
```
Должен выдать secret

3.
```bash
mkdir -p /opt/mtproto
cd /opt/mtproto

cat > config.toml << 'EOF'
secret = "ТОТ САМЫЙ SECRET"
bind-to = "0.0.0.0:3128"
EOF
```

4.
В /opt/mtproto создаем docker-compose.yml
```bash
nano docker-compose.yml
```
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
ctrl+s
ctrl+x

5.
```bash
docker compose up -d
docker compose logs -n 50 --no-color
```

6.
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

7.
В телеге:
```text
Настройки -> Данные и память -> Настройки прокси -> добавить -> тип MTProto
ставим туда IP, порт, secret
```

# Готово
