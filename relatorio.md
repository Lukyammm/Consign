# Relatório de Revisão de Código

## Visão geral
Foram analisados os artefatos Apps Script (`CODE.gs`) e front-end (`index.html`, `script.html`). O projeto expõe operações administrativas de armários, usuários e registros de visitas. Há problemas severos de segurança, autenticação e manutenção, além de más práticas na persistência de credenciais.

## Problemas críticos
1. **App Script exposto sem proteção contra embed ou autenticação**: o `doGet` permite renderizar a UI com `XFrameOptionsMode.ALLOWALL`, liberando a página para ser carregada em iframes e facilitando ataques de clickjacking. Não há checagem de autenticação antes de entregar a interface. Recomenda-se retornar erro ou redirecionar para login quando o usuário não estiver autenticado e usar `DENY`/`SAMEORIGIN` para X-Frame-Options. 【F:CODE.gs†L2-L8】
2. **API `doPost` aberta para qualquer origem sem validação**: o roteamento de ações em `handlePost` aceita parâmetros arbitrários e executa operações sensíveis (cadastro de armários, usuários, liberação, logs) sem validação de sessão, origem ou controle de acesso. Isso permite que qualquer requisição HTTP acione rotinas administrativas. É necessário validar token de sessão/usuário, checar permissões e rejeitar métodos não autenticados. 【F:CODE.gs†L1202-L1245】
3. **Senhas armazenadas e comparadas em texto puro**: o cadastro e autenticação de usuários grava e lê a senha diretamente da planilha, sem hashing, salts ou política de complexidade. Qualquer acesso à planilha expõe todas as credenciais. Substitua por derivação segura (por exemplo, `Utilities.computeDigest` com salt aleatório por usuário) e nunca persista o valor em claro. 【F:CODE.gs†L2653-L2701】【F:CODE.gs†L2956-L3021】
4. **Sessão persistida em `localStorage` sem proteção**: os dados do usuário autenticado são gravados em `localStorage`, ficando acessíveis a qualquer script injetado e sem assinatura/expiração curta. Além disso, o TTL de 8 horas é aplicado apenas na restauração, não há renovação ou invalidação no servidor. Utilize cookies HttpOnly/SameSite ou tokens assinados com escopo e expiração verificáveis no backend. 【F:script.html†L166-L195】
5. **Endpoint de Apps Script hard-coded no front-end**: a URL pública do WebApp está embutida no JS (`SCRIPT_URL`), expondo o endpoint de produção e impossibilitando troca por ambiente de testes. Isso também facilita abusos, já que o endpoint é visível mesmo antes do login. Parametrize a URL via variável de ambiente ou `PropertiesService` e aplique proxy/autenticação no servidor. 【F:script.html†L2-L4】

## Problemas importantes
1. **Controle de acesso inexistente nas ações internas**: funções como `cadastrarUsuario`, `liberarArmario` e `excluirUsuario` são chamadas diretamente pelo roteador sem checar o perfil ou a unidade do solicitante. Falta verificação de autorização para perfis não administradores. Implemente middleware que valide o usuário e o escopo de unidade antes de executar cada case. 【F:CODE.gs†L1202-L1245】
2. **Auditoria insuficiente e exposta**: `registrarLog` escreve em planilha mas o `handlePost` não registra quem chamou cada ação quando a requisição é anônima (contexto de usuário é opcional). Inclua identificação obrigatória, IP e resultado (sucesso/falha) para rastreabilidade. 【F:CODE.gs†L1180-L1199】【F:CODE.gs†L1202-L1245】
3. **Validação fraca de entrada no front-end de login**: o formulário não aplica regras de senha ou bloqueio após tentativas falhas, nem feedback de requisitos mínimos. Adicione validação de força, throttle no servidor e mensagens claras para evitar enumeração de usuários. 【F:index.html†L20-L32】

## Manutenção e qualidade
1. **Documentação e README quebrados**: o `README.md` contém apenas o título na mesma linha do prompt, sem instruções de instalação, execução ou arquitetura. Isso prejudica onboarding e manutenção. Recrie o README com setup do Apps Script, deploy e dependências. 【F:README.md†L1-L1】
2. **Acoplamento entre UI e estado do servidor**: `SCRIPT_URL` e estados de sessão são manipulados apenas no cliente, dificultando testes e ambientes múltiplos. Introduza configuração por ambiente e camada de serviço separando fetch/API do restante da UI. 【F:script.html†L2-L4】【F:script.html†L166-L195】

## Recomendações rápidas
- Implementar autenticação e autorização no backend (tokens assinados, escopo por unidade/perfil) antes de expor qualquer ação via `doPost`.
- Armazenar senhas com hashing seguro e migrar registros existentes com política de reset.
- Revisar política de clickjacking e CSP para a UI Apps Script.
- Introduzir configuração de ambiente e remover segredos/URLs fixas do front-end.
- Atualizar README e adicionar guia de segurança e operação.
