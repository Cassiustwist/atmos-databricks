ATMOS — Pipeline de Engenharia de Dados Meteorológicos

ATMOS é um projeto de Engenharia de Dados desenvolvido para construir um pipeline end-to-end de ingestão, processamento, transformação e disponibilização de dados meteorológicos utilizando serviços Azure, Databricks e integração com Google Cloud Platform (GCP).

O projeto integra dados provenientes da API Visual Crossing e dados meteorológicos do INMET, disponibilizados através do Google BigQuery, realizando a ingestão e orquestração com Azure Data Factory (ADF) e o armazenamento no Azure Data Lake Storage Gen2 (ADLS Gen2).

No Azure Databricks, os dados são processados e organizados seguindo a Arquitetura Medalhão (Bronze, Silver e Gold). As diferentes fontes são padronizadas e unificadas na camada Silver, enquanto a camada Gold disponibiliza dados agregados para consumo analítico e visualização em dashboard.

🎯 Objetivo do Projeto

O projeto ATMOS tem como objetivo construir uma solução de Engenharia de Dados capaz de integrar dados meteorológicos provenientes de diferentes fontes, centralizando essas informações em uma estrutura organizada e preparada para análise.

A proposta consiste em automatizar a ingestão de dados da Visual Crossing e do INMET, armazená-los inicialmente em sua forma bruta e posteriormente realizar processos de tratamento, padronização e consolidação.

Como resultado, o projeto disponibiliza uma camada de dados consolidada que permite analisar informações meteorológicas como temperatura, precipitação, umidade e pressão atmosférica, considerando diferentes períodos e localidades.

Além do resultado analítico, o projeto foi desenvolvido com o objetivo de aplicar na prática conceitos de Engenharia de Dados, incluindo ingestão, armazenamento, transformação, organização em camadas, integração entre diferentes fontes e orquestração de pipelines.

🏗️ Arquitetura da Solução

O fluxo integra duas fontes de dados e utiliza serviços Azure e Databricks para conduzir os dados desde a ingestão até sua disponibilização para análise.



🛠️ Tecnologias Utilizadas

Azure Data Factory (ADF) — ingestão e orquestração dos dados provenientes das diferentes fontes.

Azure Data Lake Storage Gen2 (ADLS Gen2) — armazenamento dos dados ingeridos.

Azure Key Vault — armazenamento e gerenciamento seguro das credenciais utilizadas nas integrações.

Azure Databricks — tratamento, transformação e organização dos dados utilizando a arquitetura Medalhão (Bronze, Silver e Gold).

Google BigQuery — acesso aos dados meteorológicos do INMET disponibilizados pela Base dos Dados.

Visual Crossing API — fonte de dados meteorológicos consumida através de API.

SQL — consultas, transformações e agregações dos dados.

Git / GitHub — versionamento dos pipelines e notebooks e utilização de branches e Pull Request.

📥 Fontes de Dados

O ATMOS utiliza duas fontes distintas de dados meteorológicos:

Visual Crossing Weather API
Dados meteorológicos obtidos através de API REST, utilizados para ingestões históricas e também para o fluxo de ingestão diária.

INMET — Base dos Dados / Google BigQuery
Dados meteorológicos do INMET disponibilizados através da Base dos Dados no Google BigQuery, utilizados como segunda fonte para integração e consolidação das informações meteorológicas.

A utilização de fontes diferentes permite trabalhar, dentro do mesmo projeto, com diferentes formas de aquisição de dados e posteriormente padronizá-los em um modelo comum no Databricks.

Ingestão no Azure Data Factory

Os pipelines do ADF foram responsáveis pela ingestão e orquestração dos dados das fontes até a Landing Zone no Data Lake.



🔄 Pipeline de Dados

O processamento do ATMOS foi organizado seguindo a Arquitetura Medalhão, separando os dados em diferentes camadas de acordo com seu estágio de processamento.

🥉 Bronze — Dados brutos

Os dados provenientes das diferentes fontes são carregados para a camada Bronze, preservando as informações necessárias para os processamentos posteriores.

🥈 Silver — Tratamento, padronização e consolidação

Na camada Silver, os dados do INMET e da Visual Crossing são tratados e convertidos para uma estrutura comum. Entre as transformações realizadas estão a padronização dos nomes e tipos das colunas, tratamento de datas, validação de faixas plausíveis das variáveis meteorológicas, correção de valores inválidos e remoção de duplicidades.

Os dados do INMET também são enriquecidos com informações das estações meteorológicas, enquanto os dados da Visual Crossing passam pela transformação das estruturas JSON necessárias para sua organização. Após o tratamento, as duas fontes são consolidadas em uma tabela unificada, criando uma estrutura padronizada para as etapas seguintes do pipeline.

🥇 Gold — Dados para análise

A camada Gold utiliza os dados consolidados da Silver para produzir informações agregadas por período e localidade, incluindo indicadores relacionados a temperatura, precipitação, umidade e pressão atmosférica, disponibilizando uma estrutura preparada para consumo analítico.

Dessa forma, os dados evoluem desde sua ingestão em formato bruto até uma camada consolidada e preparada para análise.



⚙️ Orquestração do Pipeline

O fluxo de processamento no Databricks foi organizado através do Job Pipeline_ATMOS, responsável por executar as etapas do pipeline respeitando as dependências entre as diferentes camadas.

O fluxo executa separadamente os dados do INMET e da Visual Crossing nas etapas Bronze e Silver. Após o tratamento das duas fontes, os dados seguem para a etapa de unificação na Silver e, posteriormente, para a construção da camada Gold e atualização do Dashboard ATMOS.

Fluxo de execução:

Bronze INMET → Silver INMET
Bronze Visual Crossing → Silver Visual Crossing
Silver INMET + Silver Visual Crossing → Silver Unified → Gold → Dashboard

Essa estrutura garante que cada etapa seja executada somente após a conclusão das dependências necessárias, mantendo uma sequência organizada de processamento dos dados.



Execução concluída com sucesso



📊 Resultado e Consumo dos Dados

Após o processamento das fontes e sua consolidação na camada Silver, o pipeline gera a tabela Gold atmos.gold.perfil_sazonal_mensal, estruturada para análise de informações meteorológicas por período e localidade.

A camada Gold disponibiliza indicadores como temperatura média, temperaturas máximas e mínimas, precipitação acumulada, umidade média, pressão atmosférica e amplitude térmica, além da quantidade de dias considerados no processamento.



Para demonstrar o consumo desses dados, foi desenvolvido o Dashboard ATMOS diretamente no Databricks, contendo visualizações de precipitação por ano e cidade, temperatura por mês/ano/cidade e a identificação da última atualização dos dados.

O dashboard tem como objetivo demonstrar a utilização da camada Gold como fonte para consumo analítico, enquanto o foco principal do projeto permanece na construção e organização do pipeline de Engenharia de Dados.



🧠 Desafios e Aprendizados

O desenvolvimento do ATMOS também envolveu situações em que as configurações ou execuções não funcionaram como esperado na primeira tentativa. A resolução desses problemas fez parte do processo de aprendizado e exigiu revisar configurações, testar alternativas e compreender melhor a relação entre as diferentes etapas do pipeline.

🔎 Configurações e caminhos entre serviços
Em uma das etapas, uma diferença de nomenclatura entre o container criado no Data Lake e o caminho utilizado posteriormente impediu que o Databricks encontrasse corretamente os dados. Foi necessário retornar às configurações anteriores, comparar os caminhos e identificar a divergência antes de prosseguir com o pipeline.

📂 Localização dos arquivos da Visual Crossing
Em outro momento, os arquivos estavam presentes no Data Lake, mas a ingestão no Databricks não encontrava os registros esperados. A análise da estrutura mostrou que os arquivos estavam distribuídos em subdiretórios organizados por ano, mês e dia. A configuração da leitura precisou ser ajustada para considerar essa organização.

🔄 Checkpoints durante os testes
Após alterações na forma de leitura, alguns arquivos continuavam sem ser processados novamente. Durante a investigação, foi identificado que havia informações de execuções anteriores mantidas nos checkpoints. Como o projeto ainda estava em desenvolvimento e havia necessidade de reprocessar os arquivos, esses controles foram reinicializados e uma nova execução conseguiu concluir o processamento.

🧩 Diferenças entre as fontes de dados
INMET e Visual Crossing não possuíam originalmente a mesma estrutura. Ao longo da construção da Silver, foram necessários ajustes para que as duas fontes chegassem a um formato compatível antes da unificação. Esse processo também envolveu corrigir incompatibilidades encontradas durante os testes.

🗃️ Alterações nas estruturas durante o desenvolvimento
Conforme as transformações foram evoluindo, algumas estruturas já criadas deixaram de ser compatíveis com as novas definições. Em alguns testes foi necessário recriar estruturas e executar novamente determinadas etapas até que o fluxo estivesse consistente.

⚙️ Parametrização no Azure Data Factory
A configuração dos pipelines no ADF foi uma das partes mais desafiadoras do projeto. Parâmetros e expressões utilizadas para construir valores dinamicamente nem sempre funcionaram como esperado na primeira tentativa. Foi necessário realizar testes, revisar as configurações e ajustar as expressões até alcançar o comportamento necessário para a ingestão.

🔗 GitHub e execução do Job
Na etapa final, também foram necessários ajustes para que o Job do Databricks utilizasse corretamente os notebooks versionados na branch main do GitHub. Depois da revisão dos caminhos e configurações, o pipeline passou a executar utilizando o código versionado.

Esses desafios fizeram parte do processo de desenvolvimento do ATMOS e ajudaram a ampliar a compreensão sobre como os diferentes componentes de um pipeline se relacionam. Mais do que executar cada ferramenta isoladamente, o projeto exigiu acompanhar o fluxo de ponta a ponta, identificar onde os problemas estavam ocorrendo, realizar ajustes e validar o resultado até obter uma execução completa do pipeline.

Versionamento do projeto

O desenvolvimento também utilizou branches e Pull Request para integrar as alterações à branch principal.



📁 Estrutura do Repositório

O repositório está organizado de acordo com as principais etapas de processamento do projeto:

atmos-databricks
│
├── 01 - Create Bronze/
│   └── Ingest_raw.ipynb
│
├── 02 - Create Silver/
│   ├── transform_inmet.ipynb
│   ├── transform_visual_crossing.ipynb
│   └── transform_unified.ipynb
│
├── 03 - Create Gold/
│   └── Perfil_sazonal_mensal.ipynb
│
├── 04 - Dashboard/
│   └── Dashboard ATMOS.lvdash.json
│
├── images/
│   └── evidências visuais utilizadas neste README
│
└── README.md

O código referente à etapa de ingestão e orquestração realizada no Azure Data Factory está mantido em um repositório separado:

ATMOS — Azure Data Factory:
https://github.com/Cassiustwist/atmos-adf-

Autor: Cassius Martins da Silveira
