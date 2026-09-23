# AquaFlow 💧

App de controle de hidratação. Sem conta, sem servidor — tudo salvo no `localStorage` do dispositivo.

## Estrutura do projeto

```
aquaflow/
├── www/                        → app web (o que roda no celular)
│   ├── index.html
│   ├── manifest.json
│   ├── sw.js
│   └── icon.svg
├── package.json                → dependências do Capacitor
├── capacitor.config.json       → configuração do app Android
├── .github/workflows/build-apk.yml → gera o APK automaticamente
└── .gitignore
```

A pasta `android/` **não** é versionada — o GitHub Actions cria ela do zero a cada build, então você não precisa instalar Android Studio nem SDK na sua máquina.

---

## Passo a passo: gerar o APK com GitHub Actions

### 1. Testar o app localmente (opcional, mas recomendado)
Abra a pasta `www/` no VS Code → botão direito em `index.html` → **Open with Live Server**. Confirma que tudo funciona antes de mandar pro GitHub.

### 2. Criar o repositório no GitHub
No GitHub, crie um repositório novo (ex: `aquaflow`), público ou privado, sem README (você já tem um).

### 3. Subir o projeto
No terminal, dentro da pasta `aquaflow`:

```bash
git init
git add .
git commit -m "AquaFlow inicial"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/aquaflow.git
git push -u origin main
```

### 4. Deixar o GitHub Actions rodar
Assim que o `push` terminar, vá na aba **Actions** do repositório. O workflow **Build APK** começa sozinho (ele está configurado para rodar a cada push na branch `main`). Leva de 3 a 6 minutos.

Se quiser rodar manualmente sem dar push, na aba Actions clique em **Build APK → Run workflow**.

### 5. Baixar o APK gerado
Quando o workflow terminar com ✅:
1. Clique na execução (run) que terminou.
2. Role até **Artifacts**.
3. Baixe o arquivo `aquaflow-apk.zip` — dentro dele está o `app-debug.apk`.

### 6. Instalar no celular
Transfira o `app-debug.apk` para o celular (Google Drive, e-mail, cabo USB, WhatsApp Web etc.) e:
1. Toque no arquivo `.apk` no celular.
2. Se aparecer aviso de "instalar apps de fontes desconhecidas", toque em **Configurações** → ative a permissão para o app que você usou pra abrir o arquivo (Chrome, Arquivos, etc.).
3. Volte e toque em **Instalar**.

Pronto — o AquaFlow abre como app nativo, com ícone próprio, sem barra do navegador.

> É um APK de debug (não assinado para a Play Store), então é só pra rodar no seu próprio aparelho. Isso é normal e esperado nesse fluxo.

---

## Atualizando o app depois

Sempre que mudar algo em `www/index.html` (ou qualquer arquivo de `www/`):

```bash
git add .
git commit -m "ajuste no app"
git push
```

O GitHub Actions builda um novo APK automaticamente. Baixe de novo em **Actions → Artifacts**.

---

## Sobre as notificações ⚠️

O app pede permissão e dispara notificações do navegador (`Notification API`) enquanto estiver aberto/recente em segundo plano. Isso funciona bem pra testar, mas um `setInterval` em JavaScript **não é confiável com o app fechado** no Android.

Pra notificações agendadas de verdade com o app fechado, o próximo passo é trocar esse bloco (procure o comentário `NOTIFICATIONS` em `www/index.html`) pelo plugin nativo:

```bash
npm install @capacitor/local-notifications
npx cap sync
```

E então agendar os horários com a API nativa dele (`LocalNotifications.schedule(...)`), que o sistema Android mantém mesmo com o app fechado. Se quiser, eu implemento essa troca depois.

## Dados salvos (localStorage)

```
aquaflow_meta        → número (ml da meta diária)
aquaflow_registros   → { "2026-09-22": 750, ... }  (total de ml por dia)
aquaflow_config      → { notify, interval, start, end, theme }
```

## Próximos passos sugeridos

- Ícones PNG (192x192, 512x512) a partir de `icon.svg` — pode gerar em realfavicongenerator.net.
- Splash screen: `npx cap add android` já usa um padrão; para customizar, veja `@capacitor/splash-screen`.
- Assinar um APK de release, se um dia quiser distribuir além do seu aparelho.
