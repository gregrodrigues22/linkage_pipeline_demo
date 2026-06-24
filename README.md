Repositório demonstrativo para construção de uma pipeline de pareamento de registros usando Python e Splink.

O objetivo é mostrar, de forma prática e reproduzível, como testar estratégias de linkage, comparar parâmetros e avaliar a qualidade do pareamento em bases de dados que podem conter registros duplicados, incompletos ou com pequenas variações de escrita.

## Objetivo do projeto

Este projeto apresenta um exemplo de pipeline para pareamento probabilístico de registros, com foco em:

- preparação da base de dados;
- padronização de campos;
- definição de regras de comparação;
- simulação de diferentes cenários de pareamento;
- avaliação dos resultados;
- identificação de possíveis pares verdadeiros;
- análise de sensibilidade de parâmetros.

A proposta é servir como ponto de partida para projetos de deduplicação, linkage interno ou integração entre bases distintas.

## Arquivo principal

O repositório contém o seguinte notebook:

```text
splink_pareamento_notebook_example.ipynb
