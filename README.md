# Դեփլոյ ենք անում nodejs պռոյեկտը, nginx եւ անվճար ssl

## Գնում ենք սերւեր եւ դոմեյն

- Նախ մեզ հարկավոր է սերւվեր որտեղ կաշխատի մեր կայքը, ես օգտագործել եմ  ubuntu server, բայց դա էական չէ, դուք կարող եք օգտագործել debian կամ centos։
- Դոմեյն կարող եք գնել օրինակ name.am ից, կա նաև անվճար տարբերակ noip.com -ը, որը ունի իր դրական եւ բացասական կողմերը։ 
 ## Տեղադրում ենք անհրաժեշտ փաթեթները

- SSH ֊ի օգնությամբ միանում ենք սերւերին (իդեալական կլինի օգտագործել key էր եւ փոխել SSH ֊ի default port ֊ը, но не этот раз).
```
ssh username@ip
```

- Թարմեցնում ենք փաթեթեները:
```
sudo apt update -y && sudo apt upgrade -y
```
- Տեղադրում ենք Curl -ը:
```
sudo apt install curl
```
- Տեղադրում ենք NVM (Node Version Manager) -ը:
```
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
source ~/.profile 
```
- Տեղադրում ենք Node -ը։
```
# Տեղադրում ենք վերջին վերսիան
nvm install node

# Տեղադրում ենք մեր ուզած վերսիան
nvm install 14.17.3
nvm use 14.17.3

# Ստուգում ենք ակտուալ վերսիան
node -v
```
- Ժամանակն է որ պռոյեկտը տեղափոխենք սերւեր, եթե github ում է ապա ուղղակի clone արեք, եթե ձեր կարգիչի մեջ է ապա filezilla ծրագրի օգնությամբ տեղափոխեք սերւվեր։
```
git clone (ռեպո ֊ի յղումը)
```
- Տեղադրում ենք node_modules ֊ները:
```
# Ենթադրենք որ դուք արդեն գտնվում եք ձեր պռոյեկտի պապկայում 

npm install
```
- Տեղադրում ենք PM2 -ը (որպեսզի մեր պռոյեկտը աշխատի ֆոնում) եւ գործարկում ենք պռոյեկտը:
```
npm install -g pm2

pm2 start index.js

pm2 status # ցույց կտա ստատուսը
pm2 logs # ցույց կտա լօգերը
```
## Տեղադրում ենք հրապատը(firewall)

```
ufw enable 
ufw allow ssh
ufw allow http
ufw allow https
```
## Տեղադրում ենք nginx -ը։
```
sudo apt install nginx 
sudo nano /etc/nginx/sites-available/default 
```
- server_name եւ location / {} դաշտը փոխում ենք՝
```
server_name your-domain.com www.your-domain.com;

location / {
    proxy_pass http://localhost:5000; # այն port -ը որը օգտագործում է ձեր պռոյեկտը
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}
```
- Ctrl + S որպեսզի պահպանենք եւ Ctrl + X որպեսզի դուրս գանք nano -ից։

- Ստուգում ենք nginx -ի սինթաքսը և վերագործարկում ենք այն։
```
sudo nginx -t
sudo service nginx restart
```

## Տեղադրում SSL սերտիֆիկատ
Անվճար SSL սերտիֆիկատը տրամադրում է Let's Encrypt -ը

```
sudo apt install certbot python3-certbot-nginx 
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
certbot renew --dry-run
```

