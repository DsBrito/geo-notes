# 📊 Queries SQL — Referência e Exemplos

Este documento reúne consultas SQL úteis utilizadas para gerenciamento de contas e relatórios estatísticos no sistema. Abaixo estão exemplos de inserção de membros em contas e geração de relatórios agregados por tipos de interação.

---

<!-- ## 👥 Adicionar Membros a Contas -->

## 🔍 Verificando usuários por nome

Consulta para localizar usuários no banco `directus_users` pelo `first_name`:

```sql
-- Dionatas
SELECT *
FROM directus_users du
WHERE du.first_name = 'Dionatas';
-- UUID: 629f1ef8-e1e4-46a9-b221-9eca294a200f (account manager)

-- Felipe
SELECT *
FROM directus_users du
WHERE du.first_name = 'Felipe';
-- UUID: 54b497ec-a919-4d79-be69-68ca5d819982
```

## 🔎 Verificar se o usuário já pertence a uma conta

```sql
SELECT *
FROM account_member a
WHERE a."user" = '54b497ec-a919-4d79-be69-68ca5d819982';

```

## ➕ Inserir novo membro em uma conta

```sql
INSERT INTO account_member (
  user_created,
  date_created,
  status,
  "user",
  account,
  role
)
VALUES (
  '75b8077c-1e38-4398-81c5-e77b353a2300', -- quem está criando
  '2023-06-28 15:41:17.239+00',
  'enabled',
  '54b497ec-a919-4d79-be69-68ca5d819982', -- Felipe
  '53de458a-4173-47a7-9c61-fca67bee126c', -- Conta
  '629f1ef8-e1e4-46a9-b221-9eca294a200f'  -- Papel (Account Manager)
);

```

## 📈 Query de Relatório Agrupado

Consulta para gerar contagem de interações com agrupamentos por grupo, subgrupo, tipo de local e tipo de veículo.

```sql
SELECT
  COUNT(it.id) AS sum_type,
  "group".title AS _group,
  subgroup.title AS _subgroup,
  pt.title AS _place_type,
  vt.title AS _vehicle_type

FROM interaction i
JOIN interaction_type it ON it.id = i.interaction_type
JOIN "group" ON "group".id = it."group"
JOIN subgroup ON subgroup.id = it.subgroup

LEFT JOIN place_responsible pr ON pr.id = i.place_description
LEFT JOIN place_type pt ON pt.id = pr.type

LEFT JOIN vehicle_responsible vr ON vr.id = i.vehicle_responsible
LEFT JOIN vehicle_type vt ON vt.id = vr.type

GROUP BY
  "group".title,
  subgroup.title,
  pt.title,
  vt.title;

```

### 🔍 O que essa query faz?

- Conta o número de interações (sum_type).

- Agrupa os dados por:

- Grupo e subgrupo da interação.

- Tipo de local responsável (se houver).

- Tipo de veículo envolvido (se houver).
