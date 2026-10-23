# Segurança e conformidade

## Controles obrigatórios

- Opt-out imediato, persistente e verificado antes de cada envio.
- Template aprovado no primeiro contato.
- Pausa automática da cadência quando o lead responde.
- Pausa geral da IA por workspace.
- Pausa da IA por conversa.
- Detecção de resposta humana enviada pelo celular ou pela caixa de entrada.
- Horário de atendimento configurável por tenant e fuso horário.
- Lista de bloqueio por telefone e identificador externo.
- Handoff claro para atendimento humano.

Uma resposta humana não é inbound do lead. Registre-a como saída, não dispare outra resposta da IA e desative a IA da conversa quando a política de handoff estiver ativa. Uma conversa encerrada não deve ser reaberta por um eco do celular.

## Transparência

A IA pode se apresentar pelo papel comercial sem iniciar cada mensagem com uma explicação técnica. Porém, se a pessoa perguntar diretamente, não deve negar que é uma IA. Ofereça encaminhamento para uma pessoa.

## Isolamento e autorização

Todas as tabelas operacionais e consultas usam `tenantId`. Resolva a empresa pela sessão ou pelo canal validado. Verifique associação e papel no backend. O frontend não define autorização.

Use RLS como camada adicional quando houver acesso direto ao banco, mas mantenha as mutações atrás da API. Teste acessos cruzados, jobs em background e webhooks, não apenas telas autenticadas.

## Segredos

Criptografe tokens, chaves e credenciais em repouso. Use uma chave de criptografia fora do banco. Nunca devolva o segredo completo ao frontend nem o registre em logs. Separe credenciais por ambiente e aplique rotação.

## Webhooks e dados

Valide assinatura sobre o corpo bruto antes de interpretar o payload. Deduplicate eventos. Limite tamanho do corpo e tipos aceitos. Guarde o mínimo necessário. Defina retenção, auditoria e processo de exclusão.

## Operação segura

Campanhas começam travadas. Exija revisão e liberação explícita. Aplique limite diário, rate limit, horário e botão de pausa. O worker deve verificar novamente todos os bloqueios imediatamente antes do envio.

Não teste endpoints de escrita em produção “para ver o que acontece”. Use mocks, sandbox ou um tenant de homologação com destinatários autorizados. Registre ações administrativas sem incluir segredos.

## Conformidade

Mapeie a base legal aplicável, a finalidade e a retenção com orientação jurídica local. Respeite políticas da Meta, regras do Google, pedidos de acesso e exclusão e preferências de contato. Mantenha identificação adequada e mecanismo simples de descadastro.

Este projeto não garante conformidade por si só. Também não promete resultado de vendas ou conversão.
