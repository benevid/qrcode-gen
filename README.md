# qrcode-gen
Neste tutorial, você aprenderá a criar um gerador de código QR para texto ou URLs usando Node.js. 

> QR Code, que significa código de resposta rápida, é a forma de um objeto gráfico que armazena dados que podem ser facilmente acessados por meio de digitalização. Inventado em 1994 pela Danson Wave Company.

### O que são códigos QR

Os códigos QR são compostos de módulos, que são pontos pretos e brancos (ou qualquer outra cor, como azul ou mesmo vermelho), que retêm os dados ao serem organizados em um determinado formato. O menor tamanho do código QR é de 2 cm x 2 cm (0,787 pol. X 0,787 pol.), Que é composto de 21 x 21 módulos.

O maior, por outro lado, pode ser muito grande e consistir em até 177 x 177 módulos. Isso pode armazenar 7089 caracteres numéricos ou 4296 caracteres alfanuméricos. Você pode ler mais sobre como os dados são armazenados no código QR [aqui](https://www.keyence.com/ss/products/auto_id/barcode_lecture/basic_2d/qr/#:~:text=A QR code is composed,(Reed-Solomon code)) .

Os códigos QR são muito eficientes para compartilhar dados. Vários dispositivos podem acessar os mesmos dados a qualquer hora e em qualquer lugar, sem restrições.

### Usos e importância dos códigos QR

Alguns dos principais usos de códigos QR no mundo como vemos hoje incluem:

- No pagamento online para acessar números de contas ou pagar contas.
- Anúncios para chegar a páginas da web.
- Por empresas para anunciar e baixar aplicativos.
- Compras e comércio eletrônico.
- Clientes diretos e potenciais em plataformas de mídia social e muito mais .

### Estrutura geral da pasta

A estrutura geral do projeto será conforme mostrado abaixo:

```
projeto-qrcode-lpoo (diretório raiz)
|--> node_modules (pasta)
|--> views (pasta)
		|--> index.ejs (arquivo) 
		|--> scan.ejs (arquivo)
|--> index.js (arquivo)
```



Vamos começar configurando nosso diretório de projeto. Crie uma pasta com o nome `projeto-qrcode-lpoo`. Abra a pasta com o Visual Studio Code. 



Uma vez dentro do VS Code, abra o terminal. Você pode fazer isso rapidamente usando o `Ctrl+`atalho em um PC ou o `Control + Shift +`atalho em um Mac.



Crie um `package.json`arquivo executando:

```bash
npm init -y
```

Depois de criado, execute o seguinte comando:

```bash
npm i qrcode express body-parser ejs
```

Isso instala os seguintes pacotes no projeto:

- analisador de corpo da URL
- ejs (modelos JavaScript incorporados)
- express
- QR code

### Adicionando um ponto de partida

Precisaremos de um ponto de partida para nosso aplicativo. Crie um arquivo chamado `index.js`.

Dentro do arquivo `index.js`, vamos fazer o seguinte em ordem sequencial:

- Importe os módulos necessários.

```javascript
const express = require("express");
const app = express();
const bp = require("body-parser");
const qr = require("qrcode");
```

- Use o pacote express para definir nosso template de modelo (mecanismo de visualização) e o middleware `body-parser` para analisar o corpos de objetos URL e JSON.

```javascript
app.set("view engine", "ejs");
app.use(bp.urlencoded({ extended: false }));
app.use(bp.json());
```

> Durante o tempo de execução, um **Template Engine** substitui as variáveis usadas no modelo de arquivo HTML pelos valores atuais. Em seguida, ele converte os modelos HTML em uma página visível que pode ser acessada. Você pode aprender mais sobre Node.js Template Engines [aqui](https://www.tutorialsteacher.com/nodejs/template-engines-for-nodejs) ou [aqui](https://expressjs.com/en/resources/template-engines.html) .

- Crie um `listener` para a rota raiz ( `/`) que renderize o arquivo `index.ejs`.

```javascript
// Simple routing to the index.ejs file
app.get("/", (req, res) => {
    res.render("index");
});
```

- Adicione um `listener`de requisições POST para converter Texto/URL em Código QR.

```javascript
app.post("/scan", (req, res) => {
    const url = req.body.url;
		//se os dados são nulo
    if (url.length === 0) res.send("Empty Data!");

    //converte a entrada em url e retorna uma imagem QRCode
    qr.toDataURL(url, (err, src) => {
        if (err) res.send("Error occured");
      
        // Retorna o QR code como fonte da pagina scan
        res.render("scan", { src });
    });
});
```

- Configurando as portas que estamos ouvindo. Esta linha inicia o servidor.

```javascript
const port = 5000;
app.listen(port, () => console.log("Server at 5000"));
```

#### Adicionando uma pasta de visualizações

Dentro do diretório principal, crie uma pasta adicional chamada `views`. Neste diretório, adicione 2 novos arquivos e nomeie-os `index.ejs` e `scan.ejs`.

> O arquivo `index.ejs` será a página padrão que carrega quando iniciarmos o aplicativo, enquanto o `scan.ejs` manterá nossa imagem QR Code após a geração.

Vamos criar uma estrutura de página simples em `index.ejs`. Em seguida, copiaremos a estrutura para o arquivo `scan.ejs` para promover a consistência do design da página da web. 

Devemos usar alguns css customizados e bootstrap online para acelerar o processo de estilização e encurtar o código.

Isso é mostrado no código abaixo:

```html
<!doctype html>
<html lang="en">
<head>
    <title>Gerador de QRCode</title>

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

    <!-- Custom CSS -->
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@500&display=swap');
        * {
            font-family: Montserrat;
        }
        body {
            margin: 10px;
            padding: 10px;
        }
    </style>
</head>

<body>
    <div class="container">
        <h1 class="text-center">Gerador QR Code</h1>
        <hr>
    </div>
</body>
</html>
```

Agora, dentro do arquivo `index.ejs`, vamos adicionar um elemento de entrada (`input`) dentro das tags `body` e um botão que chamaremos de `Gerar`, para executar o processo de geração de QR Code.

Isso é mostrado no código abaixo:

```html
<h4>Texto de Entrada</h4>
<hr>
<p>Informe o texto abaixo e clique em Gerar!</p>
<form class="form" action="/scan" method="POST">
    <input name="url" class="form-control" placeholder="URL ou Text0" type="text" required>
    <br>
    <button type="submit" class="btn btn-primary" value="Gerar QR">Gerar</button>
</form>
```

Dentro do arquivo `scan.ejs`, adicione um cartão que conterá a imagem QR Code gerada. Adicione também um botão que nos leva de volta à página anterior.

Isso é mostrado no código abaixo:

```html
<!doctype html>
<html lang="en">
<head>
    <title>Gerador de QRCode</title>

    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">


     <!-- Custom CSS -->
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@500&display=swap');
        * {
            font-family: Montserrat;
        }
        
        .card {
            margin: 0 auto;
            /* Added */
            float: none;
            /* Added */
            margin-bottom: 10px;
            /* Added */
        }
    </style>
  
</head>

<body>
    <div class="container">
        <h1 class="text-center">Gerador QR Code</h1>
        <hr>
        <img src=<%=src%> alt="QR Code Image">
        <p>Faça o escaneamento do QRCode para ver os dados!</p>
        <a href="/"><button type="button" class="btn btn-primary">Voltar</button></a>
        <br>
    </div>
</body>
</html>
```

Observe como usamos o atributo ejs source para adicionar facilmente nossa imagem à nossa página da web!

#### Executar o aplicativo Node.js

Agora vamos executar nosso aplicativo usando Node.js, executando o comando abaixo dentro do terminal:

```nodejs
node index.js
```

Acesse a página da web em `localhost:5000`.

Agora você pode inserir algum URL ou texto simples e clicar em gerar para ver a saída.

Parabéns, você criou com sucesso um programa gerador de QR Code em Node.js!

### Extras - classes bootstrap

#### index.ejs



```html
<!doctype html>
<html lang="en">

<head>
    <title>Gerador QR Code</title>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

    <!-- Custom CSS -->
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@500&display=swap');
        * {
            font-family: Montserrat;
        }
        
        .card {
            margin: 0 auto;
            /* Added */
            float: none;
            /* Added */
            margin-bottom: 10px;
            /* Added */
        }
    </style>
</head>

<body>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    <!-- Main Container -->
    <div class="container">
        <br>
        <h1 class="text-center">Gerador QR Code</h1>
        <hr>

        <!-- Input card -->
        <!-- row -->
        <div class="row mb-3">

            <!-- col.// -->
            <aside class="col-sm-12 text-center">
                <p>Gerador de QRCode LPOO</p>

                <div class="card">
                    <article class="card-body">
                        <h4 class="card-title text-center mb-4 mt-1">Entrada</h4>
                        <hr>
                        <p class="text-success text-center">Please paste in or type the URL or Text below and press Generate!</p>
                        <form action="/scan" method="POST">
                            <div class="form-group">
                                <div class="input-group">
                                    <div class="input-group-prepend">
                                        <span class="input-group-text"> <i class="fa fa-pencil"></i> </span>
                                    </div>
                                    <input name="url" class="form-control" placeholder="URL or Text" type="text" required>
                                </div>
                                <!-- input-group.// -->
                            </div>
                            <!-- form-group// -->

                            <!-- form-group// -->
                            <div class="form-group">
                                <button type="submit" class="btn btn-primary btn-block" value="Get QR">Generate</button>
                            </div>
                            <!-- form-group// -->

                            <p class="text-center"><a href="#" class="btn">Find help?</a></p>
                        </form>
                    </article>
                </div>
                <!-- card.// -->

            </aside>
            <!-- col.// -->
        </div>

        <!-- row.// -->
        <!--Image card end.//-->

    </div>
    <!--container end.//-->
</body>

</html>
```

#### scan.ejs

```html
<!doctype html>
<html lang="en">

<head>
    <title>QR Code Generator</title>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

    <!-- Custom CSS -->
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@500&display=swap');
        * {
            font-family: Montserrat;
        }
        
        .card {
            margin: 0 auto;
            /* Added */
            float: none;
            /* Added */
            margin-bottom: 10px;
            /* Added */
        }
    </style>

</head>

<body>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    <!-- Main Container -->
    <div class="container">
        <br>
        <h1 class="text-center">QR CODE GENERATOR</h1>
        <hr>

        <!-- Image card -->
        <div class="row mb-3">

            <div class="card" style="width: 18rem;">
                <img class="card-img-top" src=<%=src%> alt="QR Code Image">
                <div class="card-body">
                    <p class="card-text">Scan the QR Code to access data!</p>
                </div>
                <a href="/"><button type="button" class="btn btn-outline-secondary btn-lg">Back</button></a>
            </div>
            <!--Image card end.//-->
        </div>
        <!--container end.//-->
</body>

</html>
```

## Texto original

https://www.section.io/engineering-education/how-to-generate-qr-codes-using-nodejs/
