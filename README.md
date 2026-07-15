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


</p>

---

# ⭐ Grain da Tabela Fato

O Grain definido para este projeto é:

> **Uma linha representa exatamente uma reprodução (Play) realizada por um usuário.**

Essa decisão garante o menor nível de granularidade disponível, permitindo agregações futuras sem perda de informação.

---

# 🧩 Decisões de Modelagem

## 1. Nome e E-mail removidos da dimensão Usuário

Os campos **nome** e **email** foram removidos da dimensão por não possuírem valor analítico.

Além disso, evita exposição desnecessária de informações pessoais.

---

## 2. Seguidores removidos da dimensão Artista

O campo **seguidores** sofre alterações constantemente.

Caso fosse armazenado na dimensão, geraria milhares de novas versões dos registros.

Em um ambiente real, esse atributo seria tratado utilizando tabelas de Snapshot.

---

## 3. País do Usuário x País do Play

O projeto diferencia corretamente dois conceitos:

**dim_usuario.pais**

Representa o país de residência do usuário.

**fato_plays.pais_play**

Representa o país onde a reprodução ocorreu.

Exemplo:

Um usuário brasileiro viajando para Portugal continua sendo um usuário brasileiro, porém realiza reproduções em Portugal.

---

# 📈 Processo de Construção

## 1️⃣ Carregamento dos CSVs

Os arquivos CSV foram carregados como Views temporárias utilizando DuckDB.

### Views criadas

![Views](imagens/views_criadas.jpg)

---

## 2️⃣ Construção do Star Schema

Foram criadas as seguintes tabelas:

- dim_usuario
- dim_track
- dim_artista
- dim_data
- fato_plays

### Estrutura criada

![Tabelas](imagens/todas_as_tabelas.jpg)

---

## 3️⃣ Carga das Dimensões

Durante a carga das dimensões foram geradas Surrogate Keys utilizando:

```sql
ROW_NUMBER() OVER()
```

Além disso foram adicionados os campos:

- dt_inicio
- dt_fim
- is_current

preparando o modelo para implementação de SCD.

---

## 4️⃣ Carga da Tabela Fato

A tabela fato foi carregada realizando JOIN entre:

- Dim Usuário
- Dim Track
- Dim Artista
- Dim Data

Através das Surrogate Keys.

---

# ✔ Validação

Após o carregamento:

- Usuários: 30
- Tracks: 31
- Artistas: 21
- Plays: 170

### Validação

![Quantidade](imagens/quantidade_de_registros.jpg)

---

# 📊 Análises Realizadas

## 1. Total de reproduções por gênero musical

Objetivo:

Identificar quais gêneros possuem maior número de reproduções.

Resultado:

![Gênero](imagens/genero_musical.jpg)

---

## 2. Tempo médio de reprodução por plano

Objetivo:

Comparar o comportamento de consumo entre usuários dos diferentes planos.

Resultado:

![Plano](imagens/tempo_medio_por_plano.jpg)

---

## 3. Top 5 artistas por ouvintes únicos

Objetivo:

Identificar os artistas com maior alcance na plataforma.

Resultado:

![Top 5](imagens/top_5_artistas.jpg)

---

## 4. Reproduções por faixa etária e dispositivo

Objetivo:

Analisar como diferentes perfis de usuários consomem música em cada dispositivo.

Resultado:

![Faixa Etária](imagens/faixa_etaria_x_dispositivo.jpg)

---

# 🔄 Slowly Changing Dimensions (SCD)

Durante o desafio foram implementadas estratégias de versionamento das dimensões.

## Evento A

Mudança de plano dos usuários.

Estratégia utilizada:

**SCD Tipo 2**

Motivo:

Preservar o histórico das mudanças de plano.

---

## Evento B

Mudança de gravadora do artista.

Estratégia utilizada:

**SCD Tipo 2**

Motivo:

Permitir análises históricas considerando a gravadora vigente em cada período.

---

## Evento C

Correção do gênero musical do artista.

Estratégia utilizada:

**SCD Tipo 1**

Motivo:

Tratava-se apenas de uma correção cadastral, sem necessidade de manter histórico.

---

# 📚 Aprendizados

Durante este projeto foram praticados conceitos fundamentais de Engenharia de Dados:

- Modelagem Dimensional
- Star Schema
- Grain
- Surrogate Keys
- DuckDB
- SQL Analítico
- Slowly Changing Dimensions (Tipo 1 e Tipo 2)
- Modelagem para Data Warehouse

---

# 🚀 Conclusão

Este desafio permitiu construir um Data Warehouse completo utilizando DuckDB, aplicando conceitos de modelagem dimensional, criação de dimensões analíticas, carga de fatos e implementação de versionamento histórico através de SCD.

O resultado é um modelo preparado para suportar análises de negócio de forma consistente, escalável e alinhada às boas práticas de Engenharia de Dados.

---

## 👨‍💻 Autor

**Reginaldo Rocha**

Engenheiro de Cloud | Engenharia de Dados | Azure | SQL | Python
