# Boas-vindas ao repositório do projeto Car Shop!

Este projeto pretende mostrar a aplicação dos princípios de Programação Orientada a Objetos (`POO`) para a construção de uma API com `CRUD` para gerenciar uma concessionária de veículos. Isso será feito utilizando o banco de dados `MongoDB`.


# Orientações


<details>
  <summary>
    <strong> 🐳 Como subir o banco do MongoDB usando Docker</strong>
  </summary><br>

  Caso não tenha o MongoDB instalado em sua máquina e deseje usar o Docker, é só seguir os passos a seguir:

  1. Baixe a imagem do MongoDB:

  ```sh
  docker pull mongo
  ```

  2. Crie o contêiner do MongoDB:

  ```sh
  docker run --name <nome-do-conteiner> -p 27017:27017 -d mongo
  ```

  3. Confira se o contêiner está rodando:

  ```sh
  docker ps
  ```

</details>

<details>
  <summary>
    <strong>🐳 Rodando no Docker vs Localmente</strong>
  </summary><br>

  ## Docker

  > Rode os serviços `node` e `mongodb` com o comando `docker-compose up -d`.
  - Lembre-se de parar o `mongo` se estiver usando localmente na porta padrão (`27017`), ou adapte, caso queria fazer uso da aplicação em containers
  - Esses serviços irão inicializar um container chamado `car_shop` e outro chamado `car_shop_db`.
  - A partir daqui você pode rodar o container `car_shop` via CLI ou abri-lo no VS Code.

  > Use o comando `docker exec -it car_shop bash`.
  - Ele te dará acesso ao terminal interativo do container criado pelo compose, que está rodando em segundo plano.

  > Instale as dependências com `npm install`
  
  ⚠ Atenção ⚠ Caso opte por utilizar o Docker, **TODOS** os comandos disponíveis no `package.json` (npm start, npm test, npm run dev, ...) devem ser executados **DENTRO** do container, ou seja, no terminal que aparece após a execução do comando `docker exec` citado acima. 


  ## Localmente

  > Instale as dependências com `npm install`

  ✨ **Dica:** Para rodar o projeto desta forma, obrigatoriamente você deve ter o `node` instalado em seu computador.

</details>

# Mapa do Projeto

## 01 - Interface `IModel` genérica

É criada a interface `IModel`, que será usada para a conexão com o banco de dados. Ela deverá ter, pelo menos, as funções `create()`, `read()`, `readOne()`, `update()` e `delete()`.

Por ser genérica, nossa interface deverá receber um tipo `T` qualquer, e ela deve esperar, em cada função, as seguintes especificações:
 - `create()`: deve receber um objeto do tipo `T`e retornar uma Promise do tipo `T`.
 - `read()`: deve retornar uma Promise contendo um array de objetos do tipo `T`.
 - `readOne()`: deve receber uma string e retornar uma Promise do tipo `T` ou nula.
 - `update()`: deve receber uma string e um objeto do tipo `T` e retornar uma Promise do tipo `T` ou nula.
 - `delete()`: deve receber uma string e retornar uma Promise do tipo `T` ou nula.
 - O arquivo deve ficar no diretório `/src/interfaces/` e  ter o nome de `IModel.ts`.
 - A interface deve ser exportada com o nome de `IModel` e não deve ser exportada de forma padrão.


## 02 - Interface `IVehicle` genérica

É criada a interface `IVehicle`, que será usada para criarmos nossos tipos de carro, moto e caminhão.
Ela deverá ter todos os atributos comuns de todos os veículos que listaremos aqui. São eles:

 | Atributo | Descrição |
 | :-------: | :-------- |
 | `model`   | Marca e/ou modelo do veículo. Deve ser uma string com, pelo menos, 3 caracteres |
 | `year`    | Ano de fabricação do veículo. Deve ser maior ou igual a 1900, porém menor ou igual a 2022 |
 | `color`   | Cor principal do veículo. Deve ser uma string com, pelo menos, 3 caracteres |
 | `status`  | Status que define se um veículo pode ou não ser comprado. Deve receber valores booleanos e deve ser opcional |
 | `buyValue` | Valor de compra do veículo. Deve receber apenas números inteiros |

 - O arquivo deve ficar no diretório `/src/interfaces/` e ter o nome de `IVehicle.ts`.
 - A interface deve ser exportada com o nome de `IVehicle` e **não deve** ser exportada de forma padrão.


## 03 - Interface `ICar` a partir da interface `IVehicle`

É criada a interface `ICar`, estendendo todos os atributos da interface `IVehicle` e, também, os atributos:

 | Atributo  | Descrição |
 | :--------: | :-------- |
 | `doorsQty` | Quantidade de portas de um carro. Deve ser maior ou igual a 2 e menor ou igual a 4 |
 | `seatsQty` | Quantidade de assentos disponíveis no carro. Deve ser maior ou igual a 2 e menor ou igual a 7 |
 
 - O arquivo deve ficar no diretório `/src/interfaces/` e  ter o nome de `ICar.ts`.
 - A interface deve ser exportada com o nome de `ICar` e não deve ser exportada de forma padrão.

  
## 04 - Interface `IMotorcycle` a partir da Interface `IVehicle`

É criada a interface `IMotorcycle`, estendendo todos os atributos da interface `IVehicle` e, também, os atributos:

 | Atributos        | Descrição |
 | :--------------: | :-------- |
 | `category`       | Categoria da moto. Deve poder ser **apenas** `Street`, `Custom` ou `Trail` |
 | `engineCapacity` | A capacidade do motor. Deve ser um valor inteiro positivo menor ou igual a 2500 |

 - O arquivo deve ficar no diretório `/src/interfaces/` e  ter o nome de `IMotorcycle.ts`.
 - A interface deve ser exportada com o nome de `IMotorcycle` e não deve ser exportada de forma padrão.


## 05 - Rota para o endpoint `POST /cars`

É criada uma rota que recebe uma requisição `POST` para cadastrar um veículo do tipo carro.

Mapa da `response`:

  - A rota retorna erro `400` caso a requisição receba um objeto vazio;
  - A rota retorna erro `400` ao tentar criar um carro com quantidade de assentos inferior a 2;
  - A rota retorna erro `400` ao tentar criar um carro com quantidade de portas inferior a 2;
  - A rota retorna erro `400` ao tentar criar um carro sem `model`, `year`, `color` e `buyValue`;
  - A rota retorna erro `400` ao tentar criar um carro sem `doorsQty` e `seatsQty`;
  - Não é possível criar um carro se os atributos `model`, `year`, `color`, `buyValue`, `doorsQty` e `seatsQty` estiverem com tipos errados;
  - É possível criar um carro se todos os parâmetros forem passados corretamente. A API deve responder com status http `201` e o seguinte body:
  ```JSON
    _id: "4edd40c86762e0fb12000003",
    model: "Ferrari Maranello",
    year: 1963,
    color: "red",
    buyValue: 3500000,
    seatsQty: 2,
    doorsQty: 2
  ```


## 06 - Rota para o endpoint `GET /cars` 

É criada uma rota que recebe uma requisição `GET` para receber todos os veículos do tipo carro registrados no banco de dados.

Mapa da `response`:

  - A API responderá com status http `200` em caso de sucesso;
  - É possível listar os carros com sucesso;
  - Haverá retorno de uma lista vazia se não houver carros.
  

## 07 - Rota para o endpoint `GET /cars/id`

É criada uma rota que recebe uma requisição `GET` para receber determinado veículo do tipo carro que possua o `id` passado como parâmetro na rota.

Mapa da `response`:

  - É disparado o erro `400` `Id must have 24 hexadecimal characters` caso o id possua menos que 24 caracteres;
  - É disparado o erro `404` `Object not found` caso o id possua 24 caracteres, mas seja inválido;
  - É possível listar um carro com sucesso através do id. A API responderá com status http `200` em caso de sucesso;
  

## 08 - Rota para o endpoint `PUT /cars/id`

É criada uma rota que recebe uma requisição `PUT` para atualizar determinado veículo do tipo carro que possua o `id` passado como parâmetro na rota.

Mapa da `response`:

  - É disparado o erro `404` `Object not found` caso o id possua 24 caracteres, mas seja inválido;
  - É disparado o erro `400` `Id must have 24 hexadecimal characters` caso o id possua menos que 24 caracteres;
  - É disparado o erro `400` caso o `body` esteja vazio;
  - É possível atualizar um carro se o Id for passado corretamente. A API deve responder com status http `200` e o seguinte body:
  ```JSON
    _id: "4edd40c86762e0fb12000003",
    model: "Fiat Uno",
    year: 1963,
    color: "blue",
    buyValue: 3500,
    seatsQty: 4,
    doorsQty: 4
  ```
 

## 09 - Rota para o endpoint `DELETE /cars/id` 

É criada uma rota que recebe uma requisição `DELETE` para excluir determinado veículo do tipo carro que possua o `id` passado como parâmetro na rota. 

Mapa da `response`:

  - É disparado o erro `404` `Object not found` caso o id possua 24 caracteres, mas seja inválido;
  - É disparado o erro `400` `Id must have 24 hexadecimal characters` caso o id possua menos que 24 caracteres;
  - A API deve responder com status http `204` sem body, em caso de sucesso;
  

## 10 - Rota para o endpoint `POST /motorcycles`

É criada uma rota que recebe uma requisição `POST` para cadastrar um veículo do tipo moto.

Mapa da `response`:

  - A rota retorna erro `400` caso a requisição receba um objeto vazio;
  - A rota retorna erro `400` ao tentar criar uma moto com `category` diferente de `Street`, `Custom` ou `Trail`;
  - A rota retorna erro `400` ao tentar criar uma moto com `category` diferente de `string`;
  - A rota retorna erro `400` ao tentar criar uma moto com `engineCapacity` menor ou igual a zero;
  - A rota retorna erro `400` ao tentar criar uma moto com `engineCapacity` maior que 2500;
  - A rota retorna erro `400` ao tentar criar um moto sem `model`, `year`, `color` e `buyValue`;
  - A rota retorna erro `400` ao tentar criar um moto sem `category` e `engineCapacity`;
  - Não é possível criar uma moto se os atributos `model`, `year`, `color`, `buyValue`, `category` e `engineCapacity` estiverem com tipos errados;
  - É possível criar uma moto se todos os parâmetros forem passados corretamente. A API deve responder com status http `201` e o seguinte body:
  ```JSON
    _id: "4edd40c86762e0fb12000003",
    model: "Honda CG Titan 125",
    year: 1963,
    color: "red",
    buyValue: 3500,
    category: "Street",
    engineCapacity: 125
  ```


## 11 - Rota para o endpoint `GET /motorcycles` 

É criada uma rota que recebe uma requisição `GET` para receber todos os veículos do tipo moto registrados no banco de dados.

Mapa da `response`:

  - Sua API responderá com status http `200`;
  - É possível listar as motos com sucesso;
  - Haverá retorno de uma lista vazia se não houver motos.
  

## 12 - Rota para o endpoint `GET /motorcycles/id`

É criada uma rota que recebe uma requisição `GET` para receber determinado veículo do tipo moto que possua o `id` passado como parâmetro na rota.

Mapa da `response`:

  - É disparado o erro `400` `Id must have 24 hexadecimal characters` caso o id possua menos que 24 caracteres;
  - É disparado o erro `404` `Object not found` caso o id possua 24 caracteres, mas seja inválido;
  - É possível listar uma moto com sucesso através do id. Sua API responderá com status http `200` em caso de sucesso;
  

## 13 - Rota para o endpoint `PUT /motorcycles/id`

É criada uma rota que recebe uma requisição `PUT` para atualizar determinado veículo do tipo moto que possua o `id` passado como parâmetro na rota.

Mapa da `response`:

  - É disparado o erro `404` `Object not found` caso o id possua 24 caracteres, mas seja inválido;
  - É disparado o erro `400` `Id must have 24 hexadecimal characters` caso o id possua menos que 24 caracteres;
  - É disparado o erro `400` caso o `body` esteja vazio;
  -  É possível atualizar uma moto se o Id for passado corretamente. A API responderá com status http `200` e o seguinte body:
  ```JSON
    _id: "4edd40c86762e0fb12000003",
    model: "Honda CG Titan 125",
    year: 1963,
    color: "black",
    buyValue: 3500,
    category: "Street",
    engineCapacity: 125
  ```


## 14 - Rota para o endpoint `DELETE /motorcycles/id` 

É criada uma rota que recebe uma requisição `DELETE` para excluir determinado veículo do tipo moto que possua o `id` passado como parâmetro na rota.

Mapa da `response`:

  - É disparado o erro `404` `Object not found` caso o id possua 24 caracteres, mas seja inválido;
  - É disparado o erro `400` `Id must have 24 hexadecimal characters` caso o id possua menos que 24 caracteres;
  - A API responderá com status http `204` sem body, em caso de sucesso;


## 15 - Testes para cobertura do projeto

É escrito teste para as três cadas do MSC.
  - Os teste para camada Model  estão na pasta `src/tests/unit/models`.
  - Os teste para camada Service estão na pasta `src/tests/unit/services`.
  - Os teste para camada Controller estão na pasta `src/tests/unit/controllers`.
