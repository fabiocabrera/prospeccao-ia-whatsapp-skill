# Integrações

## Google Places

Use busca por texto para combinar segmento e localização. Faça paginação até, no máximo, 60 resultados por consulta. Aplique uma lista configurável de palavras de exclusão depois de normalizar nome, categoria e endereço. Deduplicate por identificador do lugar e, como apoio, por telefone normalizado.

Restrinja a chave no provedor: limite a API autorizada e os IPs ou origens compatíveis com o local onde a chamada ocorre. Mantenha a chave apenas no backend. Registre custo, duração, quantidade recebida e motivo de descarte, sem registrar a credencial.

## Enriquecimento por CNPJ

Use um provedor externo atrás de uma interface própria. O casamento costuma ter taxa de acerto modesta e funciona melhor para empresas com site próprio. Guarde a origem, confiança, data e critérios do match. Não transforme inferência em dado confirmado.

Faça o enriquecimento depois da revisão inicial ou apenas para prospects aprovados. O sistema descrito aqui não promete faturamento nem telefone de decisor.

## WhatsApp Cloud API da Meta

Cada empresa conecta sua WABA e um ou mais números. Use token permanente emitido para usuário de sistema e mantenha-o criptografado no servidor. Tokens podem expirar, ser revogados ou perder permissões; acompanhe a validade, renove quando aplicável e gere alerta operacional em caso de falha.

No webhook:

- implemente o desafio de verificação;
- valide `X-Hub-Signature-256` contra o corpo bruto;
- resolva o tenant pelo identificador do número recebido;
- deduplicate pelo identificador da mensagem;
- responda rapidamente e processe o restante em segundo plano.

O primeiro contato de prospecção deve usar template aprovado. Texto livre só deve ser enviado dentro da janela de 24 horas aberta por uma mensagem do lead. Verifique o status do template no servidor imediatamente antes de preparar ou liberar a campanha.

Limites de mídia a considerar:

- vídeo: 16 MB;
- imagem: 5 MB;
- documento: 100 MB.

Valide tamanho e tipo antes do upload e novamente antes do envio. Use uma margem abaixo do teto para arquivos que possam ser recodificados.

## LLM

Isole o provedor atrás de uma interface. Configure modelo, temperatura, limite de saída e credencial por tenant. Aplique timeout, repetição limitada com backoff e medição de uso. Nunca envie segredos ou dados desnecessários no prompt.

## Redis

Use Redis para debounce, locks curtos, rate limiting e filas leves. Inclua `tenantId`, canal e contato nas chaves. Defina TTL. Se o Redis falhar, escolha um comportamento seguro: não duplique envios e, para respostas, faça fallback síncrono apenas quando o risco estiver controlado.

## Contratos e testes

Crie adaptadores para cada integração e teste-os com fixtures. Separe testes de leitura de testes de escrita. Nunca chame um endpoint de escrita em produção apenas para descobrir seu comportamento. Use sandbox, ambiente de homologação ou simulação documentada.

## Armadilhas

- A próxima página do Google pode exigir espera ou token específico. Modele a paginação, não apenas um `limit` maior.
- O webhook pode ser reenviado. Idempotência é obrigatória.
- Um token da Meta que funcionou hoje pode expirar. Renovação e alerta fazem parte da integração.
- Um teto mostrado na UI pode ser superado ou reduzido pelo worker. Exponha o limite efetivo.
