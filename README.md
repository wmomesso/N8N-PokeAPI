# ⚡ Integração PokéAPI no n8n

Este repositório contém um workflow para o **n8n** que demonstra como integrar e consumir dados da [PokéAPI](https://pokeapi.co/). É um exemplo prático de como realizar requisições HTTP `GET`, manipular JSON e transformar dados dentro do n8n.

![n8n Version](https://img.shields.io/badge/n8n-1.0%2B-orange?style=flat-square)
![API Status](https://img.shields.io/badge/API-Open-green?style=flat-square)

## 📋 Sobre o Projeto

O objetivo deste fluxo é buscar dados de um Pokémon aleatório a cada execução. Ele serve como um excelente exercício para entender:
1.  **HTTP Request Node:** Como conectar APIs RESTful abertas.
2.  **Code Node:** Uso de JavaScript básico para lógica (geração de IDs aleatórios).
3.  **Set Node:** Limpeza e simplificação de estruturas JSON complexas.

## 🛠️ Como Funciona o Fluxo

O workflow segue a seguinte lógica:

1.  **Manual Trigger:** Disparo manual para teste.
2.  **Gerar ID (Code Node):** Um script JavaScript gera um número aleatório entre 1 e 1025.
3.  **Buscar Dados (HTTP Request):** Faz uma chamada `GET` para `https://pokeapi.co/api/v2/pokemon/{ID}`.
4.  **Formatar (Set Node):** Filtra a resposta da API, mantendo apenas o Nome, Tipo, ID, Peso e URLs das imagens (Sprites).

## 🚀 Como Usar

### Pré-requisitos
* Uma instância do [n8n](https://n8n.io/) instalada (local ou cloud).

### Instalação

1.  Copie o código JSON abaixo.
2.  Abra seu editor do n8n.
3.  Cole o código diretamente na área de trabalho (Canvas) usando `Ctrl+V` (ou `Cmd+V`).

```json
{
  "name": "Exemplo PokeAPI - Random Pokemon",
  "nodes": [
    {
      "parameters": {},
      "id": "trigger-node",
      "name": "Ao clicar em Executar",
      "type": "n8n-nodes-base.manualTrigger",
      "typeVersion": 1,
      "position": [460, 300]
    },
    {
      "parameters": {
        "jsCode": "// Gera um número aleatório entre 1 e 1025\nreturn [\n  {\n    json: {\n      random_id: Math.floor(Math.random() * 1025) + 1\n    }\n  }\n];"
      },
      "id": "code-node",
      "name": "Gerar ID Aleatório",
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [680, 300]
    },
    {
      "parameters": {
        "url": "={{ '[https://pokeapi.co/api/v2/pokemon/](https://pokeapi.co/api/v2/pokemon/)' + $json.random_id }}",
        "options": {}
      },
      "id": "http-node",
      "name": "Buscar na PokéAPI",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.1,
      "position": [900, 300]
    },
    {
      "parameters": {
        "keepOnlySet": true,
        "values": {
          "string": [
            { "name": "Nome", "value": "={{ $json.name.toUpperCase() }}" },
            { "name": "Tipo Principal", "value": "={{ $json.types[0].type.name }}" },
            { "name": "Imagem URL", "value": "={{ $json.sprites.front_default }}" },
            { "name": "Imagem Shiny URL", "value": "={{ $json.sprites.front_shiny }}" }
          ],
          "number": [
            { "name": "Pokemon ID", "value": "={{ $json.id }}" },
            { "name": "Peso", "value": "={{ $json.weight }}" }
          ]
        }
      },
      "id": "set-node",
      "name": "Formatar Dados",
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.2,
      "position": [1120, 300]
    }
  ],
  "pinData": {},
  "connections": {
    "Ao clicar em Executar": {
      "main": [[{ "node": "Gerar ID Aleatório", "type": "main", "index": 0 }]]
    },
    "Gerar ID Aleatório": {
      "main": [[{ "node": "Buscar na PokéAPI", "type": "main", "index": 0 }]]
    },
    "Buscar na PokéAPI": {
      "main": [[{ "node": "Formatar Dados", "type": "main", "index": 0 }]]
    }
  }
}
```

## 📄 Exemplo de Saída (Output)

Após a execução, o nó final ("Formatar Dados") retornará um JSON limpo como este:

```json
[
  {
    "Nome": "CHARIZARD",
    "Tipo Principal": "fire",
    "Imagem URL": "[https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/6.png](https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/6.png)",
    "Imagem Shiny URL": "[https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/shiny/6.png](https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/shiny/6.png)",
    "Pokemon ID": 6,
    "Peso": 905
  }
]
```

## 📚 Recursos

* [Documentação Oficial do n8n](https://docs.n8n.io/)
* [Documentação da PokéAPI](https://pokeapi.co/docs/v2)

---
*Desenvolvido para fins educacionais.*
