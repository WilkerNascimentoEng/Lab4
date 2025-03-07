# Lab4

### 1. Criar Recursos do Azure

Antes de iniciar o processo, precisamos criar os seguintes recursos:
- **Azure AI Search**: Gerenciará a indexação e a consulta.
- **Azure AI Services**: Fornecerá serviços de IA para enriquecer os dados.
- **Azure Storage Account**: Armazenará documentos brutos e dados processados.

> **Importante:** Certifique-se de que os recursos do Azure AI Search e Azure AI Services estejam na mesma região!

#### Criando o Azure AI Search
1. Acesse o [Portal do Azure](https://portal.azure.com/).
2. Clique em **+ Criar um recurso** e pesquise por **Azure AI Search**.
3. Configure os seguintes parâmetros:
   - Assinatura: Sua assinatura do Azure.
   - Grupo de Recursos: Selecione ou crie um grupo de recursos.
   - Nome do Serviço: Nome único para o recurso.
   - Localização: Escolha uma região disponível.
   - Nível de Preço: **Básico**.
4. Clique em **Revisar + Criar** e depois em **Criar**.
5. Após a implantação, clique em **Ir para o recurso**.

#### Criando o Azure AI Services
1. No portal do Azure, clique em **+ Criar um recurso** e pesquise por **Azure AI Services**.
2. Configure os seguintes parâmetros:
   - Assinatura: Sua assinatura do Azure.
   - Grupo de Recursos: O mesmo do Azure AI Search.
   - Região: O mesmo do Azure AI Search.
   - Nome: Nome único.
   - Nível de Preço: **Standard S0**.
3. Clique em **Revisar + Criar** e depois em **Criar**.

#### Criando uma Conta de Armazenamento
1. No portal do Azure, clique em **+ Criar um recurso** e pesquise por **Conta de Armazenamento**.
2. Configure os seguintes parâmetros:
   - Assinatura: Sua assinatura do Azure.
   - Grupo de Recursos: O mesmo usado anteriormente.
   - Nome: Nome exclusivo.
   - Localização: Escolha uma região.
   - Desempenho: **Padrão**.
   - Redundância: **LRS** (Armazenamento Redundante Local).
3. Clique em **Revisar + Criar** e depois em **Criar**.
4. No recurso criado, acesse **Configurações > Configuração** e habilite **Permitir acesso anônimo do Blob**.

### 2. Carregar Documentos para o Azure Storage
1. No portal do Azure, acesse sua conta de armazenamento.
2. Clique em **Contêineres > + Container** e crie um contêiner chamado `coffee-reviews`.
3. Defina o **Nível de Acesso Público** como **Container**.
4. Baixe os arquivos de avaliação de clientes em [aka.ms/mslearn-coffee-reviews](https://aka.ms/mslearn-coffee-reviews) e extraia para uma pasta local.
5. No Azure, acesse o contêiner `coffee-reviews`, clique em **Upload** e envie os arquivos extraídos.

### 3. Indexar os Documentos
1. No portal do Azure, acesse o recurso **Azure AI Search** e clique em **Importar dados**.
2. Configure os parâmetros:
   - Fonte de Dados: **Armazenamento de Blobs do Azure**.
   - Nome da Fonte de Dados: `coffee-customer-data`.
   - Dados a Extrair: **Conteúdo e Metadados**.
   - Selecione sua conta de armazenamento e o contêiner `coffee-reviews`.
3. Clique em **Próximo: Adicionar habilidades cognitivas** e conecte o **Azure AI Services** criado.
4. Adicione enriquecimentos:
   - Nome do Skillset: `coffee-skillset`.
   - Habilite OCR e mescle o texto no campo `merged_content`.
   - Adicione habilidades como **extração de localização**, **frases-chave**, **sentimento** e **tags de imagem**.
5. Configure o armazenamento de conhecimento:
   - Crie um contêiner chamado `knowledge-store`.
   - Selecione projeções como **Documentos, Páginas, Frases-chave**.
6. Crie o índice:
   - Nome do índice: `coffee-index`.
   - Chave: `metadata_storage_path`.
7. Crie o indexador:
   - Nome: `coffee-indexer`.
   - Configurações: Execute uma vez, habilite **Base-64 Encode Keys**.
8. Aguarde a execução e verifique o status em **Indexers**.

### 4. Consultar o Índice
1. No **Azure AI Search**, abra o **Search Explorer**.
2. Para visualizar todos os documentos:
   ```json
   {
       "search": "*",
       "count": true
   }
   ```
3. Para filtrar por localização (Chicago):
   ```json
   {
       "search": "locations:'Chicago'",
       "count": true
   }
   ```
4. Para filtrar por sentimento negativo:
   ```json
   {
       "search": "sentiment:'negative'",
       "count": true
   }
   ```

### 5. Revisar o Repositório de Conhecimento
1. No portal do Azure, acesse a conta de armazenamento.
2. Navegue até o contêiner `knowledge-store` e visualize os arquivos `objectprojection.json`.
3. Explore a estrutura de projeção de imagens e frases-chave.
4. Acesse **Storage Browser > Tables** e selecione `coffeeSkillsetKeyPhrases` para revisar as frases-chave extraídas.

