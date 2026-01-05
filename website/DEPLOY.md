# 🚀 Руководство по деплою сайта Timeland

## Быстрый старт

Этот сайт полностью статический и не требует серверной части. Просто загрузите файлы на любой веб-хостинг.

---

## 📦 Netlify (Рекомендуется)

### Способ 1: Drag & Drop
1. Перейдите на [netlify.com](https://netlify.com)
2. Перетащите папку `website` в окно браузера
3. Готово! Ваш сайт онлайн

### Способ 2: Через Git
1. Создайте репозиторий с файлами сайта
2. Подключите Netlify к репозиторию
3. Настройки:
   - **Build command**: оставить пустым
   - **Publish directory**: `website`
4. Deploy!

**Преимущества**: Бесплатно, автоматический HTTPS, CDN

---

## 🔷 Vercel

1. Установите Vercel CLI: `npm i -g vercel`
2. В папке `website` выполните:
```bash
vercel
```
3. Следуйте инструкциям
4. Готово!

Или через веб-интерфейс:
1. Импортируйте проект на [vercel.com](https://vercel.com)
2. Укажите папку `website`
3. Deploy

**Преимущества**: Бесплатно, быстрый CDN, автоматический HTTPS

---

## 🐙 GitHub Pages

1. Создайте репозиторий на GitHub
2. Загрузите файлы из папки `website` в корень репозитория
3. Settings → Pages
4. Source: Deploy from branch
5. Branch: `main` (или `master`), папка: `/ (root)`
6. Save

Ваш сайт будет доступен по адресу: `https://username.github.io/repository-name`

**Преимущества**: Бесплатно, интеграция с GitHub, автоматический HTTPS

---

## 🌐 Собственный хостинг

### Apache

1. Загрузите файлы в `/var/www/html/`:
```bash
sudo cp -r website/* /var/www/html/
```

2. Настройте виртуальный хост `/etc/apache2/sites-available/timeland.conf`:
```apache
<VirtualHost *:80>
    ServerName timeland.example.com
    DocumentRoot /var/www/html
    
    <Directory /var/www/html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

3. Активируйте:
```bash
sudo a2ensite timeland.conf
sudo systemctl reload apache2
```

### Nginx

1. Загрузите файлы:
```bash
sudo mkdir -p /var/www/timeland
sudo cp -r website/* /var/www/timeland/
```

2. Создайте конфигурацию `/etc/nginx/sites-available/timeland`:
```nginx
server {
    listen 80;
    listen [::]:80;
    
    server_name timeland.example.com;
    root /var/www/timeland;
    index index.html;
    
    location / {
        try_files $uri $uri/ =404;
    }
    
    # Кеширование статических файлов
    location ~* \.(css|js|jpg|png|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # Compression
    gzip on;
    gzip_types text/css text/javascript application/javascript text/html;
    gzip_min_length 1000;
}
```

3. Активируйте:
```bash
sudo ln -s /etc/nginx/sites-available/timeland /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

### SSL/HTTPS с Let's Encrypt

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d timeland.example.com
```

---

## ☁️ Другие популярные платформы

### Cloudflare Pages
1. Подключите Git репозиторий
2. Build settings: оставьте пустыми
3. Output directory: `website`
4. Deploy

### Firebase Hosting
```bash
npm install -g firebase-tools
firebase login
firebase init hosting
# Выберите папку website
firebase deploy
```

### Render
1. Создайте новый Static Site
2. Подключите репозиторий
3. Publish directory: `website`
4. Deploy

---

## 🔧 Проверка перед деплоем

### Локальный тест
```bash
cd website
python3 -m http.server 8000
# Откройте http://localhost:8000
```

### Проверочный чек-лист
- [ ] Все ссылки работают
- [ ] IP адрес правильный
- [ ] Discord/Boosty/Wiki ссылки актуальны
- [ ] Сайт адаптивен (проверьте на мобильном)
- [ ] Правила отображаются корректно
- [ ] Кнопка копирования IP работает

---

## ⚙️ Настройка домена

После деплоя подключите свой домен:

1. **Netlify/Vercel**: Settings → Domains → Add custom domain
2. **GitHub Pages**: Settings → Pages → Custom domain
3. **Собственный сервер**: Настройте DNS A-запись на IP сервера

### DNS настройки (пример)
```
Type    Name    Value               TTL
A       @       ваш.IP.адрес.тут    3600
CNAME   www     yourdomain.com      3600
```

---

## 📊 Мониторинг и аналитика

### Google Analytics (опционально)
Добавьте перед закрывающим `</head>` в `index.html`:
```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=YOUR-ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'YOUR-ID');
</script>
```

### Yandex Metrika (опционально)
Добавьте код счётчика в `index.html`

---

## 🔒 Безопасность

### Рекомендуемые заголовки безопасности
Для Nginx добавьте в конфигурацию:
```nginx
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "no-referrer-when-downgrade" always;
```

---

## 🆘 Поддержка

Если возникли проблемы с деплоем:
1. Проверьте консоль браузера на ошибки (F12)
2. Убедитесь, что все файлы загружены
3. Проверьте права доступа к файлам (644 для файлов, 755 для папок)
4. Убедитесь, что index.html находится в корне публичной директории

---

## ✅ После деплоя

1. Проверьте сайт на разных устройствах
2. Протестируйте все ссылки
3. Проверьте скорость загрузки (PageSpeed Insights)
4. Настройте мониторинг uptime (UptimeRobot, Pingdom)
5. Сделайте резервную копию файлов

---

**Удачного деплоя! 🚀**

Если нужна помощь - обратитесь к сообществу Timeland в Discord.
