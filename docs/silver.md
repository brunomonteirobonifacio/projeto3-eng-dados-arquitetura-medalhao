# Camada Silver (Cleansing & Standardization)

A camada **Silver** é onde ocorre a transformação real. O objetivo aqui é pegar os dados brutos da Bronze e transformá-los em informações limpas, consistentes e prontas para o consumo analítico.

## Objetivos da Camada
- **Padronização:** Garantir que todos os nomes de colunas sigam o mesmo padrão.
- **Limpeza:** Tratar valores nulos e converter strings para maiúsculas.
- **Tipagem:** Garantir que datas e números estejam nos formatos corretos para cálculos.

## Comandos e Transformações Aplicadas

### 1. Padronização de Nomenclatura e Case
Para garantir a consistência, aplicamos uma função que converte todos os nomes de colunas para `UPPERCASE` e substitui prefixos técnicos por nomes mais claros.

```python
# Exemplo da lógica de renomeação que aplicamos
for col_name in df.columns:
    new_col_name = col_name.upper() \
        .replace("CD_", "CODIGO_") \
        .replace("NM_", "NOME_") \
        .replace("DS_", "DESCRICAO_")
    df = df.withColumnRenamed(col_name, new_col_name)
```

### 2. Tratamento de Strings
Transformamos todos os campos de texto para caixa alta (upper) para evitar problemas de duplicidade em filtros (ex: "São Paulo" vs "são paulo").]

```python
from pyspark.sql.functions import col, upper

# Aplicando upper em colunas de texto
df = df.withColumn("NOME_CIDADE", upper(col("NOME_CIDADE")))
```

### 3. Enriquecimento de Metadados
Assim como na Bronze, adicionamos uma coluna de controle para saber quando o dado foi refinado pela última vez:

```python
from pyspark.sql.functions import current_timestamp

df = df.withColumn("DATA_PROCESSAMENTO_SILVER", current_timestamp())
```

### Regras de Negócio Implementadas
Durante o processamento das tabelas de Usuário e Ouvidoria, aplicamos:

- **Remoção de duplicatas:** Garantindo que cada ID seja único.

- **Casting de Datas:** Conversão de strings de data vindas do Supabase para o tipo DateType() do Spark.

### Persistência
Os dados são salvos no esquema ```workspace.silver``` utilizando o comando overwrite para garantir que a versão mais limpa dos dados esteja sempre disponível.

```python
df.write.format("delta").mode("overwrite").saveAsTable("workspace.silver.tabela_limpa")
```

!!! success "Resultado" 
    Ao final desta etapa, temos tabelas confiáveis onde os nomes das colunas são legíveis e os dados estão padronizados para o cruzamento que faremos na Camada Gold.