---
title: "Então, Blowfish hmmm!!! Porque?"
date: 2024-10-28
draft: false
summary: "Bem, foi uma decisão bastante simples, depois de o ter encontrado, é claro, e resumiu-se a algumas das caraterísticas, mas principalmente..."
description: "Bem, foi uma decisão bastante simples, depois de o ter encontrado, é claro, e resumiu-se a algumas das caraterísticas, mas principalmente..."
categories: ["Open-Source", "Blowfish"]
tags: ["tutorial", "blowfish", "hugo"]
---
Então, [Blowfish](https://blowfish.page/) hmmm!!! Porque?

{{< github repo="nunocoracao/blowfish" >}}

## Blowfish

Bem, foi uma decisão bastante simples, depois de o ter encontrado, é claro, e que se resumiu a algumas das caraterísticas, mas principalmente porque:

* bonito;
* suporte a markdown;
* Facilmente atualizável;
* bonito e com bom aspeto;
* multi-linguagem;
* e também era bonito e tinha bom aspeto.

No final do ano passado e no início deste, estava a pensar, *sim, sou uma espécie de overthinker ou um late bloomer quando se trata de execução, porque fico preso na parte do pensar*, em criar uma nova página pessoal / blog. Isto porque, naquela altura, havia muita coisa a acontecer na minha vida e, por muita coisa, quero dizer que estava a pensar em deixar o meu trabalho diário, concentrar-me apenas na empresa que estava numa nova fase com um novo investidor estrangeiro que se juntou a nós. Estava tudo muito agitado e pensei...

*“Devia escrever, isso iria permitir-me concentrar-me apenas numa coisa durante um breve período tempo durante o dia... escrever sobre o que estava a acontecer e também exorcizá-lo colocando-o em algo!”*

Parecia um ótimo plano, mas não o executei.

## Alternativas
Na altura, também estava a trabalhar muito com o LIDAR, tentando processar nuvens de pontos, para extrair métricas florestais, e estava a utilizar o R para isso.

![](img/hmean.png)
![](img/Rplot03.png)


Então tropecei no [Quarto](https://quarto.org/), e pareceu-me muito fixe, muito hype em volta dele, parecia mais simples do que o [blogdown](https://bookdown.org/yihui/blogdown/). Por isso, dediquei-me um pouco a ele usando o [R-Studio](https://posit.co/products/open-source/rstudio/), mas não tive tempo nem paciência de o tornar bonito.

Também estive a ver o [Hugo](https://gohugo.io/), o [Astro](https://astro.build/) e outros. E foi quando olhei para o Hugo é que descobri o Blowfish, criado por um português, [Nuno Coração](https://n9o.xyz/about/). E tinha tudo a ver comingo, ferramentas de linha de comandos, *tenho um fraquinho por ferramentas cli*, para facilitar a gestão do mesmo...

...😳😯😲😮😍 OMG tem um peixe-balão de 8 bits na linha de comando!!!
c
![](img/blowfish_cli.png)

## Portanto, uma espécie de guia rápido

### Requisitos
+ [node](https://nodejs.org/en)
+ [latest hugo](https://gohugo.io/installation/)
+ [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

Caso estejas no Fedora, como eu, é só:
  ```bash
  sudo dnf install nodejs hugo git -y
  ```
Esta é uma das vantagens do Fedora, pacotes actualizados. 

### Depois...
...colocar isto na linha de comandos
```bash
npx blowfish-tools
```

Selecionar *"Setup a new website with Blowfish ”*, e definir a pasta pretendida. Depois disso, é só começar a configurá-lo, mexer um pouco, é sempre a melhor forma de aprender, é mesmo por tentativa e erro. Podes testar e ver o resultado das tuas experiências no browser, em *localhost:1313*.

### Deploying it 

Utilizei o Github e, para garantir que era corretamente implementado, criei uma action/workflow no repositório, como esta.

```yml
name: github pages

on:
  push:
    branches:
      - main  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.136.5'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```
## E é isto...

...e agora só preciso de me preocupar em escrever o mais frequentemente possível e fazer push do commit para o github.


## Down the rabbit hole

Alguns links úteis para começar, se precisares de saber mais ou quiseres criar a tua página.

- https://n9o.xyz/posts/202310-blowfish-tutorial/
- https://blowfish.page/docs/
- https://blowfish.page/docs/configuration/
- https://blowfish.page/docs/shortcodes/
- https://blowfish.page/examples/
