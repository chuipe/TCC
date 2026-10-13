# PRD - Sistema de Gestão para Oficina Mecânica

**Versão:** 0.1 (rascunho para validação)  
**Data:** 13/10/2026  
**Produto:** aplicação web responsiva para uma oficina mecânica real

## 1. Visão do produto

Organizar a jornada do cliente e do veículo, começando pelo agendamento online e seguindo pela recepção, avaliação, orçamento e venda de peças. O cliente poderá solicitar um horário pelo celular, sem precisar ir até a oficina para marcar. A equipe gerenciará a agenda e os atendimentos em uma interface administrativa.

## 2. Problema e oportunidade

Agendamentos feitos manualmente podem gerar conflitos de horário, informações incompletas na chegada do veículo e retrabalho no atendimento. O sistema deve centralizar os horários, registrar previamente a queixa do cliente e conectar o serviço aprovado à venda das peças utilizadas.

## 3. Objetivos

- Permitir que clientes consultem horários disponíveis e solicitem agendamentos pela web, inclusive pelo celular.
- Dar à equipe uma visão centralizada da agenda e dos dados necessários para receber o veículo.
- Registrar o relato inicial do cliente sem tratá-lo como diagnóstico técnico definitivo.
- Gerar orçamento após a avaliação da oficina e registrar a decisão do cliente.
- Controlar vendas de peças no balcão e peças vinculadas aos serviços.
- Restringir o acesso aos dados de clientes a usuários autorizados.

## 4. Não objetivos do MVP

- Integração de pagamentos, contas a pagar/receber ou gestão financeira.
- Emissão de nota fiscal.
- Diagnóstico automatizado ou recomendação automática de reparos.
- Gestão de múltiplas filiais.
- Aplicativo nativo para celular; o produto será um site responsivo.

## 5. Usuários e permissões

| Perfil | Necessidades e permissões |
|---|---|
| Dono da oficina | Acesso administrativo à agenda, equipe, atendimentos, orçamentos e vendas. |
| Funcionário responsável pela gestão no computador | Gerenciar agenda, cadastro e atendimento, respeitando as permissões definidas pelo dono. Acesso aos dados pessoais somente quando necessário ao trabalho. |
| Cliente | Criar uma conta, solicitar e consultar os próprios agendamentos; informar dados do veículo e a queixa inicial. Não pode consultar dados de outros clientes. |

O acesso administrativo deve exigir autenticação. A equipe não deve compartilhar uma conta única; cada usuário administrativo deve ser identificável. O nível exato de acesso do dono e dos funcionários será configurável ou validado antes da implementação.

## 6. Requisitos por prioridade

As cores indicam prioridade para o produto, do menor para o maior impacto. Verde não significa que o item já está aprovado para o MVP; itens nessa categoria são complementares e podem ser adiados.

### 🟢 Verde - Complementar

- Cor do veículo como dado opcional para ajudar a equipe a identificá-lo.
- Fotos na recepção/pré-diagnóstico, caso a oficina confirme que são necessárias na primeira versão.
- Alerta de estoque mínimo, depois que a oficina definir os limites por peça.
- Recursos adicionais de relatório e histórico além da consulta básica do atendimento.

### 🟡 Amarelo claro - Importante

- Atalho para a equipe abrir uma conversa no WhatsApp do cliente.
- Confirmações, propostas de novo horário e atualizações enviadas manualmente pela equipe; sem API ou disparos automáticos no MVP.
- Histórico de agendamentos e atendimentos associado ao cliente e ao veículo.
- Busca de agendamentos por data, cliente, placa e situação.
- Registro de alterações importantes em agendamentos, orçamentos e estoque, com usuário e horário.

### 🟠 Laranja - Prioridade alta

**Recepção e pré-diagnóstico**
- Localizar o agendamento e registrar a entrada do veículo.
- Preservar a queixa descrita pelo cliente e permitir observações adicionais da equipe.
- Manter o relato inicial separado da avaliação técnica; o sistema não gera diagnóstico automático.

**Orçamento e autorização**
- Após a avaliação, criar orçamento com serviços, mão de obra e peças, calculando subtotais e total.
- Registrar aprovação, recusa ou pedido de alteração, com data e responsável.
- Converter orçamento aprovado em ordem de serviço sem redigitar os itens; a execução depende da aprovação registrada.

**Peças e vendas**
- Cadastrar peças com descrição, preço e quantidade em estoque, registrando entradas e saídas.
- Permitir venda de balcão com itens, quantidades, preços, total e opção de concluir ou cancelar.
- Permitir incluir peças no orçamento/ordem de serviço e relacionar a saída do estoque ao atendimento.
- Baixar o estoque uma única vez na conclusão da venda ou execução do serviço, conforme regra operacional definida.
- Registrar itens e valores, sem processar pagamentos integrados nem emitir documentos fiscais.

**Dados do veículo**
- Solicitar placa, marca e modelo como dados mínimos de identificação.
- Deixar ano, chassi e quilometragem fora do escopo inicial, salvo necessidade identificada na validação.

### 🔴 Vermelho - Essencial para o MVP

**Conta e privacidade**
- Exigir que o cliente crie uma conta e esteja autenticado para solicitar e consultar seus próprios agendamentos.
- Impedir que um cliente consulte os dados de outro.
- Exigir autenticação administrativa e identificar cada usuário; não compartilhar uma conta única entre funcionários.
- Restringir dados pessoais a usuários autorizados e coletar somente o necessário para o atendimento.

**Agendamento**
- Permitir ao cliente informar contato, veículo, serviço desejado ou queixa em texto livre; não limitar o pedido a um catálogo fechado de serviços.
- Exibir horários para o cliente solicitar como preferência de chegada e avaliação do veículo.
- Manter a solicitação pendente até a oficina confirmar ou propor outro horário.
- Impedir a confirmação de mais de um agendamento para o mesmo horário; mostrar solicitações pendentes à equipe sem apresentá-las ao cliente como confirmadas.
- Tratar o horário como chegada/avaliação: a duração do reparo, inclusive de serviços longos, não determina a duração da reserva.
- Permitir à equipe consultar, criar, alterar, confirmar, cancelar e atualizar agendamentos.
- Disponibilizar à equipe visão dos agendamentos por data e situação.

**Acesso e operação segura**
- Interface web responsiva para celular, tablet e computador, em português e com formatos brasileiros de data e moeda.
- Definir backups e recuperação de dados antes do uso com clientes reais.
- Validar com a oficina aviso de privacidade, finalidade do uso dos dados e processo para atender solicitações de correção ou exclusão, considerando a LGPD.

## 7. Fluxo principal

1. Cliente acessa o site pelo celular ou computador e entra em sua conta.
2. Informa veículo, descreve livremente o serviço/queixa e solicita um horário para chegada e avaliação.
3. A solicitação fica pendente; a oficina confirma o horário ou propõe outro e avisa o cliente manualmente pelo WhatsApp.
4. A equipe recebe o veículo e registra a entrada e as observações iniciais.
5. A oficina avalia o veículo e prepara o orçamento.
6. O cliente aprova, recusa ou solicita alteração.
7. Com a aprovação, a equipe executa o serviço e registra mão de obra e peças utilizadas.
8. A equipe conclui o atendimento ou registra a venda de balcão; o estoque é atualizado de acordo com a operação.

## 8. Requisitos não funcionais

- Interface web responsiva, utilizável em celular, tablet e computador.
- Idioma e formatos brasileiros: português, data e moeda em reais.
- Controle de acesso por usuário e perfil; cliente só acessa seus próprios dados.
- Registro de alterações importantes em agendamentos, orçamentos e estoque, incluindo usuário e horário.
- Proteção de dados desde a concepção: coletar somente o necessário, informar a finalidade, restringir acesso e oferecer processo para correção ou exclusão quando aplicável. Validar o tratamento e a política de privacidade com a oficina, considerando a LGPD.
- Backups e recuperação de dados devem ser definidos antes da operação com clientes reais.

## 9. Critérios de aceite iniciais

- Um cliente autenticado consegue solicitar um horário em um fluxo que funciona em celular.
- Uma solicitação ainda não aparece como confirmada para o cliente até a oficina aprová-la.
- Dois agendamentos não conseguem ser confirmados para o mesmo horário.
- A equipe consegue visualizar os agendamentos do dia e localizar um atendimento pela placa ou pelo cliente.
- A queixa inicial fica preservada e não é confundida com o diagnóstico feito pela oficina.
- Um orçamento aprovado pode ser convertido em serviço sem recadastrar seus itens.
- Uma venda de balcão concluída reduz o estoque correspondente; cancelamentos não deixam uma baixa indevida.
- Um cliente não consegue visualizar os dados de outro cliente.
- A equipe consegue registrar uma venda de balcão e uma venda vinculada a serviço sem processar pagamento integrado.

## 10. Indicadores de sucesso

“Confiar no sistema” é importante, mas precisamos observar se ele funciona na rotina. Durante um piloto, medir:

- Percentual de agendamentos registrados no sistema em relação aos atendimentos marcados.
- Quantidade de conflitos de horário e agendamentos perdidos.
- Percentual de atendimentos com placa, modelo e queixa registrados antes da avaliação.
- Diferenças entre estoque registrado e contagem física, além de vendas sem baixa correta.
- Tempo que a equipe leva para localizar um agendamento e registrar uma venda.

As metas numéricas devem ser definidas após observar a rotina atual da oficina e estabelecer uma linha de base.

## 11. Aprendizados da pesquisa de mercado

As páginas de produtos consultadas mostram padrões recorrentes: agenda visual, cadastro por cliente e veículo, checklist/fotos, histórico, orçamento convertido em ordem de serviço, etapas de atendimento, estoque atualizado pelas vendas e comunicação via WhatsApp. Para este produto, o diferencial de foco é começar pelo agendamento direto pelo cliente e conectar essa reserva ao restante do atendimento, sem incluir no MVP um ERP financeiro completo.

Referências de funcionalidades consultadas: [Garage System](https://www.garagesystem.com.br/), [Oficina.app](https://oficina.app/) e [Wüst Software](https://wustsoftware.com.br/). São páginas comerciais dos próprios fornecedores; foram usadas para identificar padrões de produto, não para validar alegações de desempenho.

Referência regulatória: [Lei Geral de Proteção de Dados - texto no Planalto](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm).

## 12. Decisões pendentes para fechar o escopo

1. O cliente poderá cancelar ou remarcar o pedido pela conta? Até quanto tempo antes?
2. A oficina terá uma quantidade fixa de horários de chegada por período, configurada pelo dono? Como tratar solicitações pendentes para o mesmo horário?
3. As vendas de balcão exigem registrar forma de pagamento/caixa, mesmo sem integração financeira, ou basta registrar itens, quantidades e total?
4. Fotos na recepção/pré-diagnóstico são necessárias na primeira versão?
5. O dono terá acesso a todos os dados administrativos? Qual informação pode ser ocultada dos demais funcionários?
6. Qual oficina participará do piloto e quem será responsável por validar os fluxos?