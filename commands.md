# Основные команды

## 1. Пользователь deploy
```
sudo adduser deploy
sudo usermod -aG sudo deploy
groups deploy
ssh deploy@SERVER_IP
```

## 2. SSH и запрет root-login
```
sudo nano /etc/ssh/sshd_config      # PermitRootLogin no
sudo sshd -t
sudo systemctl enable --now ssh
sudo systemctl restart ssh
grep -i "^PermitRootLogin" /etc/ssh/sshd_config
```

## 3. Firewall
```
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
sudo ufw status verbose
```

## 4. Nginx
```
sudo apt update
sudo apt install nginx -y
systemctl is-enabled nginx
sudo systemctl status nginx
```

## 5. Тестовая страница
```
sudo mkdir -p /var/www/kai-app
sudo nano /var/www/kai-app/index.html
sudo nano /etc/nginx/sites-available/kai-app
sudo ln -s /etc/nginx/sites-available/kai-app /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl reload nginx
curl http://SERVER_IP
```

## 6. Health-check
В server block добавлен:
```
location /health {
    default_type text/plain;
    return 200 "OK";
}
```
Проверка:
```
sudo nginx -t
sudo systemctl reload nginx
curl http://SERVER_IP/health
```

## 7. Логи
```
sudo tail -n 20 /var/log/nginx/access.log
sudo tail -n 20 /var/log/nginx/error.log
```