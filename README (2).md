# 🏆 Oscar Database — Análise de Dados com SQL

Repositório dedicado à resolução dos exercícios de SQL (Níveis 1 a 7) utilizando a base histórica de indicados e vencedores do **Oscar**.

O projeto abrange consultas fundamentais (`SELECT`, `WHERE`, `DISTINCT`), funções de agregação (`COUNT`, `MIN`, `MAX`, `GROUP BY`) e comandos de manipulação de estrutura e dados (`ALTER TABLE`, `UPDATE`, `INSERT`, `DELETE`).

---

## 🗄️ Estrutura do Banco de Dados

- **Banco de Dados:** `oscar_database`
- **Tabela Principal:** `indicados_ao_oscar`

| Coluna | Tipo | Descrição |
| :--- | :--- | :--- |
| `id_registro` | `INT` (PK) | Identificador único do registro |
| `ano_filmagem` | `INT` | Ano de elegibilidade do filme |
| `ano_cerimonia` | `INT` | Ano de realização da cerimônia |
| `cerimonia` | `INT` | Número da edição do Oscar |
| `categoria` | `VARCHAR(100)` | Categoria da premiação |
| `nome_indicado` | `VARCHAR(255)` | Nome do artista, equipe ou produtor indicado |
| `nome_do_filme` | `VARCHAR(255)` | Título do filme concorrente |
| `vencedor` | `VARCHAR(10)` / `BOOLEAN` | Indica se venceu (`true`) ou foi apenas indicado (`false`) |

---

## 🛠️ Tecnologias Utilizadas

- **SGBD:** MySQL
- **Ferramenta:** MySQL Workbench
- **Versionamento:** Git & GitHub

---

## 📊 Resoluções dos Exercícios (Níveis 1 a 7)

### Nível 1: Primeiros Passos

**1.1 Quantos registros existem na tabela de indicados ao Oscar?**

R: `[Executar no Workbench]` registros.

```sql
SELECT COUNT(*) AS total_registros 
FROM indicados_ao_oscar;
```

**1.2.1 Quais são as diferentes categorias de premiação que existem no banco de dados? Liste todas as categorias únicas.**

R:
```text
ACTOR
ACTOR IN A LEADING ROLE
ACTOR IN A SUPPORTING ROLE
ACTRESS
ACTRESS IN A LEADING ROLE
...
```

```sql
SELECT DISTINCT categoria 
FROM indicados_ao_oscar
ORDER BY categoria ASC;
```

**1.2.2 Quantas categorias únicas existem?**

R: `[Executar no Workbench]` categorias.

```sql
SELECT COUNT(DISTINCT categoria) AS total_categorias 
FROM indicados_ao_oscar;
```

**1.3 Qual foi o primeiro ano de cerimônia do Oscar registrado na base?**

R: 1929 (referente aos filmes de 1927/1928).

```sql
SELECT MIN(ano_cerimonia) AS primeiro_ano 
FROM indicados_ao_oscar;
```

**1.4 Qual foi o último ano de cerimônia registrado na base?**

R: `[Executar no Workbench]`

```sql
SELECT MAX(ano_cerimonia) AS ultimo_ano 
FROM indicados_ao_oscar;
```

**1.5 Quantas cerimônias do Oscar estão registradas no total?**

R: `[Executar no Workbench]` cerimônias.

```sql
SELECT COUNT(DISTINCT cerimonia) AS total_cerimonias 
FROM indicados_ao_oscar;
```

**1.6 Atualize os registros da tabela com os dados do Oscar 2025 e 2026 (pesquise os vencedores e adicione-os).**

R: Principais vencedores do 97º Oscar (2025) e 98º Oscar (2026) inseridos na base.

```sql
INSERT INTO indicados_ao_oscar (id_registro, ano_filmagem, ano_cerimonia, cerimonia, categoria, nome_indicado, nome_do_filme, vencedor)
VALUES
-- Oscar 2025 (97ª Cerimônia)
(99901, 2024, 2025, 97, 'BEST PICTURE', 'Alex Coco, Samantha Quan, Sean Baker', 'Anora', 'true'),
(99902, 2024, 2025, 97, 'DIRECTING', 'Sean Baker', 'Anora', 'true'),
(99903, 2024, 2025, 97, 'ACTOR IN A LEADING ROLE', 'Adrien Brody', 'The Brutalist', 'true'),
(99904, 2024, 2025, 97, 'ACTRESS IN A LEADING ROLE', 'Mikey Madison', 'Anora', 'true'),
(99905, 2024, 2025, 97, 'INTERNATIONAL FEATURE FILM', 'Walter Salles', 'I''m Still Here (Ainda Estou Aqui)', 'true'),
-- Oscar 2026 (98ª Cerimônia)
(99906, 2025, 2026, 98, 'BEST PICTURE', 'Produção', 'One Battle After Another', 'true'),
(99907, 2025, 2026, 98, 'DIRECTING', 'Paul Thomas Anderson', 'One Battle After Another', 'true'),
(99908, 2025, 2026, 98, 'ACTOR IN A LEADING ROLE', 'Timothée Chalamet', 'Marty Supreme', 'true'),
(99909, 2025, 2026, 98, 'ACTRESS IN A LEADING ROLE', 'Jessie Buckley', 'Hamnet', 'true');
```

---

### Nível 2: Explorando Categorias

**2.1 Quantas indicações existem para cada categoria? Agrupe por categoria e ordene da mais frequente para a menos frequente.**

R: Lista agrupada ordenada de forma decrescente pelo total de indicações.

```sql
SELECT categoria, COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
GROUP BY categoria
ORDER BY total_indicacoes DESC;
```

**2.2 Qual categoria teve mais indicações ao longo da história do Oscar?**

R: `[Executar no Workbench]`

```sql
SELECT categoria, COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
GROUP BY categoria
ORDER BY total_indicacoes DESC
LIMIT 1;
```

**2.3 Qual categoria teve menos indicações ao longo da história?**

R: `[Executar no Workbench]`

```sql
SELECT categoria, COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
GROUP BY categoria
ORDER BY total_indicacoes ASC
LIMIT 1;
```

**2.4 A partir de que ano a categoria "ACTRESS" deixou de existir?**

R: A última cerimônia com o nome exato `"ACTRESS"` foi em 1976 (48º Oscar); a partir de 1977 passou a se chamar `"ACTRESS IN A LEADING ROLE"`.

```sql
SELECT MAX(ano_cerimonia) AS ultimo_ano_actress
FROM indicados_ao_oscar
WHERE categoria = 'ACTRESS';
```

**2.5 Quais categorias existiam na primeira cerimônia (1928) e não existem mais hoje?**

R: Categorias antigas como `UNIQUE AND ARTISTIC PICTURE`, `WRITING (Title Writing)`, `ENGINEERING EFFECTS`, `DIRECTING (Comedy Picture)` e `DIRECTING (Dramatic Picture)`.

```sql
SELECT DISTINCT categoria
FROM indicados_ao_oscar
WHERE ano_filmagem = 1928
  AND categoria NOT IN (
      SELECT DISTINCT categoria
      FROM indicados_ao_oscar
      WHERE ano_cerimonia = (SELECT MAX(ano_cerimonia) FROM indicados_ao_oscar)
  );
```

**2.6 Liste todas as categorias que contêm a palavra "DIRECTING" no nome.**

R: `DIRECTING`, `DIRECTING (Comedy Picture)`, `DIRECTING (Dramatic Picture)`.

```sql
SELECT DISTINCT categoria
FROM indicados_ao_oscar
WHERE categoria LIKE '%DIRECTING%';
```

---

### Nível 3: Atores e Atrizes Famosos

#### Natalie Portman

**3.1 Quantas vezes Natalie Portman foi indicada ao Oscar?**

R: 3 vezes.

```sql
SELECT COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Natalie Portman%';
```

**3.2 Quantos Oscars Natalie Portman ganhou?**

R: 1 Oscar.

```sql
SELECT COUNT(*) AS total_vitorias
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Natalie Portman%'
  AND vencedor = 'true';
```

**3.3 Em quais anos e por quais filmes Natalie Portman foi indicada?**

R: 2005 (*Closer*), 2011 (*Black Swan*) e 2017 (*Jackie*).

```sql
SELECT ano_cerimonia, nome_do_filme
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Natalie Portman%';
```

**3.4 Liste todas as indicações de Natalie Portman mostrando: ano, categoria, filme e se venceu.**

R: Tabela detalhada com todas as participações da atriz.

```sql
SELECT ano_cerimonia, categoria, nome_do_filme, vencedor
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Natalie Portman%';
```

#### Viola Davis

**3.5 Quantas vezes Viola Davis foi indicada ao Oscar?**

R: 4 vezes.

```sql
SELECT COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Viola Davis%';
```

**3.6 Quantos Oscars Viola Davis ganhou?**

R: 1 Oscar.

```sql
SELECT COUNT(*) AS total_vitorias
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Viola Davis%'
  AND vencedor = 'true';
```

**3.7 Por quais filmes Viola Davis foi indicada?**

R: *Doubt* (2009), *The Help* (2012), *Fences* (2017) e *Ma Rainey's Black Bottom* (2021).

```sql
SELECT ano_cerimonia, nome_do_filme, vencedor
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Viola Davis%';
```

#### Amy Adams

**3.8 Amy Adams já ganhou algum Oscar?**

R: Não (0 vitórias).

```sql
SELECT COUNT(*) AS total_vitorias
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Amy Adams%'
  AND vencedor = 'true';
```

**3.9 Quantas vezes Amy Adams foi indicada sem ganhar?**

R: 6 vezes (*Junebug*, *Doubt*, *The Fighter*, *The Master*, *American Hustle* e *Vice*).

```sql
SELECT COUNT(*) AS indicacoes_sem_vitoria
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Amy Adams%'
  AND vencedor = 'false';
```

#### Denzel Washington

**3.10 Denzel Washington já ganhou algum Oscar?**

R: Sim (2 vitórias).

```sql
SELECT COUNT(*) AS total_vitorias
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Denzel Washington%'
  AND vencedor = 'true';
```

**3.11 Quantas vezes Denzel Washington foi indicado ao Oscar?**

R: 9 vezes (como ator) / 10 vezes no total (incluindo produção em *Fences*).

```sql
SELECT COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Denzel Washington%';
```

**3.12 Liste todos os Oscars que Denzel Washington ganhou (ano, categoria, filme).**

R: 1990 (*Glory* — Ator Coadjuvante) e 2002 (*Training Day* — Melhor Ator).

```sql
SELECT ano_cerimonia, categoria, nome_do_filme
FROM indicados_ao_oscar
WHERE nome_indicado LIKE '%Denzel Washington%'
  AND vencedor = 'true';
```

---

### Nível 4: Vencedores Históricos

**4.1 Quem ganhou o primeiro Oscar para Melhor Atriz (ACTRESS)? Em que ano e por qual filme?**

R: Janet Gaynor, em 1929, pelos filmes *7th Heaven*, *Street Angel* e *Sunrise*.

```sql
SELECT nome_indicado, ano_cerimonia, nome_do_filme
FROM indicados_ao_oscar
WHERE categoria = 'ACTRESS'
  AND vencedor = 'true'
ORDER BY ano_cerimonia ASC
LIMIT 1;
```

**4.2 Quem ganhou o primeiro Oscar para Melhor Ator (ACTOR)? Em que ano e por qual filme?**

R: Emil Jannings, em 1929, pelos filmes *The Last Command* e *The Way of All Flesh*.

```sql
SELECT nome_indicado, ano_cerimonia, nome_do_filme
FROM indicados_ao_oscar
WHERE categoria = 'ACTOR'
  AND vencedor = 'true'
ORDER BY ano_cerimonia ASC
LIMIT 1;
```

**4.3 Quantos vencedores existem ao todo na base de dados?**

R: `[Executar no Workbench]` registros vencedores.

```sql
SELECT COUNT(*) AS total_vencedores
FROM indicados_ao_oscar
WHERE vencedor = 'true';
```

**4.4 Liste todos os filmes que ganharam o Oscar de Melhor Filme (categoria "OUTSTANDING PICTURE" ou "BEST PICTURE").**

R: Lista cronológica de vencedores da categoria principal.

```sql
SELECT ano_cerimonia, nome_do_filme, nome_indicado
FROM indicados_ao_oscar
WHERE categoria IN ('OUTSTANDING PICTURE', 'BEST PICTURE')
  AND vencedor = 'true'
ORDER BY ano_cerimonia ASC;
```

**4.5 Quantos filmes diferentes já ganharam o Oscar?**

R: `[Executar no Workbench]` filmes únicos.

```sql
SELECT COUNT(DISTINCT nome_do_filme) AS filmes_vencedores
FROM indicados_ao_oscar
WHERE vencedor = 'true'
  AND nome_do_filme IS NOT NULL;
```

---

### Nível 5: Análise de Indicações

**5.1 Quais atores/atrizes foram indicados mais de uma vez? Liste o nome e o número de indicações.**

R: Lista de intérpretes com 2 ou mais indicações nas categorias de atuação.

```sql
SELECT nome_indicado, COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
WHERE categoria IN (
    'ACTOR', 'ACTRESS',
    'ACTOR IN A LEADING ROLE', 'ACTRESS IN A LEADING ROLE',
    'ACTOR IN A SUPPORTING ROLE', 'ACTRESS IN A SUPPORTING ROLE'
)
GROUP BY nome_indicado
HAVING COUNT(*) > 1
ORDER BY total_indicacoes DESC;
```

**5.2 Qual ator ou atriz tem o maior número de indicações na história do Oscar?**

R: Meryl Streep (21 indicações).

```sql
SELECT nome_indicado, COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
WHERE categoria IN (
    'ACTOR', 'ACTRESS',
    'ACTOR IN A LEADING ROLE', 'ACTRESS IN A LEADING ROLE',
    'ACTOR IN A SUPPORTING ROLE', 'ACTRESS IN A SUPPORTING ROLE'
)
GROUP BY nome_indicado
ORDER BY total_indicacoes DESC
LIMIT 1;
```

**5.3 Quais atores foram indicados mais de 3 vezes, mas nunca ganharam?**

R: Artistas como Glenn Close, Peter O'Toole, Amy Adams, Richard Burton, Deborah Kerr, etc.

```sql
SELECT nome_indicado, COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
WHERE categoria IN (
    'ACTOR', 'ACTRESS',
    'ACTOR IN A LEADING ROLE', 'ACTRESS IN A LEADING ROLE',
    'ACTOR IN A SUPPORTING ROLE', 'ACTRESS IN A SUPPORTING ROLE'
)
GROUP BY nome_indicado
HAVING COUNT(*) > 3
   AND SUM(CASE WHEN vencedor = 'true' THEN 1 ELSE 0 END) = 0
ORDER BY total_indicacoes DESC;
```

**5.4 Encontre todos os artistas que foram indicados em categorias diferentes (ex: ator e diretor).**

R: Lista de profissionais multifuncionais (ex: Clint Eastwood, Woody Allen, Bradley Cooper, Kenneth Branagh).

```sql
SELECT nome_indicado, COUNT(DISTINCT categoria) AS categorias_diferentes
FROM indicados_ao_oscar
GROUP BY nome_indicado
HAVING COUNT(DISTINCT categoria) > 1
ORDER BY categorias_diferentes DESC;
```

**5.5 Quantos indicados têm exatamente 1 indicação na história?**

R: `[Executar no Workbench]` indicados.

```sql
SELECT COUNT(*) AS indicados_unicos
FROM (
    SELECT nome_indicado
    FROM indicados_ao_oscar
    GROUP BY nome_indicado
    HAVING COUNT(*) = 1
) AS sub;
```

**5.6 Qual o maior número de indicados em um único ano?**

R: `[Executar no Workbench]`

```sql
SELECT ano_cerimonia, COUNT(*) AS total_indicados
FROM indicados_ao_oscar
GROUP BY ano_cerimonia
ORDER BY total_indicados DESC
LIMIT 1;
```

---

### Nível 6: Análise de Filmes

#### Toy Story

**6.1 A série de filmes Toy Story ganhou Oscars em quais anos?**

R: 2011 (*Toy Story 3* — Melhor Animação e Melhor Canção Original) e 2020 (*Toy Story 4* — Melhor Animação). *(Nota: o primeiro Toy Story recebeu um Oscar Especial em 1996).*

```sql
SELECT ano_cerimonia, nome_do_filme, categoria
FROM indicados_ao_oscar
WHERE nome_do_filme LIKE '%Toy Story%'
  AND vencedor = 'true';
```

**6.2 Quantas indicações a franquia Toy Story recebeu no total?**

R: 11 indicações competitivas ao longo dos quatro filmes.

```sql
SELECT COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
WHERE nome_do_filme LIKE '%Toy Story%';
```

**6.3 Em quais categorias os filmes Toy Story foram indicados?**

R: Roteiro Original, Trilha Sonora, Canção Original, Melhor Animação, Melhor Filme, Roteiro Adaptado e Edição de Som.

```sql
SELECT DISTINCT categoria
FROM indicados_ao_oscar
WHERE nome_do_filme LIKE '%Toy Story%';
```

#### Crash

**6.4 Em qual edição do Oscar o filme "Crash" concorreu?**

R: 78ª Cerimônia (ano de 2006).

```sql
SELECT DISTINCT cerimonia, ano_cerimonia
FROM indicados_ao_oscar
WHERE nome_do_filme = 'Crash';
```

**6.5 Quantas indicações o filme "Crash" recebeu?**

R: 6 indicações.

```sql
SELECT COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
WHERE nome_do_filme = 'Crash';
```

**6.6 "Crash" ganhou o Oscar de Melhor Filme?**

R: Sim (`true`).

```sql
SELECT vencedor
FROM indicados_ao_oscar
WHERE nome_do_filme = 'Crash'
  AND categoria = 'BEST PICTURE';
```

#### Central do Brasil

**6.7 O filme "Central do Brasil" aparece no banco de dados?**

R: Sim, registrado pelo seu título em inglês: `Central Station` (1999).

```sql
SELECT *
FROM indicados_ao_oscar
WHERE nome_do_filme LIKE '%Central Station%'
   OR nome_do_filme LIKE '%Central do Brasil%';
```

**6.8 Se sim, quantas indicações "Central do Brasil" recebeu?**

R: 2 indicações (Melhor Atriz para Fernanda Montenegro e Melhor Filme Estrangeiro).

```sql
SELECT COUNT(*) AS total_indicacoes
FROM indicados_ao_oscar
WHERE nome_do_filme LIKE '%Central Station%'
   OR nome_do_filme LIKE '%Central do Brasil%';
```

---

### Nível 7: Operações de Atualização

*(Nota: Caso o `SQL_SAFE_UPDATES` esteja ativo no MySQL Workbench, execute `SET SQL_SAFE_UPDATES = 0;` antes dos comandos `UPDATE` e `DELETE` abaixo).*

**7.1 No campo "vencedor", altere todos os valores "true" (string) para true (booleano) e "false" (string) para false (booleano).**

```sql
-- 1. Converte os textos para '1' e '0' antes de mudar o tipo da coluna
UPDATE indicados_ao_oscar
SET vencedor = CASE 
    WHEN vencedor = 'true' THEN '1'
    WHEN vencedor = 'false' THEN '0'
END;

-- 2. Altera o tipo da coluna para BOOLEAN (TINYINT(1) no MySQL)
ALTER TABLE indicados_ao_oscar
MODIFY COLUMN vencedor BOOLEAN;
```

**7.2 Inclua no banco 3 filmes que nunca foram nomeados ao Oscar, mas que você acha que merecem. Use dados fictícios, mas realistas.**

```sql
INSERT INTO indicados_ao_oscar (id_registro, ano_filmagem, ano_cerimonia, cerimonia, categoria, nome_indicado, nome_do_filme, vencedor)
VALUES
(99910, 2002, 2003, 75, 'BEST PICTURE', 'Nadine Labaki, Andrea Barata Ribeiro', 'O Auto da Compadecida', FALSE),
(99911, 2014, 2015, 87, 'ACTOR IN A LEADING ROLE', 'Jake Gyllenhaal', 'Nightcrawler (O Abutre)', FALSE),
(99912, 2018, 2019, 91, 'ACTRESS IN A LEADING ROLE', 'Toni Collette', 'Hereditary (Hereditário)', FALSE);
```

**7.3 Adicione uma nova categoria chamada "BEST INTERNATIONAL FEATURE FILM" com alguns vencedores recentes (2020-2024).**

```sql
INSERT INTO indicados_ao_oscar (id_registro, ano_filmagem, ano_cerimonia, cerimonia, categoria, nome_indicado, nome_do_filme, vencedor)
VALUES
(99920, 2019, 2020, 92, 'BEST INTERNATIONAL FEATURE FILM', 'Bong Joon-ho (Coreia do Sul)', 'Parasite', TRUE),
(99921, 2020, 2021, 93, 'BEST INTERNATIONAL FEATURE FILM', 'Thomas Vinterberg (Dinamarca)', 'Another Round', TRUE),
(99922, 2021, 2022, 94, 'BEST INTERNATIONAL FEATURE FILM', 'Ryusuke Hamaguchi (Japão)', 'Drive My Car', TRUE),
(99923, 2022, 2023, 95, 'BEST INTERNATIONAL FEATURE FILM', 'Edward Berger (Alemanha)', 'All Quiet on the Western Front', TRUE),
(99924, 2023, 2024, 96, 'BEST INTERNATIONAL FEATURE FILM', 'Jonathan Glazer (Reino Unido)', 'The Zone of Interest', TRUE);
```

**7.4 Corrija possíveis erros de digitação nos nomes dos filmes (ex: espaços extras, caracteres especiais desnecessários).**

```sql
UPDATE indicados_ao_oscar
SET nome_do_filme = TRIM(REPLACE(nome_do_filme, '  ', ' '))
WHERE nome_do_filme IS NOT NULL;
```

**7.5 Remova todos os registros com valor NULL no campo nome_do_filme.**

```sql
DELETE FROM indicados_ao_oscar
WHERE nome_do_filme IS NULL;
```
