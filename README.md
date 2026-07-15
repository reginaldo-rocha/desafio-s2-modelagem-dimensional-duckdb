# 🎵 Desafio Semana 2 – Modelagem Dimensional com DuckDB

## 📌 Sobre o projeto

Este projeto foi desenvolvido como parte do **Desafio da Semana 2** da comunidade **Simplifica Dados**.

O objetivo foi transformar dados operacionais de uma plataforma fictícia de streaming de música (**SoundData**) em um modelo dimensional (**Star Schema**) utilizando **Python** e **DuckDB**, permitindo análises analíticas e implementação de **Slowly Changing Dimensions (SCD)**.

---

# 🎯 Objetivos

- Construir um modelo Star Schema
- Definir corretamente o Grain da tabela fato
- Criar dimensões analíticas
- Carregar os dados utilizando DuckDB
- Executar consultas analíticas
- Implementar Slowly Changing Dimensions (SCD Tipo 1 e Tipo 2)

---

# 🛠 Tecnologias utilizadas

- Python
- DuckDB
- Pandas
- SQL
- Jupyter Notebook

---

# 📂 Estrutura do Projeto

```
desafio-s2-modelagem/
│
├── imagens/
├── desafio_s2.ipynb
├── sounddata.duckdb
├── sounddata_artistas.csv
├── sounddata_tracks.csv
├── sounddata_usuarios.csv
└── sounddata_plays.csv
```

---

# 📊 Modelo Dimensional

O projeto foi modelado utilizando um **Star Schema**, composto por:

- Dimensão Usuário
- Dimensão Track
- Dimensão Artista
- Dimensão Data
- Fato Plays

## Diagrama

<p align="center">

<img width="1536" height="1024" alt="ChatGPT Image 15 de jul  de 2026, 02_45_56" src="https://github.com/user-attachments/assets/1bf23641-560d-4a83-8a3c-6d743caab522" />

# ⭐ Grain da Tabela Fato

> **Grain definido:**  
> Cada linha da tabela **fato_plays** representa exatamente uma reprodução (Play) realizada por um usuário.

### Por que esse Grain?

- Menor nível de granularidade disponível.
- Permite agregações por usuário, artista, música, data, dispositivo e plano.
- Evita perda de informação durante análises futuras.

---

# 🧩 Principais Decisões de Modelagem

## 👤 1. Nome e E-mail removidos da dimensão Usuário

Os campos **nome** e **email** não foram incluídos na dimensão de usuários.

**Motivos**

- Não possuem valor analítico.
- Não são utilizados em agrupamentos.
- Evitam exposição de dados pessoais.

---

## 🎤 2. Remoção do campo Seguidores

O atributo **seguidores** foi removido da dimensão de artistas.

**Motivos**

- Sofre alterações constantes.
- Geraria milhares de versões da dimensão.
- Em ambientes reais esse tipo de informação costuma ser armazenado em tabelas Snapshot.

---

## 🌎 3. País do Usuário × País da Reprodução

| Campo | Significado |
|-------|-------------|
| **dim_usuario.pais** | País de residência do usuário |
| **fato_plays.pais_play** | País onde a reprodução ocorreu |

Exemplo:

Um usuário brasileiro viajando para Portugal continua sendo um usuário brasileiro, porém realiza reproduções em Portugal.

---

# ⚙ Processo de Construção

## 📥 1. Carregamento dos Dados

Os arquivos CSV foram carregados como **Views temporárias** utilizando DuckDB.

### Resultado

![Views](imagem/views_criadas.png)

---

## ⭐ 2. Construção do Star Schema

Foram criadas as seguintes tabelas:

| Tipo | Tabela |
|------|---------|
| Dimensão | dim_usuario |
| Dimensão | dim_track |
| Dimensão | dim_artista |
| Dimensão | dim_data |
| Fato | fato_plays |

### Estrutura criada

![Star Schema](imagem/modelo_star_schema.png)

---

## 🔑 3. Carga das Dimensões

Durante a carga das dimensões foram geradas **Surrogate Keys** utilizando:

```sql
ROW_NUMBER() OVER()
```

Também foram adicionados os campos:

- dt_inicio
- dt_fim
- is_current

preparando o modelo para implementação de Slowly Changing Dimensions.

### Resultado

![Dimensões](imagem/carga_dimensoes.png)

---

## 🔗 4. Carga da Tabela Fato

A tabela fato foi carregada realizando JOIN entre:

- Dim Usuário
- Dim Track
- Dim Artista
- Dim Data

utilizando as Surrogate Keys.

### Resultado

![Fato](imagem/carga_fato.png)

---

# ✔ Validação da Carga

Após o carregamento do Data Warehouse foram obtidos os seguintes resultados.

| Tabela | Registros |
|---------|----------:|
| Usuários | 30 |
| Tracks | 31 |
| Artistas | 21 |
| Plays | 170 |

### Validação

![Validação](imagem/validacao.png)

---

# 📊 Análises Desenvolvidas

## 🎵 Total de reproduções por gênero

Objetivo:

Identificar os gêneros mais reproduzidos.

![Análise 1](imagem/analise_genero.png)

---

## 💳 Tempo médio por plano

Objetivo:

Comparar o comportamento dos usuários entre os diferentes planos.

![Análise 2](imagem/analise_plano.png)

---

## 🎤 Top 5 artistas

Objetivo:

Identificar os artistas com maior número de ouvintes únicos.

![Análise 3](imagem/analise_artistas.png)

---

## 📱 Faixa Etária × Dispositivo

Objetivo:

Analisar como diferentes perfis de usuários consomem música.

![Análise 4](imagem/analise_dispositivo.png)

---

# 🔄 Slowly Changing Dimensions (SCD)

| Evento | Estratégia | Justificativa |
|---------|------------|---------------|
| Mudança de plano | **Tipo 2** | Preservar histórico |
| Mudança de gravadora | **Tipo 2** | Manter histórico analítico |
| Correção de gênero | **Tipo 1** | Apenas correção cadastral |

### Resultado

![SCD](imagem/scd.png)

---

# 📚 Aprendizados

Durante este desafio foram praticados conceitos fundamentais de Engenharia de Dados.

- Modelagem Dimensional
- Star Schema
- Grain
- Surrogate Keys
- DuckDB
- SQL Analítico
- Slowly Changing Dimensions (Tipo 1 e Tipo 2)
- Data Warehouse

---

# 🚀 Conclusão

Este projeto permitiu construir um Data Warehouse completo utilizando **Python** e **DuckDB**, aplicando conceitos de modelagem dimensional, construção de Star Schema, carga de dimensões e fatos e implementação de Slowly Changing Dimensions.

O resultado é um modelo preparado para análises históricas, consistente e alinhado às boas práticas de Engenharia de Dados.
