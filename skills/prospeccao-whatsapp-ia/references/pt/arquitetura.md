# Arquitetura

## Objetivo

Construa um SaaS multiempresa que encontre empresas, prepare contatos, envie mensagens oficiais pelo WhatsApp, responda com IA e registre oportunidades. Mantenha cada etapa auditável e reversível.

## Fluxo principal

```text
Google Places
  -> normalização e deduplicação
  -> enriquecimento por CNPJ
  -> revisão e aprovação do prospect
  -> campanha e fila
  -> worker separado
  -> WhatsApp Cloud API
  -> webhook de resposta
  -> regras, debounce, RAG e LLM
  -> conversa, qualificação e CRM
```

O sistema não deve afirmar que obtém faturamento ou telefone de decisor. Trate campos ausentes como ausentes.

## Stack sugerida

- Frontend: React com Vite.
- API: Node.js com Fastify e TypeScript.
- Persistência: Prisma com Supabase Postgres.
- Vetores: extensão pgvector no Postgres.
- Estado efêmero, locks e debounce: Redis.
- Envio: worker em processo separado da API.
- Canal: WhatsApp Cloud API oficial da Meta.
- IA: provedor de LLM configurável por empresa.

## Multiempresa desde o primeiro schema

Use `tenantId` em prospects, buscas, campanhas, filas, conversas, mensagens, agentes, documentos, chunks, leads, bloqueios e eventos. Resolva o tenant pela sessão nas APIs autenticadas e pelo número oficial ou canal nos webhooks. Nunca aceite um `tenantId` enviado pelo navegador como autorização.

Crie índices compostos para consultas frequentes, por exemplo `(tenantId, status)`, `(tenantId, conversationId)` e identificadores externos únicos dentro do tenant. Teste que um usuário de uma empresa não lê nem altera dados de outra.

## Domínios recomendados

- `identity`: empresa, usuário, associação e papéis.
- `prospecting`: busca, prospect, campanha, cadência, inscrição e fila.
- `channels`: conexão oficial, template, envio, webhook e status.
- `ai`: agente, regras, RAG, tools e uso.
- `crm`: contato, conversa, lead, etapa, tarefa e agendamento.
- `audit`: eventos operacionais e falhas recuperáveis.

## Fila e worker

A API cria itens em `draft`. Uma ação explícita libera a campanha. O worker processa apenas itens vencidos, de campanhas ativas e destravadas. Antes de cada envio, verifique opt-out, lista de bloqueio, resposta anterior, limite diário, horário e estado do canal.

Use lock atômico por item, identificador de tentativa e idempotência por mensagem. Preserve itens enviados, pausados e falhos para auditoria. O webhook deve confirmar recebimento rapidamente e delegar processamento pesado a uma fila.

## Estados mínimos

- Prospect: capturado, aprovado, em campanha, respondeu, opt-out, arquivado.
- Campanha: rascunho, pronta, ativa, pausada, concluída, cancelada.
- Fila: rascunho, agendada, enviando, enviada, pausada, falhou, cancelada.
- Conversa: aberta, handoff, encerrada; com IA ativa ou pausada.

## Armadilhas de arquitetura

- Deploys simultâneos podem exceder o limite de conexões do banco. Defina um limite pequeno por processo e considere um pooler compatível com o modo de conexão usado.
- Um campo numérico `0` é uma configuração válida. Não use coerção por verdade/falsidade que o transforme no valor padrão.
- Limites podem existir na interface, na API, na campanha e no worker. Defina uma única fonte de verdade e exiba o teto efetivo.
- Migração de schema não deve depender apenas do build. Crie um passo explícito e verificável para produção.
