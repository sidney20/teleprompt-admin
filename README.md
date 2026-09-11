# TelePrompT · Painel do Dono

Painel exclusivo (protegido por login Google + regra de owner) para acompanhar o uso do app TelePrompT.

> O owner é definido por UID fixo no código (`OWNER_UID`). Se for trocar o dono, edite essa constante e o
> UID nas regras do Firestore/Storage.

## Setup no Console Firebase (deshboard-d3f4a)

1. **Firestore Database** → Criar banco (modo produção).
2. **Authentication → Sign-in method** → ativar:
   - **Anônimo** (o app grava uso sem cadastro).
   - **Google** (você entra no painel).
3. **Firestore → Rules** → colar o conteúdo de `firestore.rules` (neste repo) e **Publicar**.

## O que o painel mostra

- **Visão geral**: usuários totais, ativos hoje, gravações hoje, erros hoje; tabela com **status online** (bolinha: verde <5min, âmbar 5–30min, vermelho >30min) e badge de **mensagens não lidas**.
- **Usuários**: lista completa com status; clicar abre o **modal do usuário** com:
  - aparelho parseado (OS, modelo, navegador), UID e device ID;
  - stats (gravações, tempo gravado, erros, mensagens, status);
  - **linha do tempo** (sessões, gravações, erros);
  - **chat** em tempo real com o usuário (enviar mensagens; não lidas ficam como badge no app).
- **Mensagens**: mensagens recebidas pelos usuários no chat do app, com status Nova/Lida.
- **Suporte**: solicitações com status (Nova, Lida, Respondida, Resolvida), filtro por status; responder abre o chat com o usuário.
- **Erros**: erros recentes (JS, câmera, gravação) com filtro.
- **Uso**: gráfico de gravações/aberturas por dia (últimos 21 dias).

## Coleções usadas pelo app

| Coleção            | Quem cria                            | Leitura   |
|--------------------|--------------------------------------|-----------|
| `users`            | usuário anônimo (doc id = uid)       | só owner  |
| `sessions`         | usuário (abrir, gravar + duration)   | só owner  |
| `errors`           | usuário (onerror, câmera, rec)       | só owner  |
| `messages`         | usuário e owner (chat)               | owner + dono da thread |
| `support_messages` | usuário (suporte)                       | owner + dono do pedido |

## App (lado do usuário)

- **Validação de nome**: mínimo 3 letras, rejeita números/termos genéricos, normaliza (1ª maiúscula).
- **Device ID**: UUID único gerado e guardado no localStorage (`teleprompt_device_id`); enviado em `users`, `sessions` e `errors`.
- **Chat**: botão no leitor (💬) com badge de não lidas; mensagens do dono aparecem em tempo real.
- **Suporte**: botão no leitor (✉) abre formulário de texto; o dono recebe e responde pelo chat.