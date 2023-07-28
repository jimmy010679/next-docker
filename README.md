# Next.js 13 搭配 Docker

`Next.js 13` 搭配 `Docker Compose`，開發機器只需有 `Docker`，就可進行開發，此範例還搭配了 `Ngnix` 做反向代理，讓本機也能在有HTTPS情況下開發。

## 一、本機運行環境與設定

### 安裝 VSCode Package

- `Docker`
- `ESLint`
- `Prettier - Code formmatter`
- `ES7+ React/Redux/React-Native snippets`

### .vscode/settings.json

```json
{
  "editor.formatOnPaste": true,
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```

### 憑證

讓本機擁有HTTPS(本機掛憑證)模式

- 輸入以下指令，將產生兩個檔案，放入電腦用戶目錄底下 `/certificates/`，詳細設定需搭配 `.env` 下 `NGINX_HTTPS_SERVER_NAME`、`NGINX_HTTPS_CRT`、`NGINX_HTTPS_KEY` 變數

```bash
openssl req -x509 -out localhost.example.com.crt -keyout localhost.example.com.key \
  -days 365 \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=localhost.example.com' -extensions EXT -config <( \
   printf "[dn]\nCN=localhost.example.com\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:localhost.example.com\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```

- 並將 `localhost.example.com.crt` 將此金鑰匯入本機 (Mac 電腦是在`鑰匙圈存取` > `系統鑰匙圈`)
- 對 `localhost.example.com` 憑證按右鍵 `取得資訊` > `信任` > 改成 `永遠信任`
- 在本機 hosts 設定檔(Mac 電腦是在`/private/etc/hosts`)新增一筆設定： `127.0.0.1 localhost.example.com`

### Docker

在根目錄新增 `.env` 檔案，內容如下:

```log
### NGINX VERSION
NGINX_VERSION="latest"

### NODE VERSION
NODE_VERSION="18.16.1"

### NGINX SERVER NAME
NGINX_HTTPS_SERVER_NAME="localhost.example.com"

### NGINX https crt, key
NGINX_HTTPS_CRT="~/work/.certificates/verifield.com.crt"
NGINX_HTTPS_KEY="~/work/.certificates/verifield.com.key"

### APP Docker port
APP_DOCKER_DEV_PORT=9000
```

## 二、本機開發指令

### 本機使用 docker 版本

```bash
# 重建置
$ docker compose build

# 啟動
$ docker compose up -d

# 關閉
$ docker compose down

# 查看狀態
$ docker compose ps

# 查看 logs
$ docker compose logs -f app
$ docker compose logs -f nginx

# 進入 container，安裝套件要先進入容器 
$ docker exec -it next-docker-container sh
$ docker exec -it next-docker-container bash
```

瀏覽器輸入 `https://localhost.example.com` 進行開發
