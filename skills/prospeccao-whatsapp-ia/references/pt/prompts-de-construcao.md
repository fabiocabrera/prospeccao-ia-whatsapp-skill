# Prompts de construção

Cole os prompts abaixo no Claude Code, em ordem. Antes de cada etapa, forneça o repositório e as decisões já tomadas. Peça alterações pequenas e valide o critério de pronto antes de avançar.

## 1. Fundação multiempresa

> Crie a fundação de um SaaS multiempresa com React/Vite, Node/Fastify, TypeScript, Prisma e Postgres. Modele Tenant, User e Membership. Toda entidade operacional deve ter tenantId. Resolva o tenant pela sessão no backend. Adicione testes que provem que uma empresa não acessa dados de outra. Não implemente integrações externas ainda.

**Pronto quando:** schema validado, autenticação e autorização no backend, teste de isolamento passando e nenhum segredo no frontend.

## 2. Domínio de prospecção e CRM

> Modele buscas, prospects, campanhas, cadências, inscrições, fila, opt-out, eventos, contatos, conversas, mensagens e leads. Defina estados e transições explícitas. Campanhas devem nascer travadas. Preserve histórico para auditoria. Trate zero como valor válido nas configurações numéricas.

**Pronto quando:** migrations aplicam em banco limpo, transições inválidas são rejeitadas e todas as consultas filtram por tenantId.

## 3. Google Places

> Implemente um adaptador backend para Google Places Text Search. Combine segmento e localização, pagine até 60 resultados, normalize e deduplicate, aplique palavras de exclusão e armazene somente campos necessários. A chave fica no servidor e deve ser restringida no provedor. Adicione métricas sem expor a chave.

**Pronto quando:** fixtures cobrem várias páginas, exclusões e duplicatas; uma busca controlada não supera 60 resultados.

## 4. Enriquecimento por CNPJ

> Crie uma interface para provedor de CNPJ. Enriqueça apenas prospects aprovados. Salve origem, confiança, data e motivo de falha. Trate match como incerto e não invente dados. Não afirme fornecer faturamento nem telefone de decisor.

**Pronto quando:** sucesso, ausência, ambiguidade, rate limit e indisponibilidade têm estados claros e testados.

## 5. WhatsApp Cloud API

> Implemente conexão por tenant com WABA, número e token de usuário de sistema criptografado. Implemente verificação do webhook, validação X-Hub-Signature-256 sobre corpo bruto, deduplicação e resposta rápida. Processe eventos em segundo plano. Implemente templates aprovados para primeiro contato e texto livre somente na janela de 24 horas.

**Pronto quando:** assinatura inválida é rejeitada, eventos repetidos não duplicam mensagens, o tenant é resolvido pelo número e primeiro contato sem template é bloqueado.

## 6. Fila e worker

> Crie um worker separado para itens agendados. Use claim atômico, lock com TTL e idempotência. Antes de enviar, confira campanha ativa, trava removida, opt-out, bloqueio, resposta do lead, horário, limite e estado do canal. Registre sucesso e falha. Defina limite de conexões do banco por processo.

**Pronto quando:** dois workers concorrentes não duplicam envio, reinício recupera lock expirado e pausa impede novos envios.

## 7. Webhook, pausa e takeover humano

> Ao receber resposta do lead, pause a inscrição antes de qualquer IA. Detecte também mensagens enviadas por uma pessoa pelo celular ou inbox; grave como outbound e aplique handoff sem disparar IA. Adicione pausa geral, pausa por conversa, lista de bloqueio, opt-out e horário de atendimento.

**Pronto quando:** testes cobrem inbound, eco humano, conversa encerrada, opt-out e todas as camadas de pausa.

## 8. Agente, debounce e RAG

> Implemente agente com prompt XML, respostas curtas em partes limitadas e intervalo configurável. Agrupe mensagens rápidas com debounce no Redis. Para RAG, extraia texto real de PDF e FAQ, faça chunks, embeddings e retrieval filtrados por tenant e agente. Nunca indexe bytes brutos de PDF.

**Pronto quando:** mensagens em sequência geram uma resposta, PDF retorna trechos legíveis e não existe vazamento entre tenants ou modelos de embedding.

## 9. Tools, materiais e qualificação

> Implemente tools tipadas para enviar material e criar ou atualizar lead. A IA só pode prometer material quando chamar a tool no mesmo turno. Valide arquivo, tenant, janela e limites de 16 MB para vídeo, 5 MB para imagem e 100 MB para documento. Faça qualificação curta e idempotente.

**Pronto quando:** promessa sem tool é bloqueada, arquivo acima do limite falha antes do provedor e a mesma conversa não cria leads duplicados.

## 10. Interface, observabilidade e liberação

> Construa o fluxo Buscar, Revisar, Preparar, Enviar e Monitorar. Mostre o teto efetivo quando houver limites em várias camadas. Adicione auditoria, métricas, alertas de token da Meta, estado do worker e botões de pausa. Crie preflight de segredos, banco, Redis, templates e políticas antes da produção.

**Pronto quando:** um operador entende por que cada item enviou, pausou ou falhou; nenhum segredo aparece no bundle ou logs; todos os checks passam em homologação.

## 11. Revisão final de segurança

> Faça uma revisão somente leitura. Procure ausência de tenantId, escrita direta em produção, logs com segredos, valores zero substituídos por defaults, limites divergentes, PDF binário no RAG, promessa de material sem tool, webhook lento, locks frágeis, excesso de conexões e token da Meta sem renovação. Liste achados por risco antes de corrigir.

**Pronto quando:** os achados estão documentados com evidência, prioridade e teste de regressão proposto.
