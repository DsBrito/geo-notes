# Configurando Push Notifications no Quasar com Capacitor

Este guia explica como configurar e receber notificações push em um projeto **Quasar** com **Vue 3** e **Capacitor**, utilizando **Firebase Cloud Messaging (FCM)**.

## 📌 Passo 1: Instalar Dependências

Execute o seguinte comando no terminal:

```sh
pnpm add @capacitor/push-notifications
pnpm add -D @capacitor/core @capacitor/android @capacitor/ios
```

Se o Capacitor já estiver instalado, pule a instalação de `@capacitor/core`, `@capacitor/android` e `@capacitor/ios`.

## 📌 Passo 2: Configurar o Capacitor

Caso ainda não tenha inicializado o Capacitor, execute:

```sh
pnpm dlx cap init
```

Depois, adicione a plataforma desejada:

```sh
pnpm dlx cap add android
pnpm dlx cap add ios
```

E sincronize as alterações:

```sh
pnpm dlx cap sync
```

## 📌 Passo 3: Configurar no Quasar

No arquivo `quasar.conf.js` ou `quasar.config.js`, adicione a configuração do Capacitor:

```js
capacitor: {
  hideSplashscreen: true;
}
```

## 📌 Passo 4: Configurar Firebase Cloud Messaging (FCM)

1. Acesse o [Firebase Console](https://console.firebase.google.com/).
2. Crie um novo projeto (ou use um existente).
3. Vá para **Configurações do Projeto > Cloud Messaging**.
4. Baixe `google-services.json` e coloque em `android/app/`.
5. Para iOS, baixe `GoogleService-Info.plist` e adicione no Xcode.

Instale a dependência do Firebase:

```sh
pnpm add @capacitor-firebase/messaging
pnpm dlx cap sync
```

## 📌 Passo 5: Implementação no Código

Crie um arquivo `src/services/pushNotifications.js`:

```js
import { PushNotifications } from "@capacitor/push-notifications";

export const registerPush = async () => {
  try {
    const permStatus = await PushNotifications.requestPermissions();
    if (permStatus.receive !== "granted") {
      console.warn("Permissão de push negada!");
      return;
    }

    await PushNotifications.register();

    PushNotifications.addListener("registration", (token) => {
      console.log("Token de notificação:", token.value);
    });

    PushNotifications.addListener("registrationError", (err) => {
      console.error("Erro no registro de push:", err);
    });

    PushNotifications.addListener(
      "pushNotificationReceived",
      (notification) => {
        console.log("Notificação recebida:", notification);
      }
    );

    PushNotifications.addListener(
      "pushNotificationActionPerformed",
      (action) => {
        console.log("Ação da notificação:", action);
      }
    );
  } catch (error) {
    console.error("Erro ao configurar push notifications:", error);
  }
};
```

No `App.vue`, importe e chame a função `registerPush()`:

```vue
<script setup>
import { onMounted } from "vue";
import { registerPush } from "./services/pushNotifications";

onMounted(() => {
  registerPush();
});
</script>
```

## 📌 Passo 6: Testar

📱 **Para Android**

```sh
pnpm dlx cap run android
```

📱 **Para iOS**

```sh
pnpm dlx cap open ios
```

No Xcode, vá para **Signing & Capabilities** e ative **Push Notifications**.

Agora, envie uma notificação de teste pelo Firebase e verifique se ela chega no app! 🚀
