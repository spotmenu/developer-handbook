# Web API Standard - Spotmenu

## Princípios Gerais

Este documento descreve os padrões para a construção e manutenção da **Spotmenu API**, uma API para gerenciamento de menus de restaurantes. O objetivo é fornecer uma API segura, eficiente e consistente que seja fácil de integrar e utilizar.

### 1. **Design Focado no Usuário**
A API deve ser intuitiva, com respostas previsíveis e bem estruturadas, otimizando a experiência de desenvolvedores.

### 2. **Autenticação e Segurança**
Toda comunicação deve ser feita através de **HTTPS** para garantir a segurança dos dados. A autenticação será gerenciada via **OAuth 2.0** ou **API Keys**, sendo necessária em todos os endpoints.

### 3. **Versionamento**
A API utilizará um cabeçalho personalizado para gerenciar o versionamento. Isso permite a evolução da API sem impactar clientes que utilizam versões anteriores.

### 4. **Busca Rápida**
A API oferece suporte a buscas rápidas e precisas em seus dados, com base em uma engine de pesquisa eficiente, que permite filtrar, ordenar e paginar os resultados de forma rápida, proporcionando uma experiência de busca responsiva.

---

## Diretrizes para Endpoints

### 1. **Verbos HTTP**
- **GET**: Recuperar dados (consultar recursos).
- **POST**: Criar novos dados (criar recursos).
- **PUT**: Atualizar dados existentes.
- **DELETE**: Remover dados.

### 2. **Estrutura de URLs**
URLs devem ser claras e baseadas em substantivos no plural, evitando complexidade desnecessária. Elas devem refletir diretamente os recursos que representam.

- **Recurso**: `/menus`  
  Usado para operações de criação e listagem de menus.
- **Recurso Específico**: `/menus/{menuId}`  
  Usado para manipulação de um menu específico.
- **Sub-recurso**: `/menus/{menuId}/items`  
  Usado para gerenciar os itens de um menu específico.

### 3. **Search (Busca de Dados)**
A API oferece uma endpoint dedicada para realizar buscas rápidas em menus e itens de menus, permitindo filtros e paginação para resultados relevantes.

- **Buscar menus**:  
  `GET https://search.spotmenu.com/menus?q={query}`

- **Buscar itens de menu**:  
  `GET https://search.spotmenu.com/items?q={query}&menuId={menuId}`

### Exemplos de URLs:
- Listar menus:  
  `GET /menus`
- Criar um novo menu:  
  `POST /menus`
- Obter detalhes de um menu:  
  `GET /menus/{menuId}`
- Atualizar um menu:  
  `PUT /menus/{menuId}`
- Excluir um menu:  
  `DELETE /menus/{menuId}`
- **Buscar menus por nome**:  
  `GET https://search.spotmenu.com/menus?q=dinner`
- **Buscar itens de menu com filtro**:  
  `GET https://search.spotmenu.com/items?q=burger&menuId=1`

---

## Busca de Dados

A API oferece suporte a uma engine de pesquisa rápida que permite realizar consultas eficientes e relevantes nos dados disponíveis. A busca permite que usuários filtrem, classifiquem e paginem os resultados com base em critérios específicos.

### Exemplos de Pesquisa:
- **Buscar menus por nome**:
  ```http
  GET https://search.spotmenu.com/menus?q=dinner
  ```

  **Resposta**:
  ```json
  {
    "hits": [
      { "id": 1, "name": "Dinner Menu", "items_count": 15 },
      { "id": 2, "name": "Vegan Dinner Menu", "items_count": 10 }
    ],
    "total_hits": 2,
    "page": 1,
    "per_page": 10
  }
  ```

- **Buscar itens de menu**:
  ```http
  GET https://search.spotmenu.com/items?q=burger&menuId=1
  ```

  **Resposta**:
  ```json
  {
    "hits": [
      { "id": 101, "name": "Cheeseburger", "price": 12.99 },
      { "id": 102, "name": "Vegan Burger", "price": 14.99 }
    ],
    "total_hits": 2,
    "page": 1,
    "per_page": 10
  }
  ```

### Filtros de Busca:
A API suporta filtros avançados, permitindo que usuários busquem dados com base em atributos específicos, como preço, categoria de item ou disponibilidade.

- **Buscar itens de menu com filtro de preço**:
  ```http
  GET https://search.spotmenu.com/items?q=pasta&min_price=10&max_price=20
  ```

  **Resposta**:
  ```json
  {
    "hits": [
      { "id": 201, "name": "Spaghetti Carbonara", "price": 18.50 },
      { "id": 202, "name": "Penne Arrabiata", "price": 12.75 }
    ],
    "total_hits": 2,
    "page": 1,
    "per_page": 10
  }
  ```

### Paginação:
A engine de busca permite a paginação dos resultados para otimizar a performance.

- **Parâmetros de paginação**:
  - **page**: Especifica a página de resultados (iniciando em 1).
  - **per_page**: Especifica o número de resultados por página.

Exemplo de URL com paginação:
```http
GET https://search.spotmenu.com/menus?q=dinner&page=2&per_page=5
```

---

## Versionamento via Cabeçalho

O versionamento da API será realizado através do cabeçalho HTTP `X-Api-Version`, em vez de incluir a versão na URL. Isso permite que diferentes clientes utilizem versões distintas sem alterar o endpoint base.

### Exemplo de uso do cabeçalho de versão:
```http
X-Api-Version: v1
```

Se houver novas versões da API, o cliente poderá continuar utilizando a versão anterior especificando-a no cabeçalho:

```http
X-Api-Version: v2
```

### Detalhes:
- **v1**: Versão inicial da API.
- **v2**: Versão com novos recursos e aprimoramentos, mantendo compatibilidade com v1.
- **v3**: Versão com mudanças significativas que podem alterar o funcionamento dos endpoints.

---

## Retornos e Status HTTP

A API deve sempre retornar um código de status HTTP apropriado, juntamente com uma resposta em JSON. Alguns códigos de status comuns incluem:

- **200 OK**: Operação bem-sucedida.
- **201 Created**: Recurso criado com sucesso.
- **400 Bad Request**: Erro nos dados enviados.
- **401 Unauthorized**: Falha de autenticação.
- **404 Not Found**: Recurso não encontrado.
- **500 Internal Server Error**: Erro interno do servidor.

### Exemplo de Resposta ao Criar um Novo Menu:
```json
{
  "id": 1,
  "name": "Dinner Menu",
  "created_at": "2024-09-18T12:00:00Z",
  "items": []
}
```

### Exemplo de Erro ao Criar um Menu com Dados Inválidos:
```json
{
  "error": "Invalid data",
  "message": "The field 'name' is required."
}
```

---

## Paginação

Para melhorar a eficiência ao lidar com grandes volumes de dados, a API deve suportar paginação. Os parâmetros `limit` e `page` serão usados para controlar o número de itens retornados.

### Exemplo de Solicitação Paginada:
- **Limite**: `/menus?limit=10`
- **Página**: `/menus?page=2`

### Exemplo de Resposta:
```json
{
  "menus": [
    { "id": 11, "name": "Dessert Menu", "items_count": 8 },
    { "id": 12, "name": "Drinks Menu", "items_count": 25 }
  ],
  "pagination": {
    "current_page": 2,
    "per_page": 10,
    "total_pages": 5,
    "total_count": 50
  }
}
```

---

## Autenticação e Segurança

### 1. **OAuth 2.0**
A API suporta autenticação via **OAuth 2.0**. Os clientes devem enviar o token de acesso no cabeçalho `Authorization`:

```http
Authorization: Bearer {access_token}
```

### 2. **Chave de API (API Key)**
Como alternativa, a autenticação pode ser feita via chave de API. A chave deve ser incluída no cabeçalho `Authorization`:

```http
Authorization: Bearer {API_KEY}
```

### 3. **HTTPS Obrigatório**
Toda comunicação entre clientes e servidores deve ser realizada via **HTTPS**, garantindo a criptografia dos dados em trânsito.

---

## Caching e Performance

### 1. **Cache-Control**
Para melhorar a eficiência, a API deve utilizar cabeçalhos de **Cache-Control** para reduzir a carga no servidor e melhorar o desempenho.

Exemplo:
```http
Cache-Control: max-age=3600
```
