---
title: "DB docs"
date: 2025-11-29
draft: false
description: "DB documenting"
tags: ["TIL", "DB", "docs"]
---

Another break — between leisure, work, and a healthy dose of laziness, everything ends up more complicated. Not for lack of ideas, but for lack of focus. Still, today will be a quick post, more in the **TIL** (Today I Learned) spirit, and maybe mostly for future reference so I don’t have to get lost again in searches and manuals.

Documenting a database is a tricky business. It’s not enough to design and structure the DB — we also have to ensure that the whole structure makes sense to whoever will evaluate whether the “design” meets the requirements before it moves to production. In fact, it becomes a very useful tool during *staging*, when the DB is ready for production, and even afterwards. In other words: documentation is never a waste.

And since I’m dealing with exactly that problem now, producing proper documentation ends up helping the final decision-maker… well, decide!

I’ve seen several approaches — including some in spreadsheets, with domains and all sorts of extras…

![](https://media1.tenor.com/m/_HmmbN60g3QAAAAd/shoot-myself-blow.gif)

Yes, yes!!!

But I wanted something simpler, especially for myself, cause I was getting lost in the "design".

It was then that I remembered the folks from [3liz](https://3liz.org/), who make tools directly or indirectly connected to QGIS, and who almost always ship excellent documentation. I don’t remember if it was there that I first found [SchemaSpy](https://schemaspy.org/), but it fit exactly what I needed: letting users explore the entire DB structure without having to worry about a PostgreSQL server, restoring dumps, and other headaches that fuel memes like this:

![](https://i-download.imgflip.com/8uf0as.gif)

## SchemaSpy

Using [SchemaSpy](https://schemaspy.org/) is genuinely simple: download a `.jar` file (so yes, you need Java installed) and run a relatively long but easy-to-understand command.

```bash
java -jar schemaspy.jar -t mssql05 -dp C:/sqljdbc4-3.0.jar \
-db DATABASE -host SERVER -port 1433 -s dbo -u USER -p PASSWORD -o DIRECTORY
```

Breaking down the command from the website:

- **-t** database type  
- **-dp** path to the DB driver  
- **-db** database name  
- **-host** server IP  
- **-port** port  
- **-s** schema to use  
- **-u** username  
- **-p** password  
- **-o** output directory  

Simple enough, but…

If we want diagrams, we need to ensure we have GraphViz installed, and we also need the [JDBC driver for our DB](https://schemaspy.readthedocs.io/en/latest/installation.html#jdbc-driver).

In my case, I just adapted the command a bit:

```bash
java -jar schemaspy-6.2.4.jar -t pgsql -dp postgresql-42.5.4.jar \
-db dev2 -host localhost -port 5432 -u user -p password \
-schemas schema_da_db -o ./doc
```

For some reason, I had to use **-schemas** instead of **-s**, but it worked perfectly and was incredibly fast.

And with that, we get interactive access to the entire DB structure — much easier to explore and understand.

![schemaspy](img/schemaspy.png)

Another non-essential but *nice to have* step is documenting the DB itself: not only tables, but every field as well — comment everything. It takes time, but the payoff is huge.

## Schemacrawler

Another option is [Schemacrawler](https://www.schemacrawler.com/), especially if you want output as a `.pdf` or a single `.html` file — ideal for sending to someone who just wants “everything in one place”.

Running Schemacrawler was a bit more involved than SchemaSpy, but Docker/Podman helps — although it’s not a simple one-liner.

```bash
podman run --network=host -v $(pwd):/home/schcrwlr/share:Z \
--name schemacrawler \
-u $(id -u):$(id -g) \
--rm -i -t \
--entrypoint=/bin/bash \
schemacrawler/schemacrawler
```

The command above works *as is*, since the important part is **pwd** to map the working directory, and **-u $(id -u):$(id -g)** to avoid permission issues.

Then, inside the container, we define what we want — in this case, **EVERYTHING**:

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

And with that, we get a very complete report.

![schemacrawler](img/schemacrawler.png)

If we want a PDF version, just pass it through *wkhtmltopdf*:

```bash
wkhtmltopdf -s A0 database_report.html database_report.pdf
```

Another cool tool, but for early stages, is [chartDB](https://chartdb.io/).
