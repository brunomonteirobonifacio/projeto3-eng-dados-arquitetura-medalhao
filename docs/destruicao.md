# Limpeza e Reset do Ambiente

Para garantir a integridade dos testes e permitir que o pipeline seja executado do zero sem resíduos de execuções anteriores, desenvolvemos um script de **Limpeza Total**.

## Atenção
O uso do comando `CASCADE` removerá permanentemente todos os dados e metadados associados aos esquemas. Utilize este processo apenas em ambiente de desenvolvimento ou quando desejar reiniciar o ciclo da **Arquitetura Medallion**.

## 1. Script de Limpeza Total
O comando abaixo apaga sistematicamente todas as camadas do nosso Lakehouse no **Unity Catalog**.

```sql
-- Apagar todas as tabelas da camada bronze
DROP SCHEMA IF EXISTS workspace.bronze CASCADE;

-- Apagar todas as tabelas da camada silver
DROP SCHEMA IF EXISTS workspace.silver CASCADE;

-- Apagar todas as tabelas da camada gold
DROP SCHEMA IF EXISTS workspace.gold CASCADE;

-- Apagar todas as tabelas da camada landing (origem)
DROP SCHEMA IF EXISTS workspace.landing CASCADE;
```

2. Validação da Limpeza
Após a execução do script, validamos se o catálogo workspace está limpo. O resultado esperado é que os nomes dos esquemas acima não constem mais na lista oficial.

``` sql
-- Confirmar se tudo foi limpo
SHOW SCHEMAS IN workspace;
```