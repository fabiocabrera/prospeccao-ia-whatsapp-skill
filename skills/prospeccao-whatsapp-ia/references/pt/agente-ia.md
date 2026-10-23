# Agente de IA

## Prompt estruturado

Organize o prompt de sistema em tags XML simples, por exemplo:

```xml
<identidade>...</identidade>
<objetivo>...</objetivo>
<tom>...</tom>
<limites>...</limites>
<qualificacao>...</qualificacao>
<uso_de_tools>...</uso_de_tools>
<handoff>...</handoff>
```

Separe fatos da empresa, regras operacionais e instruções de conversa. Diga ao modelo para não inventar preços, disponibilidade ou dados do prospect. Se perguntarem diretamente, a IA não deve negar que é uma IA.

## Respostas no WhatsApp

Prefira mensagens curtas e naturais. Quando uma resposta precisar de várias ideias, divida em poucas partes e envie com intervalo configurável. Limite a quantidade de partes. Não divida templates de primeiro contato como se fossem várias mensagens livres.

Agrupe mensagens rápidas do lead com debounce. Cada nova mensagem reinicia a janela. No flush, processe o conjunto uma vez. Use chave por tenant, canal e contato, limite de buffer e token de comparação para impedir dois flushes.

## RAG sobre PDF e FAQ

Extraia texto real de PDFs. Não gere embeddings dos bytes brutos do arquivo: PDF indexado como binário produz uma base inútil. Normalize o texto, preserve referência de página, divida em chunks com sobreposição e grave o modelo de embedding usado.

No retrieval, filtre sempre por `tenantId` e agente. Não compare vetores produzidos por modelos diferentes. Inclua somente os trechos necessários e instrua o modelo a admitir quando a base não contém a resposta.

## Biblioteca de materiais e tools

Cadastre materiais com tipo, tamanho, descrição de quando usar e referência segura. O modelo escolhe pela descrição e chama uma tool, como `enviar_material`, com um identificador permitido. A tool valida tenant, janela de 24 horas, tipo, tamanho e existência do arquivo.

Nunca aceite uma resposta em que a IA promete enviar um material sem chamar a tool no mesmo turno. Trate isso como falha observável e registre o motivo.

## Qualificação e CRM

Colete apenas o necessário, uma pergunta por vez. Exemplos: necessidade, contexto, prazo e interesse em falar com uma pessoa. Quando os critérios forem atendidos, use uma tool para criar ou atualizar o lead. A operação deve ser idempotente e vinculada à conversa e ao tenant.

Não invente telefone de decisor, faturamento ou estágio. Grave um resumo baseado na conversa, origem, temperatura explicável e próximo passo. Faça handoff quando solicitado, quando houver incerteza sensível ou quando uma regra exigir atendimento humano.

## Ordem de decisão

Antes de chamar o LLM, avalie: opt-out, bloqueio, módulo habilitado, pausa geral, pausa da conversa, handoff, campanha que permite IA, horário, regras de palavras-chave e aprovação manual. Somente depois faça RAG, LLM e tools.
