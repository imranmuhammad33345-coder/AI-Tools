# Deployment Guide - AI Tools Platform

This guide will help you export this project from Replit and deploy it to your own web hosting server.

## 📦 Exporting from Replit

### Option 1: Download as ZIP (Recommended)
1. In your Replit workspace, click the **three dots menu** (⋮) in the top right
2. Select **"Download as zip"** 
3. Save the ZIP file to your computer
4. Extract the ZIP file to access all project files

### Option 2: Git Clone (Alternative)
If your Repl is connected to a Git repository:
```bash
git clone <your-replit-git-url>
```

---

## 🖥️ Server Requirements

Your hosting server needs:
- **Node.js**: Version 18 or higher
- **PostgreSQL**: Version 14 or higher
- **npm** or **yarn** package manager
- **At least 512MB RAM** (1GB recommended)
- **HTTPS support** (required for authentication)

---

## 🚀 Deployment Steps

### Step 1: Upload Files to Your Server

Upload all extracted files to your server using one of these methods:
- **FTP/SFTP**: Use FileZilla, WinSCP, or Cyberduck
- **SSH**: Use `scp` or `rsync` commands
- **Control Panel**: Use cPanel, Plesk, or your hosting's file manager

Example using `scp`:
```bash
scp -r /path/to/extracted/folder user@your-server.com:/var/www/ai-tools
```

### Step 2: Install Dependencies

SSH into your server and navigate to your project directory:
```bash
cd /var/www/ai-tools
npm install
```

### Step 3: Set Up PostgreSQL Database

1. **Create a new PostgreSQL database:**
   ```bash
   # Connect to PostgreSQL
   sudo -u postgres psql
   
   # Create database
   CREATE DATABASE ai_tools_db;
   
   # Create user (optional, if not using default postgres user)
   CREATE USER ai_tools_user WITH PASSWORD 'your_secure_password';
   
   # Grant privileges
   GRANT ALL PRIVILEGES ON DATABASE ai_tools_db TO ai_tools_user;
   
   # Exit
   \q
   ```

2. **Get your database connection string:**
   ```
   postgresql://ai_tools_user:your_secure_password@localhost:5432/ai_tools_db
   ```

### Step 4: Configure Environment Variables

Create a `.env` file in your project root:

```bash
nano .env
```

Add the following environment variables:

```env
# Database Configuration
DATABASE_URL=postgresql://ai_tools_user:your_secure_password@localhost:5432/ai_tools_db
PGHOST=localhost
PGPORT=5432
PGDATABASE=ai_tools_db
PGUSER=ai_tools_user
PGPASSWORD=your_secure_password

# OpenAI API Key (get from https://platform.openai.com/api-keys)
OPENAI_API_KEY=sk-your-actual-openai-api-key-here

# Session Secret (generate a random string)
SESSION_SECRET=generate-a-long-random-string-here

# Replit Auth Configuration (if using Replit Auth)
REPL_ID=your-repl-id
ISSUER_URL=https://replit.com/oidc

# Node Environment
NODE_ENV=production
```

**Important Notes:**
- Replace `your_secure_password` with a strong password
- Get a valid OpenAI API key from https://platform.openai.com/api-keys (costs money)
- Generate a secure random string for SESSION_SECRET (you can use: `openssl rand -base64 32`)
- If not using Replit Auth, you'll need to implement alternative authentication

### Step 5: Initialize the Database

Run database migrations:
```bash
npm run db:push
```

This will create all necessary tables (users, sessions, messages, tool_history).

### Step 6: Build the Frontend

Build the production-ready frontend:
```bash
npm run build
```

### Step 7: Start the Application

#### Option A: Using Node.js directly (for testing)
```bash
npm start
```

#### Option B: Using PM2 (Recommended for Production)
PM2 keeps your app running and restarts it if it crashes.

1. **Install PM2 globally:**
   ```bash
   npm install -g pm2
   ```

2. **Start your application:**
   ```bash
   pm2 start npm --name "ai-tools" -- run dev
   ```

3. **Set PM2 to start on system boot:**
   ```bash
   pm2 startup
   pm2 save
   ```

4. **Useful PM2 commands:**
   ```bash
   pm2 status              # Check app status
   pm2 logs ai-tools       # View logs
   pm2 restart ai-tools    # Restart app
   pm2 stop ai-tools       # Stop app
   ```

### Step 8: Configure Reverse Proxy (Nginx)

To make your app accessible via a domain name with HTTPS:

1. **Install Nginx:**
   ```bash
   sudo apt update
   sudo apt install nginx
   ```

2. **Create Nginx configuration:**
   ```bash
   sudo nano /etc/nginx/sites-available/ai-tools
   ```

3. **Add this configuration:**
   ```nginx
   server {
       listen 80;
       server_name yourdomain.com www.yourdomain.com;

       location / {
           proxy_pass http://localhost:5000;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
           proxy_set_header Host $host;
           proxy_cache_bypass $http_upgrade;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

4. **Enable the site:**
   ```bash
   sudo ln -s /etc/nginx/sites-available/ai-tools /etc/nginx/sites-enabled/
   sudo nginx -t
   sudo systemctl restart nginx
   ```

5. **Set up HTTPS with Let's Encrypt (FREE SSL):**
   ```bash
   sudo apt install certbot python3-certbot-nginx
   sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
   ```

---

## 🌐 Domain Configuration

Point your domain to your server:
1. Log in to your domain registrar (GoDaddy, Namecheap, etc.)
2. Go to DNS settings
3. Create an **A record** pointing to your server's IP address:
   - **Type**: A
   - **Name**: @ (for root domain) or www
   - **Value**: Your server IP address (e.g., 192.168.1.100)
   - **TTL**: 3600 or default

---

## 🔧 Alternative Hosting Platforms

### Deploy to VPS Providers:
- **DigitalOcean** ($4-$6/month): Easy droplets with one-click apps
- **Linode** ($5/month): Similar to DigitalOcean
- **Vultr** ($2.50-$6/month): Budget-friendly option
- **AWS EC2**: More complex but scalable
- **Google Cloud Platform**: Free tier available

### Deploy to Platform-as-a-Service:
- **Heroku**: `git push heroku main` (requires Heroku Postgres add-on)
- **Railway**: Automatic deployments from GitHub
- **Render**: Free tier with PostgreSQL included
- **Vercel** + **Supabase**: Frontend on Vercel, database on Supabase

---

## 📝 Important Notes

### Authentication Setup
This project uses **Replit Auth** which requires Replit's infrastructure. For external hosting, you have two options:

**Option 1: Remove Replit Auth (Simplest)**
- Remove authentication middleware from `server/routes.ts`
- Make all endpoints public (not recommended for production)

**Option 2: Implement Alternative Auth**
- Use **Passport.js** with Google OAuth or email/password
- Use **Auth0**, **Firebase Auth**, or **Supabase Auth**
- Requires code modifications in `server/replitAuth.ts` and auth routes

### OpenAI API Costs
- This app uses OpenAI GPT-5 and DALL-E 3 APIs
- **GPT-5**: ~$0.03 per 1K tokens (varies by model)
- **DALL-E 3**: ~$0.04-$0.12 per image
- Monitor your usage at https://platform.openai.com/usage
- Set usage limits in your OpenAI account

### Database Backups
Create regular backups:
```bash
# Backup database
pg_dump ai_tools_db > backup_$(date +%Y%m%d).sql

# Restore from backup
psql ai_tools_db < backup_20250115.sql
```

---

## 🐛 Troubleshooting

### App won't start
```bash
# Check logs
pm2 logs ai-tools

# Check if port 5000 is in use
sudo lsof -i :5000

# Kill process on port 5000
sudo kill -9 $(sudo lsof -t -i:5000)
```

### Database connection errors
```bash
# Test PostgreSQL connection
psql -U ai_tools_user -d ai_tools_db -h localhost

# Check if PostgreSQL is running
sudo systemctl status postgresql
sudo systemctl start postgresql
```

### Nginx errors
```bash
# Test Nginx configuration
sudo nginx -t

# Check Nginx logs
sudo tail -f /var/log/nginx/error.log
```

---

## 📞 Support

If you encounter issues:
1. Check the logs: `pm2 logs ai-tools`
2. Verify environment variables are correct
3. Ensure PostgreSQL is running and accessible
4. Check that your OpenAI API key is valid and has credits
5. Verify your firewall allows traffic on ports 80 and 443

---

## 🎉 Success Checklist

- [ ] Code downloaded from Replit
- [ ] Files uploaded to server
- [ ] Node.js and PostgreSQL installed
- [ ] Dependencies installed (`npm install`)
- [ ] Environment variables configured (`.env` file)
- [ ] Database initialized (`npm run db:push`)
- [ ] Frontend built (`npm run build`)
- [ ] App running with PM2
- [ ] Nginx configured with reverse proxy
- [ ] SSL certificate installed (HTTPS)
- [ ] Domain pointing to server
- [ ] OpenAI API key configured and working
- [ ] Authentication configured (or removed)

---

**Your AI Tools Platform is now live! 🚀**

Visit `https://yourdomain.com` to see your website in action.
