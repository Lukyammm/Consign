# Consign

## Instalação
1. Publique o projeto como WebApp no Google Apps Script mantendo o acesso aos usuários autorizados.
2. Atualize o `PASTA_DRIVE_FOTOS_ID` em `CODE.gs` se precisar apontar para outra pasta do Drive.

## Permissões
- A aplicação solicita acesso ao Drive para salvar PDFs e fotos de evidência.
- Habilite o escopo para manipulação de arquivos do Drive (createFile/getFolder).

## Principais funções
- **Registro de volumes no termo:** cada volume agora exige uma foto capturada no ato do cadastro.
- **Encerramento do termo:** antes da assinatura final é necessário anexar foto da entrega ao acompanhante.
- **Movimentações (entrada/saída):** cada registro requer uma foto e é salvo na pasta configurada.

## Limitações conhecidas
- As fotos são armazenadas apenas na pasta configurada e precisam de conectividade com o Drive.
- O tamanho máximo aceito depende das cotas do Apps Script para arquivos base64.