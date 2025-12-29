# Dashboard BI para Cosign (Apps Script + WebApp)

## Objetivo
Entregar uma aba/tela "Dashboard" completa para perfis ADM e autorizados, com visão de KPIs, gráficos, acompanhamento por armário, alertas e exportações, mantendo alta performance e segurança em Apps Script.

## Dados de entrada
- Aba/planilha de LOG/MOVIMENTOS contendo (mínimo):
  - `timestamp_criacao`, `timestamp_inicio`, `timestamp_conclusao`
  - `status` (aberto, em_producao, entregue, cancelado, pendente, etc.)
  - `armario_id`
  - `usuario_solicitante`, `usuario_atendente`
  - `perfil`
  - `unidade_setor` (opcional)
  - `observacoes` (opcional)
- Abas auxiliares criadas/validadas automaticamente:
  - `CONFIG` (regras de SLA, limites de backlog, flags de alerta/sons/e-mail)
  - `SNAPSHOTS` (histórico diário de KPIs)
  - `CONFIG_HISTORY` (opcional, versiona mudanças de SLA e limites)

## KPIs (cards no topo)
- Solicitações: hoje, ontem, semana, mês
- Abertas agora (backlog), em produção agora
- Entregues hoje
- Canceladas (hoje/semana/mês)
- Taxa de conclusão e taxa de cancelamento
- Tempos médios: total (criação→conclusão), atendimento (início→conclusão), fila (criação→início)
- Percentis: P50/P75/P90/P95/P99 do tempo total
- SLA: % dentro do SLA configurável (ex.: 15/30/60 min)
- Armários em uso agora / livres
- Top 5 armários, solicitantes e atendentes
- Pico por hora do dia e por dia da semana

## Gráficos
- Série temporal (linha): volume por dia/hora (últimos 30 dias)
- Barras: volume por status
- Barras empilhadas: status ao longo do tempo
- Heatmap: dia da semana × hora (demanda)
- Histograma: distribuição de tempos (fila, atendimento, total)
- Pareto: armários com maior concentração (80/20)
- Boxplot simulado (percentis) por armário ou setor/unidade

## Acompanhamento por armário
- Tabela filtrável por `armario_id`: total no período, abertos, cancelados, tempo médio total, P95, última movimentação, status atual.
- Modal por armário: timeline completa, gráfico de tempos, lista filtrável de eventos.

## Filtros
- Período (hoje, 7d, 30d, mês atual, customizado)
- Status, setor/unidade, armario_id, perfil
- Solicitante, atendente
- Flag "somente fora do SLA"

## Alertas e regras
- Configuração em `CONFIG`: SLA (min), limite de backlog, tempo máximo de item aberto, regra de armário travado.
- Alertas visuais no dashboard; opcionais: som (ADM/Recepção) e e-mail.
- Severidade (info/atenção/crítico) e runbook curto por alerta.

## Exportação e relatórios
- Exportar CSV do período filtrado.
- Gerar PDF ou Google Doc resumido (KPIs + gráficos).
- Salvar snapshot diário em `SNAPSHOTS` com parâmetros de filtro e versão de regra.

## Implementação técnica
- **Performance**: leitura em lote (`getValues()`), cache (`CacheService`), sem loops com `setValue` individual.
- **Agregações**: `groupByDay`, `groupByHour`, `computeDurations` (fila/atendimento/total), `computePercentiles` (p50/p75/p90/p95/p99), `computeSLACompliance`.
- **Segurança**: validar perfis no backend antes de entregar o dashboard e antes de cada chamada de dados; restringir escopos; evitar exposição de URLs sensíveis.
- **Front-end**: layout responsivo (cards → colunas fluidas; tabelas viram cards no mobile), tema claro/escuro opcional, Chart.js ou Google Charts embutido no HTML.

## Estrutura recomendada de arquivos (Apps Script)
- `Code.gs`/`app.gs`: rotas (`doGet`), segurança/autorização, leitura da planilha, agregações e cache.
- `dashboard.html`: markup principal (cards, filtros, gráficos, tabelas, modais) com chamadas a `google.script.run`.
- `dashboard.css`/`styles.html`: estilos responsivos para cards, grids e tabelas.
- `dashboard.js`/`scripts.html`: inicialização, chamadas de dados, atualização de KPIs/gráficos, controle de filtros e exportações.

## Fluxo de dados (exemplo)
1. `doGet` verifica perfil e entrega `dashboard.html`.
2. Front-end chama `google.script.run.withSuccessHandler(renderDashboard).getDashboardData(filtros)`.
3. Backend carrega LOG em lote, normaliza colunas, aplica filtros, calcula KPIs/series/tabelas, usa cache quando possível.
4. Front-end preenche cards, renderiza gráficos (Chart.js), aplica drill-down e exportações.
5. Ao salvar snapshot, backend grava linha em `SNAPSHOTS` com KPIs e parâmetros.

## Pseudo-API Apps Script sugerida
- `getDashboardData(filtros)` → `{kpis, series, statusCounts, stackedStatus, heatmap, histogram, pareto, boxplot, armarios, alertas}`
- `exportarCSV(filtros)` → `Blob`/URL
- `gerarRelatorio(filtros)` → `URL` de PDF/Doc
- `salvarSnapshot(filtros)` → confirmação

## Considerações de qualidade
- Normalizar fuso horário (ex.: America/Sao_Paulo) e garantir ordenação de timestamps.
- Validar status e transições; capturar inconsistências (registros sem conclusão, reaberturas).
- Mostrar "dados atualizados há X min" e fallback quando cache expira.
- Documentar dependências, permissões e limites de cota.

## Próximos passos sugeridos
1. Mapear colunas reais da aba LOG/MOVIMENTOS e alinhar nomenclatura.
2. Implementar funções de agregação e cache no backend.
3. Criar `dashboard.html` com containers de KPI, filtros, gráficos e tabelas.
4. Integrar exportação CSV/PDF/Doc e gravação de snapshots.
5. Testar com dados reais e validar performance e segurança.
