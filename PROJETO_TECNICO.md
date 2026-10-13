# Projeto Técnico - Sistema de Gestão para Oficina Mecânica

**Versão:** 0.1 - proposta técnica  
**Estado:** planejamento; a aplicação ainda não foi implementada  
**Base funcional:** [PRD.md](PRD.md)  
**Referência visual:** [prototipos.html](prototipos.html)

## 1. Objetivo deste documento

Descrever uma implementação inicial coerente com o PRD, com foco no site responsivo usado pela equipe da oficina e pelos clientes. As escolhas de tecnologia abaixo são recomendações para iniciar o projeto, não componentes já instalados ou validados.

## 2. Design de referência e ressalva

O protótipo tem três alternativas e abre por padrão em **“Decisões primeiro”** (`view=fila`). Como ainda não há uma escolha explícita registrada, esta proposta usa essa alternativa como direção provisória:

- A tela administrativa prioriza a fila de pedidos de horário pendentes.
- Os pedidos pendentes mostram nome, veículo, placa, horário solicitado e serviço/queixa em texto livre.
- As ações principais são confirmar o horário ou propor outro.
- A agenda de horários já confirmados aparece ao lado, sem confundir solicitações com confirmações.
- O status é apresentado por texto e ícone/forma, além da cor; cor não é o único indicador.
- A composição se adapta a celular: a navegação lateral e a agenda secundária cedem espaço ao conteúdo principal.

O protótipo é uma demonstração estática com dados fictícios. Seus botões, busca e diálogo não substituem autenticação, persistência, regras de agenda ou comunicações reais. Antes de congelar a implementação visual, o responsável pelo produto deve confirmar que “Decisões primeiro” é a alternativa escolhida. As outras opções permanecem referências comparativas, não três telas a manter em produção.

## 3. Escopo técnico

### Incluído na primeira versão

- Cadastro, login e consulta de agendamentos pelo cliente.
- Gestão administrativa dos pedidos, confirmação ou proposta de outro horário.
- Cadastro de cliente e veículo (placa, marca e modelo; cor opcional).
- Registro do relato inicial e da recepção do veículo.
- Avaliação técnica, orçamento, aprovação e ordem de serviço.
- Cadastro simples de peças, movimentação de estoque, venda de balcão e peças vinculadas ao serviço.
- Atalho para WhatsApp; envio manual pela equipe.
- Perfis e autorização para cliente, gerente/funcionário e dono.

### Fora da primeira versão

Pagamento integrado, gestão financeira, emissão fiscal, API/disparo automático do WhatsApp, diagnóstico automático, aplicativo nativo e múltiplas oficinas. Não implementar esses módulos sem alteração aprovada do PRD.

## 4. Arquitetura recomendada

### 4.1 Estilo

Aplicação web monolítica, renderizada no servidor. Um único deploy contém páginas, regras de negócio e acesso ao banco. Essa abordagem reduz a complexidade operacional para um TCC com uma oficina e favorece validação rápida de formulários, permissões e fluxos transacionais.

### 4.2 Stack proposta

| Camada | Recomendação | Motivo |
|---|---|---|
| Linguagem | Python em versão suportada pela versão do framework selecionada | Ecossistema simples para aplicação de gestão e manutenção pelo grupo. |
| Framework web | Django, preferencialmente uma versão LTS suportada no início da implementação | Inclui autenticação, sessões, formulários, ORM, proteção CSRF e migrações. |
| Interface | Django Templates, HTML semântico, CSS próprio com tokens e JavaScript mínimo | Mantém o fluxo simples e permite reproduzir a hierarquia visual do protótipo sem uma SPA. |
| Banco de dados | PostgreSQL em desenvolvimento integrado e produção | Restrições relacionais, transações e bloqueio de concorrência adequados à agenda e ao estoque. |
| Testes | Testes do Django para domínio, permissões e integração; Playwright para fluxos essenciais no navegador | Verifica regras de negócio e jornada responsiva. |
| Deploy | HTTPS atrás de servidor/proxy WSGI compatível, configuração por variáveis de ambiente e backup gerenciado do PostgreSQL | Evita incluir segredos no repositório e dá um caminho simples para operação. |

Não introduzir API REST, microserviços, filas ou framework de frontend separado no MVP. Adicionar essas peças somente se um requisito concreto exigir clientes externos, processamento assíncrono ou escala que justifique a complexidade.

### 4.3 Organização sugerida do projeto

```text
config/                 Configuração Django, rotas e settings
apps/
  accounts/             Login, perfis e autorização
  customers/            Clientes e veículos
  scheduling/           Horários, disponibilidade e pedidos
  workshop/             Recepção, avaliação, orçamento e ordem de serviço
  inventory/            Catálogo e movimentações de estoque
  sales/                Vendas de balcão e itens vendidos
templates/              Templates base e páginas por módulo
static/
  css/                  Tokens, layout e estilos responsivos
  js/                   Interações progressivas da interface
  images/               Mídia otimizada e aprovada para uso
tests/                  Testes de integração e jornadas ponta a ponta
```

É uma divisão lógica, não uma exigência de criar todos os aplicativos antes de implementar as jornadas. Evitar duplicar regras de negócio entre views, templates e JavaScript.

## 5. Módulos e responsabilidades

### Contas e autorização

- Autenticar clientes e usuários da oficina.
- Associar cada cliente autenticado apenas aos próprios dados e agendamentos.
- Associar funcionários a um papel e verificar permissão no servidor para toda operação administrativa.
- Manter ações administrativas atribuíveis a um usuário individual; não compartilhar credenciais.
- Papel inicial recomendado: `OWNER`, `MANAGER` e `CUSTOMER`. A matriz de acesso entre dono e gerente continua sujeita à decisão indicada no PRD.

### Clientes e veículos

- Manter perfil do cliente separado da credencial de autenticação.
- Permitir vários veículos por cliente.
- Normalizar a placa para busca (caixa alta, removendo espaços e separadores), preservar a apresentação compreensível ao usuário e validar formato sem depender exclusivamente de uma regex rígida.
- Não solicitar ano, chassi ou quilometragem na primeira versão sem aprovação do produto.

### Agendamento

- Exibir ao cliente horários que a oficina aceita receber solicitações.
- Criar pedido em estado pendente; não comunicar confirmação antes da ação da equipe.
- Permitir à equipe confirmar o horário ou registrar uma alternativa e tratar o retorno manualmente pelo WhatsApp.
- Ao confirmar, revalidar disponibilidade dentro de uma transação no banco. Não confiar na disponibilidade exibida anteriormente no navegador.
- Impedir exceder a capacidade configurada no período confirmado. A política de quantidade por horário e o tratamento de vários pedidos pendentes continuam pendentes de validação; manter a capacidade configurável, não embutida em código.
- Agendar chegada/avaliação, não a duração do reparo. Uma revisão de motor de vários dias não deve ocupar todos os horários futuros na agenda de recepção.

### Recepção, avaliação e ordem de serviço

- Converter um pedido confirmado em atendimento após a chegada do veículo.
- Preservar imutável o relato original feito pelo cliente; registrar observações da recepção e avaliação técnica em campos/eventos separados.
- Criar orçamento após a avaliação, com itens de mão de obra e peças.
- Guardar uma fotografia dos valores e descrições no orçamento para que alterações posteriores no catálogo não reescrevam documentos já apresentados.
- Registrar o resultado e o responsável pela decisão do cliente.
- Só iniciar execução com aprovação registrada; converter o orçamento aprovado em ordem de serviço sem duplicar os itens.

### Estoque e vendas

- Manter catálogo de peças com descrição, preço de venda, unidade e saldo atual.
- Registrar entradas, saídas e ajustes como movimentações rastreáveis; não apagar movimentações concluídas para “corrigir” saldo.
- Venda de balcão e consumo em ordem de serviço devem usar a mesma operação de saída de estoque.
- Atualizar saldo e criar a movimentação de estoque na mesma transação que conclui a venda/consumo.
- Impedir venda/consumo acima do saldo disponível ou exigir autorização explícita para saldo negativo, conforme decisão do dono. Recomendação inicial: bloquear saldo negativo.
- Não baixar peça ao criar orçamento nem antes da execução. A regra exata do instante de baixa precisa ser aprovada; recomendação: concluir venda de balcão na venda e consumo da OS quando a peça for usada.
- Dinheiro deve usar tipo decimal, não ponto flutuante. Quantidades devem respeitar a unidade do produto; permitir frações quando fizer sentido (por exemplo, fluido vendido por volume).

## 6. Modelo de domínio inicial

Os nomes abaixo são conceituais; validar campos, cardinalidades e retenção antes das migrações.

| Entidade | Dados e relações principais |
|---|---|
| `User` | Conta Django; e-mail/login, credencial com hash e estado ativo. |
| `StaffProfile` | Relação 1:1 com `User`; papel, nome e estado do funcionário. |
| `CustomerProfile` | Relação 1:1 com `User`; nome, telefone e preferências essenciais de contato. |
| `Vehicle` | Cliente proprietário, placa normalizada, marca, modelo e cor opcional. |
| `AvailabilityRule` | Dia da semana, janela de recepção, intervalo e capacidade por slot definidos pela oficina. |
| `Appointment` | Cliente, veículo, horário solicitado, horário alternativo/confirmado, descrição livre, estado e responsável da oficina. |
| `WorkshopVisit` | Atendimento realizado; referência ao agendamento, chegada, veículo, estado operacional e queixa preservada. |
| `Assessment` | Avaliação técnica separada do relato inicial, autoria e data. |
| `Estimate` | Atendimento, versão, valores, estado e dados de aprovação/recusa. |
| `EstimateLine` | Tipo (`LABOR` ou `PART`), descrição, quantidade e preço unitário em snapshot. |
| `WorkOrder` | Orçamento aprovado, estado de execução, datas e responsáveis. |
| `Product` | Descrição da peça, unidade, preço e saldo disponível. |
| `StockMovement` | Produto, quantidade, direção/tipo, origem e usuário; histórico auditável de estoque. |
| `CounterSale` | Venda sem ordem de serviço, estado, itens, responsável e total calculado. |
| `CounterSaleLine` | Produto, quantidade e preço unitário em snapshot. |
| `AuditEvent` | Ator, ação, entidade, data e metadados mínimos não sensíveis para operações relevantes. |

Usar chaves estrangeiras e restrições do banco para relações e invariantes simples. Toda regra envolvendo várias linhas, como confirmar horário com capacidade limitada ou concluir uma venda com baixa de estoque, deve ser executada no servidor dentro de `transaction.atomic()` e revalidada sob bloqueio apropriado.

## 7. Estados e transições

### Pedido de horário

```text
PENDING -> CONFIRMED
PENDING -> ALTERNATIVE_PROPOSED
PENDING -> REJECTED
PENDING -> CANCELLED
ALTERNATIVE_PROPOSED -> CONFIRMED
ALTERNATIVE_PROPOSED -> CANCELLED
CONFIRMED -> CANCELLED | ARRIVED | NO_SHOW
```

`NO_SHOW` pode ser implementado após validação da operação. Uma mudança de estado exige autorização, valida a transição permitida e registra ator e horário. O pedido continua visualmente distinto do horário confirmado. A confirmação de uma proposta alternativa deve ocorrer apenas após acordo com o cliente; se esse acordo ficará registrado no site ou apenas no contato manual ainda é decisão pendente.

### Atendimento, orçamento e ordem de serviço

```text
CONFIRMED -> ARRIVED -> ASSESSED -> ESTIMATE_SENT
ESTIMATE_SENT -> APPROVED -> IN_PROGRESS -> COMPLETED
ESTIMATE_SENT -> REJECTED | REVISION_REQUESTED
```

Não iniciar `IN_PROGRESS` sem aprovação registrada. Revisões do orçamento criam nova versão ou novo conjunto de itens sem apagar o histórico anterior.

### Estoque e venda

Venda em rascunho pode ser cancelada sem movimentação. Conclusão da venda cria uma saída e reduz o saldo uma única vez. Cancelamento posterior a uma venda concluída requer operação compensatória auditada, não exclusão ou edição destrutiva do movimento original.

## 8. Interfaces e rotas propostas

Os caminhos são indicativos e podem seguir convenções do framework:

| Área | Página/ação |
|---|---|
| Público | `/` apresentação curta e acesso a login/cadastro/agendamento |
| Conta do cliente | `/conta/cadastro`, `/conta/entrar`, `/conta/sair` |
| Cliente | `/cliente/agendamentos`, `/cliente/agendamentos/novo`, `/cliente/agendamentos/<id>` |
| Gestão | `/gestao/agenda`, `/gestao/pedidos/<id>`, `/gestao/atendimentos/<id>` |
| Orçamento/OS | `/gestao/atendimentos/<id>/avaliacao`, `/orcamento`, `/ordem` |
| Estoque/vendas | `/gestao/pecas`, `/gestao/vendas/balcao`, `/gestao/vendas/<id>` |
| Configuração | `/gestao/configuracao/agenda`, `/gestao/usuarios` |

Cada view aplica autorização no servidor e chama uma operação de domínio. Templates exibem o resultado e mensagens; não são responsáveis por decidir estado permitido, disponibilidade ou saldo.

## 9. Design system e acessibilidade

- Reaproveitar a direção do protótipo de fila: fundo claro, tinta verde-escura, teal para ação primária, lime como acento e tipografia DM Sans/Space Grotesk/IBM Plex Mono ou substitutos locais quando indisponíveis.
- Definir tokens CSS para cor, espaço, tipo, borda, foco, status e breakpoint; evitar estilos duplicados por página.
- Manter o conteúdo principal da fila acima de estatísticas e conteúdo decorativo. Em telas pequenas, transformar colunas em fluxo vertical; nunca ocultar ações necessárias.
- Utilizar HTML semântico, ordem de títulos consistente, rótulos ligados aos campos, mensagens de erro próximas ao campo e confirmação de ações destrutivas.
- Dar suporte a teclado, foco claramente visível, zoom e preferência `prefers-reduced-motion`.
- Usar texto explícito para “Pendente”, “Confirmado”, “Alternativa proposta” e “Cancelado”. Cor e ícone reforçam, mas nunca substituem o rótulo.
- Alvo de conformidade: WCAG 2.2 nível AA; verificar contraste, teclado, zoom/reflow e formulários com ferramenta automática e teste manual.
- A página do cliente deve funcionar em celular com campos curtos e validação progressiva; agendar continua sendo uma solicitação, não confirmação automática.
- Não depender de imagem externa remota como informação essencial; servir imagens próprias/otimizadas e texto alternativo apropriado.

## 10. Segurança, privacidade e operação

- Ativar CSRF, cookies `Secure`/`HttpOnly`/`SameSite` adequados em produção, HTTPS e política de sessão segura.
- Usar hash de senha mantido pelo framework; nunca armazenar ou registrar senha, token de reset, conteúdo integral de mensagem, nem segredos em logs.
- Validar autorização em toda requisição e filtrar dados de cliente no servidor pelo usuário autenticado. Ocultar um botão na interface não é autorização.
- Usar ORM e queries parametrizadas. Validar e normalizar entradas; escapar conteúdo em templates; limitar tamanho de queixas e campos de texto.
- Guardar segredos em variáveis de ambiente e documentar `.env.example` sem credenciais reais.
- Definir retenção, exportação/correção/exclusão e cópias de segurança com a oficina antes de operar dados reais. Não usar dados reais de clientes como fixtures ou capturas de tela.
- Backups automáticos devem ser cifrados e ter procedimento de restauração testado. Registrar incidentes e mudanças críticas de agenda/estoque.
- Usar horário consciente de fuso; proposta inicial `America/Sao_Paulo`, configurável para a localização da oficina. Guardar timestamps consistentes e exibir no fuso configurado.

## 11. Estratégia de testes

### Testes de domínio

- Transições permitidas e proibidas de agendamento, orçamento e ordem de serviço.
- Normalização de placa e validação dos dados essenciais.
- Cálculo de totais com decimal e quantidades/unidades.
- Não confirmar horário acima da capacidade e não reduzir estoque duas vezes.

### Testes de integração

- Cliente autenticado agenda e consulta somente seus próprios pedidos.
- Funcionário autorizado confirma e funcionário sem permissão recebe acesso negado.
- Duas confirmações concorrentes para a última vaga não excedem a capacidade.
- Concluir venda cria movimento de estoque e atualiza saldo atomicamente; falha no meio da operação não deixa estado parcial.
- Orçamento aprovado conserva os valores aprovados ao virar ordem de serviço.

### Testes de interface

- Fluxo do cliente no celular: cadastro, login, solicitação pendente e consulta do estado.
- Fluxo da oficina: fila pendente, busca, confirmar/propor horário, recepção, orçamento e venda.
- Navegação por teclado, rótulos de campos, leitura dos estados sem cor e layout em larguras estreitas.
- Usar dados sintéticos. Uma interação de WhatsApp deve abrir um link preparado e não declarar mensagem enviada automaticamente.

## 12. Plano de implementação

1. Confirmar direção visual, política de vagas/pendências, permissões da oficina e política de cancelamento.
2. Inicializar Django, PostgreSQL, configuração por ambiente, autenticação, layout responsivo e testes de base.
3. Entregar cadastro/login de cliente, perfis da oficina e cadastro de veículos.
4. Entregar disponibilidade, solicitação, fila administrativa e confirmação concorrente segura.
5. Entregar recepção, avaliação, orçamento, aprovação e ordem de serviço.
6. Entregar catálogo, movimentações e vendas de balcão/consumo em serviço.
7. Executar revisão de segurança, acessibilidade, backups/restauração e piloto com a oficina.

Cada etapa deve ser testável e utilizável antes de avançar. Não migrar os botões demonstrativos do protótipo como se fossem comportamento implementado; substituí-los por formulários/views reais e testes.

## 13. Decisões técnicas pendentes

1. Confirmar se a direção “Decisões primeiro” é a escolhida ou se outra aba do protótipo prevalece.
2. Definir grade de horários, capacidade por período e se pedidos pendentes reservam vaga.
3. Definir se o cliente aceita uma nova proposta de horário no site ou se a equipe registra o acordo feito no WhatsApp.
4. Definir política de cancelamento, reagendamento e falta.
5. Aprovar matriz de acesso para dono e funcionário gestor.
6. Definir quando ocorre a baixa da peça consumida no reparo e como registrar devolução/cancelamento.
7. Decidir se venda de balcão registra forma de pagamento, sem integração, e se fotos entram no primeiro lançamento.
8. Confirmar stack, hospedagem, domínio, fuso horário e política de backup com a equipe responsável pelo TCC/oficina.

## 14. Critérios de pronto para produção

- Requisitos críticos do PRD implementados e aprovados pela oficina.
- Nenhum cliente consegue ler ou alterar dados de outro cliente por URL ou requisição direta.
- Dupla confirmação acima da capacidade e baixa dupla de estoque são impedidas sob concorrência.
- Pedidos pendentes nunca são apresentados como confirmados.
- Rotas administrativas estão protegidas e registram autor de mudanças críticas.
- Fluxos essenciais foram testados em desktop e celular, por teclado e com leitor de tela em amostra representativa.
- HTTPS, segredos, backups e restauração foram verificados no ambiente final.
- Aviso de privacidade e procedimentos de atendimento ao titular foram validados com a oficina.