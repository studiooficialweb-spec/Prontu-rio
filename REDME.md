# Prontuário Digital — aplicativo (PWA)

Prontuário instalável no celular: ícone na tela de início, funciona offline e avisa
por notificação quando uma nova consulta é registrada.

## Arquivos que devem existir no repositório

```
index.html            app completo (interface + lógica + botão "Instalar")
manifest.webmanifest  identidade do app (nome, ícone, cor, modo aplicativo)
sw.js                 service worker (offline + verificação de consultas)
icons/                7 PNGs: 192, 512, maskable, apple-touch, favicon, badges
README.md             este arquivo
```

**Devem ser removidos** (sobras de versões anteriores, não são usados pelo app):

```
js.js                   ← era o service worker com nome errado
webmanifest.webmanifest ← manifest com nome errado
script.js               ← não é referenciado pelo index.html
style.css               ← não é referenciado pelo index.html
*.zip                   ← não deve ficar no repositório
```

## Publicar (GitHub Pages)

1. Substitua o `index.html` pelo desta pasta;
2. Envie `manifest.webmanifest`, `sw.js`, `README.md` e a pasta `icons/` na **raiz** do repositório;
3. Apague os arquivos listados acima como "remover";
4. Settings → **Pages** → Source: *Deploy from a branch* → `main` / `/ (root)` → Save.

Depois de 1–2 minutos, confira no navegador:

- `https://SEU-USUARIO.github.io/SEU-REPO/manifest.webmanifest` → deve abrir o JSON
- `https://SEU-USUARIO.github.io/SEU-REPO/sw.js` → deve abrir o código
- `https://SEU-USUARIO.github.io/SEU-REPO/icons/icon-192.png` → deve mostrar o ícone

Se os três abrirem, o app está instalável.

## Instalar no celular

Abra o link no celular:

- **Android (Chrome):** botão **⤓ Instalar app** dentro do app, ou menu **⋮ → Instalar aplicativo**.
- **iPhone (Safari):** **Compartilhar** (quadrado com seta) → **Adicionar à Tela de Início** → **Adicionar**.
  No iPhone o botão de instalar mostra essas instruções (o Safari não tem instalação automática).

Dentro do app o botão aparece em três lugares: no topo (computador), na barra lateral
(celular) e em **Ajustes → Aplicativo e notificações**, além do banner de convite.

## Notificações de novas consultas

Ative em **Ajustes → Aplicativo e notificações → Ativar notificações** e permita no aviso
do navegador. Use **Testar notificação** para confirmar.

| Situação | Android (instalado) | iPhone (iOS 16.4+) |
|---|---|---|
| Consulta registrada neste aparelho | na hora | na hora |
| Consulta de outro aparelho, app aberto | ao sincronizar (a cada 2 min) | ao sincronizar (a cada 2 min) |
| Consulta com o app fechado | verificação em segundo plano | ao reabrir o app |

Para aviso com o app totalmente fechado em qualquer aparelho seria necessário um servidor
de push (VAPID). Hoje o app cobre os casos acima.

## Atualizar depois

1. Substitua os arquivos alterados no servidor;
2. No `sw.js`, incremente `const VERSAO = 'v1';` → `'v2'`;
3. Quem já instalou recebe a atualização na próxima abertura.

## Testar no computador

```bash
cd prontuario-pwa
python3 -m http.server 8080
# abra http://localhost:8080
```

`localhost` conta como seguro para o navegador, então service worker e notificações funcionam.

## Dados

O prontuário fica no aparelho (localStorage) e sincroniza com o Supabase. O service worker
**nunca** guarda dados clínicos em cache: as chamadas ao Supabase vão sempre para a rede.
