# Guia completo do Cosign (gerenciamento de armários)

Este manual detalha todos os recursos do Cosign para controle de armários de visitantes e acompanhantes no hospital. Cada seção explica onde as informações são salvas, como acessar, quais fotos ou documentos são gerados e como recuperar tudo depois.

## 1. Acesso e autenticação
1. Abra o link público do WebApp do Cosign no navegador (computador ou celular).
2. Na tela **Acesso Restrito**, informe **Usuário** e **Senha** e clique em **Entrar**.
3. Em caso de erro de credenciais, confirme com a TI ou coordenação. O acesso só é liberado para logins cadastrados.
4. Após entrar, a interface principal é carregada e o menu lateral é exibido. Use o botão **Sair** (rodapé do menu) para encerrar a sessão.

### Onde as credenciais ficam
- Os usuários são mantidos na aba **Usuários** da planilha vinculada ao Apps Script, incluindo nome, e-mail, senha e permissões.
- O front-end grava dados mínimos de sessão no navegador para reabrir o app, mas o acesso depende dos dados na planilha.

## 2. Estrutura geral da interface
- **Menu lateral**: navegação entre todas as páginas (Dashboard, Visitantes, Acompanhantes, Históricos, Liberações, Achados e Perdidos, Termo de Responsabilidade, Cadastros e LOGS).
- **Cabeçalho**: título da página, saudação com perfil e turno, seletores de **Perfil** (geral/visitante/acompanhante) e **Unidade**, alternância de tema, sino de notificações e avatar do usuário.
- **Filtro rápido**: os seletores de Perfil e Unidade afetam listagens e cards em todas as páginas.

## 3. Fluxos por página

### 3.1 Dashboard (visão geral)
- **Cards principais**: Em uso, Livres, Próximo do horário, Contingência e Vencidos. Clicar em um card aplica o filtro correspondente na lista.
- **Filtros de status**: botões Todos/Livre/Em uso/Próximo/Vencido/Contingência.
- **Busca rápida**: localiza por número de armário, paciente, prontuário ou acompanhante.
- **Ocultar dados**: esconde/mostra informações sensíveis na tabela.
- **Ações da tabela**: editar armário, finalizar devolução ou abrir registros de imagem.

**Onde os dados ficam**
- As contagens e registros exibidos refletem as abas **Acompanhantes** e **Visitantes** da planilha principal.
- Fotos mostradas pela ação **Registros em imagens** são buscadas da aba **Registro de Imagens** (criada automaticamente) ou reconstruídas dos dados de termos e movimentações.

### 3.2 Visitantes
- **Novo Cadastro**: registra uso de armário por visitante. Campos principais: unidade, número do armário, visitante, paciente, leito, horários de entrada/saída previstos, volumes e observações.
- **Tabela**: mostra status atual, tempos e contatos. Use a coluna **Ações** para editar ou finalizar devolução.
- **Finalizar/Devolver**: registra saída e libera o armário, atualizando status no dashboard.

**Armazenamento**
- Todos os cadastros e atualizações são gravados na aba **Visitantes** da planilha principal.

### 3.3 Acompanhantes
- **Novo Cadastro**: registra armário de acompanhante com paciente, acompanhante responsável, contatos, leito, unidade e volumes. Esse fluxo alimenta o módulo de termo.
- **Ações**: editar, finalizar devolução ou acessar o termo de responsabilidade.

**Armazenamento**
- Registros ficam na aba **Acompanhantes** da planilha principal.

### 3.4 Contingência (Sem Armários)
- Disponível no painel superior do Dashboard quando o perfil é **Acompanhante**.
- Preencha paciente, acompanhante, prontuário, observações e anexe **foto obrigatória** (JPG/PNG até 2 MB).
- A foto é capturada no ato e registrada como evidência do atendimento sem armário.

**Armazenamento**
- Dados de contingência são gravados na aba **Contingência** (na planilha principal). A foto é salva na pasta do Drive configurada em `PASTA_DRIVE_FOTOS_ID` e indexada na aba **Registro de Imagens**.

### 3.5 Históricos
- **Histórico de Visitantes**: lista registros finalizados de visitantes, com horários de início/fim, status e usuário responsável.
- **Histórico de Acompanhantes**: o mesmo para armários de acompanhantes.
- Use os cabeçalhos para ordenar e pesquisar por armário, paciente, leito ou WhatsApp.

**Armazenamento**
- Dados vêm das abas **Visitantes** e **Acompanhantes**, filtrados pelos status de término.

### 3.6 Liberações
- Filtro por **Data inicial** e **Data final** (obrigatórios) e, opcionalmente, por **Prontuário** ou **Paciente**.
- Clique em **Aplicar filtros** para carregar apenas os registros no intervalo.
- Útil para conferência diária das devoluções e entregas.

**Armazenamento**
- Os dados são lidos da aba **Liberação** da planilha principal. Há integração opcional com uma planilha externa dedicada, definida pelas constantes de configuração (`PLANILHA_LIBERACAO_ID`, aba `LIBERACAO`).

### 3.7 Achados e Perdidos
- Registra itens encontrados nos armários.
- Preencha identificação do item, armário, data/hora e observações. Use a coluna **Ações** para resolver ou devolver.

**Armazenamento**
- Os registros são escritos na planilha de Achados e Perdidos configurada (`PLANILHA_PERTENCES_PERDIDOS_ID`, aba `PERTENCES PERDIDOS GUARDA-VOLUMES`). A planilha externa guarda o histórico e pode ser consultada diretamente.

### 3.8 Termo de Responsabilidade
- Página dedicada aos armários de acompanhantes.
- **Lista de termos**: mostra status (pendente/aplicado), data de aplicação e link do PDF.
- **Assistente (wizard) em 3 passos**:
  1. **Paciente**: nome, prontuário, data de nascimento, setor e leito.
  2. **Acompanhante responsável**: nome, telefone, documento e endereço.
  3. **Revisão e assinatura**: conferência final, captura de fotos obrigatórias dos volumes e da entrega, e geração do PDF.
- **Geração de PDF**: ao finalizar, o termo é salvo em PDF na pasta Drive configurada em `PASTA_DRIVE_ID`. O link fica disponível na tabela.
- **Encerramento e liberação unificados**: finalizar o termo já libera o armário e encerra o atendimento.

**Armazenamento e imagens**
- Dados textuais do termo ficam na aba **Termos de Responsabilidade** da planilha principal.
- Fotos obrigatórias (volumes, movimentações e entrega) são salvas na pasta `PASTA_DRIVE_FOTOS_ID` e indexadas em **Registro de Imagens** com contexto (termo ou movimentação). A pasta **temporária** (`PASTA_DRIVE_TEMP_ID`) é usada para compor o PDF antes de gravar na pasta final.

### 3.9 Cadastro de Armários
- Cria ou edita armários com número, tipo (visitante ou acompanhante), unidade, localização, status e observações.
- Use para marcar armários como ativos, bloqueados ou em manutenção.

**Armazenamento**
- Mantido na aba **Armários** da planilha principal. O dashboard lê esses status para classificar disponibilidade.

### 3.10 Cadastro de Unidades
- Mantém a lista de setores/unidades e indica se estão ativas.
- Impacta os filtros e a criação de novos registros em todas as páginas.

**Armazenamento**
- Dados na aba **Unidades** da planilha principal.

### 3.11 Usuários
- Cadastre nome, e-mail, senha, papel (admin ou usuário) e permissões de acesso (visitantes, acompanhantes e unidades autorizadas).
- Use a coluna **Ações** para editar ou remover usuários.

**Armazenamento**
- Todos os usuários ficam na aba **Usuários** da planilha principal. A autenticação do login consulta esta aba.

### 3.12 LOGS
- Painel de auditoria das ações executadas no sistema (cadastros, finalizações, erros de imagem, etc.).
- Ajuda a rastrear quem realizou cada operação e quando.

**Armazenamento**
- Os registros ficam na aba **LOGS** da planilha principal.

## 4. Fluxos com fotos e arquivos
- **Volumes no termo**: cada volume exige foto no passo 3 do assistente.
- **Movimentações (entrada/saída)**: toda movimentação registrada salva uma foto obrigatória.
- **Entrega do termo**: antes da assinatura final, registre foto da entrega ao acompanhante.
- **Contingências**: sempre exigem uma foto.
- Todas as imagens são salvas na pasta de fotos (`PASTA_DRIVE_FOTOS_ID`) e indexadas na aba **Registro de Imagens**, permitindo consulta pelo botão **Registros em imagens**.

## 5. Recuperação de documentos e evidências
- **PDF do termo**: disponível na coluna de ações da página Termo de Responsabilidade e guardado na pasta do Drive (`PASTA_DRIVE_ID`).
- **Fotos**: acessíveis pela aba **Registro de Imagens** na planilha ou pelos botões de cada armário (Dashboard, Acompanhantes, Termos). A coluna da planilha contém ID, URL, nome do arquivo, contexto e data/hora.
- **Planilhas de apoio**: Achados e Perdidos e Liberação podem ser abertas diretamente nos IDs configurados, caso precise validar ou exportar dados.

## 6. Boas práticas de uso diário
1. Ajuste **Perfil** e **Unidade** antes de operar para evitar lançamentos no setor errado.
2. Preencha todos os campos obrigatórios e confira horários para prevenir alertas de atraso.
3. Garanta que fotos estejam nítidas e com tamanho dentro do limite (até 2 MB por imagem).
4. No fim do turno, revise armários **Vencidos**, finalize termos pendentes e registre devoluções em **Liberações**.
5. Use o botão **Sair** para encerrar a sessão.

## 7. Limitações conhecidas
- Dependência de conectividade com Google Drive e Google Sheets para salvar PDFs, fotos e registros.
- Tamanho máximo das imagens depende das cotas do Apps Script para arquivos base64.
- A disponibilidade dos dados externos (Achados e Perdidos, Liberação) depende das planilhas configuradas e de suas permissões de acesso.

## 8. Onde cada informação é salva (resumo rápido)
- **Visitantes**: aba `Visitantes` na planilha principal.
- **Acompanhantes**: aba `Acompanhantes`.
- **Contingência**: aba `Contingência` + pasta de fotos `PASTA_DRIVE_FOTOS_ID` + índice `Registro de Imagens`.
- **Termos de Responsabilidade**: aba `Termos de Responsabilidade` + PDFs em `PASTA_DRIVE_ID` + fotos em `PASTA_DRIVE_FOTOS_ID` + pasta temporária `PASTA_DRIVE_TEMP_ID` para montagem.
- **Movimentações e volumes**: imagens salvas em `PASTA_DRIVE_FOTOS_ID` e indexadas em `Registro de Imagens` com data/hora e contexto.
- **Liberações**: aba `Liberação` (ou planilha externa `PLANILHA_LIBERACAO_ID`).
- **Achados e Perdidos**: planilha externa `PLANILHA_PERTENCES_PERDIDOS_ID`, aba `PERTENCES PERDIDOS GUARDA-VOLUMES`.
- **Cadastros de Armários**: aba `Armários`.
- **Cadastros de Unidades**: aba `Unidades`.
- **Usuários**: aba `Usuários`.
- **LOGS**: aba `LOGS` (auditoria).
- **Registro de Imagens**: aba `Registro de Imagens`, contendo ID, armário, contexto, responsável, data/hora, URL e nome do arquivo.

Com este guia, é possível operar todas as abas do Cosign, entender o caminho de cada informação e localizar rapidamente PDFs, fotos e registros na planilha e no Google Drive configurado.
