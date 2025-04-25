# Guia de Desenvolvimento

## 1. Mantra (Fluxo Padrão de Atualização)

Para garantir que o ambiente local esteja atualizado com a branch `develop`, siga os passos abaixo:

```bash
git checkout develop
git remote update
git pull
pnpm clean-and-build
pnpm migrate:latest
```

## 2. Merge (Mesclar Develop na Branch de Trabalho)

Para mesclar as últimas mudanças da branch `develop` na sua branch de trabalho:

1. Certifique-se de estar na sua branch de trabalho.
2. Execute o comando abaixo para mesclar `develop`:

   ```bash
   git merge develop
   ```

3. Opcionalmente, após resolver conflitos, você pode deletar a branch `develop` localmente:

   ```bash
   git branch -d develop
   ```

   **Nota:** Esse comando não cria um commit automaticamente, permitindo a revisão de conflitos antes de confirmar as alterações.

## 3. Extração de Dump do Banco de Dados

Para restaurar um dump de banco de dados PostgreSQL:

```bash
psql -U postgres -h localhost -p 5438 agrocontrol-20231204 < Downloads/agrocontrol.20231204_122201.dump
```

Caso o arquivo esteja compactado, descompacte antes de restaurar:

```bash
bzip2 -dk Downloads/agrocontrol.20231204_122201.dump.bz2
bunzip2 Downloads/agrocontrol.20231204_122201.dump.bz2
```

## 4. Criar uma Nova Branch

Para iniciar o desenvolvimento de uma nova funcionalidade ou correção de bug:

```bash
git checkout -b feature/19522
```

## 5. Liberar Porta Ocupada

Se uma porta estiver em uso e precisar ser liberada, utilize:

```bash
sudo fuser -k 3306/tcp
```

## 6. Uso do Bun no Projeto `device-gateway-project`

### 6.1 Instalação do Bun

Se o Bun ainda não estiver instalado, execute:

```bash
asdf plugin add bun
asdf install bun latest
```

### 6.2 Executar o Projeto com Bun

Se o Bun já estiver instalado, use os seguintes comandos:

```bash
bun install
bun run index.ts  # Ou
bun run src/index.ts
```

### 6.3 Rodar Testes com Bun

Para executar os testes em TypeScript com cobertura de código:

```bash
bun test --coverage
```

Para rodar um teste específico:

```bash
bun test --coverage tests/devices/queclink-gv57cg/handler/decoder.test.ts
```

Modo `watch` (reexecuta o teste ao detectar mudanças no código):

```bash
bun test --watch tests/devices/queclink-gv57cg/handler/decoder.test.ts
```

## 7. Uso do Plugin Capacitor Notifications

Para configurar e utilizar o plugin [Capacitor Notifications](https://capacitorjs.com/docs/apis/push-notifications):

### 7.1 Instalação

Execute o comando abaixo para adicionar o plugin ao projeto:

```bash
npm install @capacitor/push-notifications
```

Após a instalação, certifique-se de sincronizar o Capacitor:

```bash
npx cap sync
```

### 7.2 Solicitar Permissão e Registrar Notificações

No código do seu aplicativo, adicione a seguinte lógica para solicitar permissões e registrar notificações:

```typescript
import { PushNotifications } from "@capacitor/push-notifications";

// Solicitar permissão para notificações push
PushNotifications.requestPermissions().then((result) => {
  if (result.receive === "granted") {
    PushNotifications.register();
  }
});

// Lidar com notificações recebidas
PushNotifications.addListener("pushNotificationReceived", (notification) => {
  console.log("Notificação recebida:", notification);
});

// Lidar com notificações interagidas pelo usuário
PushNotifications.addListener(
  "pushNotificationActionPerformed",
  (notification) => {
    console.log("Ação da notificação:", notification);
  }
);
```

### 7.3 Testando as Notificações

Para testar notificações locais no iOS ou Android, utilize o seguinte código:

```typescript
import { LocalNotifications } from "@capacitor/local-notifications";

LocalNotifications.schedule({
  notifications: [
    {
      title: "Teste de Notificação",
      body: "Essa é uma notificação de teste.",
      id: 1,
      schedule: { at: new Date(Date.now() + 1000 * 5) }, // 5 segundos
    },
  ],
});
```

Com isso, o aplicativo estará preparado para enviar e receber notificações usando Capacitor Notifications.
