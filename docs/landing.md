# Extração de Dados (Landing Zone)

A camada **Landing** é o ponto de entrada de todos os dados no nosso Data Lakehouse. O objetivo aqui foi estabelecer uma conexão segura com o banco de dados operacional e replicar as tabelas brutas para o ambiente analítico.

## Conexão com a Fonte (Supabase)
Diferente de extrações baseadas em arquivos CSV, optamos por uma conexão direta via **JDBC/Psycopg2**. 

- **Tecnologia:** Python + SQL.
- **Protocolo:** Conexão segura com o endpoint do PostgreSQL no Supabase.
- **Desafio Técnico:** Configuramos o driver para suportar a extração em lote, garantindo que o volume de dados não sobrecarregasse a memória do cluster.

## Tabelas Extraídas
O script percorre o esquema `public` do Supabase e identifica as tabelas do sistema de ouvidoria. Foram extraídas 7 tabelas fundamentais:

- `estado`
- `cidade`
- `usuario`
- `ouvidoria`
- `tipo_ouvidoria`
- `servico_afetado`
- `anexo`

## Lógica de Extração
O processo segue os seguintes passos lógicos:

1. **Conexão:** Autenticação no Supabase utilizando credenciais seguras.
2. **Varredura:** Consulta ao `information_schema` para listar todas as tabelas disponíveis no schema configurado.
3. **Ingestão:** Leitura dos dados via Pandas para processamento inicial e conversão imediata para Spark DataFrame.
4. **Persistência:** Escrita dos dados no catálogo `workspace.landing` utilizando o formato Delta para garantir a integridade.

## Tratamento de Erros e Permissões
Durante o desenvolvimento, implementamos verificações de:

- **Validacão de Esquema:** Garantia de que o código aponta para o schema correto (`public`).
- **Controle de Acesso:** Gerenciamento de permissões via Unity Catalog para permitir a criação de tabelas no destino.

!!! info "Nota Técnica" 
    O uso do modo `overwrite` na escrita garante que a Landing Zone seja atualizada a cada execução, servindo como um "snapshot" atualizado do sistema operacional.