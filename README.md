# Pokédex

Uma aplicação web que funciona como uma Pokédex, permitindo aos usuários pesquisar, visualizar e filtrar informações sobre os Pokémon, utilizando a [PokeAPI](https://pokeapi.co/docs/v2).



## Tecnologias Utilizadas

- **Vue.js 3** com **Composition API**
- **Vite**
- **TypeScript**
- **Consumo da PokeAPI**

## Link da Aplicação

A aplicação está disponível em produção no seguinte link: [Pokédex em Produção](https://vue-pokedex-vert.vercel.app/)

## Instruções

### Pré-requisitos

Certifique-se de que você tem o [Node.js](https://nodejs.org/) instalado em sua máquina.

### Instalação

1. Clone o repositório:
   ```bash
   git clone <URL do repositório>
   cd <nome do repositório>

2. Instale as dependências:
  ```bash
  npm install
```
3. Execute a aplicação:
  ```bash
  npm run dev
```
## Funcionalidades Implementadas

## Listagem de Pokémon:

Exibição de uma lista paginada de Pokémon com imagens, nomes e números.
Cada item mostra a imagem, nome e número do Pokémon.
Implementação de paginação clássica ou scroll infinito para navegação.
Detalhes do Pokémon:

### Informações detalhadas ao selecionar um Pokémon:
Estatísticas: HP, Attack, Defense, Special Attack, Special Defense e Speed.
Tipos: Exibição dos tipos com cores correspondentes.
Evoluções: Cadeia de evolução com imagens e nomes.
Busca e Filtro:

### Busca por Nome ou Número:
Barra de busca para pesquisar pelo nome ou número do Pokémon.
Resultados exibidos em tempo real ou ao submeter a pesquisa.
Filtro por Tipo:
Seleção de um ou mais tipos para filtrar a lista de Pokémon.
Atualização da listagem conforme os filtros aplicados.

### Responsividade e Desempenho:

A aplicação é responsiva, funcionando bem em dispositivos móveis e desktops.
Favoritos:

Permite ao usuário marcar Pokémon como favoritos e manter essa lista armazenada localmente.
Seção para listar os Pokémon favoritos.

## Notas sobre Decisões Técnicas
A escolha do Vue.js 3 e da Composition API foi feita para melhor organização do código e facilidade de manutenção.

A PokeAPI foi selecionada por fornecer uma ampla gama de informações sobre Pokémon de forma acessível e em tempo real.

O layout da aplicação foi inspirado no design disponível no [Figma](https://www.figma.com/design/3DgAD98hNgtjFAbzCTNkZP/Pok%C3%A9dex-(Community)?node-id=1016-1461&node-type=instance&t=AoqohvA2o8ytoppL-0)
