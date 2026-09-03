# System Design — Encaminhamento de Proposta de Crédito
 
## Contexto
 
A empresa opera um pipeline de crédito composto por várias etapas sequenciais. Para esta avaliação, considere que as seguintes etapas **já foram concluídas** e estão fora do escopo do design:
 
- Prospecção
- Originação e solicitação
- Análise de crédito e compliance


Sua responsabilidade começa no momento em que um **arquivo** contendo propostas já aprovadas internamente é recebido pelo sistema, e essas propostas precisam ser encaminhadas a um fundo parceiro para formalização e liberação do crédito.
 
O arquivo de entrada segue um único formato definido (por exemplo, um CSV ou um layout posicional como CNAB — comum no mercado financeiro) e pode conter **uma ou múltiplas propostas de crédito** em um único envio (processamento em lote).
 
---
 
## O problema
 
A empresa possui dois fundos parceiros: **Fundo Alpha** e **Fundo Beta**. O modelo de integração com cada fundo é diferente:
 
- **Fundo Alpha** — é o fundo primário. Toda proposta deve ser enviada ao Alpha primeiro. Após o recebimento, o Alpha notifica sua plataforma com o resultado (aprovado ou recusa) por meio de um **webhook**.
- **Fundo Beta** — é o fundo de fallback. Caso o Fundo Alpha esteja indisponível após tentativas de envio, a proposta deve ser automaticamente encaminhada ao Beta. A integração com o Beta funciona por **polling**: é necessário consultar periodicamente a API deles para verificar se o status da proposta foi atualizada.
Ao final do processo — independentemente de qual fundo processou a proposta — o **cliente deve ser notificado** sobre o resultado.
 
Importante: como o arquivo pode conter N propostas, cada proposta segue seu próprio ciclo de vida de forma independente — uma falha, recusa ou indisponibilidade de fundo em uma proposta não pode impactar o processamento das demais.
 
---
 
## O que deve ser projetado
 
Projete o sistema responsável por:
 
1. Receber o arquivo (ex: CSV ou CNAB), extrair e validar as propostas nele contidas (uma ou várias), e iniciar o processo de encaminhamento individualmente para cada proposta aprovada.
2. Enviar cada proposta a algum dos fundos.
3. Processar a resposta do fundo (qualquer que seja, para cada proposta) e notificar o cliente correspondente sobre o resultado.
