# Projeto Pipeline de Dados - Ouvidoria (Arquitetura Medalhão)

Projeto desenvolvido para a disciplina de Engenharia de Dados, focado na construção de um ecossistema de dados completo utilizando **Databricks** e **Delta Lake**. O pipeline realiza a extração de um banco de dados relacional **Supabase (PostgreSQL)** e organiza os dados seguindo a arquitetura de medalhão dentro do **Unity Catalog**.

## Funcionalidades

* **Extração Robusta:** Conexão via JDBC/Psycopg2 com base de dados Supabase na nuvem.
* **Arquitetura MedalliMedalhãon:** Implementação completa das camadas Landing, Bronze, Silver e Gold.
* **Governança de Dados:** Utilização do Unity Catalog para controlo de permissões e linhagem.
* **Modelagem Dimensional:** Criação de um Star Schema com tabelas Fato e Dimensão para BI.
* **Documentação Automatizada:** Site estático gerado via MkDocs com o tema Material.

## Arquitetura

O fluxo de dados segue o padrão de medalhão, garantindo a qualidade da informação em cada etapa do processo:

![Arquitetura da Pipeline de Dados](docs/Imagens/arquitetura.png)

# Estrutura do Projeto
```
projeto3-eng-dados-arquitetura-medalhao/
├── docs/                        # Arquivos da documentação MkDocs (.md)
├── notebooks/                   # Notebooks Databricks (Código Fonte)
│   ├── 01_preparacao.sql        # Configuração de Schemas e Permissões
│   ├── 02_extracao_landing.py   # Conexão Supabase -> Landing
│   ├── 03_bronze_layer.py       # Landing -> Bronze (Delta + Metadados)
│   ├── 04_silver_layer.py       # Bronze -> Silver (Limpeza e Padronização)
│   └── 05_gold_layer.sql        # Silver -> Gold (Fato e Dimensões)
├── mkdocs.yml                   # Configuração do site de documentação
├── pyproject.toml               # Dependências Python (Poetry)
└── README.md                    # Documentação principal
```

# Tecnologias Utilizadas

- Databricks — Plataforma de processamento distribuído.
- Apache Spark (PySpark) — Engine de processamento de Big Data.
- Delta Lake — Formato de armazenamento com suporte a transações ACID.
- Unity Catalog — Governança e segurança de dados.
- PostgreSQL (Supabase) — Fonte de dados operacional.
- MkDocs (Material Theme) — Framework para documentação do projeto.

# Como Executar o Projeto

### 1. Databricks (Notebooks)
Os notebooks devem ser executados na ordem numérica na plataforma Databricks:

01_preparacao_ambiente: Cria os esquemas (databases) no seu catálogo.

02_extracao_landing: Realiza a extração dos dados do Supabase.

03_bronze_layer: Persiste os dados em formato Delta com carimbos de data/hora.

04_silver_layer: Aplica regras de limpeza, conversão para maiúsculas e padronização.

05_gold_layer: Cria o Star Schema final com chaves substitutas (Surrogate Keys).

06_destruicao_ambiente: Destroi o ambiente permitindo executar os notebooks novamente de maneira limpa.

### 2. Documentação Local (MkDocs)
Para visualizar a documentação detalhada das transformações e regras de negócio:
```
# Instalar dependências (Poetry)
poetry install

# Iniciar o servidor local
poetry run mkdocs serve
```
# Equipe]

- Bruno M.
- Gianluca A.
- Luis Filipe D.
