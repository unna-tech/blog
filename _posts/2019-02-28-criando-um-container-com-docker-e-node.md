---
layout: post
current: post
navigation: 'True'
class: post-template
cover: /assets/images/cover-docker-node.png
placeholder: >-
  data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABgAAAAOCAIAAAC6mkspAAABS2lUWHRYTUw6Y29tLmFkb2JlLnhtcAAAAAAAPD94cGFja2V0IGJlZ2luPSLvu78iIGlkPSJXNU0wTXBDZWhpSHpyZVN6TlRjemtjOWQiPz4KPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyIgeDp4bXB0az0iQWRvYmUgWE1QIENvcmUgNS42LWMxNDIgNzkuMTYwOTI0LCAyMDE3LzA3LzEzLTAxOjA2OjM5ICAgICAgICAiPgogPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4KICA8cmRmOkRlc2NyaXB0aW9uIHJkZjphYm91dD0iIi8+CiA8L3JkZjpSREY+CjwveDp4bXBtZXRhPgo8P3hwYWNrZXQgZW5kPSJyIj8+nhxg7wAAAWdJREFUKJGt0D1vE0EQxvHn1pu7kMhWhFzT0qWh4BMgqhSIEppI+UK0SDRpKGigQBRIlNQUSBRIkMgRSAYi2Y5fovM9f4q1kuVMOk+1O5r9zc4U73+cYbkxBoulbWgA3EA6pwNgXBtSvbxMr8CEjShA2IjiJWEjCg1h+G1r+D0OT8p15fH9uy3lyYP9pw/3W8qk05vEXgBoZPvXoPw9qPK/vPr4pfWX47efJLWUNFOk0SoQ5s9ZdXRwR1m8/PA5n2gF/asAwbbtpKTs89enOdTay7M3Xw8f3RsXvVzBFC/eDXIlZUF7ty/WtztS93q0rF5S/K8i8/N8r+rW6dr1aF2Z9RdYCFlAxIwpVWjXC4AGSTbL0KlHhSQQO12L+axa9GeSbEvayhRJcUyZ+lx0qp3LeVKm8RZ2UiRNJ9uSc0VSXZeSUmOkmJSUmsZtWwq6Uub9yWrnjXJFKFcE4UqRlMpuUq5jTZH0F6WnufG+q1kbAAAAAElFTkSuQmCC
title: Criando um container com Docker e Node
description: Dockerino
date: '2019-02-28 01:40:40'
author: estevanmaito
category: dev
tags:
  - docker
---
Este é o segundo post dessa série:

- [Backend com Node, GraphQL, Mongo e Docker](https://blog.unna.tech/backend-com-node-graphql-mongo-e-docker)
- Você está aqui: Criando um container com Docker e Node

Há uns 3 anos eu comecei a buscar informações sobre esse tal de Docker que muita gente falava e até pouco tempo eu não tinha entendido a real utilidade dessa ferramenta. Eu sempre seguia os "Getting started" por aí, só que geralmente quem faz esse tipo de tutorial não faz ideia do que tá fazendo. Basicamente a galera cria uma pasta com um nome "docker-tutorial", joga uns comandos ali dentro e "agora você sabe como usar o Docker". Bom, não deixa de ser verdade, só que não serve pra nada!

Esse vai ser o post mais longo da série, e olha que nem terminei de criar essa aplicação! Mas eu garanto que se você acompanhar até o fim desse tutorial, você vai ser capaz de extrair o melhor essa ferramenta e entender o Docker de uma vez por todas.

## Pra que serve o Docker?

O Docker é uma ferramenta que cria e ajuda a administrar containeres nas nossas aplicações. Ótimo, não entendi.

Toda vez que você vai iniciar um projeto, vamos dizer com o `create-react-app`, você precisa executar essa sequência de comandos:

```shell
npx create-react-app projeto
cd projeto
npm start
```

Se precisar do pacote `styled-components`, por exemplo, lá vai você executar `npm install styled-components`, e assim vai pra todos os pacotes que precisar. Uma solução pra esse problema pode ser copiar o arquivo `package.json` de um projeto anterior e rodar o `npm install`, assim você já vai ter todas as dependências.

A solução acima tem uma limitação: você não tem a mesma estrutura de pastas do projeto anterior. Suave, é só copiar a pasta do outro projeto e matamos os dois problemas! Até aqui, não tem necessidade de usar o Docker.

A sacada aqui é que você tá fazendo tudo isso na sua máquina que já tem o Node (e o npm) instalado. Se você usar as abordagens que eu mostrei acima, e passar esse projeto pra outro dev ou formatar sua máquina, já era pra esse fluxo, porque você vai precisar instalar o Node do site oficial e agora, qual versão? Enquanto estamos no nível da aplicação apenas, falando só em pacotes, é muito trivial, mas quando falamos em infraestrutura, em manter uma base comum e ainda por cima resolvendo esses contratempos de ficar instalando pacote, aqui o Docker brilha.

## Como o Docker funciona?

Pense numa máquina virtual. Com ela você pode instalar um sistema operacional e nele um ambiente de desenvolvimento completo, por exemplo, desde o editor até o Node e os pacotes do projeto que falávamos há pouco. O Docker funciona bem parecido com isso, só que ele é mais portátil. Muito mais portátil.

No caso do projeto dessa série, o único pré-requisito vai ser o Ubuntu 18.04, todo o resto vai ser de responsabilidade do Docker, desde instalar todas as dependências (Node, Mongo, Redis, etc.) até copiar as pastas e rodar comandos. Isso porque o Docker depende de algumas partes do sistema operacional, o que garante que ele vai rodar no meu computador, no servidor e onde mais eu quiser!

E pra tudo isso acontecer, nós só vamos precisar instalar o `docker` e o `docker-compose` e criar dois arquivos.

Nós vamos chegar na parte em que eu explico como o código abaixo funciona, mas só pra dar água na boca, compara o [código necessário pra instalar o MongoDB manualmente](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/#install-mongodb-community-edition-using-deb-packages) com isso aqui, que é o que precisa no Docker:

```yaml
mongodb:
  image: mongo:4.0.6
  command: mongod --smallfiles
  environment:
    - MONGO_DATA_DIR=/data/db
  ports:
    - 27017:27017
  volumes:
    - ./data/db:/data/db
```

> Desenvolvedores sadistas vão a loucura ao saber que o código acima instala e também executa o MongoDB

Eu acho a curva de aprendizado do Docker parecida com a do Git. São poucos comandos, bem simples, que escondem um teoria complexa por trás de seu funcionamento, mas que você não precisa dominar para usar a ferramenta com propriedade.

## Instalação

Reforçando o que eu falei antes, você vai precisar do Ubuntu 18.04 pra continuar seguindo essa instalação.

Pra garantir a versão mais recente, vamos instalar o Docker a partir do repositório oficial e não do Ubuntu, que às vezes pode estar desatualizado.

1. Pra isso, vamos atualizar a lista de pacotes:

```shell
$ sudo apt-get update
```

2. Em seguida, instale os pacotes que são necessários para o `apt` utilizar pacotes via HTTPS:

```shell
$ sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

3. Adicione a chave GPG do repositório oficial do Docker ao sistema:

```shell
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

4. O comando a seguir vai configurar o repositório *stable*:

```shell
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

Show de bola, agooora que a gente vai começar a instalar o Docker CE (Community Edition):

1. Atualize a lista de pacotes (sim, de novo):

```shell
$ sudo apt-get update
```

2. Instale a última versão do Docker CE

```shell
$ sudo apt-get install docker-ce docker-ce-cli
```

3. Verifique que o comando abaixo retorna uma versão:

```shell
$ docker -v
Docker version 18.09.2, build 6247962
```

Pra não ficar tendo que rodar depois todos os comandos com `sudo`, eu aconselho criar um grupo no sistema pro Docker:

```shell
$ sudo groupadd docker
```

E adicionar o seu usuário a ele com poderes de `sudo`

```shell
$ sudo usermod -aG docker $USER
```

Deslogue e logue de volta que vai funcionar (às vezes pode precisar reiniciar, mas aí você vai descobrir quando rodarmos algum comando).

## Primeiro servidor com Node e Docker

Antes de continuar com `docker-compose`, instalar MongoDB, GraphQL e tudo mais, eu preciso cumprir minha promessa de te fazer ver o Docker como uma ferramenta que pode fazer a diferença no seu dia-a-dia.

























