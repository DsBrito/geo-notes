# Utilitários e Padrões Comuns

Este documento reúne trechos de código e padrões úteis usados no desenvolvimento de aplicações, especialmente com foco em Vue.js, Quasar e manipulação de estilos e dados computados.

---

## 📌 Encontrar Dado Quando Não Sei (Computed)

Essa abordagem é útil quando estamos lidando com dados assíncronos ou incertos, como chamadas de API que podem retornar `null` ou `undefined`.

```ts
const lastCultivation = computed(() => {
  return lastCultivation.value && lastCultivation.value.result
    ? (lastCultivation.value.result.laminaLiquida as {
        laminaLiquida: number | null;
      })
    : null;
});
```

### 🔍 O que faz?

- Verifica se lastCultivation.value e lastCultivation.value.result existem.

- Se existirem, faz um cast para um objeto com laminaLiquida.

- Se não, retorna null.

---

## Style Function Dinâmica (:style-fn)

Essa função de estilo é usada para ajustar dinamicamente a altura de elementos com base em parâmetros como offsets e altura total da janela ou container

```ts
:style-fn="
  (o, h) => ({
    minHeight: `${h - o}px`,
    maxHeight: `${h - o}px`,
    height: `${h - o}px`,
  })
"
```

### 🔍 O que faz?

- Define altura mínima, máxima e fixa de um elemento.

- Utiliza h - o, onde:

- h: altura total (por exemplo, da janela).

- o: offset a ser subtraído (como cabeçalho, rodapé, etc).

- ✅ Útil para garantir responsividade e aproveitamento total do espaço da tela.

---

## 🔎 Encontrar Item em Lista (Find)

Busca por um elemento específico dentro de um array, como quando queremos saber se um email já está cadastrado em um store.

```ts
const hasSameEmail = authStore.users.find(
  (user) => user.email === model.value.email
);
```

### 🔍 O que faz?

- Percorre o array authStore.users.

- Retorna o primeiro usuário cujo email seja igual ao model.value.email.

- Se nenhum for encontrado, retorna undefined.
