# Configurando Push Notifications no Quasar + Vue com Capacitor e Firebase

Este guia explica como configurar notificações push no seu aplicativo Quasar + Vue usando `@capacitor/push-notifications` e Firebase Cloud Messaging (FCM).

---

## 📌 1. **Instalar Capacitor no projeto**

Se o Capacitor ainda não estiver instalado no seu projeto, execute:

```sh
pnpm add @capacitor/core @capacitor/cli
```

Agora, inicialize o Capacitor:

```sh
pnpm dlx cap init
```

Durante a inicialização, informe:

- **Nome do App:** (Exemplo: `MeuApp`)
- **ID do pacote:** (Exemplo: `com.meuapp.mobile`)

---

## 📌 2. **Adicionar a Plataforma Android**

```sh
pnpm dlx cap add android
```

Isso criará a pasta `android/` no seu projeto.

Caso já tenha a pasta e queira atualizar, use:

```sh
pnpm dlx cap update android
```

---

## 📌 3. **Instalar o plugin de Push Notifications**

```sh
pnpm add @capacitor/push-notifications
```

Depois, sincronize com o projeto Android:

```sh
pnpm dlx cap sync android
```

---

## 📌 4. **Configurar Firebase no Android**

### 🔹 Criando um projeto no Firebase

1. Acesse [Firebase Console](https://console.firebase.google.com/)
2. Clique em **Criar Projeto** e siga as instruções
3. No painel do Firebase, clique no ícone **Android**
4. Preencha os detalhes:
   - **Nome do pacote do Android** (Deve ser o mesmo definido no `capacitor.config.ts`)
   - **Apelido do App** (Opcional)
   - **SHA-1** (Recomendado para recursos avançados)
5. Clique em **Registrar App**
6. Baixe o arquivo `google-services.json`
7. Coloque o arquivo na pasta correta:
   ```
   android/app/google-services.json
   ```

### 🔹 Adicionando Dependências ao `build.gradle`

Abra o arquivo `android/build.gradle` e adicione na seção `dependencies`:

```gradle
classpath 'com.google.gms:google-services:4.3.10'
```

Agora, no arquivo `android/app/build.gradle`, adicione no final:

```gradle
apply plugin: 'com.google.gms.google-services'
```

---

## 📌 5. **Implementar o Código de Push Notifications**

Abra o seu arquivo principal (`src/boot/push.ts`) e adicione:

```ts
import { PushNotifications } from "@capacitor/push-notifications";

export function registerPush() {
  PushNotifications.requestPermissions().then((result) => {
    if (result.receive === "granted") {
      PushNotifications.register();
    }
  });

  PushNotifications.addListener("registration", (token) => {
    console.log("Token de Push:", token.value);
  });

  PushNotifications.addListener("registrationError", (error) => {
    console.error("Erro no registro de push:", error);
  });

  PushNotifications.addListener("pushNotificationReceived", (notification) => {
    console.log("Notificação Recebida:", notification);
  });

  PushNotifications.addListener(
    "pushNotificationActionPerformed",
    (notification) => {
      console.log("Ação da Notificação:", notification);
    }
  );
}
```

Agora, importe e chame essa função no `src/main.ts`:

```ts
import { registerPush } from "./boot/push";
registerPush();
```

---

## 📌 6. **Testando as Notificações Push**

1. Conecte um dispositivo Android via USB e rode:

   ```sh
   pnpm dlx cap run android
   ```

2. Vá para o [Firebase Console → Cloud Messaging](https://console.firebase.google.com/)
3. Envie uma notificação de teste usando o **Token do App**
4. Verifique se o app recebe a notificação corretamente

---

## 🎯 Conclusão

Agora seu aplicativo está configurado para receber notificações push usando Capacitor e Firebase. Se precisar de mais ajustes, confira a [documentação oficial do Capacitor](https://capacitorjs.com/docs/apis/push-notifications).
