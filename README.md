# linkage_pipeline_demo

Repositório demonstrativo para construção de uma pipeline de pareamento probabilístico de registros usando Python e Splink.

O projeto usa um notebook principal e uma base de teste em CSV para mostrar, de forma prática, como preparar dados, configurar regras de comparação, treinar um modelo de linkage, gerar pares candidatos, analisar clusters e interpretar os resultados.

## Visão geral

O pareamento de registros, também chamado de record linkage ou entity resolution, é usado para identificar registros que provavelmente representam a mesma entidade.

Neste projeto, a entidade pode ser uma pessoa, profissional de saúde, paciente, estabelecimento ou outro registro administrativo.

A ideia central é simples:

```text
Base com registros possivelmente duplicados
        ↓
Padronização e preparação dos campos
        ↓
Configuração das regras de comparação
        ↓
Treinamento probabilístico
        ↓
Geração de pares candidatos
        ↓
Análise dos scores de pareamento
        ↓
Formação de clusters
        ↓
Tabela final com grupos prováveis
```

## Objetivo do projeto

Este repositório tem como objetivo documentar uma pipeline básica, porém completa, para:

- testar pareamento em uma única base com registros duplicados;
- avaliar diferentes estratégias de comparação;
- entender como o Splink calcula probabilidades de match;
- inspecionar pares candidatos;
- analisar clusters de registros semelhantes;
- definir limiares de decisão;
- gerar uma base final com grupos de registros provavelmente equivalentes.

## Arquivos do repositório

```text
linkage_pipeline_demo/
│
├── README.md
├── splink_pareamento_notebook_example.ipynb
└── teste_pareamento.csv
```

### `README.md`

Arquivo de documentação do projeto.

Explica o objetivo, a estrutura, os conceitos principais, o fluxo da análise e as instruções de execução.

### `splink_pareamento_notebook_example.ipynb`

Notebook principal do projeto.

Contém o passo a passo da pipeline de pareamento, incluindo:

- leitura da base;
- preparação dos dados;
- criação de identificador único;
- configuração do Splink;
- definição de regras de comparação;
- treinamento do modelo;
- geração de pares candidatos;
- análise dos scores;
- criação de clusters;
- exportação dos resultados.

### `teste_pareamento.csv`

Base de teste usada no notebook.

Esse arquivo serve para executar a pipeline em um exemplo controlado. A base pode conter registros repetidos ou semelhantes, permitindo testar diferentes regras de pareamento.

O arquivo deve estar na mesma pasta do notebook para facilitar a leitura.

Exemplo de leitura no notebook:

```python
import pandas as pd

df = pd.read_csv("teste_pareamento.csv")
df.head()
```

Caso o arquivo esteja com problemas de acentuação, tente:

```python
df = pd.read_csv("teste_pareamento.csv", encoding="utf-8")
```

ou:

```python
df = pd.read_csv("teste_pareamento.csv", encoding="latin1")
```

## O que é Splink?

Splink é uma biblioteca Python para pareamento probabilístico de registros.

Ela permite deduplicar ou integrar bases de dados quando não existe um identificador único confiável.

Na prática, o Splink ajuda a responder perguntas como:

- estes dois registros representam a mesma pessoa?
- estes dois profissionais são a mesma entidade?
- este paciente aparece mais de uma vez na base?
- há vínculos duplicados para o mesmo indivíduo?
- quantos registros únicos existem depois do pareamento?

## Quando usar pareamento probabilístico

Use pareamento probabilístico quando os registros não são perfeitamente iguais, mas podem representar a mesma entidade.

Exemplos comuns:

```text
João da Silva
Joao Silva
J. da Silva
JOAO DA SILVA
```

Ou:

```text
Maria Aparecida Souza, nascimento 1980-05-12
Maria A. de Souza, nascimento 12/05/1980
```

Em dados reais, os problemas mais comuns são:

- nomes com acentuação diferente;
- abreviações;
- erros de digitação;
- campos vazios;
- CPF, CNS ou outros documentos ausentes;
- datas em formatos diferentes;
- múltiplos vínculos para a mesma pessoa;
- registros duplicados por mudança de unidade, CBO, CNES ou município.

## Casos de uso em saúde

Este tipo de pipeline pode ser aplicado em projetos como:

- deduplicação de pacientes;
- consolidação de profissionais de saúde;
- integração entre CNES, CNS, CPF, CBO e estabelecimentos;
- linkage entre bases clínicas, administrativas e epidemiológicas;
- pareamento entre bases de atenção primária, hospitalar e vigilância;
- identificação de múltiplos vínculos de um mesmo profissional;
- construção de base mestre de pessoas;
- preparação de dados para painéis, análises preditivas ou estudos observacionais.

## Conceitos principais

### Registro

Uma linha da base de dados.

Exemplo:

```text
id_linha | nome             | data_nascimento | cpf
1        | Maria Silva      | 1980-01-10      | 123
2        | Maria da Silva   | 1980-01-10      | 123
```

### Par candidato

Um par de registros comparados pelo modelo.

Exemplo:

```text
registro 1 comparado com registro 2
```

O Splink avalia se esse par parece representar a mesma entidade.

### Match

Dois registros que provavelmente representam a mesma entidade.

### Non-match

Dois registros que provavelmente representam entidades diferentes.

### Score de pareamento

Pontuação gerada pelo modelo indicando a força da evidência de que dois registros são equivalentes.

Quanto maior o score, maior a chance de match.

### Limiar de decisão

Ponto de corte usado para decidir se um par será tratado como match.

Exemplo:

```text
match_weight >= 5
```

ou:

```text
match_probability >= 0.95
```

### Cluster

Grupo de registros conectados por pares considerados matches.

Exemplo:

```text
Cluster 10:
- registro 1
- registro 2
- registro 7
```

Isso indica que os três registros provavelmente representam a mesma entidade.

## Deduplicação versus linkage entre bases

### Deduplicação

Acontece quando a comparação é feita dentro da mesma base.

Exemplo:

```text
teste_pareamento.csv contra ele mesmo
```

Objetivo:

```text
Encontrar registros duplicados dentro da própria base.
```

### Linkage entre bases

Acontece quando a comparação é feita entre duas bases diferentes.

Exemplo:

```text
base_A.csv contra base_B.csv
```

Objetivo:

```text
Encontrar registros correspondentes entre bases diferentes.
```

Este repositório usa uma estrutura que pode ser adaptada para os dois cenários.

## Fluxo da pipeline

## 1. Leitura dos dados

Primeiro, o notebook carrega o arquivo `teste_pareamento.csv`.

Exemplo:

```python
import pandas as pd

df = pd.read_csv("teste_pareamento.csv")
```

Depois disso, é importante avaliar:

```python
df.shape
df.head()
df.info()
df.isna().sum()
```

Essa etapa ajuda a entender:

- número de linhas;
- número de colunas;
- tipos de dados;
- campos ausentes;
- possíveis problemas de codificação;
- possíveis colunas úteis para pareamento.

## 2. Padronização dos campos

Antes do pareamento, é importante padronizar os campos usados na comparação.

Exemplos de tratamento:

- colocar texto em minúsculo;
- remover espaços duplicados;
- remover acentos;
- padronizar datas;
- limpar documentos;
- transformar campos vazios em nulos;
- criar versões auxiliares dos nomes.

Exemplo:

```python
import unicodedata
import re

def normalizar_texto(valor):
    if pd.isna(valor):
        return None

    valor = str(valor).strip().lower()
    valor = unicodedata.normalize("NFKD", valor)
    valor = "".join([c for c in valor if not unicodedata.combining(c)])
    valor = re.sub(r"\s+", " ", valor)

    return valor
```

Exemplo de aplicação:

```python
df["nome_norm"] = df["nome"].apply(normalizar_texto)
```

## 3. Criação de identificador único

O Splink precisa de uma coluna que identifique cada linha da base.

Exemplo:

```python
df = df.reset_index(drop=True)
df["unique_id"] = df.index.astype(str)
```

Esse `unique_id` identifica a linha, não necessariamente a pessoa única.

Depois do pareamento, vários `unique_id` diferentes podem pertencer ao mesmo cluster.

## 4. Escolha das colunas de comparação

As melhores colunas dependem da base.

Exemplos de colunas úteis:

```text
nome
nome_norm
cpf
cns
data_nascimento
sexo
municipio
uf
nome_mae
cnes
cbo
```

Campos fortes:

- CPF;
- CNS;
- data de nascimento;
- nome completo;
- nome da mãe.

Campos auxiliares:

- sexo;
- município;
- UF;
- CNES;
- CBO.

Em saúde, o ideal é combinar campos fortes com campos auxiliares, porque documentos podem estar ausentes ou preenchidos incorretamente.

## 5. Blocking

Blocking é a etapa que reduz o número de comparações.

Sem blocking, uma base com muitas linhas poderia gerar um número enorme de pares.

Exemplo:

```text
10.000 registros
Comparação completa: quase 50 milhões de pares
```

Blocking cria regras para comparar apenas pares plausíveis.

Exemplos:

```text
comparar apenas registros com mesma data de nascimento
comparar apenas registros com mesmo CPF
comparar apenas registros com mesmo primeiro nome e mesma UF
comparar apenas registros com mesmo ano de nascimento
```

Exemplo conceitual:

```python
blocking_rules = [
    "l.cpf = r.cpf",
    "l.data_nascimento = r.data_nascimento",
    "l.primeiro_nome = r.primeiro_nome and l.uf = r.uf"
]
```

O objetivo do blocking é reduzir custo computacional sem perder muitos pares verdadeiros.

## 6. Regras de comparação

As regras de comparação dizem ao Splink como comparar cada campo.

Exemplos:

### Comparação exata

```text
CPF igual
data de nascimento igual
sexo igual
```

Boa para campos estruturados.

### Comparação aproximada

```text
nome parecido
nome da mãe parecido
município parecido
```

Boa para campos textuais com variação de escrita.

### Comparação por níveis

Para o nome, por exemplo, é possível ter níveis como:

```text
igual exato
muito parecido
moderadamente parecido
diferente
```

Isso é útil porque `João Silva` e `Joao da Silva` não são idênticos, mas podem representar a mesma pessoa.

## 7. Treinamento do modelo

O Splink estima parâmetros para calcular a probabilidade de dois registros serem o mesmo indivíduo.

Em termos práticos, o modelo aprende quais campos são mais informativos.

Exemplo:

```text
CPF igual tem evidência muito forte
sexo igual tem evidência fraca
nome parecido tem evidência intermediária
data de nascimento igual tem evidência forte
```

Nem todos os campos têm o mesmo peso.

## 8. Predição dos pares

Depois da configuração e do treinamento, o modelo gera pares candidatos com indicadores como:

```text
unique_id_l
unique_id_r
match_weight
match_probability
```

Interpretação:

- `unique_id_l`: identificador do primeiro registro do par;
- `unique_id_r`: identificador do segundo registro do par;
- `match_weight`: peso do pareamento;
- `match_probability`: probabilidade estimada de match.

## 9. Análise dos scores

É importante analisar a distribuição dos scores antes de escolher o ponto de corte.

Exemplos de perguntas:

- os scores estão concentrados em valores baixos?
- existem grupos claramente separados?
- há uma faixa intermediária que exige revisão?
- quantos pares aparecem acima de cada limiar?
- o limiar escolhido gera muitos falsos positivos?

Exemplo de tabela:

| Faixa de score | Interpretação sugerida | Ação sugerida |
|---|---|---|
| Muito alto | Par provavelmente verdadeiro | Aceitar automaticamente |
| Intermediário | Par duvidoso | Revisar amostra |
| Baixo | Par provavelmente falso | Não considerar match |

## 10. Escolha do limiar

O limiar define a regra final de decisão.

Exemplo:

```text
Considerar match se match_probability >= 0.95
```

ou:

```text
Considerar match se match_weight >= 5
```

A escolha depende do objetivo.

### Quando priorizar precisão

Use limiar mais alto quando falso positivo é muito problemático.

Exemplo:

```text
Unir duas pessoas diferentes como se fossem uma só.
```

Esse erro pode ser grave em bases de saúde.

### Quando priorizar sensibilidade

Use limiar mais baixo quando é mais importante capturar quase todos os possíveis matches.

Exemplo:

```text
Gerar lista para revisão manual posterior.
```

## 11. Clustering

Depois de selecionar os pares prováveis, o Splink pode formar clusters.

Exemplo:

```text
Registro A combina com B
Registro B combina com C
Então A, B e C podem ficar no mesmo cluster
```

O cluster representa uma entidade consolidada provável.

## 12. Exportação dos resultados

Resultados úteis para exportar:

```text
pares_candidatos.csv
clusters.csv
base_com_cluster.csv
resumo_clusters.csv
```

Exemplo:

```python
df_resultado.to_csv("base_com_cluster.csv", index=False)
```

Também é possível salvar em Parquet:

```python
df_resultado.to_parquet("base_com_cluster.parquet", index=False)
```

## Como executar o projeto

## Opção 1: GitHub + Jupyter local

Clone o repositório:

```bash
git clone https://github.com/gregrodrigues22/linkage_pipeline_demo.git
```

Entre na pasta:

```bash
cd linkage_pipeline_demo
```

Instale as dependências:

```bash
pip install pandas numpy splink duckdb jupyter matplotlib seaborn pyarrow openpyxl
```

Abra o notebook:

```bash
jupyter notebook splink_pareamento_notebook_example.ipynb
```

## Opção 2: Google Colab

1. Baixe o notebook `splink_pareamento_notebook_example.ipynb`.
2. Abra no Google Colab.
3. Faça upload do arquivo `teste_pareamento.csv`.
4. Instale o Splink no início do notebook:

```python
!pip install splink duckdb
```

5. Execute as células em sequência.

## Requisitos

Recomendado:

```text
Python >= 3.9
pandas
numpy
splink
duckdb
jupyter
matplotlib
seaborn
pyarrow
openpyxl
```

Instalação rápida:

```bash
pip install pandas numpy splink duckdb jupyter matplotlib seaborn pyarrow openpyxl
```

## Exemplo de estrutura de dados

A base `teste_pareamento.csv` pode conter colunas como:

```text
nome
cpf
cns
data_nascimento
sexo
municipio
uf
nome_mae
cnes
cbo
```

Nem todas as colunas são obrigatórias.

O importante é adaptar o notebook para os campos disponíveis.

## Exemplo de problema resolvido

Imagine uma base com profissionais de saúde.

Um mesmo profissional pode aparecer mais de uma vez porque tem:

- mais de um CBO;
- mais de um CNES;
- mais de um vínculo;
- mais de um município de atuação;
- variações no nome;
- documentos ausentes em algumas linhas.

A pipeline ajuda a identificar quais linhas provavelmente pertencem ao mesmo profissional.

Depois do pareamento, é possível criar uma tabela consolidada com colunas de resumo, como:

```text
id_entidade
nome_representativo
qtd_linhas_no_cluster
qtd_cbos_diferentes
qtd_cnes_diferentes
tem_mais_de_1_cbo
tem_mais_de_1_cnes
lista_cbos
lista_cnes
```

Esse tipo de saída é útil para análise gerencial, painéis e auditoria.

## Interpretação prática em projetos de saúde

Em dados de saúde, o resultado do linkage não deve ser tratado como verdade absoluta sem validação.

O ideal é separar os resultados em três grupos:

### 1. Match automático

Pares com evidência muito forte.

Exemplo:

```text
CPF igual + data de nascimento igual + nome muito semelhante
```

### 2. Revisão manual

Pares intermediários.

Exemplo:

```text
nome parecido + mesma data de nascimento, mas documento ausente
```

### 3. Não match

Pares com evidência fraca.

Exemplo:

```text
nome diferente + data diferente + município diferente
```

## Métricas úteis

Quando existe uma base rotulada, ou seja, com verdade conhecida, é possível calcular:

- precisão;
- sensibilidade;
- F1-score;
- falsos positivos;
- falsos negativos;
- matriz de confusão.

Quando não existe verdade conhecida, é possível avaliar por:

- revisão manual de amostra;
- inspeção de pares com score alto;
- inspeção de pares na faixa intermediária;
- distribuição dos scores;
- tamanho dos clusters;
- clusters suspeitos com muitos registros;
- comparação com regras determinísticas.

## Boas práticas

### Antes do pareamento

- conferir duplicidades exatas;
- padronizar nomes;
- padronizar datas;
- limpar documentos;
- avaliar nulos;
- criar identificador único por linha;
- documentar as colunas usadas;
- evitar usar campos muito instáveis sem análise prévia.

### Durante o pareamento

- testar mais de uma regra de blocking;
- comparar diferentes limiares;
- avaliar pares intermediários;
- verificar campos que mais contribuem para o score;
- evitar aceitar automaticamente resultados sem inspeção.

### Depois do pareamento

- revisar clusters grandes;
- exportar pares e clusters;
- manter rastreabilidade;
- documentar versão da base;
- documentar versão do notebook;
- salvar critérios usados;
- gerar amostras para auditoria.

## Riscos e cuidados

### Falso positivo

Ocorre quando o modelo junta dois registros que não deveriam ser unidos.

Em saúde, esse erro pode ser grave porque pode consolidar informações de pessoas diferentes.

### Falso negativo

Ocorre quando o modelo deixa de juntar registros que deveriam ser unidos.

Esse erro pode gerar contagem duplicada, histórico fragmentado ou subestimação de vínculos.

### Campos sensíveis

Dados de saúde podem conter informações pessoais ou sensíveis.

Em projetos reais, é importante:

- usar bases anonimizadas sempre que possível;
- restringir acesso;
- proteger identificadores diretos;
- evitar publicar dados reais no GitHub;
- usar dados sintéticos ou bases de teste;
- seguir normas de privacidade e governança aplicáveis.

## Sugestão de organização dos resultados

Uma estrutura simples de saída seria:

```text
outputs/
│
├── pares_candidatos.csv
├── pares_acima_limiar.csv
├── clusters.csv
├── base_com_cluster.csv
└── resumo_execucao.csv
```

## Sugestão de próximas melhorias

Possíveis próximos passos para evoluir o repositório:

- criar um arquivo `requirements.txt`;
- adicionar uma base de teste anonimizada maior;
- incluir cenários com diferentes regras de blocking;
- gerar gráficos de distribuição de score;
- criar relatório automático de clusters;
- incluir validação manual amostral;
- comparar limiares diferentes;
- exportar resultados em CSV e Parquet;
- criar notebook específico para análise de sensibilidade;
- adicionar exemplos de linkage entre duas bases diferentes.

## Exemplo de `requirements.txt`

```text
pandas
numpy
splink
duckdb
jupyter
matplotlib
seaborn
pyarrow
openpyxl
```

## Perguntas que este projeto ajuda a responder

- Quantos registros únicos existem depois do pareamento?
- Quais registros parecem duplicados?
- Quais pares têm maior chance de serem verdadeiros?
- Quais pares precisam de revisão manual?
- Qual limiar parece mais adequado?
- Quais campos ajudam mais na identificação de duplicidades?
- Existem clusters grandes demais ou suspeitos?
- A base tem múltiplos vínculos para a mesma entidade?
- Quantos CBOs, CNES ou vínculos diferentes uma mesma entidade possui?

## Referências

- Splink documentation: https://moj-analytical-services.github.io/splink/
- Splink PyPI: https://pypi.org/project/splink/
- Splink GitHub: https://github.com/moj-analytical-services/splink

## Autor

Gregório Victor Rodrigues

Médico, especialista em dados em saúde e criador da Patients2Python.

Portfólio:

```text
https://portfolio.patients2python.com.br/en
```

## Observação final

Este repositório é demonstrativo e educacional.

Antes de usar a pipeline em bases reais, especialmente bases de saúde, recomenda-se revisar os critérios de privacidade, governança, segurança da informação e validação dos resultados.
