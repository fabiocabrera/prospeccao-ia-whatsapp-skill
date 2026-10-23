---
name: prospeccao-whatsapp-ia
description: Use quando a pessoa quiser criar um sistema de prospecção por WhatsApp com agente de IA, Google Places, enriquecimento por CNPJ e CRM.
---

# Prospecção WhatsApp com IA

Detecte o idioma do usuário. Para português, leia apenas os arquivos necessários em `references/pt/`. Para inglês, use os equivalentes em `references/en/`. Responda no idioma detectado.

## Fluxo em 5 etapas

1. Descobrir empresas no Google Places.
2. Enriquecer dados por CNPJ, sem inventar campos ausentes.
3. Revisar prospects e preparar uma campanha.
4. Enviar o primeiro contato com template aprovado pela API oficial da Meta.
5. Atender respostas com IA, qualificar e registrar no CRM.

## Ordem de construção

Leia, nesta ordem e conforme a tarefa: arquitetura, integrações, agente de IA, segurança/conformidade e prompts de construção. Construa primeiro isolamento multiempresa e persistência; depois integrações; em seguida fila e worker; por último IA, CRM e interface.

## Regras de conformidade

- Use `tenantId` em toda entidade e consulta operacional.
- Exija template aprovado no primeiro contato e respeite a janela de 24 horas.
- Implemente opt-out, lista de bloqueio e pausas antes de ativar envios.
- Pause a cadência quando o lead responder ou quando uma pessoa assumir a conversa.
- Nunca exponha credenciais, engane sobre a natureza da IA ou teste escrita em produção por curiosidade.
- Não prometa resultados comerciais nem dados que o sistema não obtém.

## Referências

- Português: `references/pt/arquitetura.md`, `integracoes.md`, `agente-ia.md`, `seguranca.md`, `prompts-de-construcao.md`.
- English: `references/en/architecture.md`, `integrations.md`, `ai-agent.md`, `compliance.md`, `build-prompts.md`.
