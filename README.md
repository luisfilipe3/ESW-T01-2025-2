# RottenPotatoes - Gerenciador de Filmes em Ruby on Rails

Este projeto é uma aplicação desenvolvida em Ruby on Rails para gerenciar uma lista de filmes.  
A aplicação permite criar, visualizar, editar, deletar e ordenar filmes.

## Funcionalidades Implementadas

* **Listagem de Filmes**: Visualização de todos os filmes em uma tabela.
* **CRUD Completo**:
    * **Create**: Adicionar novos filmes ao banco de dados através de um formulário.
    * **Read**: Ver os detalhes de um filme específico.
    * **Update**: Editar as informações de um filme existente.
    * **Delete**: Remover um filme do banco de dados.
* **Ordenação de Colunas**: A lista de filmes pode ser ordenada em ordem alfabética pelo **Título do Filme** ou em ordem cronológica pela **Data de Lançamento** clicando nos cabeçalhos da tabela. A coluna ativa é destacada visualmente.

## Explicação da Implementação

A arquitetura da aplicação segue o padrão **Model-View-Controller (MVC)**, fundamental do Ruby on Rails:

* **Model (`app/models/movie.rb`)**: A classe `Movie` herda de `ActiveRecord::Base`, sendo responsável por representar os dados dos filmes e interagir diretamente com a tabela `movies` no banco de dados.

* **View (`app/views/movies/`)**: Os arquivos nesta pasta, escritos em HAML, são responsáveis pela camada de apresentação.
    * O arquivo `index.html.haml` foi editado para transformar os cabeçalhos da tabela em links com o helper `link_to`. Estes links passam um parâmetro `sort_by` na URL, que o controller utiliza para a ordenação. A view também usa uma variável de instância (`@sort_by`) para adicionar a classe CSS `hilite` e destacar a coluna ordenada.

* **Controller (`app/controllers/movies_controller.rb`)**: Atua como o intermediário, recebendo as requisições do usuário.
    * A action `index` foi melhorada para receber `params[:sort_by]` da URL. Com base nesse parâmetro, ela utiliza o método `Movie.order(...)` do ActiveRecord para consultar o banco de dados com a ordenação correta antes de enviar a lista de filmes para a view.

## Como Instalar e Executar a Aplicação

Estas instruções são para um ambiente de desenvolvimento Linux (Ubuntu) ou WSL no Windows.

### Pré-requisitos

* **Git**
* **asdf Version Manager**: Para gerenciar as versões do Ruby e Node.js.
* **Plugins do asdf**: `ruby` e `nodejs`.

### Passos para Instalação

1.  **Clone o repositório:**
    ```bash
    git clone [https://github.com/luisfilipe3/ESW-T01-2025-2.git](https://github.com/luisfilipe3/ESW-T01-2025-2.git)
    ```

2.  **Navegue para a pasta do projeto:**
    ```bash
    cd ESW-T01-2025-2
    ```

3.  **Instale as versões corretas do Ruby e Node.js:**
    *(O `asdf` lerá o arquivo `.tool-versions` e instalará as versões necessárias).*
    ```bash
    asdf install
    ```

4.  **Instale as dependências (gems) do projeto:**
    ```bash
    bundle install
    ```

5.  **Configure o banco de dados:**
    *(Este comando irá criar o banco de dados, executar as migrações e popular com dados iniciais).*
    ```bash
    rails db:create
    rails db:migrate
    rails db:seed
    ```

6.  **Inicie o servidor Rails:**
    ```bash
    rails server
    ```

7.  **Acesse a aplicação:**
    Abra seu navegador e visite `http://localhost:3000/movies`.
