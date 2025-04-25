## BOX — Problema de Overflow / Largura

Descrição
A janeça apresentava um problema de overflow horizontal, especialmente em conteúdos com texto longo ou sem espaçamento adequado. O comportamento indesejado acontecia por conta do modo de layout padrão não conseguir conter o conteúdo de forma adequada em 100% da largura do container pai.

Solução
Foi aplicada uma estratégia de layout baseada em tabela, com table-layout: fixed, garantindo que a largura da .box respeite os limites do container e distribua o conteúdo uniformemente.

Código Corrigido

```css
<style>
.box {
  display: table;
  table-layout: fixed;
  width: 100%;
}
</style>
```

Observações
Essa abordagem é útil quando o conteúdo da .box precisa se adaptar a uma largura fixa sem ultrapassar os limites do container pai.

---
