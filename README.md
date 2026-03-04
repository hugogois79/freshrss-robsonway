# FreshRSS - Robsonway News Feed

Self-hosted RSS feed aggregator with a custom Bloomberg-style dark theme and pre-configured feeds for AI, Tech, Portugal, Financial Markets, World News, and Crypto.

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

# Create user (after container is ready)
docker exec freshrss php /var/www/FreshRSS/cli/do-install.php --default-user admin --base-url https://news.yourdomain.com --language en --db-type sqlite
docker exec freshrss php /var/www/FreshRSS/cli/create-user.php --user YOUR_USERNAME --password 'YOUR_PASSWORD' --language en
docker exec freshrss cli/access-permissions.sh

# Import pre-configured feeds
docker cp feeds.opml freshrss:/tmp/feeds.opml
docker exec freshrss php /var/www/FreshRSS/cli/import-for-user.php --user YOUR_USERNAME --filename /tmp/feeds.opml

# Fetch articles
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

## 🎨 Robsonway Compact Theme

Custom Bloomberg-style dark theme with productivity features:

- **Dark mode** on all pages (feed list, articles, settings)
- **Auto-collapsible sidebar** — collapses to a thin strip, expands on hover
- **Compact typography** — 80% base font size, fits more headlines per screen
- **Unread indicators** — blue left border + bold text for unread articles
- **Read articles dimmed** — instantly spot what's new
- **Favorites** — gold accent border

To activate: Settings > Display > Theme > **Robsonway Compact**

## ⌨️ Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `j` / `k` | Next / Previous article |
| `n` / `p` | Next / Previous unread |
| `space` | Open article website |
| `r` | Mark as read |
| `f` | Toggle favorite |
| `t` | Toggle sidebar |
| `h` | Next unread article |
| `s` | Share article |
| `a` | Focus search |
| `1` / `2` / `3` | Normal / Global / Reading view |
| `?` / `F1` | Show help |

## 📰 Pre-configured Feeds

### 🤖 AI
OpenAI Blog, Hugging Face Blog, Google AI Blog, DeepMind Blog, The Verge AI, MIT Technology Review AI, Anthropic Blog

### 🇵🇹 Portugal
Público, Observador, ECO, Jornal de Negócios

### 📊 Financial Markets
Bloomberg Markets, Financial Times, CNBC, MarketWatch, Investing.com, Yahoo Finance, Reuters Business

### 💻 Tech
Hacker News, TechCrunch, Ars Technica, The Verge

### 🌍 World News
BBC News, Reuters Top News, Al Jazeera

### ₿ Crypto
CoinDesk, Cointelegraph, The Block

## 📁 Structure

```
freshrss-robsonway/
├── docker-compose.yml              # Docker configuration
├── feeds.opml                      # Pre-configured feeds (6 categories, 28 sources)
├── themes/
│   └── Robsonway-Compact/          # Custom Bloomberg-style dark theme
│       ├── metadata.json
│       ├── origine.css
│       └── robsonway-compact.css
├── nginx/
│   └── freshrss.conf               # Nginx config example
├── data/                           # FreshRSS data (gitignored)
└── extensions/                     # FreshRSS extensions
```

## 🔄 Maintenance

```bash
# Update feeds manually
docker exec freshrss php /var/www/FreshRSS/cli/actualize-user.php --user USERNAME

# Backup feeds
docker exec freshrss php /var/www/FreshRSS/cli/export-opml-for-user.php --user USERNAME > backup.opml

# Update FreshRSS
docker compose pull
docker compose up -d

# View logs
docker compose logs -f --tail 50
```

## 📝 License

MIT License - Robsonway 2026
