🤖 **Assistente Telegram com Groq (Llama 3.3 + Whisper) no Google Colab**

Este projeto implementa um bot inteligente para o Telegram rodando diretamente no Google Colab. Ele utiliza a API da Groq com o modelo Llama-3.3-70b-versatile para processamento de linguagem natural e Tool Use (chamada de funções), além do modelo Whisper-large-v3 para transcrição de áudios (STT - Speech-to-Text).



O bot é capaz de interagir tanto por texto quanto por mensagens de voz, interpretando a intenção do usuário e acionando ferramentas externas de forma autônoma.



🌟 Funcionalidades

Suporte a Texto e Áudio: Entende mensagens digitadas ou notas de voz enviadas pelo Telegram.



Consulta de Clima: Utiliza a API pública do wttr.in para informar a previsão do tempo e temperatura de qualquer cidade.



Busca de Notícias: Conecta-se ao NewsAPI para buscar as manchetes mais recentes sobre qualquer tema solicitado.



Resumo de Textos: Usa a capacidade analítica do LLM para criar resumos claros e diretos de textos longos.



Processamento Autônomo (Tool Use): O bot decide sozinho quando deve apenas conversar e quando deve acionar uma de suas ferramentas (clima, notícias ou resumo) com base no contexto da conversa.



🛠️ Tecnologias Utilizadas

Python 3: Linguagem principal do script.



python-telegram-bot: Biblioteca para interagir com a API oficial do Telegram.



Groq API: Provedor de inferência ultrarrápida.



llama-3.3-70b-versatile: "Cérebro" principal do bot e orquestrador de ferramentas.



whisper-large-v3: Responsável por converter os áudios do Telegram em texto.



NewsAPI: Fonte de dados para a busca de notícias.



Google Colab: Ambiente de execução em nuvem.



nest\_asyncio: Utilitário para permitir a execução assíncrona fluida dentro de notebooks Jupyter/Colab.



🚀 **Pré-requisitos**

Antes de rodar o código, você precisará de três chaves de acesso (Tokens/API Keys):



Telegram Token: Crie um bot no Telegram falando com o @BotFather e copie o token gerado.



Groq API Key: Crie uma conta no Groq Console e gere uma chave gratuita.



News API Key: Cadastre-se em NewsAPI.org e pegue sua chave gratuita.



⚙️ **Como Configurar e Rodar no Colab**

Passo 1: Configurar as Variáveis de Ambiente (Secrets)

No menu lateral esquerdo do Google Colab, clique no ícone de Chave (Secrets). Adicione três novos secrets e ative o botão "Notebook access" para cada um deles:



TELEGRAM\_TOKEN (Cole seu token do Telegram)



GROQ\_API\_KEY (Cole sua chave do Groq)



NEWS\_API\_KEY (Cole sua chave do NewsAPI)



Passo 2: Instalar as Dependências

Crie uma célula no topo do seu notebook Colab e execute o seguinte comando para instalar as bibliotecas necessárias:



Bash

!pip install python-telegram-bot groq requests nest\_asyncio -q

Passo 3: Rodar o Script Principal

Cole o código Python do bot em uma nova célula e clique em Play (Executar). O console exibirá a mensagem indicando que o bot está rodando. Vá até o Telegram e mande um /start ou uma mensagem de áudio para testar!



🧠 **Como o Código Funciona (Por debaixo dos panos)**

1\. Resolução de Conflitos Assíncronos

O Colab possui seu próprio loop de eventos rodando em segundo plano. O nest\_asyncio.apply() é utilizado para permitir que a biblioteca do Telegram crie seu próprio loop de monitoramento de mensagens sem entrar em conflito e causar erros de execução.



2\. O Fluxo de Áudio (STT)

Quando o usuário envia uma nota de voz, a função handle\_audio é acionada:



O bot faz o download temporário do arquivo de áudio (.ogg) do Telegram.



O arquivo é enviado via API para o modelo Whisper na Groq.



O Whisper retorna o texto transcrito.



O arquivo de áudio é deletado do servidor do Colab para economizar espaço.



O texto transcrito é encaminhado para o fluxo principal de processamento (process\_user\_input), agindo exatamente como se o usuário tivesse digitado.



3\. O Fluxo de Tool Use (Function Calling)

A função process\_user\_input é o coração do bot:



A mensagem (texto digitado ou transcrito) é enviada ao Llama 3.3 junto com uma lista (em formato JSON) descrevendo as ferramentas disponíveis e os parâmetros que elas exigem.



O Llama 3.3 analisa a mensagem. Se notar que o usuário quer saber o clima de "São Paulo", ele interrompe a geração de texto e devolve uma ordem de execução (tool\_call) solicitando a ferramenta consultar\_clima com o argumento {"cidade": "São Paulo"}.



O script Python intercepta isso, executa a função localmente (buscando no site wttr.in) e obtém os dados reais.



O script envia esses dados de volta ao Llama 3.3.



O modelo analisa as informações brutas recém-chegadas e, por fim, redige a resposta natural e amigável que é enviada ao usuário no Telegram.

