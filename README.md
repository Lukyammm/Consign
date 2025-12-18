# Consign

## Instalação
1. Publique o projeto como WebApp no Google Apps Script mantendo o acesso aos usuários autorizados.
2. Atualize o `PASTA_DRIVE_FOTOS_ID` em `CODE.gs` se precisar apontar para outra pasta do Drive.
3. Certifique-se de que a planilha tenha a aba **Registro de Imagens** (é criada automaticamente no primeiro uso) para indexar as fotos salvas nos termos e nas movimentações.

## Permissões
- A aplicação solicita acesso ao Drive para salvar PDFs e fotos de evidência.
- Habilite o escopo para manipulação de arquivos do Drive (createFile/getFolder).
- Garanta acesso apenas à pasta configurada em `PASTA_DRIVE_FOTOS_ID` para salvar as imagens de termos, movimentações e contingências.

## Principais funções
- **Registro de volumes no termo:** cada volume agora exige uma foto capturada no ato do cadastro.
- **Encerramento do termo:** antes da assinatura final é necessário anexar foto da entrega ao acompanhante.
- **Movimentações (entrada/saída):** cada registro requer uma foto e é salvo na pasta configurada.
- **Registro de imagens:** toda foto anexada (volumes, movimentações e entrega do termo) é indexada na aba "Registro de Imagens" para facilitar consultas posteriores.
- **Contingências:** ao registrar uma contingência é obrigatório anexar uma foto (JPG/PNG até 2 MB), salva automaticamente na pasta de evidências e indexada na planilha.

- **Encerramento e liberação unificados:** a finalização do termo e a liberação do armário agora ocorrem em uma única ação para reduzir o tempo de espera e manter o fluxo mais ágil.

## Resolução rápida para tela em branco
1. Confirme que a URL pública do WebApp (em **Implantar > Implantar como aplicativo da web**) está ativa e copiável.
2. Acesse novamente o WebApp; se a URL não for injetada, a página exibirá um aviso em fundo escuro com a URL detectada ou com a instrução de publicar o aplicativo.
3. Caso veja o aviso de erro crítico, republique o WebApp, limpe o cache do navegador e tente novamente.
4. Enquanto a URL não estiver válida, o botão de login bloqueará as requisições e mostrará o alerta de URL faltando em vez de enviar o formulário para uma página em branco.

## Limitações conhecidas
- As fotos são armazenadas apenas na pasta configurada e precisam de conectividade com o Drive.
- O tamanho máximo aceito depende das cotas do Apps Script para arquivos base64.