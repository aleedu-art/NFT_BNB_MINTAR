# NFT Minter com Análise de Sentimentos por IA

Este projeto é uma aplicação web completa para criação e mintagem de NFTs com análise de sentimentos baseada em IA. A aplicação permite fazer upload de imagens para o IPFS via Pinata, analisar automaticamente as imagens com a API OpenAI para extrair sentimentos, psicologia das cores, signos e linguagem visual, criar metadados enriquecidos e mintar NFTs em contratos ERC-721.

## Funcionalidades

- **Upload de imagens para IPFS** via Pinata
- **Análise de sentimentos com IA** usando a API OpenAI (GPT-4 Vision)
- **Geração automática de metadados enriquecidos** com análise de sentimentos
- **Criação de metadados no formato ERC-721** e upload para IPFS
- **Mintagem de NFTs** em contratos ERC-721 compatíveis
- **Interface responsiva** para desktop e dispositivos móveis
- **Integração com MetaMask** para conectar carteiras e assinar transações
- **Suporte a diferentes redes blockchain** (Ethereum, Polygon, etc.)
- **Fluxo integrado** de criação de metadados e mintagem em uma única ação

## Requisitos

- Python 3.6+
- Flask
- Conta na Pinata com chaves de API
- Conta na OpenAI com chave de API
- MetaMask ou carteira Web3 compatível
- Contrato ERC-721 implantado (para mintagem)

## Instalação

1. Clone ou extraia o repositório:
```
unzip nft_minter_project_v2_mint.zip
cd nft_minter_project_v2
```

2. Instale as dependências:
```
pip install -r requirements.txt
```

3. Crie um arquivo `.env` na raiz do projeto com suas chaves de API:
```
PINATA_API_KEY=sua_chave_api_pinata_aqui
PINATA_SECRET_KEY=sua_chave_secreta_pinata_aqui
OPENAI_API_KEY=sua_chave_api_openai_aqui
```

4. Certifique-se de que a pasta `uploads` existe e tem permissões de escrita:
```
mkdir -p uploads
chmod 777 uploads
```

## Uso

1. Inicie o servidor Flask:
```
python src/main.py
```

2. Acesse a aplicação em seu navegador:
```
http://localhost:5000/metadata
```

3. Siga o fluxo na interface:
   - Conecte sua carteira MetaMask
   - Faça upload da imagem
   - Preencha o nome e descrição básica do NFT
   - Clique em "Fazer Upload e Analisar Imagem"
   - Revise a análise gerada pela IA
   - Insira o endereço do contrato NFT e verifique o ABI
   - Clique em "Criar Metadados e Mintar NFT"

## Estrutura do Projeto

```
nft_minter_project_v2/
├── src/
│   ├── static/
│   │   ├── mint.html        # Interface para mintagem de NFTs
│   │   ├── metadata.html    # Interface para criação de metadados e mintagem
│   │   └── js/              # Bibliotecas JavaScript (se houver)
│   ├── templates/
│   ├── routes/
│   ├── models/
│   └── main.py              # Servidor Flask principal
├── uploads/                 # Pasta para armazenamento temporário de imagens
├── requirements.txt         # Dependências do projeto
├── .env.example             # Exemplo de arquivo de configuração
└── README.md                # Este arquivo
```

## Rotas da API

- **GET /** - Página inicial (redireciona para /metadata)
- **GET /mint** - Interface para mintagem de NFTs
- **GET /metadata** - Interface para criação de metadados e mintagem
- **POST /api/upload** - Upload de imagem para o Pinata
- **POST /api/analyze-image** - Análise de imagem com OpenAI
- **POST /api/create-metadata** - Criação de metadados e upload para Pinata
- **POST /api/create-metadata-only** - Criação de metadados sem mintagem

## Fluxo Detalhado

1. **Upload de Imagem**:
   - A imagem é enviada para o servidor Flask
   - O servidor salva temporariamente a imagem na pasta `uploads`
   - A imagem é enviada para o Pinata via API
   - O hash IPFS da imagem é retornado

2. **Análise de Imagem**:
   - O hash IPFS é usado para criar uma URL da imagem
   - A URL é enviada para a API OpenAI (GPT-4 Vision)
   - A API analisa a imagem e retorna informações sobre sentimentos, cores, signos e linguagem visual
   - A análise é processada e estruturada para uso nos metadados

3. **Criação de Metadados**:
   - Os metadados são criados no formato ERC-721
   - A descrição é enriquecida com a análise de sentimentos
   - Atributos são gerados com base na análise
   - Os metadados são enviados para o Pinata
   - O hash IPFS dos metadados é retornado

4. **Mintagem de NFT**:
   - O hash IPFS dos metadados é usado como tokenURI
   - A carteira MetaMask é usada para assinar a transação
   - O NFT é mintado no contrato ERC-721 especificado
   - O hash da transação é retornado e exibido na interface

## Implantação em Produção

### VPS (Servidor Virtual Privado)

Para implantar esta aplicação em um servidor VPS:

1. **Configuração do servidor**:
   ```bash
   # Instalar dependências
   sudo apt update
   sudo apt install python3-pip python3-dev nginx
   
   # Instalar virtualenv
   pip3 install virtualenv
   
   # Clonar o repositório ou fazer upload dos arquivos
   git clone <seu-repositorio> /var/www/nft-minter
   cd /var/www/nft-minter
   
   # Criar ambiente virtual
   virtualenv venv
   source venv/bin/activate
   
   # Instalar dependências
   pip install -r requirements.txt
   pip install gunicorn
   
   # Configurar variáveis de ambiente
   cp .env.example .env
   nano .env  # Edite com suas chaves de API
   ```

2. **Configurar Gunicorn como serviço**:
   Crie um arquivo `/etc/systemd/system/nft-minter.service`:
   ```
   [Unit]
   Description=Gunicorn instance to serve NFT Minter
   After=network.target
   
   [Service]
   User=www-data
   Group=www-data
   WorkingDirectory=/var/www/nft-minter
   Environment="PATH=/var/www/nft-minter/venv/bin"
   ExecStart=/var/www/nft-minter/venv/bin/gunicorn --workers 3 --bind 0.0.0.0:5000 --chdir src main:app
   
   [Install]
   WantedBy=multi-user.target
   ```

3. **Configurar Nginx como proxy reverso**:
   Crie um arquivo `/etc/nginx/sites-available/nft-minter`:
   ```
   server {
       listen 80;
       server_name seu-dominio.com;
   
       location / {
           include proxy_params;
           proxy_pass http://127.0.0.1:5000;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
       }
   }
   ```

4. **Ativar o site e iniciar os serviços**:
   ```bash
   sudo ln -s /etc/nginx/sites-available/nft-minter /etc/nginx/sites-enabled
   sudo systemctl start nft-minter
   sudo systemctl enable nft-minter
   sudo systemctl restart nginx
   ```

5. **Configurar SSL com Let's Encrypt**:
   ```bash
   sudo apt install certbot python3-certbot-nginx
   sudo certbot --nginx -d seu-dominio.com
   ```

### Expor Localmente com Ngrok

Para expor temporariamente sua aplicação local:

1. Instale o Ngrok:
   ```bash
   pip install pyngrok
   ```

2. Execute sua aplicação Flask:
   ```bash
   python src/main.py
   ```

3. Em outro terminal, execute o Ngrok:
   ```bash
   ngrok http 5000
   ```

4. Use a URL fornecida pelo Ngrok para acessar sua aplicação

## Integração com Aplicativos Mobile

Para criar um aplicativo Android que se integre com esta aplicação:

1. **Opções de desenvolvimento**:
   - **Aplicativo nativo**: Use Kotlin/Java com bibliotecas como Retrofit para API
   - **Aplicativo híbrido**: Use React Native ou Flutter
   - **WebView**: Encapsule a interface web em um aplicativo nativo

2. **Integração com carteiras**:
   - Use WalletConnect para integração com várias carteiras
   - Implemente deep links para abrir carteiras externas

3. **Considerações de CORS**:
   Se você estiver desenvolvendo um aplicativo separado que se comunica com esta API, certifique-se de habilitar CORS no servidor Flask:
   ```python
   from flask_cors import CORS
   
   app = Flask(__name__)
   CORS(app)  # Habilita CORS para toda a aplicação
   ```

## Solução de Problemas

### Problemas com a API OpenAI

Se você encontrar erros relacionados à API OpenAI:

1. Verifique se sua chave de API está correta no arquivo `.env`
2. Confirme se sua conta tem créditos suficientes
3. Verifique se está usando a versão correta da biblioteca OpenAI:
   ```bash
   pip install --upgrade openai
   ```

### Problemas com o Pinata

Se você encontrar erros ao fazer upload para o Pinata:

1. Verifique se suas chaves de API estão corretas no arquivo `.env`
2. Confirme se sua conta Pinata tem espaço disponível
3. Verifique se o tamanho da imagem não excede os limites

### Problemas com o MetaMask

Se você encontrar problemas ao conectar o MetaMask:

1. Certifique-se de que a extensão MetaMask está instalada e desbloqueada
2. Verifique se você está na rede correta (Ethereum Mainnet, Testnet, etc.)
3. Em dispositivos móveis, use o navegador interno do MetaMask

## Contribuição

Contribuições são bem-vindas! Sinta-se à vontade para abrir issues ou enviar pull requests.

## Licença

Este projeto está licenciado sob a licença MIT.

## Agradecimentos

- OpenAI pela API de análise de imagens
- Pinata pelo serviço de pinagem IPFS
- MetaMask pela integração de carteira Web3
- Comunidade Ethereum pelos padrões ERC-721
"# MINT_NFT_BNB" 
# MINT_NFT_BNB 
# MINT_NFT_BNB 
# MINT_NFT_BNB 
