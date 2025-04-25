# Explicação das Queries

Este documento explica as queries SQL utilizadas para consulta e inserção de dados em tabelas relacionadas a contas e interações.

## Query para Adicionar Contas

### **Consulta de Usuários no Directus**

```sql
SELECT *
FROM directus_users du
WHERE du.first_name = 'Dionatas';
```

Esta query retorna todos os dados do usuário cujo `first_name` é "Dionatas" na tabela `directus_users`.

```sql
SELECT *
FROM directus_users du
WHERE du.first_name = 'Felipe';
```

Semelhante à query anterior, mas retorna os dados do usuário "Felipe".

### **Consulta de Membros de Conta**

```sql
SELECT *
FROM account_member a
WHERE a."user" = '54b497ec-a919-4d79-be69-68ca5d819982';
```

Esta query busca todas as entradas na tabela `account_member` relacionadas ao usuário com o ID `54b497ec-a919-4d79-be69-68ca5d819982`.

### **Inserção de um Novo Membro na Conta**

```sql
INSERT INTO account_member(user_created, date_created, status, "user", account, role)
VALUES ('75b8077c-1e38-4398-81c5-e77b353a2300', '2023-06-28 15:41:17.239+00', 'enabled', '54b497ec-a919-4d79-be69-68ca5d819982', '53de458a-4173-47a7-9c61-fca67bee126c', '629f1ef8-e1e4-46a9-b221-9eca294a200f');
```

Essa query adiciona um novo membro na tabela `account_member`, especificando quem criou o registro (`user_created`), a data da criação (`date_created`), o status (`enabled`), o ID do usuário, a conta relacionada e o papel (`role`).

---

## Query para Contagem e Agrupamento de Interações

```sql
SELECT
    count(it.id) AS sum_type,
    "group".title AS _group,
    subgroup.title AS _subgroup,
    pt.title AS _place_type,
    vt.title AS _vehicle_type
FROM
    interaction i
JOIN
    interaction_type it ON it.id = i.interaction_type
JOIN
    "group" ON "group".id = it."group"
JOIN
    subgroup ON subgroup.id = it.subgroup
LEFT JOIN
    place_responsible pr ON pr.id = i.place_description
LEFT JOIN
    place_type pt ON pt.id = pr.type
LEFT JOIN
    vehicle_responsible vr ON vr.id = i.vehicle_responsible
LEFT JOIN
    vehicle_type vt ON vt.id = vr.type
GROUP BY
    "group".title,
    subgroup.title,
    pt.title,
    vt.title;
```

### **Explicação**

- Conta (`count(it.id)`) quantas interações existem para cada grupo.
- Junta (`JOIN`) diversas tabelas para trazer informações detalhadas sobre cada interação.
- Usa `LEFT JOIN` para garantir que mesmo sem dados correspondentes em algumas tabelas, os registros principais ainda apareçam.
- Agrupa (`GROUP BY`) os resultados por grupo, subgrupo, tipo de local e tipo de veículo.

---

Essas queries são úteis para gestão de usuários e análise de interações no sistema. Se precisar de ajustes ou mais explicações, fique à vontade para perguntar!
