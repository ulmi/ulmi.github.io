---
title: "DB docs"
date: 2025-11-29
draft: false
description: "DB documenting"
tags: ["TIL", "DB", "docs"]
---

Mais um interregno, mas entre o ócio, trabalho e muita perguiça, tudo se torna mais complicado. Não por falta de ideias, mas sim por falta de foco. Apesar disso tudo hoje será um post rápido, tipo **TIL** (Today I Learned), e talvez mais para memória futura, para não ter de me "perder" em pesquisas e manuais.

A documentação de uma base de dados é algo tramado, já que não basta ter de delinear e estruturar a BD, depois ainda temos de garantir que toda a estrutura faz sentido para quem vai avaliar se o “desenho” responde ao que é pretendido antes de avançar para produção. Aliás acaba por ser também, uma ferramenta útil em _staging_, no momento em que a BD está pronta a entrar em produção e até nas fases seguintes, ou seja, é sempre bom ter!!

E, como estou precisamente a lidar com esse problema, produzir a documentação acaba por ajudar o decisor final… a decidir!

Tenho visto várias abordagens, ao ponto de ver umas, como de folhas de cálculo, com o dominios...

![](https://media1.tenor.com/m/_HmmbN60g3QAAAAd/shoot-myself-blow.gif)

sim, sim!!!

Mas queria algo mais simples, sobretudo para mim, que já estava a ficar "perdido" no desenho. 

E foi ai que me lembrei do pessoal da [3liz](https://3liz.org/), que produzem ferramentas ligadas directa ou indirectamente ao QGIS, e que normalmente têm sempre uma documentação de apoio. Já não me recordo se foi lá que encontrei o [SchemaSpy](https://schemaspy.org/), encaixou perfeitamente no que procurava: permitir que os utilizadores avaliem toda a estrutura da BD sem terem de preocupar-se com um servidor PostgreSQL, restores de dumps e outras questões que alimentam memes como este:

![](https://i-download.imgflip.com/8uf0as.gif)

## SchemaSpy

Utilizar o [SchemaSpy](https://schemaspy.org/) é realmente simples: basta fazer o download de um ficheiro .jar (logo, ter o Java instalado) e correr um comando relativamente longo, mas fácil de entender.

```bash
java -jar schemaspy.jar -t mssql05 -dp C:/sqljdbc4-3.0.jar -db DATABASE -host SERVER -port 1433 -s dbo -u USER -p PASSWORD -o DIRECTORY
```

Fazendo um breakdown, do comando que está no site, temos que:

- **-t** tipo de base de dados;
- **-dp** caminho para o driver da BD;
- **db** nome da base de dados;
- **host** ip do servidor;
- **port** porta;
- **s** schema a utilizar;
- **u** utilizador;
- **p** palavra-passe;
- **o** pasta de output.

Simples, mas...

Se queremos ter o diagrama, temos de garantir que temos, p.e. o GraphViz instalado, e garantir temos o [JDBC driver para conseguirmos ligar à BD](https://schemaspy.readthedocs.io/en/latest/installation.html#jdbc-driver).

No meu caso bastou replicar parte do comando e adaptá-lo:

```bash
java -jar schemaspy-6.2.4.jar -t pgsql -dp postgresql-42.5.4.jar -db dev2 -host localhost -port 5432 -u user -p password -schemas schema_da_db -o ./doc
```

Por alguma razão, tive de usar o **-schemas** em vez de **-s**, mas funcionou perfeitamente e foi extremamente rápido.

Com isto ficamos com acesso interativo a toda a estrutura da BD, e podemos explorá-la com muito mais clareza.

![schemaspy](img/schemaspy.png)

Outro requisito não essencial, mas _nice to have_ é termos a BD documentada, não só as tabelas, mas todos os campos também, ou seja, comentar tudo - dá trabalho, mas faz uma diferença.

## Schemacrawler

Outra alternativa é o [Schemacrawler](https://www.schemacrawler.com/), especialmente se quisermos produzir algo em .pdf ou um único ficheiro .html para facilitar a vida de quem recebe.

Correr o Schemacrawler foi um pouco mais trabalhoso do que o SchemaSpy, mas com Docker/Podman facilita — embora não fique num simples oneliner.

```bash
podman run --network=host -v $(pwd):/home/schcrwlr/share:Z \
--name schemacrawler \
-u $(id -u):$(id -g) \
--rm -i -t \
--entrypoint=/bin/bash \
schemacrawler/schemacrawler
```

O comando acima pode ser usado as is, já que o mais importante é o **pwd** para mapear a pasta actual e o **-u $(id -u):$(id -g)** para evitar problemas de permissões.

Depois, dentro do container, definimos o que queremos — neste caso, **TUDO**.

```bash
schemacrawler \
--server=postgresql  \
--host=localhost   \
--port=5432   \
--database=dbname   \
--schemas=schemaname   \
--user=user   \
--password=password   \
--info-level=maximum   \
--command=details   \
--output-format=htmlx   \
--output-file=/home/schcrwlr/share/database_report.html
```

E com isto obtemos algo muito completo.

![schemacrawler](img/schemacrawler.png)


Se quisermos um PDF, basta passar pelo _wkhtmltopdf_

```bash
wkhtmltopdf -s A0 database_report.html database_report.pdf
```

Outra ferramenta muito útil, mas nas fases iniciais é o [chartDB](https://chartdb.io/).
