# Coleta de Dados de Licitações - Alagoas

## Visão Geral
Este projeto consiste em um script em R que realiza a extração de dados de licitações do portal de transparência do estado de Alagoas. Os dados são coletados via API e armazenados em um arquivo CSV para posterior análise.

## Tecnologias Utilizadas
- **R** (linguagem de programação)
- **Pacotes:**
  - `httr`: Para realizar requisições HTTP.
  - `jsonlite`: Para manipulação de dados JSON.
  - `dplyr`: Para manipulação de dados tabulares.

## Funcionalidades
- Extração de dados de licitações por ano (de 2020 a 2025).
- Tratamento de erros e retentativas automáticas em caso de falha (ex: erro 502 - Bad Gateway).
- Armazenamento dos dados extraídos em um arquivo CSV.
- Uso de paginação para garantir a coleta completa dos dados.

## Estrutura do Projeto
- `v1_chamada_inicial.R`: Script principal para coleta de dados.
- `editais_2020_a_2025.csv`: Arquivo gerado contendo os dados coletados.

## Como Executar
1. Certifique-se de ter o R instalado.
2. Instale os pacotes necessários executando:
   ```r
   install.packages(c("httr", "jsonlite", "dplyr"))
   ```
3. Execute o script no R:
   ```r
   source("v1_chamada_inicial.R")
   ```

## Detalhes da Implementação
- A URL base da API é: `https://transparencia.al.gov.br/licitacao/json-editais/`.
- Parâmetros utilizados na requisição:
  - **limit**: 100 (limite de registros por requisição)
  - **offset**: Controle de paginação
  - **data_realizacao_dti_** e **data_realizacao_dtf_**: Definem o período da consulta.
- O script utiliza um laço para percorrer os anos de 2020 a 2025.
- Em caso de erro 502, é feita uma nova tentativa até 3 vezes antes de abortar a coleta.

## Contato
Para mais informações ou dúvidas, entre em contato com o mantenedor do projeto.

---
Este projeto foi desenvolvido para auxiliar na obtenção de dados públicos de forma eficiente e estruturada.
