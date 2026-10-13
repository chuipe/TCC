# Diretrizes do Projeto

Este repositório contém o planejamento de um sistema web para uma oficina mecânica. A implementação ainda não existe; não presuma que a stack ou os módulos propostos já estejam configurados.

## Fontes de verdade

- [PRD.md](PRD.md) define escopo funcional e requisitos aprovados.
- [PROJETO_TECNICO.md](PROJETO_TECNICO.md) registra arquitetura proposta, modelo de domínio, decisões pendentes e abordagem de implementação.
- [prototipos.html](prototipos.html) contém protótipos demonstrativos, com dados fictícios; não é aplicação de produção.
- A aba “Decisões primeiro” é apenas a seleção inicial do protótipo. Trate-a como direção provisória até confirmação explícita do responsável pelo produto.

## Arquitetura

- Stack recomendada: Django monolítico, templates server-rendered, CSS/JavaScript mínimos e PostgreSQL. Confirme a decisão antes de inicializar o projeto.
- Mantenha regras de negócio no servidor e valide permissões em toda requisição; ocultar elementos na interface não substitui autorização.
- Confirmação de agenda e movimentação de estoque devem ser transacionais, concorrentes com segurança e auditáveis.
- Leia [PROJETO_TECNICO.md](PROJETO_TECNICO.md) antes de alterar arquitetura ou modelo de dados.

## Escopo e UX

- Preserve o fluxo: cliente autenticado solicita horário; a solicitação fica pendente até ação da oficina; confirmação de contato por WhatsApp é manual.
- O horário representa chegada/avaliação, não a duração do reparo. Não limite serviços a um catálogo fechado; a descrição é texto livre.
- Não adicione pagamentos integrados, gestão financeira, emissão fiscal, automação de WhatsApp ou diagnóstico automático sem atualização aprovada do PRD.
- Preserve layout responsivo, acessibilidade por teclado, foco visível e estados com rótulos textuais; não comunique estado somente por cor.
- Não reutilize dados reais de clientes em testes, exemplos, fixtures ou screenshots.

## Código e validação

- Siga os padrões e ferramentas que forem adicionados ao projeto; não invente comandos de build ou dependências antes de verificar a configuração existente.
- Inclua testes para autorização por perfil, transições de estado, concorrência de horários e consistência de estoque.
- Use `Decimal` para valores monetários e migrações revisáveis para mudanças de banco.
- Mantenha segredos fora do Git; forneça apenas exemplos sem credenciais.
- Não remova nem sobrescreva alterações existentes sem solicitação explícita.