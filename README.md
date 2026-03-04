# FreshRSS - Robsonway News Feed

Self-hosted RSS feed aggregator with pre-configured feeds for AI, Portugal news, and Financial Markets.

## 🚀 Quick Deploy

### Prerequisites
- Docker & Docker Compose
- Nginx (for reverse proxy with SSL)
- Domain pointing to your server

### Installation

```bash
# Clone repository
git clone https://github.com/robsonway/freshrss-robsonway.git
cd freshrss-robsonway

# Start FreshRSS
docker compose up -d

# Import pre-configured feeds (after creating user)
docker cp feeds.opml freshrss:/tmp/feeds.opml
docker exec freshrss php /var/www/FreshRSS/cli/import-for-user.php --user YOUR_USERNAME --filename /tmp/feeds.opml

# Update feeds
docker exec freshrss php /var/www/FreshRSS/cli/actualize-user.php --user YOUR_USERNAME
```

### Nginx Configuration

```nginx
server {
    listen 80;
    server_name news.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name news.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/news.yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/news.yourdomain.com/privkey.pem;
    
    location / {
        proxy_pass http://127.0.0.1:8086;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## 📰 Pre-configured Feeds

### 🤖 AI
- OpenAI Blog
- Hugging Face Blog
- Google AI Blog
- DeepMind Blog
- The Verge AI

### 🇵🇹 Portugal
- Público
- Observador
- ECO

### 📊 Financial Markets
- Bloomberg Markets
- Financial Times
- CNBC
- MarketWatch
- Investing.com
- Yahoo Finance

## 📁 Structure

```
freshrss-robsonway/
├── docker-compose.yml    # Docker configuration
├── feeds.opml            # Pre-configured feeds
├── nginx/
│   └── freshrss.conf     # Nginx config example
├── data/                 # FreshRSS data (gitignored)
└── extensions/           # FreshRSS extensions
```

## 🔄 Maintenance

```bash
# Update feeds manually
docker exec freshrss php /var/www/FreshRSS/cli/actualize-user.php --user USERNAME

# Backup
docker exec freshrss php /var/www/FreshRSS/cli/export-opml-for-user.php --user USERNAME > backup.opml

# Update FreshRSS
docker compose pull
docker compose up -d
```

## 📝 License

MIT License - Robsonway 2026
