# Camada Bronze (Raw Data)

A camada **Bronze** representa o primeiro estágio de persistência dentro do nosso **Data Lakehouse**. Aqui, os dados deixam de ser meras cópias da origem e passam a ser tabelas gerenciadas pelo **Delta Lake** com metadados de auditoria.

## Objetivo
O propósito desta camada é manter a fidelidade total aos dados originais (estado bruto), mas garantindo:
- **Imutabilidade:** Um registro histórico do que foi extraído.
- **Rastreabilidade:** Saber exatamente quando o dado entrou no Lakehouse.
- **Confiabilidade:** Utilizar transações ACID do formato Delta.

## Comandos e Lógica de Ingestão

### 1. Leitura da Landing Zone
Os dados são lidos diretamente das tabelas temporárias da camada `landing` que foram populadas via conexão com o Supabase.

```python
# Lendo a tabela bruta
df_landing = spark.table("workspace.landing.ouvidoria")
```

### 2. Enriquecimento com Metadados Técnicos
Para garantir a governança, adicionamos colunas que não existiam no banco original. Isso é essencial para depuração (debug) e auditoria.

```python
from pyspark.sql.functions import current_timestamp, lit

# Adicionando timestamp de processamento e a origem do dado
df_bronze = df_landing \
    .withColumn("DATA_HORA_BRONZE", current_timestamp()) \
    .withColumn("FONTE_ORIGEM", lit("SUPABASE_POSTGRES"))
```

### 3. Persistência em Formato Delta
Diferente de um banco relacional comum, salvamos os dados no formato Delta, que permite alta performance em Big Data.

```python
# Escrita na camada Bronze do Unity Catalog
(df_bronze.write
  .format("delta")
  .mode("overwrite")
  .option("overwriteSchema", "true")
  .saveAsTable("workspace.bronze.ouvidoria"))
```

### Tabelas Processadas
Este mesmo padrão de comandos foi aplicado em loop para todas as tabelas do sistema:

ESTADO, CIDADE, USUARIO, OUVIDORIA, TIPO_OUVIDORIA, SERVICO_AFETADO.

### Garantia de Qualidade
Ao final deste processo, as tabelas na workspace.bronze estão prontas. Embora os nomes das colunas ainda possam conter prefixos técnicos (como cd_ ou nm_), elas já possuem:

- Tipagem inicial garantida pelo Spark.

- Carimbo de tempo (DATA_HORA_BRONZE).

- Suporte a Time Travel (viagem no tempo) nativo do Delta Lake.

!!! warning "Nota de Arquitetura" 
    A Bronze nunca deve ser acessada por usuários finais ou ferramentas de BI. Ela é uma camada exclusiva para os Engenheiros de Dados.