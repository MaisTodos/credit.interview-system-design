# System Design — Encaminhamento de Proposta de Crédito

## Contexto

A empresa opera um pipeline de crédito composto por várias etapas sequenciais. Para esta avaliação, considere que as seguintes etapas **já foram concluídas** e estão fora do escopo do design:

- Prospecção
- Originação e solicitação
- Análise de crédito e compliance

Sua responsabilidade começa no momento em que uma proposta é **aprovada internamente** e precisa ser encaminhada a um fundo parceiro para formalização e liberação do crédito.

---

## O problema

A empresa possui dois fundos parceiros: **Fundo Alpha** e **Fundo Beta**. O modelo de integração com cada fundo é diferente:

- **Fundo Alpha** — é o fundo primário. Toda proposta deve ser enviada ao Alpha primeiro. Após o recebimento, o Alpha notifica sua plataforma com o resultado (aprovado ou recusa) por meio de um **webhook**.

- **Fundo Beta** — é o fundo de fallback. Caso o Fundo Alpha esteja indisponível após tentativas de envio, a proposta deve ser automaticamente encaminhada ao Beta. A integração com o Beta funciona por **polling**: é necessário consultar periodicamente a API deles para verificar se o status da proposta foi atualizado.

Ao final do processo — independentemente de qual fundo processou a proposta — o **cliente deve ser notificado** sobre o resultado.

---

## O que deve ser projetado

Projete o sistema responsável por:

1. Receber a proposta aprovada e iniciar o processo de encaminhamento.
2. Enviar a proposta ao algum dos fundos.
3. Processar a resposta do fundo (qualquer que seja) e notificar o cliente sobre o resultado.
