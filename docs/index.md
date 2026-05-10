# Projeto de Engenharia de Dados: Sistema de Ouvidoria

## Introdução
Este projeto apresenta a implementação de um ecossistema de dados completo para um **Sistema de Ouvidoria**. O objetivo principal foi centralizar as informações armazenadas em um banco de dados operacional (Supabase) e transformá-las em um ambiente analítico de alta performance utilizando o **Databricks** e a arquitetura **Data Lakehouse**.

A solução permite que gestores analisem manifestações, identifiquem os serviços mais afetados e entendam o perfil geográfico dos usuários, facilitando a tomada de decisão baseada em dados reais.

## Objetivo do Trabalho
O desafio consistiu em construir um pipeline de dados automatizado que:

1. **Extração** dados brutos de uma fonte externa (Cloud).
2. **Organização** esses dados seguindo os padrões de governança do Unity Catalog.
3. **Limpeza e tratamento** inconsistências para garantir a qualidade da informação.
4.  **Modelagem** os dados em um esquema dimensional (Star Schema) pronto para ferramentas de BI.

## Stack Tecnológica
Para a execução deste projeto, utilizamos as seguintes tecnologias:

* **Fonte de Dados:** Supabase (PostgreSQL)
* **Processamento:** Apache Spark (PySpark)
* **Plataforma:** Databricks
* **Governança:** Unity Catalog
* **Formato de Armazenamento:** Delta Lake (Arquitetura Medalhão)
* **Documentação:** MkDocs

## Equipe
- **Bruno Monteiro**
- **Luis Filipe Damiani**
- **Gian luca Andrade**

## Como este site está organizado
Navegue pelo menu lateral para entender cada fase do desenvolvimento:

- **Preparação:** Configuração do catálogo e permissões.
- **Extração:** Detalhes técnicos da conexão Supabase -> Databricks.
- **Bronze/Silver:** O processo de refinamento e limpeza.
- **Gold:** O modelo final com Tabelas Fato e Dimensões.