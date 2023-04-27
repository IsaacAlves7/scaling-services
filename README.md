<a href="https://github.com/IsaacAlves7/scaling-and-load-balancing-webapp">

![1679593818556](https://user-images.githubusercontent.com/61624336/230903035-222d7763-61d5-4d90-bf41-5a741d016d21.jpg)

</a>

# Scaling and Load balancing Web App with Docker
Na vida real, sabemos que a aplicação é maior que somente dois containers, geralmente temos dois, três ou mais containers para segurar o tráfego da aplicação, distribuindo a carga. Além disso, temos que colocar todos esses containers para se comunicar com o banco de dados em um outro container, mas quanto maior a aplicação, devemos ter mais de um container para o banco também.

E claro, se temos três aplicações rodando, não podemos ter três endereços diferentes, então nesses casos utilizamos um Load Balancer em um outro container, para fazer a distribuição de carga quando tivermos muitos acessos. Ele recebe as requisições e distribui para uma das aplicações, e ele também é muito utilizado para servir os arquivos estáticos, como imagens, arquivos CSS e JavaScript.

Assim, a nossa aplicação controla somente a lógica, as regras de negócio, com os arquivos estáticos ficando a cargo do Load Balancer.

[![dockerfile](https://img.shields.io/badge/-docker--compose.yaml-pink?style=social&logo=docker&logoColor=magenta)](#)

Então, para isso foi criada uma ferramenta no Docker chamada **Docker Compose**, que ele executa e auxilia na build ou criação de vários (múltiplos) containers simultaneamente. Ele funciona seguindo um arquivo de texto, chamado `docker-compose.yaml`.

Esse arquivo serve para dar um conjunto de passos ao Docker e com ele podemos controlar quais containers sobem primeiro, ou seja, funciona como um processo de BIOS ou um automatizador de containers. O **Docker Compose** cria uma rede padrão, também é possível criar uma nova rede usando o comando `docker network`.

Essa aplicação possui servidor, rotas e banco de dados. De novidade, é que agora precisamos criar o **NGINX**, que é mais um container que devemos subir.

Então, ao utilizarmos a **imagem nginx**, ou criamos a nossa própria. Como vamos configurar o NGINX para algumas coisas específicas, como lidar com os **arquivos estáticos**, vamos criar a nossa própria imagem, por isso que na aplicação há o `nginx.dockerfile`:

[![dockerfile](https://img.shields.io/badge/-nginx.dockerfile-blue?style=social&logo=docker&logoColor=blue)](#)

```dockerfile
FROM nginx:latest
MAINTAINER isaacalves7
COPY /public /var/www/public
COPY /docker/config/nginx.conf /etc/nginx/nginx.conf
EXPOSE 80 443
ENTRYPOINT ["nginx"]
# Parametros extras para o entrypoint
CMD ["-g", "daemon off;"]
```

Nesse arquivo, nós utilizamos a última versão disponível da imagem do nginx como base, e copiamos o conteúdo da pasta `public`, que contém os arquivos estáticos, e um arquivo de configuração do NGINX para dentro do container. Além disso, abrimos as portas `80` e `443` e executa o NGINX através do comando nginx, passando os parâmetros extras `-g` e `daemon off`.

Por fim, vamos ver um pouco sobre o *arquivo de configuração do NGINX*, para entendermos um pouco como o **load balancer** está funcionando.

No arquivo `nginx.conf`, dentro server, está a parte que trata de servir os *arquivos estáticos*. Na `porta 80`, no localhost, em `/var/www/public`, ele será responsável por servir as pastas `css`, `img` e `js`. E todo resto, que não for esses três locais, ele irá jogar para o `node_upstream`.

No **node_upstream**, é onde ficam as configurações para o NGINX redirecionar as conexões que ele receber para um dos três containers da nossa aplicação. O redirecionamento acontecerá de forma circular, ou seja, a **primeira conexão irá para o primeiro container**, a **segunda irá para o segundo container**, a **terceira irá para o terceiro container**, na **quarta, começa tudo de novo**, **e ela vai para o primeiro container e assim por diante**.
