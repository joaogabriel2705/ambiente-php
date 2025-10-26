# Ambiente de Desenvolvimento PHP com Docker

Este projeto é um ambiente de desenvolvimento PHP completo e personalizável, construído com Docker. O objetivo é substituir ferramentas tradicionais como XAMPP, Laragon ou WAMP, oferecendo um ambiente moderno, isolado e controlado por versão, usando o servidor de alta performance FrankenPHP.

## Tecnologias Utilizadas

- Servidor Web: FrankenPHP (baseado em Caddy)

- Banco de Dados: MariaDB (substituto do MySQL)

- Gerenciador de BD: phpMyAdmin

## Pré-requisitos

Antes de começar, garanta que você tenha os seguintes softwares instalados e configurados:

- WSL 2 (Subsistema do Windows para Linux) com uma distribuição Linux (ex: Ubuntu).

- Docker Desktop (instalado e configurado para usar o backend do WSL 2).

- Git

""Nota: Se você não usa o Docker Desktop, pode seguir <a href="https://daniel.es/blog/how-to-install-docker-in-wsl-without-docker-desktop/">este guia</a> para instalar o Docker Engine e o Docker Compose diretamente no WSL.""

## Como Usar

Siga estes passos para iniciar um novo projeto usando este ambiente como base.

1. Clone o Repositório

Em vez de clonar o repositório e mover os arquivos, clone-o diretamente com o nome da pasta do seu novo projeto:

```
git clone [https://github.com/joaogabriel2705/ambiente-php.git](https://github.com/joaogabriel2705/ambiente-php.git) nome-do-seu-projeto
```

2. Acesse a Pasta do Projeto

```
cd nome-do-seu-projeto
```

3. (Opcional) Limpe o Histórico do Git

Se este for um projeto novo, você vai querer iniciar um histórico do Git limpo, desvinculado deste template.

```
rm -rf .git
git init -b main
```


4. Crie a Pasta da Aplicação

O Docker está configurado para "espelhar" uma pasta local chamada app para dentro do servidor.

```
mkdir app
```

Coloque seu arquivo index.php ou seu projeto Laravel/Symfony dentro desta pasta.

5. Suba o Ambiente

Este comando irá construir as imagens (se for a primeira vez) e iniciar todos os serviços (PHP, BD, phpMyAdmin) em segundo plano.

```
docker compose up -d --build
```

6. Acesse sua Aplicação

Seu ambiente está pronto!

- Aplicação PHP: http://localhost

- phpMyAdmin: http://localhost:8080

## Customização

A principal vantagem deste ambiente é a facilidade de personalização.

### Variáveis de Ambiente

Você pode (e deve) personalizar as variáveis de ambiente, como senhas e nomes de banco, no arquivo docker-compose.yml, na seção environment de cada serviço.

Exemplo (docker-compose.yml):

```
services:
  frankenphp:
    environment:
      # Variáveis para sua aplicação (ex: Laravel)
      - DB_HOST=db
      - DB_DATABASE=mydb
      - DB_USERNAME=root
      - DB_PASSWORD=secret_password
      # ...
  db:
    environment:
      # Variáveis para o MariaDB
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: mydb
      MYSQL_USER: root
      MYSQL_PASSWORD: secret_password
```

## Configuração do Servidor (Caddyfile)

O arquivo Caddyfile controla o servidor web.

Para PHP Puro (Padrão): A configuração padrão já aponta para a pasta /app.

```
localhost {
    root * /app
    php_server
}
```

Para Laravel/Symfony: Se seu projeto usa uma pasta public, basta ajustar o root:
```
localhost {
    root * /app/public
    php_server
}
```

## Extensões PHP

Precisa de mais extensões PHP (como gd, zip ou xdebug)? Basta adicioná-las ao arquivo php/Dockerfile:

```
RUN install-php-extensions \ 
    pdo_mysql \
    pdo_sqlite \
    bcmath \
    gd \
    zip
```

Depois de alterar o Dockerfile, lembre-se de rodar `docker compose up -d --build` para que as mudanças sejam aplicadas.

## Comandos Úteis do Docker

- Iniciar o ambiente: `docker compose up -d`

- Parar o ambiente: `docker compose down`

## Verificar status dos serviços: docker compose ps

- Ver logs de um serviço (ex: php): `docker compose logs -f` frankenphp

- Acessar o terminal do contêiner PHP: `docker compose exec frankenphp sh`
