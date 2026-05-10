# Preparação do Ambiente e Arquitetura

O sucesso de um projeto de Engenharia de Dados depende de um ambiente bem estruturado. Nesta etapa inicial, configuramos a fundação do nosso **Data Lakehouse** no Databricks utilizando o **Unity Catalog**.

## A Arquitetura Medallion
Adotamos a **Arquitetura Medallion**, que organiza os dados em camadas de qualidade crescente:

1. **Landing Zone:** Dados brutos extraídos diretamente do Supabase.
2. **Bronze:** Dados técnicos com metadados de rastreabilidade.
3. **Silver:** Dados limpos, padronizados e tipados.
4. **Gold:** Tabelas dimensionais e fatos prontas para análise de BI (Star Schema).



## Configuração do Catálogo
Para garantir a governança e segurança, estruturamos o ambiente no Databricks da seguinte forma:

- **Catálogo:** `workspace`
- **Esquemas (Databases):** - `landing`: Armazenamento temporário dos dados do Supabase.
    - `bronze`: Tabelas Delta iniciais.
    - `silver`: Tabelas Delta processadas.
    - `gold`: Modelo de dados final.

## Segurança e Governança
Durante esta fase, gerenciamos as permissões de acesso através do Unity Catalog, garantindo que o usuário tenha os privilégios necessários:
- `USE CATALOG`
- `USE SCHEMA`
- `CREATE TABLE`

## Ferramentas Utilizadas
- **Databricks:** Plataforma principal de processamento Spark.
- **Python (PySpark & Psycopg2):** Para orquestração e extração.
- **Unity Catalog:** Para governança de dados e controle de acesso.