# Projeto: Jogo de Adivinhação com Arquitetura Serverless na AWS

Este repositório documenta a criação e implementação de uma aplicação web interativa, o "Jogo de Adivinhação", utilizando uma arquitetura 100% serverless na AWS. Este projeto foi desenvolvido como parte de um laboratório prático da **Escola da Nuvem**.

**Instrutor:** Tomas Alric
**Aluno:** Artur Costa ([@arturcosta86](https://github.com/arturcosta86))

## 🎯 Visão Geral do Projeto

O objetivo foi construir uma aplicação web completa, desde o backend até o frontend, utilizando serviços gerenciados da AWS para evitar a necessidade de gerenciar servidores. A aplicação permite que o usuário tente adivinhar um número entre 1 e 10, com o backend processando o palpite e retornando o resultado.

---

## 🛠️ Arquitetura e Serviços Utilizados

A solução integra três serviços principais da AWS de forma desacoplada:

1.  **Amazon S3:**
    * **Função:** Hospedagem do website estático (`index.html`).
    * **Detalhes:** O S3 foi configurado para servir o conteúdo da aplicação (HTML, CSS e JavaScript) publicamente na internet.

2.  **AWS Lambda:**
    * **Função:** Backend da aplicação (lógica do jogo).
    * **Detalhes:** Uma função Python (`lambda_function.py`) que gera um número aleatório, recebe o palpite do usuário via evento do API Gateway e retorna uma resposta em JSON.

3.  **Amazon API Gateway:**
    * **Função:** Ponto de entrada (endpoint) para o backend.
    * **Detalhes:** Uma API RESTful HTTP foi criada com uma rota `GET /jogo`. Essa rota é integrada à função Lambda, passando os parâmetros da requisição e expondo a lógica do backend de forma segura e gerenciável. O CORS foi configurado para permitir que o frontend hospedado no S3 possa chamar a API.

![Arquitetura da Solução](URL_PARA_UM_DIAGRAMA_SIMPLES_SE_TIVER) ---

## 🚀 Demonstração

O vídeo abaixo mostra o site em funcionamento, com o usuário interagindo e recebendo as respostas processadas pela arquitetura serverless.

https://github.com/arturcosta86/aws-serverless-guessing-game/assets/103693439/208889aa-5561-455b-80a5-87bd754b5dfd

---

## ✅ Evidências da Implementação

A seguir estão as capturas de tela que comprovam a configuração de cada componente da solução na AWS.

**1. Bucket S3 Criado**
* **Arquivo:** `Print - Bucket S3 - Artur Costa.jpeg`
* **Descrição:** Criação do bucket `s3-website-arturcosta` para hospedar os arquivos do frontend.

![Bucket S3](Print%20-%20Bucket%20S3%20-%20Artur%20Costa.jpeg)

**2. Função Lambda com a Lógica do Jogo**
* **Arquivo:** `Print - Função Lambda - Artur Costa.jpeg`
* **Descrição:** Configuração da função `LambdaGame-arturcosta` com o código Python que implementa a lógica do jogo.

![Função Lambda](Print%20-%20Função%20Lambda%20-%20Artur%20Costa.jpeg)

**3. API Gateway com a Rota `/jogo`**
* **Arquivo:** `Print - API Gateway - Artur Costa.jpeg`
* **Descrição:** Criação da API e da rota `GET /jogo` para expor a função Lambda ao mundo externo.

![API Gateway](Print%20-%20API%20Gateway%20-%20Artur%20Costa.jpeg)

---

## 🔧 Como Replicar

1.  **Backend (Lambda):** Crie uma função Lambda com runtime Python e adicione o código do arquivo `lambda_function.py`.
2.  **API (API Gateway):** Crie uma API HTTP, adicione uma rota `GET /jogo` e integre-a com a função Lambda criada no passo anterior. Configure o CORS para permitir requisições de qualquer origem (`*`).
3.  **Frontend (S3):**
    * Copie a URL de invocação da sua API Gateway.
    * Edite o arquivo `index.html` e substitua a URL da variável `url` pela sua.
    * Crie um bucket S3 com um nome único globalmente.
    * Desabilite o "Bloqueio de todo o acesso público" nas permissões do bucket.
    * Adicione uma política de bucket para permitir a ação `s3:GetObject` para todos (`Principal: "*"`).
    * Habilite a "Hospedagem de site estático" nas propriedades do bucket, definindo `index.html` como o documento de índice.
    * Faça o upload do arquivo `index.html` modificado para o bucket.
4.  **Acessar:** Abra o endpoint do site estático do S3 em seu navegador e jogue!