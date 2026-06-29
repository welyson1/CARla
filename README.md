# CARla - Assistente de voz para o Cadastro Ambiental Rural

Este repositorio contem o modulo `index.js`, uma assistente de voz pronta para apoiar o usuario durante o preenchimento do Cadastro Ambiental Rural (CAR), especialmente no fluxo do Modulo de Cadastro Pre-preenchido do SICAR.

O modulo foi pensado para ser integrado diretamente ao sistema [Rural-Environmental-Registry/core](https://github.com/Rural-Environmental-Registry/core), funcionando como uma camada de assistencia visual e conversacional sobre a interface ja existente, sem exigir alteracao nas regras de negocio do backend, no banco de dados ou no motor geoespacial.

## Live demo

A demonstracao online esta disponivel em:

https://carla.shardweb.app/

Para testar, basta abrir a demo, iniciar a conversa, permitir o uso do microfone e simular uma conversa de cadastro do CAR. A assistente inicia o atendimento por voz, pergunta o nome do usuario e conduz o passo a passo do cadastro, exibindo imagens e referencias visuais quando necessario.

## O que e o core do RER/CAR

O `Rural-Environmental-Registry/core` e o orquestrador principal do RER (Rural Environmental Registry), uma solucao de Bem Publico Digital para modernizar o Cadastro Ambiental Rural. O core organiza a plataforma em componentes modulares e escalaveis, incluindo:

- `frontend`: interface web em Vue.js 3 para cadastro e visualizacao dos dados ambientais rurais.
- `backend`: API principal em Spring Boot com suporte a PostGIS.
- `map_component`: componente de mapa interativo baseado em Leaflet, com ferramentas de desenho geoespacial.
- `authentication`: autenticacao e autorizacao com Keycloak.
- `calc_engine`: motor de calculos e processamento geoespacial.
- `Gateway`: roteamento entre os microsservicos com Spring Cloud Gateway.

Nesse contexto, o `index.js` entra como um modulo de frontend: ele adiciona a assistente CARla na tela do sistema, acompanha o usuario por voz e mostra referencias visuais do guia de cadastro. Como o RER ja possui arquitetura modular, a integracao pode ser feita como um bloco de interface adicional no `frontend`.

## O que o index.js faz

O arquivo `index.js` e autocontido e nao depende de build, framework ou pacote npm especifico. Ao ser carregado no navegador, ele:

- cria um widget flutuante em Shadow DOM para evitar conflito com os estilos do sistema;
- adiciona uma personagem assistente arrastavel na lateral da tela;
- abre uma sessao em tempo real com a Gemini Live API via WebSocket;
- captura o audio do microfone do usuario e envia em PCM 16 kHz;
- recebe audio de resposta do modelo em tempo real;
- usa o modelo `models/gemini-2.5-flash-native-audio-latest`;
- segue um `SYSTEM_INSTRUCTION` com o roteiro do CAR pre-preenchido;
- chama a ferramenta interna `renderGuideVisual` para exibir imagens, GIFs ou textos de apoio;
- conduz a conversa uma acao por vez, aguardando confirmacao do usuario antes de avancar.

## Uso simplificado

Para usar o modulo, o fluxo minimo e:

1. Inserir uma chave de API live do Gemini no ponto de configuracao do arquivo.
2. Carregar o `index.js` no frontend do sistema.
3. Abra o site de cadastro do CAR oficial.
4. Aperte F12 abra o console e cole o código do `index.js`.
5. Clique no icone da Carla que vai aparecer na tela e permitir o uso do microfone.

No arquivo, a chave fica nesta constante:

```js
const apiKey = "SUA_CHAVE_LIVE_DO_GEMINI";
```

Para o prototipo, basta substituir esse valor pela chave de API live do Gemini. A chave usada no ambiente atual da demonstracao esta liberada para uso amplo no fluxo de simulacao do cadastro, funcionando na pratica como uma chave sem limite operacional para os testes. Em producao, recomenda-se trocar a chave exposta no frontend por token efemero gerado em backend, mantendo a mesma logica do modulo.

## Pontos de customizacao

Os principais pontos que podem ser ajustados no `index.js` sao:

- `apiKey`: chave live do Gemini usada para abrir a conexao WebSocket.
- `MODEL`: modelo Gemini Live usado na conversa.
- `GUIDE_VISUALS`: lista de imagens e GIFs exibidos durante o passo a passo.
- `CAR_GUIDE`: roteiro base do Cadastro Ambiental Rural.
- `SYSTEM_INSTRUCTION`: protocolo de comportamento da assistente.
- `DEBUG_LOGS`: logs tecnicos no console do navegador.

## Requisitos

- Navegador moderno com suporte a WebSocket.
- Permissao de microfone.
- Chave live do Gemini habilitada para a Live API.

## Observacoes de seguranca e privacidade

O modulo foi feito para demonstracao e integracao rapida. Para ambiente oficial de producao, recomenda-se:

- usar token efemero gerado por backend, em vez de expor uma chave fixa no JavaScript;
- apresentar aviso de uso do microfone e tratamento de dados antes da conversa;
- revisar politicas de privacidade, consentimento e retencao;
- alinhar a integracao com o fluxo de autenticacao do RER/Keycloak quando houver dados reais do produtor.
