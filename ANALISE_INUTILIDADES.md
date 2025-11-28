# Análise de trechos potencialmente inúteis

## Escopo
- Arquivos revisados: `CODE.gs`, `index.html`, `script.html`, `style.html`.

## Método
- Busca automatizada por funções declaradas apenas uma vez (indicando não serem chamadas) via regex sobre os arquivos `.gs` e `script.html`.
- Inspeção pontual do início do `index.html` e dos recursos de estado inicial no `script.html` para encontrar constantes soltas ou marcações não referenciadas.

## Achados
- Nenhuma função declarada ficou sem uso detectado em `CODE.gs` ou `script.html`; todas aparecem ao menos uma vez além da definição.
- Não foram identificadas tags ou constantes isoladas sem referência evidente nos trechos inspecionados do `index.html`.

## Observação
- Caso surjam novos módulos ou alterações em rotas do Apps Script, recomenda-se repetir a checagem para garantir que funções obsoletas sejam removidas.
