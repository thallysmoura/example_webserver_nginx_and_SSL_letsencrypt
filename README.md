
# 🔐  Exemplo de servidor web utilizando nginx e SSL let's encrypt

A seguir será mostrado passo-a-passo como configurar um servido web utilizando nginx e instalação de certificado de segurança com certbot let's encrypt.


## Instalação do Nginx e Certbot com Ubuntu Server

Este guia fornece um passo a passo claro e objetivo para a instalação do Nginx e do Certbot em um servidor Ubuntu. Siga os passos abaixo para configurar um servidor web e instalar um certificado SSL:

### Instalação do Nginx e Certbot

1. Acesse a VM via SSH e atualize o sistema:

```bash
sudo apt update
```

2. Instale o Nginx e o Certbot:

```bash
sudo apt install nginx certbot python3-certbot-nginx
```

3. Verifique se o servidor web está ativo acessando http://localhost ou http://127.0.0.1 em seu navegador.

### Diretórios

O diretório onde as aplicações web devem ser colocadas é */var/www/html/*.

Para realizar o deploy de uma aplicação, basta criar uma subpasta com o nome da sua aplicação dentro desse diretório:

```bash
cd /var/www/html/
mkdir meuprojeto
```

Acesse a aplicação em http://127.0.0.1/meuprojeto.

### Gerenciamento de DNS com Cloudflare

Após adquirir um domínio (por exemplo, http://meudominio.com), é necessário configurar o apontamento do path no Nginx.

1. Acesse o painel do Cloudflare e clique na aba "DNS".
2. Verifique os registros DNS do seu domínio e configure os registros A para o domínio principal e os registros CNAME ou A para subdomínios, apontando para o endereço IP do seu VPS.

### Apontamento do path no Nginx

Configure o arquivo de domínio no Nginx:

```bash
cd /etc/nginx/sites-available
sudo nano meudominio.com.conf
```

Insira o seguinte conteúdo:

```
server {
  server_name meudominio.com www.meudominio.com;
  listen: 80;

  error_page 500 502 503 504 /50x.html;

  location / {
    root /var/www/html/meuprojeto;
  }
}
```

Salve o arquivo com CTRL + O e saia com CTRL + X.

Crie um link simbólico para o diretório sites-enabled:

```bash
sudo ln -s /etc/nginx/sites-available/meudominio.com.conf /etc/nginx/sites-enabled
```

### Finalizando o apontamento

Reinicie o Nginx para aplicar as alterações:

```bash
sudo service nginx restart
```

Acesse seu domínio http://meudominio.com, e o projeto será carregado pelo Nginx.

### Instalação do SSL

Para instalar um certificado SSL com o Certbot, execute o seguinte comando:

```bash
sudo certbot --nginx
```

Após a conclusão, reinicie o Nginx para aplicar as alterações:

```bash
sudo service nginx restart
```

Agora seu servidor Nginx está configurado corretamente e protegido com um certificado SSL, permitindo acesso seguro através do HTTPS.

### Proxy Reverso de API dentro do Nginx

Acesse */etc/nginx/sites-enabled/meudominio.com.conf*

adicione o trecho a seguir:

```
 location /api {
        proxy_pass http://<endereçoAPI>:<porta>;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
  ```

  Feito isso, certifique-se que o prefixo dentro do app.js da sua api esteja semelhante ao apontamento do nginx, neste caso /api.

Feito isso, reinicie o nginx

```bash
sudo service nginx restart
```

Acesse: https://meudominio.com/api/

## 

![App Screenshot](https://www.hardware.com.br/wp-content/uploads/static/wp/2022/07/06/blog_15418_large.jpg)


## Referência

 - [Nginx](https://www.nginx.com/)
 - [CertBot](https://certbot.eff.org/)



## Autores

- [@thallysmoura](https://github.com/thallysmoura)

