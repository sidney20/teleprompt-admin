# TelePrompT · Painel do Dono

Painel exclusivo (protegido por login Google + regra "admin") para acompanhar o uso do app TelePrompT.

## Setup no Console Firebase (deshboard-d3f4a)

1. **Firestore Database** → Criar banco (modo produção).
2. **Authentication → Sign-in method** → ativar:
   - **Anônimo** (o app grava uso sem cadastro).
   - **Google** (você entra no painel).
3. **Firestore → Rules** → colar o conteúdo de `firestore.rules` (neste repo) e **Publicar**.
4. **Criar sua conta de admin** (é assim que o painel sabe que "você é o dono"):

   - Abra o painel publicado → "Entrar com Google".
   - Pegue seu `uid` onde? No **Authentication → Users** (coluna UID) após logar uma vez; ou no console do navegador com `firebase.auth().currentUser.uid`.
   - No **Firestore → admins** crie um documento com **id = seu UID** e conteúdo: `{ uid: "<SEU-UID>", admin: true }`.
   - Logado de novo no painel, você será reconhecido como admin e verá os dados.

   > Importante: sem esse documento em `admins`, o painel bloqueia o acesso (regra `isAdmin()`).

## O que o painel mostra (MVP)

- **Visão geral**: usuários totais, ativos hoje, gravações hoje, erros hoje + tabela de usuários.
- **Usuários**: lista completa; clicar abre ficha (aparelho, tela, RAM, erros).
- **Erros**: erros recentes (JS, câmera, gravação) com filtro.
- **Uso**: gráfico de gravações/aberturas por dia (últimos 21 dias).

## Coleções usadas pelo app

| Coleção   | Quem cria                                    | Leitura   |
|-----------|----------------------------------------------|-----------|
| `users`   | usuário anônimo (doc id = uid do Firebase)   | só admin  |
| `sessions`| usuário (abrir app, gravar)                  | só admin  |
| `errors`  | usuário (onerror, câmera, rec)               | só admin  |
| `admins`  | você, manualmente no Console                 | só admin  |
