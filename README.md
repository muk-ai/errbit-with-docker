
httpsを使う時は、 `/etc/letsencrypt` にLet's Encryptで生成した証明書があることを前提としています。  
https不要な場合は `docker-compose.yml` のnginxのserviceを削除したら動くはずです。

## .env

FQDNとRAILS_SECRETを設定します。  
secretは `rails secret` で生成できます  

以下の手順で使うので読み込んでおきます

```
$ source .env
```

`docker-compose` はとくに指定しなくてもこのファイルを読むようです。

## SSL証明書未取得の時


一時的にnginxを立てる

```
sudo docker run -p 80:80 -v /usr/share/nginx/html:/usr/share/nginx/html nginx:alpine
```

Let's Encrypt証明書取得
```
sudo docker run -it --rm --name certbot \
  -v "/etc/letsencrypt:/etc/letsencrypt" \
  -v "/var/log/letsencrypt:/var/log/letsencrypt" \
  -v "/usr/share/nginx/html:/usr/share/nginx/html" \
  certbot/certbot:latest certonly \
    --agree-tos \
    --webroot \
    --webroot-path /usr/share/nginx/html \
    -d ${FQDN} \
    -m your@mail.address
```

`/etc/letsencrypt` 配下に証明書が生成されます

## errbitを動かす

nginx, errbit, mongodbを起動
```
sudo docker-compose up
```

初期設定。adminのcredentialsが表示される。
```
sudo docker-compose exec errbit rake errbit:bootstrap
```
