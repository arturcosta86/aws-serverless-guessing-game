# Projeto: Jogo de Adivinhação com Arquitetura Serverless na AWS

Este repositório documenta a criação e implementação de uma aplicação web interativa, o "Jogo de Adivinhação", utilizando uma arquitetura 100% serverless na AWS. Este projeto foi desenvolvido como parte de um laboratório prático da **Escola da Nuvem**.

**Instrutor:** Tomas Alric ([@TomasAlric](https://github.com/TomasAlric/TomasAlric))
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

 ---

## 👨‍💻 Código da Aplicação

Abaixo estão os códigos completos para o backend e frontend, prontos para copiar e colar.

### Backend (`lambda_function.py`)
Este código Python é implantado na função Lambda e contém toda a lógica do jogo.

```python
import random

def lambda_handler(event, context):
    # Número pensado pela máquina
    numero_secreto = random.randint(1, 10)
    
    # Obtendo o palpite dos parâmetros da URL
    palpite = int(event['queryStringParameters']['palpite'])
    
    # Verificando o palpite e definindo a resposta
    if palpite == numero_secreto:
        resposta = f"Parabéns! Você acertou o número {numero_secreto}! 😎🤜🤛"
    elif palpite == numero_secreto - 1 or palpite == numero_secreto + 1:
        resposta = f"Quase! O número era {numero_secreto}. Tente novamente! 🤓"
    else:
        resposta = f"Incorreto, o correto é {numero_secreto}. Vou pensar em outro número. 😋"

    # Retornando a resposta no formato esperado pelo API Gateway
    return {
        'statusCode': 200,
        'headers': {
            'Access-Control-Allow-Origin': '*',
            'Access-Control-Allow-Headers': 'Content-Type',
            'Access-Control-Allow-Methods': 'GET,OPTIONS'
        },
        'body': f'{{"resultado": "{resposta}"}}'
    }
```

### Frontend (`index.html`)
Este é o código para a página web que é hospedada no S3. Ele contém a estrutura (HTML), o estilo (CSS) e o script (JavaScript) para chamar a API.

```html
<!DOCTYPE html>
<html lang="pt">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Jogo de Adivinhação</title>
    <style>
      body {
        font-family: 'Arial', sans-serif;
        background: linear-gradient(to right, #4facfe, #00f2fe);
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
        color: #fff;
      }
      .container {
        text-align: center;
        background: rgba(0, 0, 0, 0.7);
        border-radius: 10px;
        padding: 40px;
        box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
        max-width: 400px;
      }
      h2 {
        color: #ffdd57;
      }
      input[type="number"] {
        padding: 10px;
        width: 80%;
        margin-bottom: 20px;
        border: none;
        border-radius: 5px;
        text-align: center;
      }
      button {
        background-color: #ff6f61;
        color: white;
        padding: 15px 30px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
      }
      #resultado {
        margin-top: 20px;
        font-size: 1.5em;
        font-weight: bold;
        color: #ffdd57;
      }
      footer {
        margin-top: 30px;
        color: #ddd;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h2>Jogo de Adivinhação</h2>
      <p>Pense em um número de 1 a 10. Tente adivinhar!</p>
      <input type="number" id="palpite" min="1" max="10" />
      <button onclick="enviarPalpite()">Enviar</button>
      <div id="resultado"></div>
      <footer>
        <p>Escola da Nuvem💙- Artur Costa</p>
      </footer>
    </div>

    <script>
      function enviarPalpite() {
        const palpite = document.getElementById("palpite").value;
        // IMPORTANTE: Substitua a URL abaixo pela URL de invocação da sua API Gateway.
        const url =
          "[https://paffiass44.execute-api.us-east-2.amazonaws.com/prod/jogo?palpite=](https://paffiass44.execute-api.us-east-2.amazonaws.com/prod/jogo?palpite=)" +
          palpite;

        const resultadoDiv = document.getElementById("resultado");
        resultadoDiv.innerText = "Pensando...";

        fetch(url)
          .then((response) => response.json())
          .then((data) => {
            resultadoDiv.innerText = data.resultado;
          })
          .catch((error) => {
            console.error("Erro:", error);
            resultadoDiv.innerText = "Erro ao comunicar com a API.";
          });
      }
    </script>
  </body>
</html>
```

---

## 🚀 Demonstração

O GIF abaixo mostra o site em funcionamento, com o usuário interagindo e recebendo as respostas processadas pela arquitetura serverless.

![Jogo de Adivinhação](https://github.com/arturcosta86/aws-serverless-guessing-game/blob/main/GIF%20do%20Site%20Funcionando%20-%20Artur%20Costa.gif)

---

## ✅ Evidências da Implementação

A seguir estão as capturas de tela que comprovam a configuração de cada componente da solução na AWS.

**1. Bucket S3 Criado**
* **Descrição:** Criação do bucket `s3-website-arturcosta` para hospedar os arquivos do frontend.

![Bucket S3](https://github.com/arturcosta86/aws-serverless-guessing-game/blob/main/Print%20-%20Bucket%20S3%20-%20Artur%20Costa.jpeg)

**2. Função Lambda com a Lógica do Jogo**
* **Descrição:** Configuração da função `LambdaGame-arturcosta` com o código Python que implementa a lógica do jogo.

![Função Lambda](https://github.com/arturcosta86/aws-serverless-guessing-game/blob/main/Print%20-%20Fun%C3%A7%C3%A3o%20Lambda%20-%20Artur%20Costa.jpeg)

**3. API Gateway com a Rota `/jogo`**
* **Descrição:** Criação da API e da rota `GET /jogo` para expor a função Lambda ao mundo externo.

![API Gateway](https://github.com/arturcosta86/aws-serverless-guessing-game/blob/main/Print%20-%20API%20Gateway%20-%20Artur%20Costa.jpeg)

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
