# Camada Gold (Business & Modeling)

A camada **Gold** é o estágio final da nossa jornada de dados. Aqui, as informações são modeladas no formato **Star Schema** (Modelo Estrela), otimizadas para consultas rápidas e criação de dashboards em ferramentas como Power BI ou Tableau.

## Objetivos da Camada
- **Modelagem Dimensional:** Criação de Tabelas Fato e Dimensões.
- **Identidade Única:** Implementação de *Surrogate Keys* (SK) para garantir a integridade histórica.
- **Regras de Negócio:** Consolidação de métricas e relacionamentos complexos.

## O Modelo de Dados
Nosso modelo foca no processo de **Ouvidoria**, relacionando-o com as dimensões de **Tempo**, **Usuário** e **Localidade**.

### 1. Dimensão Tempo
Essencial para análises de sazonalidade e evolução temporal das manifestações.
```sql
-- Criamos a dimensão tempo com base no intervalo de datas das ouvidorias
CREATE TABLE workspace.gold.dim_tempo USING DELTA AS 
SELECT 
    EXPLODE(SEQUENCE(to_date('2024-01-01'), to_date('2026-12-31'), interval 1 day)) AS DATA,
    YEAR(DATA) AS ANO,
    MONTH(DATA) AS MES,
    DAY(DATA) AS DIA;
```
### 2. Dimensão Localidade (Merge Incremental)
Aqui resolvemos a normalização entre Cidades e Estados. Utilizamos o comando MERGE para garantir que, se um nome de cidade mudar, a Gold seja atualizada sem duplicar registros.

```sql
MERGE INTO workspace.gold.dim_localidade AS d
USING (
    SELECT c.ID_CIDADE, c.NOME_CIDADE, e.NOME_ESTADO, e.SIGLA_ESTADO
    FROM silver_cidade c
    INNER JOIN silver_estado e ON c.COD_ESTADO = e.ID_ESTADO
) AS r
ON d.ID_CIDADE = r.ID_CIDADE
WHEN NOT MATCHED THEN
  INSERT (ID_CIDADE, NOME_CIDADE, NOME_ESTADO, SIGLA_ESTADO)
  VALUES (r.ID_CIDADE, r.NOME_CIDADE, r.NOME_ESTADO, r.SIGLA_ESTADO);
```

### 3. Tabela Fato Ouvidoria (O Coração do Projeto)
O maior desafio técnico foi a Ponte de Localidade. Como a tabela de ouvidoria não possuía a cidade diretamente, cruzamos os dados através do usuário.


```sql
INSERT INTO workspace.gold.fato_ouvidoria
SELECT 
    t.DATA             AS FK_TEMPO,
    u_dim.SK_USUARIO   AS FK_USUARIO,
    l.SK_LOCALIDADE    AS FK_LOCALIDADE,
    COUNT(1)           AS QUANTIDADE_OUVIDORIA
FROM silver_ouvidoria o
INNER JOIN workspace.gold.dim_tempo t ON CAST(o.DATA_OUVIDORIA AS DATE) = t.DATA
INNER JOIN workspace.gold.dim_usuario u_dim ON o.COD_USUARIO = u_dim.ID_USUARIO
INNER JOIN silver_usuario u_silv ON o.COD_USUARIO = u_silv.ID_USUARIO
INNER JOIN workspace.gold.dim_localidade l ON u_silv.COD_CIDADE = l.ID_CIDADE
GROUP BY t.DATA, u_dim.SK_USUARIO, l.SK_LOCALIDADE;
```
## Valor Agregado ao Negócio
Com a Camada Gold finalizada, conseguimos responder perguntas como:

- "Qual o estado com maior número de reclamações?"

- "Qual a evolução mensal de ouvidorias por perfil de usuário?"

- "Existe concentração de problemas em cidades específicas?"

!!! success "Conclusão Técnica" 
    O uso de Surrogate Keys (SK_USUARIO, SK_LOCALIDADE) garante que, mesmo que o ID do banco operacional mude, nosso histórico analítico permaneça íntegro.