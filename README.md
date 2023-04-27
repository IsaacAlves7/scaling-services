![1679593818556](https://user-images.githubusercontent.com/61624336/230903035-222d7763-61d5-4d90-bf41-5a741d016d21.jpg)

# Scaling and Load balancing Web App with Docker
Na vida real, sabemos que a aplicação é maior que somente dois containers, geralmente temos dois, três ou mais containers para segurar o tráfego da aplicação, distribuindo a carga. Além disso, temos que colocar todos esses containers para se comunicar com o banco de dados em um outro container, mas quanto maior a aplicação, devemos ter mais de um container para o banco também.

E claro, se temos três aplicações rodando, não podemos ter três endereços diferentes, então nesses casos utilizamos um Load Balancer em um outro container, para fazer a distribuição de carga quando tivermos muitos acessos. Ele recebe as requisições e distribui para uma das aplicações, e ele também é muito utilizado para servir os arquivos estáticos, como imagens, arquivos CSS e JavaScript.

Assim, a nossa aplicação controla somente a lógica, as regras de negócio, com os arquivos estáticos ficando a cargo do Load Balancer.

[![dockerfile](https://img.shields.io/badge/-docker--compose.yaml-pink?style=social&logo=docker&logoColor=magenta)](#)

Então, para isso foi criada uma ferramenta no Docker chamada **Docker Compose**, que ele executa e auxilia na build ou criação de vários (múltiplos) containers simultaneamente. Ele funciona seguindo um arquivo de texto, chamado `docker-compose.yaml`.

Esse arquivo serve para dar um conjunto de passos ao Docker e com ele podemos controlar quais containers sobem primeiro, ou seja, funciona como um processo de BIOS ou um automatizador de containers. O **Docker Compose** cria uma rede padrão, também é possível criar uma nova rede usando o comando `docker network`.

Essa é uma aplicação possui servidor, rotas e banco de dados. De novidade, é que agora precisamos criar o **NGINX**, que é mais um container que devemos subir.

Então, ao utilizarmos a **imagem nginx**, ou criamos a nossa própria. Como vamos configurar o NGINX para algumas coisas específicas, como lidar com os **arquivos estáticos**, vamos criar a nossa própria imagem, por isso que na aplicação há o `nginx.dockerfile`:
