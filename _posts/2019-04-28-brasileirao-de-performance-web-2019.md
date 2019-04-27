---
layout: post
current: post
navigation: 'True'
class: post-template
cover: /assets/images/br-perf-cover-01.png
title: 'Brasileirão de performance web 2019'
description: >-
  Uma análise da performance do site dos 20 clubes da série A do Campeonato Brasileiro de 2019.
date: '2019-04-27 15:50:00'
author: estevanmaito
category: dev
tags:
  - ''
---
Todo começo de campeonato brasileiro de futebol surgem diversas comparações entre elencos, caixa, títulos e até o momento dos clubes. Mas ninguém nunca pensou em comparar o site de cada time (não sei porquê...).

Meu objetivo, além de criar uma competição descontraída e inovadora, é mostrar erros e acertos nos sites dos times que amamos e que podem nos ajudar em nossas escolhas diárias. Se a melhor forma de aprender é errando, com o erro dos outros deve ser melhor ainda. O Jake Archibald teve essa ideia, mas [com as equipes da Fórmula 1](https://jakearchibald.com/2019/f1-perf/){:target="_blank"}, e foi minha inspiração.

Já deixo avisado que **não estou julgando o trabalho dos desenvolvedores** envolvidos com cada site nem com suas escolhas. Só quem está inserido num projeto sabe o motivo de cada escolha, os pedidos do cliente para colocar 350 plugins inúteis ou o prazo para entrega.

Por fim, dei o meu melhor possível analisando 20 sites em 5 dias. Em alguns dias foram até 7 análises. Foi só o que eu fiz. Evidentemente que algum erro pode ter ocorrido (nesse caso você pode propor a correção [no GitHub](https://github.com/estevanmaito/brasileirao-perf-2019){:target="_blank"}) ou algum item pode ter sido deixado de fora. Fiz isso por hobby, me diverti, aprendi muita coisa e espero que possa te ajudar de alguma forma também.

## TL;DR

Se você pretende ler apenas a análise de um clube ou do seu clube do coração, leia pelo menos a do Athletico, que é a primeira análise e eu expliquei melhor os meus métodos nela.

## Clubes

- [Athletico](#athletico)
- [Atlético](#atlético)
- [Avaí](#avaí)
- [Bahia](#bahia)
- [Botafogo](#botafogo)
- [Ceará](#ceará)
- [Chapecoense](#chapecoense)
- [Corinthians](#corinthians)
- [Cruzeiro](#cruzeiro)
- [CSA](#csa)
- [Flamengo](#flamengo)
- [Fluminense](#fluminense)
- [Fortaleza](#fortaleza)
- [Goiás](#goiás)
- [Grêmio](#grêmio)
- [Internacional](#internacional)
- [Palmeiras](#palmeiras)
- [Santos](#santos)
- [São Paulo](#são-paulo)
- [Vasco](#vasco)

## Especificações dos testes

A fim de tornar o ambiente de teste o mais parecido possível, utilizei o [WebPageTest](https://www.webpagetest.org){:target="_blank"} para a geração dos relatórios em uma **conexão 3G de um Moto G4** emulado. Se o resultado seria o mesmo em um Moto G4 real é outro assunto, pois o que importa é que todos os sites foram testados da mesma forma. Os relatórios de coverage e a seção de custos se baseiam em resultados do Chrome Dev Tools, executado no desktop, portanto, ainda que seja outro ambiente, todos foram analizados da mesma forma. Se houver alguma distorção, todos estão sujeitos a ela.

**Todos os testes foram executados 3 vezes** e apenas a mediana de cada um foi considerada, tanto para a primeira visita (primeiro tempo) quanto para a segunda (segundo tempo).

## Critérios

A pontuação/classificação se baseia **unicamente no tempo de carregamento da página**, calculada da seguinte forma:

Tr1 = Tempo até o carregamento na primeira visita

Tr2 = Tempo até o carregamento na segunda visita (cacheada)

Resultado = Tr1 + Tr2

Simples, **quanto menores os tempos de renderização em cada visita, menor o total, melhor a classificação do site**. Como carregamento considero a partir do momento que a página foi renderizada ou que pelos menos o conteúdo útil dela já possa ser consumido e a thread principal esteja desbloqueada.

## Itens avaliados

O resultado, medido pelo tempo até a renderização, isoladamente não ajuda a entender todo contexto e ambiente que resulta em determinado tempo de carregamento. Para ajudar, tanto o leitor quanto os desenvolvedores interessados, a melhorar os resultados, cada site será avaliado a partir dos seguintes itens, mas não exclusivamente:

<ul>
  <li>HTTPS</li>
  <li>HTTP/2</li>
  <li>Preload de fontes</li>
  <li>Gzip</li>
  <li>Minificação</li>
  <li>Compressão de imagens</li>
  <li>Renderização bloqueada por scripts</li>
  <li>Download atrasado de fontes</li>
  <li>Download atrasado de CSS</li>
  <li>Download atrasado de JS</li>
  <li>Cache</li>
  <li>CSS desnecessário</li>
  <li>JS desnecessário</li>
  <li>Fontes com baixa compressão</li>
  <li>Fontes de ícones</li>
  <li>Excesso de conexões</li>
</ul>

## Bônus

Como veremos, grande parte do peso dos sites se deve ao mau tratamento de imagens. Afim de tornar os benefícios da compressão de imagens mais óbvios, escolhi duas imagens de cada site para comprimir. As que eram maiores que 1000px foram redimensionadas, já que estamos falando de um ambiente mobile. Fiz isso usando o [Squoosh](https://squoosh.app){:target="_blank"}, uma ferramenta online para compressão.

Além disso, como estamos falando de conexão 3G, calculei o custo de acessar cada site, uma vez por dia, durante um mês, num plano de 100 MB diários, com custo de R$ 1,49, da Vivo. Por que da Vivo? Foi o valor que eu encontrei mais rapidamente acessando várias operadoras. Levei em conta também o pior cenário possível, que seria sem cache.

# Athletico

[Site](https://athletico.com.br/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190420_Y4_e2b4cfb600a34199765515afc6d33332/){:target="_blank"}.

## Primeiro tempo

Essa primeira análise vai ser um pouco mais longa que as seguintes pra que eu possa detalhar o processo de análise usado e as ferramentas. Para os demais times, vou ser mais direto, já que tudo vai estar explicado aqui.

Eu geralmente uso apenas o Chrome Dev Tools (CDT daqui em diante) e o Lighthouse pra esse tipo de análise, já que dá pra fazer tudo ali dentro, desde verificar as requisições de rede (Network), medir o uso dos recursos carregados (Coverage), identificar a execução (e possíveis gargalos) de scripts (Performance e Audits), enquanto que o Lighthouse gera um relatório bonitinho e já dá umas dicas do que pode ser melhorado além de uma estimativa de ganhos seguindo algumas práticas.

Então, pra que usar o WebPageTest? Apesar de ser possível (e até mais detalhado) fazer esse tipo de análise usando as ferramentas que eu citei acima, o WebPageTest, além de ser confiável e apresentar resultados muito próximos, gera imagens das conexões e rolos de screenshots tiradas a cada 0.5s do carregamento da página e com isso eu posso simplesmente salvar e apresentar isso pra você de uma forma muito prática. De qualquer forma, usarei algumas funcionalidades do CDT, mas vai ficar claro quando isso acontencer.

Dos testes executados acima, a mediana para a primeira visita foi a execução de número 3 e a linha do tempo da renderização é a seguinte:

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/athletico/imgs/filmstrip-first-view-run-3.png" class="lazy">
</div>

**Começamos com 9.9s de nada**. Aos 10s já é possível interagir com a página, ainda que o conteúdo só comece a aparecer aos 10.9s

<div class="expand-report">
    <img data-src="/assets/dist/images/brasileiro/athletico/imgs/first-view-run-3.png" class="lazy">
</div>

O primeiro problema aqui já é a segunda requisição. Nosso CSS principal, apesar de estar minificado, pesa, ironicamente, 525.8 KB! E piora. Ele vem do servidor desse jeito mesmo, **sem compressão**. A compressão (gzip), do lado do servidor, reduziria *em* 450 KB o tamanho desse arquivo. Mas como nada é tão ruim que não possa piorar, o relatório de coverage aponta que 94.4% desse arquivo **não é usado** na página inicial. No fim das contas, dos 525 KB iniciais, a página está consumindo de fato em torno de 5 KB (considerando compressão).

<img data-src="/assets/dist/images/brasileiro/athletico/imgs/coverage.png" class="lazy">

A solução pra isso seria ativar a compressão (gzip) no servidor e se fôssemos levar a otimização ao extremo, uma ferramenta como o [critical](https://github.com/addyosmani/critical){:target="_blank"} extrairia **só as regras necessárias para apresentar o conteúdo visível da viewport** (*above the fold*) e as aplicaria inline. Dessa forma reduziríamos uma requisição (que precisaria ser feita depois, pra completar a página, de qualquer forma) e a renderização aconteceria muito mais rapidamente.

O relatório de coverage já deu o spoiler: vários scripts sofrem do mesmo problema. O D3.js, importado direto do site da biblioteca (poderia no mínimo estar vindo de uma CDN, que já é usada para outros scrits), além de **não ser minificado** (baixa quase 70 KB enquanto poderia estar baixando 15 KB), não é usado em 90.5% de sua totalidade. A lib moment.js também é tratada da mesma forma.

Muitas vezes, esse percentual tão pequeno que é utilizado na verdade é só a inicialização e colocação de event listeners no documento, ou seja, a lib só está esperando a hora de ser chamada, mas talvez isso nem aconteça.

Pra fechar o tópico de scripts, você pode ver que eles se concentram exatamente antes das linhas verde (start render) e amarela (DOM Interactive), mais expecificamente das linhas 4 até 33. Isso não é por acaso, já que os scripts estão sendo **baixados e executados de forma síncrona**. Ou seja, estão **bloqueando a renderização**! Esse é um trecho do `head`:

```html
<script type='text/javascript' src='https://s7.addthis.com/js/300/addthis_widget.js?ver=4.9.6#pubid=wp-011be5fd79766513e808597c12d96ced'></script>
<script type='text/javascript' src='https://cdnjs.cloudflare.com/ajax/libs/jquery/3.1.0/jquery.min.js?ver=3.2.1'></script>
<script type='text/javascript' src='https://cdnjs.cloudflare.com/ajax/libs/bootstrap-3-typeahead/4.0.2/bootstrap3-typeahead.min.js'></script>
<script type='text/javascript' src='https://connect.facebook.net/pt_BR/sdk.js?ver=4.9.6'></script>
<script type='text/javascript' src='https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.3/umd/popper.min.js?ver=4.9.6'></script>
<script type='text/javascript' src='https://cdnjs.cloudflare.com/ajax/libs/summernote/0.8.8/summernote-bs4.js?ver=4.9.6'></script>
<script type='text/javascript' src='https://d3js.org/d3.v3.js?ver=4.9.6'></script>
<script type='text/javascript' src='https://cdn.jsdelivr.net/parallax.js/1.4.2/parallax.min.js?ver=4.9.6'></script>
<script type='text/javascript' src='https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.21.0/moment.js'></script>
<script type='text/javascript' src='https://cdnjs.cloudflare.com/ajax/libs/jquery.mask/1.14.15/jquery.mask.min.js?ver=4.9.6'></script>
<script type='text/javascript' src='https://athletico.com.br/wp-content/themes/hurricane/assets/js/loadmore.min.js'></script>
<script type='text/javascript' src='https://athletico.com.br/wp-includes/js/underscore.min.js?ver=1.8.3'></script>
<script type='text/javascript' src='https://athletico.com.br/wp-includes/js/backbone.min.js?ver=1.2.3'></script>
```

Esses scripts poderiam se beneficiar do atributo `defer`, que permite que eles continuem sendo **baixados com antecedência, mas evita que eles bloqueiem a renderização**. Dessa forma, a página renderiza muito antes, mas pode haver problemas em componentes que dependam de algum destes scripts. Na minha opinião, mais da metade disso tudo aí não faria falta.

Não bastasse isso, no fim do documento (o famoso "antes do fim do `body`"), tem mais uma leva de quase 10 recursos, entre CSS e JS. Eles começam a ser **baixados com 8.5s de atraso**. É bem fácil de identificar esse problema pelos "degraus" que ele cria:

<img data-src="/assets/dist/images/brasileiro/athletico/imgs/degrau.png" class="lazy">

A partir da linha 26 estão os scripts que o navegador descobriu só depois de ter passado por todo documento, e aí ele volta a bloquear a renderização e baixa tudo.

Mais uma vez, esses scripts poderiam se beneficiar do atributo `defer`, além de serem posicionados no `head`.

Esse trecho do degrau aí em cima traz ainda dois detalhes que eu não posso deixar de citar. O primeiro está na linha 25. Se você olhou com mais calma o relatório de coverage, viu na penúltima linha o arquivo `font-awesome.min.css`, com 99% de conteúdo não utilizado. Para o leitor não familiarizado, essa é uma **fonte de ícones**, usado aqui para os ícones: arrow-right, arrow-left e play. 9 linhas usadas de 2929 (e ainda tem o arquivo `.woff`, da fonte em si, com 75 KB). E o que me deixa confuso é que você pode ver que o site já faz download de ícones em png, como pode ser visto abaixo:

<img data-src="/assets/dist/images/brasileiro/athletico/imgs/icones.png" class="lazy">

Pra fechar o primeiro tempo (já estouramos os 13 minutos de acréscimo dado), da linha 37 em diante temos o download das fontes usadas no site. Esse download atrasado, depois de tudo, acontece porque **o navegador só sabe das fontes usadas no documento depois que todo CSS foi analisado** (CSSDOM pronto) e adivinha: nós temos aqui um arquivo CSS de +500 KB para baixar e depois analisar. Isso resulta nas nossas fontes sendo baixadas a partir dos 9.7s. Para reduzir esse impacto, pode-se pré-carregar as fontes usadas no site com o atributo `rel=preload` nos `links`. Você pode ler mais a respeito do `preload` nesse [trecho bem curto de um artigo do Google](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/webfont-optimization?hl=pt#pre-carregar_seus_recursos_de_webfont){:target="_blank"}, mas ficaria algo parecido com isso:

```html
<link rel="preload" as="font" href="...">
```

Isso já faria o browser baixar as fontes e só esperar pra ver onde colocar cada uma. Usar o formato `woff2` ainda traria [reduções de até 30%](https://groups.google.com/a/chromium.org/forum/#!topic/chromium-dev/j27Ou4RtvQI/discussion){:target="_blank"} em relação ao formato `woff` usado em algumas fontes aqui.

## Segundo tempo

A segunda visita parece com isso:

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/athletico/imgs/filmstrip-second-view-run-3.png" class="lazy">
</div>

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/athletico/imgs/second-view-run-3.png" class="lazy">
</div>

O site está com o cache funcionando perfeitamente, mas ainda assim demora **3.5s mostrando nada**, e quando mostra, fica travado (com um breve intervalo) até **6.5s** (barra vermelha - Page is Interactive - na última linha do gráfico acima). Isso acontece pois todos os scripts já estão prontos, então eles executam de uma vez só. As soluções que propus acima, carregar só os scripts necessários e usar `defer` onde possível, permitiriam renderizar antes do JS.

Permitir um mínimo de cache, algo como 5 minutos na página inicial, removeria esses 1751 ms de atraso também e não comprometeria muito as atualizações do site.

## Custo

De um total de 13.8 MB transferidos, 11.7 MB são imagens. Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 6,17 por mês, ou 4 dias de internet.

## Imagens

Falamos muito sobre scripts, CSS, bloqueio de renderização entre outras práticas, mas se você reparou no primeiro relatório de carregamento, notou que a página leva praticamente 90s para carregar completamente e que praticamente 100 requisições a partir da linha 52 são imagens.

**Nenhuma imagem passou por compressão**, entre as servidas pelo Athletico. Entre as que foram comprimidas, estão as **trinta e quatro** de um carousel do Instagram.

Uma das imagens que servem de fundo ao slider principal e que tem a largura total da tela pesa 1.17 MB e tem 2880x1200px. Quanto às dimensões, uma solução rápida seria servir uma imagem adequada ao mobile, com no máximo 1000px de largura. A solução ideal mesmo seria usar `srcset` e `sizes` no `img` ou `picture`s. Quanto ao peso, **reduzi o JPEG original de 1.17 MB para 44 KB (96% menor) em JPEG ou 25.8 KB em WebP**.

Em outra imagem, **reduzi o PNG original de 996 KB para 43.5 KB (96% menor) em JPEG ou 24.9 KB em WebP**.

Você pode [conferir o resultado](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/athletico/imgs/squoosh){:target="_blank"} dessas imagens e me dizer se dá pra notar diferença.

Fica MUITO claro o benefício da compressão de imagens, mas isso não é tudo. Reduzir o tamanho das imagens ajuda bastante, mas você não precisa carregar a imagem se o usuário não está vendo ela 🧐

Atrasar o carregamento de imagens até que elas entrem no viewport (*image lazy loading*) deveria ser sempre a primeira opção. Isso pode ser alcançado usando uma lib como o [Echo](https://github.com/toddmotto/echo){:target="_blank"} (já usei e ainda uso) ou o [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API){:target="_blank"} que é nativo do navegador, mas ainda não tem tanta [implementação](https://caniuse.com/#search=intersection){:target="_blank"}.

## Resultado

1. Athletico - 17.4s

Graças ao H, o Afhletico se mantém na ponta, com o primeiro e único resultado até o momento.

Esse site tem muito potencial, pois as requisições já desfrutam do HTTP/2, além do cache e da segurança do HTTPS. Um ponto que não comentei antes é que alguns recursos externos ainda desfrutam de uma conexão mais rápida graças ao `dns-prefetch` de alguns domínios no `head`, como por exemplo:

```html
<link rel='dns-prefetch' href='//d3js.org' />
```

Tenho a sensação de que seria possível reduzir em uns 7s o tempo de carregamento inicial reorganizando o download e compressão dos scripts no `head`, aquele CSS imenso e as fontes de ícones.

<div class="criterios-br bom">
<ul>
  <li>HTTPS</li>
  <li>HTTP/2</li>
  <li>Prefetch</li>
  <li>Cache</li>
</ul>
</div>

<div class="criterios-br ruim">
<ul>
  <li>Sem minificação</li>
  <li>Sem gzip</li>
  <li>Compressão de imagens</li>
  <li>Compressão de fontes</li>
  <li>JS desnecessário</li>
  <li>CSS desnecessário</li>
  <li>Fontes de ícones</li>
  <li>Download atrasado de JS</li>
  <li>Download atrasado de CSS</li>
  <li>Download atrasado de fontes</li>
  <li>Scripts bloqueiam renderização</li>
</ul>
</div>

# Atlético

[Site](https://www.atletico.com.br/home){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_W3_c7bc71208382393d060e62085c8ac71a/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/atletico/imgs/filmstrip-first-view-run-1.png" class="lazy">
</div>

Começamos com **7.6s de nada**, e ainda que surja um pouco de cor na tela, só temos **conteúdo útil aos 10.3s**. Isso acontece pois esta página não é renderizada no servidor, como a do Athletico, e sim no cliente pelo JavaScript, que leva esse tempo pra baixar, montar a estrutura e começar a baixar o que precisa.

Vamos ao relatório da rede, que não deixa mentir:

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/atletico/imgs/first-view-run-1.png" class="lazy">
</div>

Coincidência ou não (eu realmente estou escrevendo na ordem que você está lendo, o que te surpreende, me surpreende) o primeiro problema aqui é o CSS. Não o tamanho, até porque ele está **minificado e gzipado**, vindo em 39.9 KB, mas como está sendo usado Webpack pra fazer o bundle de tudo (você pode ver que só tem um script e um CSS principal), a viagem deveria ter sido aproveitada e essa folha de estilo ter sido dividida só pelo que **é usado nessa página, nesse caso 17.6%**. Isso ajudaria, mas não faria muuuita diferença (ok, uns 35 KB a menos). O que prejudica um pouco mais é a primeira linha desse CSS:

```css
@import url(https://fonts.googleapis.com/css?family=Roboto+Condensed:300,400,700);@import url(https://fonts.googleapis.com/css?family=Roboto:400,400i,500);
```

Dois `imports` de fontes, que você pode ver na linha 4 e 5. É praticamente o mesmo problema do Athletico, apresentado de outra forma: **o browser só sabe dessa fonte depois que o arquivo CSS inteiro é computado.** Isso pode ser evitado adicionando essa requisição ao HTML:

```html
<link href="https://fonts.googleapis.com/css?family=Roboto+Condensed:300,400,700|Roboto:400,400i,500" rel="preload" as="font">
```

Isso resolveria o problema das fontes serem carregadas tão tarde, mas não resolveria aquela linha colorida verde, laranja e rosa que mostra o tempo negociando e conectando com o servidor onde estão estes recursos. Usando a solução que o Athletico usou, podemos solicitar ao navegador que faça o `prefetch` desses domínios, ou seja, "vai adiantando essa conexão aí que eu vou precisar dela logo":

```html
<link rel='dns-prefetch' href='//fonts.googleapis.com' />
<link rel='dns-prefetch' href='//c.neodatagroup.com' />
```

Note que eu já aproveitei e fiz o `prefetch` de outro recurso que também estava sofrendo com a conexão atrasada, na linha 7 (que chama um script `bahia.js`... vai entender)

Falando em script, nosso maior atraso se deve a ele, na linha 3. Como todo `body` é gerado no cliente, o JS foi posicionado lá no fim, isso significa que ele só será baixado e executado depois de analisado o documento inteiro. Isso pode ser melhorado, jogando esse script pro `head` com `defer`. Dessa forma ele começa a ser baixado antes e vai ser executado depois do documento pronto, da mesma forma.

Mais uma vez, o relatório de coverage me diz que **50.6% desse bundle não é usado**. Desperdício. O Webpack já tá sendo usando, é só uma questão de mandar só o que a página precisa (code split).

## Segundo tempo

Rola a conexão, começa a segunda visita...

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/atletico/imgs/filmstrip-second-view-run-3.png" class="lazy">
</div>

Dessa vez temos *apenas* **2s de nada** sendo mostrado, aí entra o logo placeholder até **5s onde a interação está pronta**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/atletico/imgs/second-view-run-3.png" class="lazy">
</div>

Cache OK, nenhuma requisição desperdiçada. Só acho que o Service Worker (linha 13) tá sendo usado inutilmente aqui. Geralmente usado pra apresentar algum conteúdo **útil** cacheado em estados offline, aqui ele é usado somente como placeholder. Fora o fato de que, na primeira renderização, da linha 87 em diante, tudo o que é baixado é pra alimentar o cache do SW e não serve pra nada na página atual.

## Custo

São 5.7 MB baixados (4.5 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 2,55 por mês, ou mais de 1 dia e meio de internet.

## Imagens

Nenhuma imagem é servida de acordo com o tamanho da tela, ou seja, no nosso teste, **uma imagem com 555 KB e 2048x1365 seria reduzida para 114 KB (80% menor) em JPEG ou 80.2 KB em WebP.**

Você pode estar pensando: mas esse cara está reduzindo o tamanho das imagens para 1000px, isso ajuda a reduzir o peso!!11 Nesse exemplo, **uma imagem com 625 KB 640x480 foi escalada para 1000px de largura e reduziu para 130 KB (80% menor) em JPEG ou 90.6 KB em WebP.**

A compressão, aliada ao carregamento atrasado das imagens (lazy load) reduziria o tamanho total em MB e o número de requisições.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/atletico/imgs/squoosh)

## Resultado

1. Atlético - 15.3s
1. Athletico - 17.4s

Temos um novo líder! Este tempo poderia ser melhor reduzindo o tamanho dos bundles do CSS e JS e pré-carregando as fontes.

<div class="criterios-br bom">
<ul>
  <li>HTTPS</li>
  <li>HTTP/2</li>
  <li>gzip</li>
  <li>Cache</li>
  <li>Minificação</li>
</ul>
</div>

<div class="criterios-br ruim">
<ul>
  <li>Service Worker </li>
  <li>Renderizado completamente no cliente</li>
  <li>Compressão de imagens</li>
  <li>JS desnecessário</li>
  <li>CSS desnecessário</li>
  <li>Download atrasado de fontes</li>
  <li>Scripts bloqueiam renderização</li>
</ul>
</div>

# Avaí

[Site](http://www.avai.com.br){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_X3_475d396e7988a2ef43d94caba0deb738/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/avai/imgs/filmstrip-first-view-run-3.png" class="lazy">
</div>

**22.9s sem nenhum conteúdo na tela**. Não começamos muito bem por aqui. Apesar da linha amarela que indica o DOM interativo estar em 35s, **a thread está bloqueada até os 37s**, quando de fato é possível interagir com com o conteúdo.

Por que isso?

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/avai/imgs/first-view-run-3.png" class="lazy">
</div>

Em respeito a quem está lendo, escondi uma parte dos mais de 7000px de height dessa imagem. São **409 requisições**. A primeira redireciona temporariamente (302) para /novo, que redireciona permanentemente para novo/ (301), que fica **mais de 13s esperando por uma resposta do servidor**. Existe um sério problema com o servidor aqui que, sozinho, **reduziria o tempo de carregamento em 35%**.

São 47 arquivos CSS, 2 ou 3 devem estar minificados. 54 são scripts, a maioria não minificada. Exatas 300 imagens, mas isso é assunto pra depois. Todas essas requisições são feitas de forma síncrona, ou seja, **1 requisição por vez já que está sendo usado HTTP/1** aqui. Pra piorar não tem `keep-alive` nos cabeçalhos das requisições, ou seja, o servidor tá livre pra fechar a conexão com o cliente ao fim de cada requisição. Um update no servidor para HTTP/2 permitiria acelerar um pouco o processo.

As fontes também respondem por 240 KB, sendo que duas versões da fonte de ícones `fontawesome-webfont.woff` são baixadas, além das versões cyrilic, greek, vietnamese, latin e suas variações `-ext` das fontes Open Sans, Roboto, ABeeZee e Open Sans Condensed.

Trazer os scripts pro `head` com `defer` ajudaria muito com o bloqueio da renderização e traria conteúdo visível pra tela mais cedo.

Fiquei com tanta imagem sendo carregada e fui olhar a página de perto. No fim dela tem uma galeria com, aparentemente, as últimas 4 partidas do Avaí. **Cada galeria roda um slider que contém TODAS as fotos do jogo.** A imagem abaixo não me deixa perder a conta: uma tem 88 imagens e outra 34. Tem mais duas ainda...

<img data-src="/assets/dist/images/brasileiro/avai/imgs/galerias.png" class="lazy">

Eu poderia ser radical e dizer que só uma thumbnail de capa pra cada galeria já seria suficiente, mas vamos deixar umas 5, só pra aproveitar o slider. Resolve 95% do problema com imagens.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/avai/imgs/filmstrip-second-view-run-2.png" class="lazy">
</div>

**Tela branca até 16.6s** graças a 13.2s esperando o servidor dar um sinal. A página **só fica interativa aí por 24.6s**, quando a thread principal é liberada.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/avai/imgs/second-view-run-2.png" class="lazy">
</div>

Como **não há cache**, o navegador dá uma mão e verifica que nada mudou, então não precisa baixar de novo, mas de qualquer forma perdemos tempo com cada requisição aqui, além de todos aqueles scripts rodando de uma vez só.

## Custo

São 16.4 MB baixados (13.5 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 7,33 por mês, praticamente 5 dias de internet.

## Imagens

A imagem do cabeçalho mede 1600x820px e tem 3.06 MB, ficaria com **136 KB (96% menor) em JPEG ou 115 KB em WebP.**

Selecionei essa segunda imagem e não vou redimensionar para 1000px, vai ficar original, apenas para demonstrar o ganho, mesmo com uma péssima prática. A imagem se chama *Captura-de-Tela-2019-03-26-às-13.24.14* tem 237x401px e 83.3 KB. Comprimida passaria a ter 11.1 KB (87% menor) em JPEG ou 6.93 KB em WebP.

A compressão ajudaria muito a reduzir o tamanho total do site, mas tão importante quanto isso seria reduzir a **quantidade** de imagens usadas.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/avai/imgs/squoosh)

## Resultado

1. Atlético - 15.3s
1. Athletico - 17.4s
1. Avaí - 61.6s

Só analisei estes 3 times até agora, mas aposto que essa vai ser a melhor posição do Avaí nessa tabela. **Resolver apenas o problema inicial do servidor já reduziria 26s desse tempo.**

<div class="criterios-br bom">
<ul>
  <li>Carregou</li>
</ul>
</div>

<div class="criterios-br ruim">
<ul>
  <li>Sem keep alive</li>
  <li>Sem cache</li>
  <li>Sem minificação</li>
  <li>Sem gzip</li>
  <li>HTTP/1</li>
  <li>HTTP</li>
  <li>Compressão de imagens</li>
  <li>JS desnecessário</li>
  <li>CSS desnecessário</li>
  <li>Fontes desnecessárias</li>
  <li>Fontes de ícones</li>
  <li>Download atrasado de JS</li>
  <li>Download atrasado de CSS</li>
  <li>Download atrasado de fontes</li>
  <li>Scripts bloqueiam renderização</li>
  <li>Excesso de requisições</li>
  <li>Muitas imagens</li>
</ul>
</div>

# Bahia

[Site](https://www.esporteclubebahia.com.br/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_WW_4f02fcf7f5e68dc4ebd3398f2d3d3571/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/bahia/imgs/filmstrip-first-view-run-3.png" class="lazy">
</div>

Somos recebidos por **4.9s de tela branca** e pelos próximos **48.3s**, tudo o que vemos é o logo no header e um fundo de torcida. Será que queimei a língua falando do Avaí?

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/bahia/imgs/first-view-run-3.png" class="lazy">
</div>

Eu vi antes este relatório e depois as imagens do carregamento e confesso que estava muito empolgado. Conexões simultâneas graças ao HTTP/2, gzip, HTTPS, tudo cacheado, a linha rosa na vertical que mostra o `DOMContentLoaded` entre 10 e 15s, parecia que teríamos uma boa surpresa, mas eu me enganei.

<img data-src="/assets/dist/images/brasileiro/bahia/imgs/scripts.png" class="lazy">

Me surpreendeu o WebPageTest não marcar em vermelho lá embaixo onde diz "Page is Interactive", pois a thread está bloqueada (por isso prefiro o CDT pra análises sérias) até os 48s pelo JS das linhas 10, 11 e 27, principalmente. Isso fica muito claro pelos traços rosa na horizontal. Note que são **traços**. Acaba por parecer uma linha de tamanho o trabalho que está sendo executado.

<img data-src="/assets/dist/images/brasileiro/bahia/imgs/profiler.png" class="lazy">

Rodando o JavaScript Profiler do CDT no desktop, sem emular uma conexão e CPU de celular, mostra o responsável pelo problema (proporcionalmente o problema é o mesmo). O plugin do jQuery `mCustomScrollbar` analisa e repinta o DOM MUITAS vezes e não por acaso isso acontece logo depois do `DOMContentLoaded`, afinal deve ter algum script esperando pelo `ready` pra começar a executar uma função. A ideia de alterar o comportamento padrão do scroll resultou na thread parada por 40s. Se tem uma coisa que me incomoda é o scroll do mouse não se comportar **da mesma forma que no resto da internet inteira**. Dou uma volta no scroll e já estou no fim da página.

A solução milagrosa que vai fazer esse site ficar 40s mais rápido (*REMOVI ESSE SCRIPT E MEU SITE FICOU 1 MINUTO MAIS RÁPIDO - VOCÊ VAI FICAR CHOCADO* 🙀) é tirar esse script. Resolve a performance e a usabilidade.

<img data-src="/assets/dist/images/brasileiro/bahia/imgs/coverage.png" class="lazy">

CSS e scripts, em grande parte, **não estão minificados** e, de modo geral, 74% nem é usado. Code splitting aqui reduziria em 1.4 MB o código carregado na página segundo o relatório de coverage. As fontes, ainda que estejam num arquivo só delas (`font.css`, linha 2), o que permite que o navegador saiba de antemão onde elas estão, não adianta o download, que poderia ser resolvido com `preload`. Todos os scripts estão no `head`, com exceção de 1, e poderiam se beneficiar do `defer`.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/bahia/imgs/filmstrip-second-view-run-3.png" class="lazy">
</div>

**Tela branca até os 3s** e interatividade aos 8.3s, mas **como não há conteúdo ainda, considero 8.5s**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/bahia/imgs/second-view-run-3.png" class="lazy">
</div>

Cache OK, poderia cachear as mídias de áudio mp3 também. O mesmo problema do **script bloqueando massivamente o render** se repete aqui.

## Custo

São 8.5 MB baixados (7.8 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 3,80 por mês, 2 dias e meio de internet.

## Imagens

O banner que fica no fundo do header é uma imagem de 2560x805px com 339 KB, **fica com 26.2 KB em JPEG (92% menor) ou 12.8 KB em WebP.**

O logo do clube, que é repetido no site em 3 versões de tamanhos diferentes, mede 1374x1660px com 303 KB, **passaria a 93.9 KB em PNG (69% menor) ou 36.1 KB em WebP**, reduzindo para as mesmas dimensões fixas usadas no site, de 400px. [Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/bahia/imgs/squoosh){:target="_blank"}.

Como dito acima, dos 8.5 MB baixados, apenas 700 KB não são de imagens, que são 159 no total. **Compressão e lazy loading** resolveriam muita coisa.

## Resultado

1. Atlético - 15.3s
1. Athletico - 17.4s
1. Bahia - 56.8s
1. Avaí - 61.6s

É como se o Bahia contratasse o Messi e colocasse ele aos 40" do segundo tempo na lateral esquerda. Este site tem muito potencial e uma base muito sólida, mas um script tirou pelo menos 40s do carregamento dele. O que eu falei acima ajudaria em todos os setores, mas remover esse comportamento alterado do scroll já colocaria o Bahia brigando pela primeira colocação. Uma pena.

<div class="criterios-br bom">
  <ul>
<li>HTTP/2</li>
<li>HTTPS</li>
<li>gzip</li>
<li>Cache</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Sem minificação</li>
<li>Compressão de imagens</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de fontes</li>
<li>Scripts bloqueiam renderização</li>
<li>Muitas imagens</li>
  </ul></div>

# Botafogo

[Site](https://www.botafogo.com.br/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_JD_1a258ae4286bec75f4d0f74ba1b14b15/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/botafogo/imgs/filmstrip-first-view-run-1.png" class="lazy">
</div>

**13.2s sem nada na tela**, e o conteúdo vai carregando até que a página fica **interativa a partir de 17.7s**. Esse tempo é baseado no que tanto o Lighthouse quanto o WebPageTest informam, já que **a página não é responsiva** e não tem como ver se o conteúdo realmente está ali. Em 2019.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/botafogo/imgs/first-view-run-1.png" class="lazy">
</div>

O primeiro ponto que você percebe quando bate o olho nesse relatório é que todas as requisições têm um traçado colorido, ausente nos relatórios anteriores. É o mesmo efeito do site do Avaí, que não tem o cabeçalho `keep alive` que faz com que o servidor "apague" a conexão a cada recurso e cada um precisa refazer todo o processo. Aliado a isso, **o HTTP/1 usado não deixa mais de uma conexão acontecer simultaneamente**, aí entra tudo numa fila.

De modo geral, **os arquivos CSS e JS não estão minificados e o servidor os serve sem gzip**. Os dois primeiros, e principais arquivos **CSS têm 45% e 64.8% não utilizados**, respectivamente.

*Only 90s kids will remember*: este site é compatível com RealPlayer e IE8-.

A renderização ainda sofre com o bloqueia de scripts no `head` sem `defer`.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/botafogo/imgs/filmstrip-second-view-run-1.png" class="lazy">
</div>

Na segunda etapa são **5s sem nada na tela**, até que **aos 10.3s temos interatividade**, de acordo com o relatório, já que temos aqui o mesmo problema com a **falta de responsividade** da página.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/botafogo/imgs/second-view-run-1.png" class="lazy">
</div>

Mais uma vez o navegador salva o dia cacheando o conteúdo por conta própria, já que não temos cache aqui. É possíve ver ainda que a partir do momento que temos conteúdo na tela, a thread principal fica travada dos 5 aos 8.2s aproximadamente.

## Custo

São 7.7 MB baixados (6 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 3,44 por mês, pouco mais de 2 dias de internet.

## Imagens

A imagem do treinador, usada no slider do header mede **1800x891px e tem 1.2 MB, teria 78 KB em JPEG (93% menor) ou 46.9 KB em WebP** se fosse comprimida.

A imagem do estádio mede 644x226px e pesa 180 KB, **teria 66.7 KB em JPEG (63% menor) ou 50.1 KB em WebP, tendo escalado a largura para 1000px**. [Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/botafogo/imgs/squoosh){:target="_blank"}.

A *compressão das imagens* aliada ao *carregamento atrasado* delas (lazy loading) ajudaria no tamanho da página. A redução de requisições também é uma opção.

## Resultado

1. Atlético - 15.3s
1. Athletico - 17.4s
1. Botafogo - 28s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

Muito tempo é perdido com configurações do servidor: HTTP/1 atrasa o download e a falta de um simples cabeçalho como `keep alive` adiciona tempo de conexão desnecessário a cada requisição. Carregamento adiantado das fontes, compressão de CSS e scripts, além destes últimos carregados com `defer`, ajudariam um pouco, mas não resolveriam o fato do site não ser responsivo.

<div class="criterios-br bom">
  <ul>
<li>HTTPS</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Sem keep alive</li>
<li>Sem cache</li>
<li>Sem gzip</li>
<li>HTTP/1</li>
<li>Sem minificação</li>
<li>Compressão de imagens</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de fontes</li>
<li>Scripts bloqueiam renderização</li>
<li>Muitas imagens</li>
<li>Não é responsivo</li>
  </ul></div>

# Ceará

[Site](http://www.cearasc.com/home/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_J7_ca152e955d92072127226d2a92f2eb59/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/ceara/imgs/filmstrip-first-view-run-2.png" class="lazy">
</div>

**Uma tela em branco nos recebe e dura 5.1s**. Em 10.4s o site está interativo, apesar de **não ser responsivo**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/ceara/imgs/first-view-run-2.png" class="lazy">
</div>

**3s é o tempo que o servidor demora para responder** à primeira requisição. Quando responde, recebemos nossos arquivos **sem minificação, porém gzipados**.

O HTTP/1 atrapalha, já que tudo precisa ser baixado em sequência, enquanto que o primeiro CSS `style.css` joga lenha na fogueira:

```css
@import url('boilerplate.css');
@import url('960.css');
@import url(http://fonts.googleapis.com/css?family=Arvo:400,700);
@import url(http://fonts.googleapis.com/css?family=Droid+Sans:400,700);
@import url('estrutura.css');
@import url('menu.css');
@import url('home.css');
@import url('formularios.css');
@import url('widgets.css');
@import url('1914.css');
@import url('soumais.css');
@import url('fabrica.css');
```

Com isso, o CSS é analisado por inteiro, o documento volta a fazer o que é sua prioridade, que é baixar e executar o que está no HTML e você volta a ver estes arquivos sendo baixados a partir da linha 9, depois de todo o resto que está no `head` ser baixado. A solução pra isso, como vimos em outros casos, seria consolidar todos os arquivos em um ou separar as requisições no próprio HTML, cada um com seu `link`. Isso, é claro, depois de **remover o que não é usado, que juntando CSS e JS totaliza 66%** do que é baixado.

<img data-src="/assets/dist/images/brasileiro/ceara/imgs/script.png" class="lazy">

Terminado de baixar o CSS avulso, o navegador parte para baixar e executar o resto dos scripts que estão no fim do `body` e lá encontramos o responsável por atrasar a renderização, na linha 21, que **bloqueia o render por mais de 3s**. Colocar todos os scripts no `head` e utilizar o `defer` ajudaria a evitar casos como este.

<img data-src="/assets/dist/images/brasileiro/ceara/imgs/fontes.png" class="lazy">

Mais pra baixo, nossas fontes são carregadas com muito atraso, o que poderia ser evitado com o `preload` e a intenção explícita do download delas em um `link` ao invés de escondidas num CSS.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/ceara/imgs/filmstrip-second-view-run-1.png" class="lazy">
</div>

Ainda que tenhamos conteúdo na tela em 3.4s, **a thread está bloqueada até os 4.5s**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/ceara/imgs/second-view-run-1.png" class="lazy">
</div>

Como o cache está fazendo o seu trabalho, não há muito trabalho a ser feito na segunda visita. Mesmo assim, o servidor demora a responder e compromete em 2.2s o tempo de resposta.  Os scripts de antes voltam a atacar a renderização aqui e esses dois problemas são os responsáveis pela maior parte desse tempo todo, que não chega a ser muito, mas poderia ser bem menor.

## Custo

São 3.6 MB baixados (1.6 MB de imagens). Este é o menor site até agora. Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 1,61 por mês, praticamente 1 dia de internet.

## Imagens

Nenhuma imagem neste site tem mais do que 69.7 KB, e isso inclui um banner de 1600px de largura. É perceptível o trabalho de compressão de imagens.

Ainda assim, **o banner de 1600x327px e 69.7 KB, teria 23.7 KB em JPEG (66% menor) ou 16.2 KB em WebP**. Lembre-se que estou redimensionando para 1000px, afinal, estamos no mobile.

Queria muito encontrar um sprite e achei aqui, ele tem 569x290px e 66 KB, teria 65.2 KB em PNG *(1% menor)* ou 39.7 KB em WebP.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/brasileiro/ceara/imgs/squoosh){:target="_blank"}.

## Resultado

1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Athletico - 17.4s
1. Botafogo - 28s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

Uma versão mobile desse site, só com minificação, HTTP/2, um tempo de resposta melhor do servidor e `defer` reduziria esse tempo em uns 5s tranquilamente. A grande responsável por esse tempo é a compressão das 91 imagens, não tenho dúvidas. Uma melhor configuração de servidor e scripts não bloqueando a renderização, esse tempo baixaria em mais 4s! Em um site de 2010...

<div class="criterios-br bom">
  <ul>
<li>Cache</li>
<li>gzip</li>
<li>Compressão de imagens</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>HTTP</li>
<li>HTTP/1</li>
<li>Sem minificação</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de fontes</li>
<li>Scripts bloqueiam renderização</li>
<li>Não é responsivo</li>
  </ul></div>

# Chapecoense

[Site](https://chapecoense.com/pt){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_AP_9adfdc13c780b0456cb059a42ba25426/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/chapecoense/imgs/filmstrip-first-view-run-3.png" class="lazy">
</div>

Começamos com **3.6s de nada na tela**. Ainda que o conteúdo comece a aparecer em seguida, a interatividade com a página está bloqueada por um script até 13.4s.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/chapecoense/imgs/first-view-run-3.png" class="lazy">
</div>

O responsável por esse atraso na interatividade é em grande parte o script da linha 5. Analisando o conteúdo dele, descobri que **ele contém 12 outros scripts**, de jQuery a lazy load, que após o download (que demora) são todos executados (mas **apenas 38.1% é usado**). Além disso, todos os scripts estão localizados no fim do `body`. Inclusive um deles, lá no fim, usa o atributo `async`, só que `async` no fim da página tem o mesmo efeito que `defer` no começo. Ou seja, a solução aqui seria, primeiro, dividir esse script imenso em arquivos individuais, trazer todos pro `head` e usar o `defer`. Essa divisão em vários arquivos **se beneficiaria do fato do site usar HTTP/2**.

Infelizmente **o site não faz uso de gzip**, o que afeta a compressão de forma geral, mas chamo a atenção para as fontes. Duas famílias de fontes são usadas aqui, uma em `woff` (11, 12, 13), carregada localmente e outra em `woff2` (21, 22, 23) do Google Fonts. **As locais, sem compressão e num formato não ideal**, pesam em torno de 56 KB, enquanto as últimas aproximadamente 13 KB. Isso, aliado ao `preload` carregaria tudo com antecedência e menos dados.

<img data-src="/assets/dist/images/brasileiro/chapecoense/imgs/conexoes.png" class="lazy">

Outro ponto importante é a **quantidade de conexões extras** para servidores diversos. A imagem acima é uma parte do que também ocorre próximo da linha 57. Isso acontece pois o navegador não sabe de antemão com quem ele precisa se conectar, e pode ser resolvido com `dns-prefetch`, como o Athletico fez.

O maior arquivo CSS da página **não é minificado e mais de 75% dele não é usado**.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/chapecoense/imgs/filmstrip-second-view-run-2.png" class="lazy">
</div>

**Tela em branco por 2.2s**, página visualmente completa já aos 3s, mas a **thread principal fica bloqueada até os 5s**, graças àquele script que falei anteriormente.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/chapecoense/imgs/second-view-run-2.png" class="lazy">
</div>

O cache aqui faz o seu papel, mas poderia ser melhor, já que tempo dele é de apenas 4 horas. Temos uma requisição extra, na linha 2, que aparentemente é de um trecho de banner aleatório a cada visita.

Mas o grosso desse tempo mesmo são os 2.5s que a thread fica parada devido aos scripts.

## Custo

São 4.3 MB baixados (3.9 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 1,92 por mês, pouco mais de 1 dia de internet.

## Imagens

Este site faz uso do carregamento atrasado de imagens (lazy loading), o que reduz consideravelmente a quantidade e o peso das requisições. Note que esta página baixou apenas 79 arquivos no total!

Ainda assim, encontrei um sprite de 138x**6354**px e 898 KB, que **poderia ser reduzido a 784 KB em PNG ou 277 KB em WebP.**

O cabeçalho é composto por uma imagem de 1920x822px e 91 KB, que **seria reduzida para 23.2 KB em JPEG (75% menor) ou 11.2 KB em WebP.** 

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/chapecoense/imgs/squoosh){:target="_blank"}.

## Resultado

1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Botafogo - 28s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

Só de separar aquele script imenso em arquivos individuais já teríamos um ganho considerável, principalmente em tempo de thread bloqueada. Além disso, coisas simples como gzip no servidor, `defer` e `prefetch` das conexões poderia trazer esse tempo uns 8s pra baixo.

<div class="criterios-br bom">
  <ul>
<li>HTTPS</li>
<li>HTTP/2</li>
<li>Cache</li>
<li>Image lazy loading</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Compressão de imagens</li>
<li>Minificação parcial</li>
<li>Sem gzip</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Compressão de fontes</li>
<li>Scripts bloqueiam renderização</li>
<li>Excesso de conexões</li>
  </ul></div>

# Corinthians

[Site](https://www.corinthians.com.br/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_TC_37133d6dcc7f049f52b01da90839a845/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/corinthians/imgs/filmstrip-first-view-run-2.png" class="lazy">
</div>

**Aos 5.4s a tela branca sai de cena** e a renderização começa. Note a diferença entre o frame 10 e o 10.5s. As setas do slider são um sinal claro de que há scripts bloqueando a renderização e colocando aos poucos componentes na tela. Não precisaria de um slider completo nesse momento, já que apenas a primeira partida seria suficiente pro carregamento.

**Como estado pronto para interação vou tomar 15.1s**, ainda que o WebPageTest indique 15.6s, estou considerando o ponto onde a thread principal destrava, além de que o slider já está até trocando de imagem.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/corinthians/imgs/first-view-run-2.png" class="lazy">
</div>

Analisar um site com 23.5 MB baixados, sendo 22.1 MB imagens, é como resgatar uma pessoa de um desabamento: você sabe onde a pessoa está, comida e água ajudariam, mas ainda tem 10 tonelados de escombros no caminho. **Qualquer otimização que não seja tirar tudo isso do caminho seria arranhar a superfície.**

Digo isso pois essa quantidade de dados distorce os gráficos. Tudo parece menor do que seria num contexto melhor.

Das primeiras 7 requisições, sem contar a própria página, temos 4 conexões para lugares diferentes, e ao todo são 22 na página. O tempo para conectar-se a estes recursos poderia ser reduzido com `dns-prefetch`, como o Athletico fez.

O CSS, em grande parte, **não é minificado**. Além disso, **duas versões da fonte de ícones** Font Awesome são baixadas, uma do próprio desenvolvedor e outra está dentro de `site.full.css`.

<img data-src="/assets/dist/images/brasileiro/corinthians/imgs/coverage.png" class="lazy">

Como mostra o relatório de coverage, a versão baixada de pro.fontawesome tem **apenas 0.7% usados**. Procurei um pouco no arquivo e descobri que são os logos do Spotify, Medium e G+ (pode ter mais algum, mas o arquivo é enorme). Tudo isso pra na linha 11 usar um ícone de busca em SVG (que na minha opinião seria o certo pra todos os ícones).

Muitos scripts estão no fim do `body`, o que atrasa sua descoberta, execução e posteriormente ainda bloqueia a renderização. Isso poderia ser resolvido trazendo-os para o `head` e usando o atributo `defer`. Inclusive, alguns scripts que estão no `head` já fazem uso do `async`, então seria um passo fácil a dar.

A fontes poderiam se aproveitar de um carregamento antecipado também, fazendo o uso do atributo `rel="preload"`.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/corinthians/imgs/filmstrip-second-view-run-3.png" class="lazy">
</div>

**Tela branca padrão até 3s** e, apesar de renderizado, a **thread principal fica bloqueada até 10.8s**.


<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/corinthians/imgs/second-view-run-3.png" class="lazy">
</div>

Como não temos cache aqui, o navegador entra em ação e faz este trabalho. Carregar um DOM desse tamanho e re-executar os scripts que já bloquearam a primeira visita são os responsáveis pelo tempo até a interação.

## Custo

São 23.5 MB baixados (22.1 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 10,50 por mês, ou 7 dias de internet. Sem contar o fato de que você já consumiu 1/4 dos dados do dia inteiro.

## Imagens

 Ninguém deveria passar por isso, mas eu peguei essa foto do Cássio em 4K, com 4096x1280px e 5.95 MB, e comprimi para 76.7 KB em JPEG (99% menor) ou 60.6 KB em WebP. Aliás, **essa imagem pesa mais do que o site inteiro da Chapecoense, do Ceará e do Athletico**, pegando só os que eu analisei até agora.

 Os números a seguir estão corretos: **esse ícone de um jipe tem 5906x5906px** e pesa 253 KB. O ideal seria convertê-lo num SVG de uns 32px, mas eu *apenas reduzi* para 1000px, que em PNG ficou com 20.7 KB (92% menor) ou 21 KB em WebP.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/corinthians/imgs/squoosh){:target="_blank"}.

## Resultado

1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. Botafogo - 28s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

Com uma imagem que só é menor do que um dos times à frente, o quinto lugar poderia ser melhor com a compressão e lazy load das imagens. Essa deveria ser a prioridade. Indo além, minificação, carregamento apenas do necessário e `defer` ajudariam a reduzir alguns segundos. 

<div class="criterios-br bom">
  <ul>
<li>HTTPS</li>
<li>HTTP/2</li>
<li>gzip</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Sem cache</li>
<li>Minificação parcial</li>
<li>Compressão de imagens</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Fontes de ícones</li>
<li>Scripts bloqueiam renderização</li>
<li>Excesso de conexões</li>
<li>Imagens excessivamente grandes</li>
  </ul></div>

# Cruzeiro

[Site](https://www.cruzeiro.com.br/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_YE_f3b54d67f740e9fdc6bc2848d58b9cd1/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/cruzeiro/imgs/filmstrip-first-view-run-3.png" class="lazy">
</div>

**4.2s de tela branca** e após um breve bloqueio da thread, **temos conteúdo aos 7.3s**. Não temos o ícone do menu ainda e a fonte final só entra aos 9.6s, mas **não estou julgando beleza, e sim performance**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/cruzeiro/imgs/first-view-run-3.png" class="lazy">
</div>

Seguindo essa mesma linha, aproveito para falar sobre as fontes, que são Oswald, Roboto, East Sea Dokdo, Encode Sans Expanded e **respondem por 280 KB**. O download delas está espalhado entras as linhas 37 e 61, e isso poderia ser evitado, corrigindo o carregamento de uma fonte genérica antes da fonte final (FOUT - Flash Of Unstyled Text) usando `preload` (remover algumas fontes ajudaria também).

De volta à ordem cronológica, temos diversos arquivos CSS sendo carregados, **todos sem minificação** e entre eles `materialize.css` e `animate.css`, com **93.6% e 99.8% não utilizados**, respectivamente. Como **o site faz uso do protocolo HTTP/1**, cada recurso gera uma conexão extra. Não bastasse isso, o cabeçalho `keep-alive` presente limita a 5 conexões máximas antes de fechar a conexão. Por esse motivo você vê pelo menos **53 conexões feitas para o próprio domínio do Cruzeiro**.

Mantendo o protocolo HTTP/1, o ideal seria reduzir o número de conexões, consolidando os arquivos CSS (só com o que for usado pela página) em um só.

Por fim, todos os script estão localizados ao final da página e poderiam melhorar os tempo de carregamento se estivessem no `head` usando `defer`.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/cruzeiro/imgs/filmstrip-second-view-run-2.png" class="lazy">
</div>

Ainda que tenhamos uma **tela em branco até 2.8s**, a renderização, que começa logo em seguida, já completa em 5.5s. Apesar disso, a thread principal fica bloqueada por 3 segundos e só libera a **interação a partir de 6.1s**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/cruzeiro/imgs/second-view-run-2.png" class="lazy">
</div>

Apesar do bom cache, ainda há dois gifs que precisam ser baixados, que juntos somam quase 2 MB! Ainda assim o que bloqueia a renderização e posterior interação são as imagens excessivas e os scripts.

## Custo

São 29.3 MB baixados (28.1 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 13,09 por mês, ou quase 9 dias de internet. Sem contar o fato de que você já consumiu mais de 1/4 dos dados do dia inteiro.

## Imagens

Uma camisa, apresentada na loja mede **1200x1200px e tem 1.2 MB. Comprimida teria 52.2 KB em JPEG (96% menor), ou 26.5 KB em WebP.**

Um ícone do Instagram com 82x82px e 4.47 KB, **ficaria melhor ainda sendo um SVG e customizado com CSS**, mas ainda assim, comprimido teria 2.67  KB em JPEG (**40% menor**), ou 3.05 KB em WebP.

A compressão, aliada ao lazy load das imagens faria milagres nesse site, no ponto de vista de custo de dados transmitidos.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/cruzeiro/imgs/squoosh){:target="_blank"}.

## Resultado

1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. Botafogo - 28s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

E o Cruzeiro assume duas lideranças: a de site mais rápido até a interação e o de mais pesado. O segredo está nos scripts que não atrasam tanto a renderização, de modo que o site já está utilizável num momento adiantado. Utilizável também não significa que tudo está lá. O tempo poderia ser menor e o resultado em tela também com o `preload` das fontes, `defer` dos scripts e o não carregamento de código que não é usado.

<div class="criterios-br bom">
<ul>
<li>HTTPS</li>
<li>Cache</li>
<li>gzip</li>
</ul></div>

<div class="criterios-br ruim">
<ul>
<li>Keep alive</li>
<li>Compressão de imagens</li>
<li>HTTP/1</li>
<li>Sem minificação</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Scripts bloqueiam renderização</li>
<li>Muitas imagens</li>
<li>Excesso de conexões</li>
</ul></div>

# CSA

[Site](http://www.centrosportivoalagoano.com/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_KR_db00013eb870ccf21cca5bd8c6254612/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/csa/imgs/filmstrip-first-view-run-1.png" class="lazy">
</div>

A única certeza que eu tenho é que teremos **tela em branco, dessa vez por 8.9s**. Apesar disso, a **thread principal fica bloqueada, com pequenos intervalos até 18.8s**. Analisaremos isso logo mais.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/csa/imgs/first-view-run-1.png" class="lazy">
</div>

A maior parte do CSS e uma parte considerável do JS **não está minificada**, além do **código não usado em geral ser de 79%**.

As fontes são carregadas em um `link` próprio, porém, não se beneficiam do `preload`, o que faz com que sejam carregadas tarde. Além disso, todos os scripts estão no fim do `body`, o que atrasa seu download, execução e consequentemente a renderização do que depende deles. Se você está lendo todos os times, parabéns, você já é um(a) vencedor(a) e sabe o que eu vou dizer aqui: scripts no `head` com `defer` pra destrancar o render (slogan de laxante?).

<img data-src="/assets/dist/images/brasileiro/csa/imgs/scripts.png" class="lazy">

Com imensa destreza, recortei só a requisição dos scripts do site e aproximei o gráfico no final do relatório que mostra a situação da thread. Veja que todos os bloqueios acontecem ao final ou execução de um script (traçado/linha horizontal rosa).

Tão ruim quanto não ter um cabeçalho `keep alive`, é ter um mal configurado, e aqui nós temos um que **encerra as conexões depois de um timeout de 1 segundo**, o que resulta em 50 conexões, sendo 18 delas para o próprio servidor do CSA. Se colocar 1 zero aí já resolve o problema. Servidor esse, já que você entrou no assunto, que **utiliza HTTP/1, sem gzip e sem cache.**

Não é por falta de coisa boa pra falar não, mas eu olhei o código escrito nesse site e acho que é o mais bonito até agora, quiçá num possível top 100 que já vi. Bem comentado, **organizado**, usando BEM pro CSS. E digo isso pois **qualquer coisa fica mais fácil de corrigir quando tudo fica tão óbvio com um código limpo**. O que falei acima, com exceção do servidor, liberaria a thread, aceleraria o render e não daria muito trabalho.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/csa/imgs/filmstrip-second-view-run-3.png" class="lazy">
</div>

Talvez o site visualmente completo com maior antecedência até agora, *MAS*, apesar da **renderização pronta aos 2.1s**, a **thread principal fica bloqueada até os 9.1s**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/csa/imgs/second-view-run-3.png" class="lazy">
</div>

Os problemas com script bloqueando a renderização se intensificam em visualizações cacheadas (ainda que aqui o cache vá pra conta do navegador), já que todo conteúdo está pronto, só falta executar.

O que sugeri no primeiro tempo ajudaria aqui também.

## Custo

São 8 MB baixados (5.2 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 3,57 por mês, ou quase 2 dias e meio de internet.

## Imagens

A imagem dos volantes do time mede 1234x537px e tem 822 KB. **Comprimida teria 571 KB em PNG (30% menor) ou 91.4 KB em WebP.**

Outra imagem sobre associados tem 788x402px, pesando 83.3 KB. **Comprimida teria 79.1 KB em JPEG (5% menor) ou 50.6 KB em WebP**. Pouca diferença? Não se esqueça que eu aumentei ela para 1000px.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/brasileiro/csa/imgs/squoosh){:target="_blank"}.

*Compressão e lazy load for the win.*

## Resultado

1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. CSA - 27.9s
1. Botafogo - 28s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

Nem tudo está perdido. Algumas coisas bem simples trariam resultados consideráveis e imediatos: scripts com `defer`, `preload` de fontes, e uma conexão com o servidor que não se feche a cada 1 segundo, poderia reduzir o tempo do CSA em pelo menos 12s.

<div class="criterios-br bom">
  <ul>
<li>Código limpo ¯\_(ツ)_/¯</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Sem cache</li>
<li>Sem gzip</li>
<li>Compressão de imagens</li>
<li>HTTP</li>
<li>Sem keep alive</li>
<li>HTTP/1</li>
<li>Sem minificação</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Fontes de ícones</li>
<li>Scripts bloqueiam renderização</li>
<li>Excesso de conexões</li>
  </ul></div>

# Flamengo

[Site](http://www.flamengo.com.br/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_QQ_fbf21a0a0329b4ed785ff56490349049/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/flamengo/imgs/filmstrip-first-view-run-1.png" class="lazy">
</div>

**Sem conteúdo na tela até 4.8s**, quando começa a renderizar com alguns gargalos dos scripts, **ficando interativa a partir de 7.9s**, quando carrega o cabeçalho.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/flamengo/imgs/first-view-run-1.png" class="lazy">
</div>

A maioria dos arquivos CSS e JS **não estão minificados** e no total, **72% do código não é usado na renderização**. Do pouco de JS que é usado, atrapalha a renderização. A maioria dos scripts, com exceção de um, está localizada no `head` mas não se beneficia do uso do `defer` para atrasar a execução e evitar o bloqueio da renderização e da thread. Ainda assim, são só 38 KB de CSS e 121 KB de fontes.

<img data-src="/assets/dist/images/brasileiro/flamengo/imgs/font.png" class="lazy">

O PROERD precisa incluir fontes de ícones nas aulas. **98.8% da fonte não é usada**, o que poderia ser substuído por alguns ícones em SVG.

Ainda no assunto das fontes, `preload` em cada `link` agilizaria o carregamento delas (linha 28)

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/flamengo/imgs/filmstrip-second-view-run-3.png" class="lazy">
</div>

**Tela branca até 2.4s**, e 1s depois, **aos 3.5s, página interativa** (após 1 segundo de thread bloqueada).

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/flamengo/imgs/second-view-run-3.png" class="lazy">
</div>

Graças ao navegador, todos os recursos estão cacheados. O bloqueio da thread é um pouco mais pesado aqui pois tudo acontece em menos tempo, mas poderia ser corrigido com o que eu falei acima.

## Custo

São 2.5 MB baixados (2 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 1,12 por mês, menos de 1 dia de internet.

## Imagens

Aqui eu fico feliz e puto. O Flamengo tem imagens para mobile! Mas elas são carregadas de qualquer forma no desktop e vice versa. Isso porque quem decide se elas estão **VISÍVEIS** é o CSS. Com Bootstrap, uma classe esconde as imagens pequenas acima de determinado breakpoint e mostra abaixo dele, mas isso não interfere no fato de que elas são baixadas da mesma forma. Aliás, isso é pior que se fosse carregada só a imagem grande!

O mais inacreditável é que já tá sendo usado `picture`, seria só colocar as duas imagens ali dentro e criar as regras.

Em sinal de protesto, vou comprimir duas imagens iguais, uma pro desktop e a outra pro mobile, **sem redimensionar**.

Um dos banners mede 2560x480px e 382 KB, e **passaria para 162 KB em JPEG (58% menor) ou 137 KB em WebP**.

O mesmo banner na versão mobile mede 767x480px e tem 141 KB. **Comprimido teria 58.1 KB em JPEG (59% menor) ou 45.5 KB em WebP**

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/flamengo/imgs/squoosh){:target="_blank"}.

## Resultado

1. Flamengo - 11.5s
1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. CSA - 27.9s
1. Botafogo - 28s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

Assim como o Cruzeiro, o Flamengo assume duas lideranças ao mesmo tempo: site mais leve e mais rápido. Pra ser ter uma ideia, ele **pesa mais de 2x menos que uma imagem do site do Corinthians**. Esse tempo poderia melhorar com `defer` nos scripts e `dns-prefetch` nos recursos que dependem de CDN. Se minificar o código, melhorar o `keep alive` (de 5 pra 10 de timeout) e em últimos casos mudar o servidor para HTTP/2, **consegue baixar pelo menos 2s desse tempo**.

<div class="criterios-br bom">
  <ul>
<li>gzip</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Sem cache</li>
<li>HTTP</li>
<li>Compressão de imagens</li>
<li>Keep alive poderia ser maior</li>
<li>HTTP/1</li>
<li>Sem minificação</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Fontes de ícones</li>
<li>Scripts bloqueiam renderização</li>
  </ul></div>

# Fluminense

[Site](http://www.fluminense.com.br/site/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_NV_dca79f2f1c56c66a6b018d464f437278/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/fluminense/imgs/filmstrip-first-view-run-1.png" class="lazy">
</div>

**5.7s de tela branca**, seguidos por uma rápida renderização, porém, **o conteúdo útil depende do JavaScript** e só aos 13.4s o slider do cabeçalho aparece.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/fluminense/imgs/first-view-run-1.png" class="lazy">
</div>

Este site só carrega um arquivo de script do próprio servidor, mas aparentemente é uma condensação de vários outros scripts, afinal são 210 KB **sem gzip nem cache**. Além disso, **64.2% nem é usado, assim como 90.2% do CSS**.

Como o servidor serve esses recursos em HTTP/1, até é bom que venha tudo num arquivo só, porém isso se reflete depois em tempo de download, execução e renderização, como dá pra ver pelas linhas 2 e 4, como nossos arquivos gigantes, porém únicos.

Bem breve, o script já está no `head` e poderia se beneficiar do `defer`, contato que houvesse uma solução para o banner que depende dele para renderizar (apresentar o primeiro slide fixo até carregar o script que dá interação e tudo mais). O CSS carregando o necessário, e o `preload` da fonte adiantaria o render.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/fluminense/imgs/filmstrip-second-view-run-2.png" class="lazy">
</div>

**Em 1.2s a tela branca some** e já temos o começo da renderização. Aqui fica muito claro o que eu disse há pouco sobre o script que comando o slider do cabeçalho. Veja como as imagens dele estão empilhadas. Elas **só se reorganizam aos 1.9s, quando a thread é desbloqueada.**

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/fluminense/imgs/second-view-run-2.png" class="lazy">
</div>

Com tão poucos recursos, o navegador que se deu ao trabalho de cachear tudo, já que não temos cache por parte do site (ainda que praticamente tudo pareça ter uma versão/timestamp), agora só precisa renderizar tudo. Ainda que a thread tenha ficado 0.6s travada, temos o site mais rápido a carregar até agora na segunda visita.

## Custo

São 1.9 MB baixados (1.4 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 0,85 por mês, praticamente meio dia de internet.

## Imagens

Um banner medindo 900x485px com 207 KB, teria, após a compressão, 120 KB em JPEG (42% menor) ou 88.8 KB em WebP.

O logo de um patrocinador tinha originalmente 144x119px e 13.9 KB, comprimido teria 2 KB em JPEG (86% menor) ou 1.16 KB em WebP, **sem redimensionamento**.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/fluminense/imgs/squoosh){:target="_blank"}.

## Resultado

1. Flamengo - 11.5s
1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Fluminense - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. CSA - 27.9s
1. Botafogo - 28s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

Como eu não estou aqui para avaliar conteúdo, vamos ao que interessa. O Fluminense empata com o Atlético e fica atrás pelo fato de ter menos itens na categoria "Bom". Ainda assim, marca um tempo baixo, que poderia ser melhor com a redução dos códigos não utilizados no CSS e JS, além do carregamento que não atrapalharia a renderização. Este passa a ser o site mais leve, o que poderia ser melhor também, houvesse gzip e compressão de imagens.

<div class="criterios-br bom">
  <ul>
<li>Minificação</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Sem gzip</li>
<li>Sem cache</li>
<li>HTTP</li>
<li>HTTP/1</li>
<li>Compressão de imagens</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Scripts bloqueiam renderização</li>
  </ul></div>

# Fortaleza

[Site](http://www.fortaleza1918.com.br/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_GY_8c27b162b9169be2ec43f6bc1b5ab946/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/fortaleza/imgs/filmstrip-first-view-run-2.png" class="lazy">
</div>

São **4.2s de tela em branco**, até que aos 8.8s o JavaScript adiciona o botão do menu e temos a página interativa.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/fortaleza/imgs/first-view-run-2.png" class="lazy">
</div>

O primeiro problema é algo que já vimos algumas vezes: diversas conexões para servidores diferentes (as linhas verde-laranja-rosa). Além disso, você percebe antes dos arquivos CSS uma linha laranja, que aponta que a conexão está sendo refeita com o servidor. Isso poderia ser resolvido com `dns-prefetch`, que já é usado na página para outros domínios:

```html
<link rel='dns-prefetch' href='//platform.twitter.com' />
<link rel='dns-prefetch' href='//www.google.com' />
<link rel='dns-prefetch' href='//s.w.org' />
```

A maior parte dos arquivos CSS **não está minificada**, porém o gzip está presente para diminuir o sofrimento. O mesmo ocorre com os scrits.

A maior parte dos scripts é carregada no fim do `body` e poderia ajudar a melhorar a performance se estivesse no `head` com `defer`. Além disso, algumas fontes carregadas localmente usam o formato `woff`, que não é o mais recente e mais comprimido. Não fosse o bastante, elas são importadas dentro do CSS e precisam esperar pela execução dele para começar a baixar. Isso poderia ser resolvido com `preload`.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/fortaleza/imgs/filmstrip-second-view-run-1.png" class="lazy">
</div>

A renderização começa aos 2.6s, mas **a thread principal não é liberada até os 4.4s**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/fortaleza/imgs/second-view-run-1.png" class="lazy">
</div>

Apesar de não termos cache no site e de o navegador tentar o possível, ainda assim temos o download de alguns scripts e CSS novamente.

A thread principal ainda fica bloqueada novamente por algumas vezes devido a execução de scripts, o que seria resolvido com as ações que citei acima.

## Custo

São 4.3 MB baixados (2.7 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 1,92 por mês, pouco mais de 1 dia de internet.

## Imagens

O site mostra a imagem de todos os jogadores, mas elas **são baixadas de acordo com o carregamento** num carousel. A primeira é do goleiro e tem 1200x1185px, pesa 1.32 MB e **teria 850 KB em PNG (36% menor) ou 163 KB em WebP**.

A imagem do título mede 1200x800px, tem 131 KB, e comprimida teria 103 KB em JPEG (21% menor) ou 63 KB em WebP. A redução em JPEG parece pequena se comparada às demais compressões, isso porque, se você reparar no nome da imagem, **ela veio do Whatsapp**, que já maltrata as imagens.

Lazy load e compressão reduziriam o tamanho geral da página.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/fortaleza/imgs/squoosh){:target="_blank"}.

## Resultado

1. Flamengo - 11.5s
1. Fortaleza - 13.2s
1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Fluminense - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. CSA - 27.9s
1. Botafogo - 28s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

Se for pra ter pelo menos uma coisa certa, que seja gzip. É a conclusão que eu chego, já que os scripts vão ser carregados de forma que bloquearão a renderização, pelo menos que baixem rápido.

<div class="criterios-br bom">
  <ul>
<li>gzip</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Sem minificação</li>
<li>Sem cache</li>
<li>HTTP</li>
<li>HTTP/1</li>
<li>Compressão de imagens</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Fontes com baixa compressão</li>
<li>Scripts bloqueiam renderização</li>
  </ul></div>

# Goiás

[Site](http://www.goiasec.com.br/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_ZH_5d89905c0e7d80980948fe63164b5785/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/goias/imgs/filmstrip-first-view-run-1.png" class="lazy">
</div>

A primeira experiência do usuário aqui são **3.8s de tela vazia**. Pelo fato do texto do cabeçalho ser branco, considero a **página pronta para interação** a partir do momento em que ele se torna legível, ou seja, quando a imagem de fundo carrega o suficiente para o texto estar visível, o que acontece em 15.4s.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/goias/imgs/first-view-run-1.png" class="lazy">
</div>

As primeiras linhas dão a prévia do que virá pela frente: na linha 3 a versão 3.1.1 do Bootstrap é baixada (**97.6% não é usado**) e na linha 4 a versão 2.3.2 (**89.8% não é usado**). **Sem minificação e sem cache, porém gzipados**. Nas linhas 11 e 31 também temos **duas versões diferentes do jQuery**, uma baixada do `head` e outra do fim do `body`.

Os scripts localizados no fim do documento poderiam se beneficiar de um carregamento mais rápido, além de não bloquear a renderização se fizessem uso do `defer` e estivesse no `head`.

As fontes usadas diretamente no site são requisitadas de dentro do CSS e dependem da análise completa do arquivo para começarem a ser requisitados para o servidor:

```css
@font-face {
    font-family: 'MarkBold';
    src: url('../fonts/MarkBold.eot');
    src: local('☺'), 
    url('../fonts/MarkBold.woff') format('woff'), 
    url('../fonts/MarkBold.ttf') format('truetype'), 
    url('../fonts/MarkBold.svg') format('svg');
    font-weight: normal;
    font-style: normal;
}
```

Este processo poderia ser agilizado usando `link`s com `preload`.

<img data-src="/assets/dist/images/brasileiro/goias/imgs/fontes.png" class="lazy">

Mais pra baixo um script ainda solicita fontes no formato `ttf`, que tem uma das piores compressões possíveis.

O fato do servidor servir o conteúdo em HTTP/1 também acaba comprometendo a performance geral, já que muitos arquivos grandes ocupam as poucas conexões disponíveis.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/goias/imgs/filmstrip-second-view-run-1.png" class="lazy">
</div>

A renderização começa aos 2.8s e parece completa aos 4.4s, porém, a **thread principal está bloqueada até 14.9s**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/goias/imgs/second-view-run-1.png" class="lazy">
</div>

O navegador faz o que pode. Ainda que não haja cache por parte da página, o browser serve o conteúdo da última visita, porém, como **várias requisições são externas**, são 14 conexões que precisam ser feitas, além de que os **scripts bloqueiam a interação por 12s**.

A eliminação de scripts desnecessário e o atraso em suas execuções, propostas que citei acima, ajudariam aqui.

## Custo

São 36.1 MB baixados (33 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 16,14 por mês, praticamente 11 dias de internet.

## Imagens

Todos os jogadores do time têm sua foto carregada desde o início. A que eu peguei mede **3456x5186px e pesa 1.23 MB**. Comprimida teria 95.9 KB em JPEG (92% menor) ou **46.2 KB em WebP**.

O site usa uma fonte de ícones mas também vários ícones no formato SVG, o problema é o tamanho desses ícones. Um deles pesa 4 KB e teria 739 bytes (82% menor) comprimido, ainda em SVG. Para essa compressão usei a ferramenta [SVGOMG](https://jakearchibald.github.io/svgomg/){:target="_blank"}.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/goias/imgs/squoosh){:target="_blank"}.

23 imagens tem pelo menos 1 MB, de um total de 156 imagens e 33 MB. Só com compressão daria para trazer esse número para uns 5 MB.

## Resultado

1. Flamengo - 11.5s
1. Fortaleza - 13.2s
1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Fluminense - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. CSA - 27.9s
1. Botafogo - 28s *
1. Goiás - 30.3s
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

Lembra do meu conselho de antes pra se for ter uma coisa, que seja gzip? É o que tem aqui, mas há milagre que ele faça com o site *mais pesado* até agora.

<div class="criterios-br bom">
  <ul>
<li>gzip</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Sem minificação</li>
<li>Sem cache</li>
<li>HTTP</li>
<li>HTTP/1</li>
<li>Compressão de imagens</li>
<li>JS desnecessário</li>
<li>Scripts duplicados</li>
<li>CSS duplicado</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Fontes de ícones</li>
<li>Fontes com baixa compressão</li>
<li>Scripts bloqueiam renderização</li>
<li>Excesso de requisições</li>
<li>Muitas imagens</li>
  </ul></div>

# Grêmio

[Site](https://gremio.net/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_QE_cf47f142f8a4784b2e7cc4b8d4a50a18/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/gremio/imgs/filmstrip-first-view-run-2.png" class="lazy">
</div>

Após **5s de nada** a renderização começa. O site já está usável em 7s, após **1s de thread principal bloqueada**.

Se você está acompanhando em ordem, percebeu a estratégia que **rendeu ao Grêmio alguns segundos enquanto que o Goiás perdeu tempo aqui**: fundo escuro onde virá a imagem para que o texto esteja legível o mais breve possível e não dependa do carregamento infinito da imagem ali atrás.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/gremio/imgs/first-view-run-2.png" class="lazy">
</div>

Uma desatenção aqui me deu um ótimo exemplo. Na linha 4 o arquivo `style.css` importa fontes e outros arquivos CSS.

```css
@import url("https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css");
@import url("https://use.fontawesome.com/releases/v5.6.3/css/all.css");
@import url("https://fonts.googleapis.com/css?family=Roboto+Condensed:300,400,500,700,900&subset=latin,latin-ext");
@import url("owl.carousel.css");
@import url("sub-escola.css");
```

Dessa forma, estes arquivos só começarão a ser baixados depois da análise completa do arquivo atual, o que pode ser comprovado pelas linhas 5, 7, 8, 9 e 10. Acontece que o arquivo `owl.carousel.css` é o primeiro a ser baixado, já na linha. Você consegue imaginar o porquê?

Isso se deve pelo fato do mesmo arquivo já ter sido requisitado por um `link` no HTML, que é o que **deveria estar sendo feito com todos estes recursos**. Isso reduziria em pelo menos 2s o tempo de início do download do `bootstrap.min.css`, na linha 8.

Além disso, são carregados 212 KB de fontes, sendo que **166.6 KB são de fontes de ícones**. A título de comparação, a soma de todo JS da página pesa 179 KB e todo CSS 49.8 KB. Destes, 64% não é usado.

<img data-src="/assets/dist/images/brasileiro/gremio/imgs/script.png" class="lazy">

Aproximei estas requisições do gráfico que mostra a thread principal para você ver o culpado pelo 1s de thread bloqueada: o script `main.js`.

Isso poderia ser otimizado trazendo os scripts que estão no fim do `body` para o `head` com o atributo `defer`.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/gremio/imgs/filmstrip-second-view-run-3.png" class="lazy">
</div>

**Mais uma vez a renderização é bloqueada por um script**, depois de **1.3s de tela em branco**, o site estaria no mesmo estado de interatividade do primeiro tempo aos 2.3s, mas a thread bloqueada segura isso até os 2.9s.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/gremio/imgs/second-view-run-3.png" class="lazy">
</div>

Apesar de o site não ter cache, o navegador dá uma mãozinha e apenas duas requisições são feitas. A thread principal fica bloqueada mais uma vez, provavelmente pelo mesmo script. A correção dele refletiria no resultado daqui.

## Custo

São 4.5 MB baixados (4 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 2,01 por mês, pouco mais de 1 dia de internet.

## Imagens

O Grêmio comprime as imagens no servidor e as serve ao navegador prontas.

Essa imagem com 1371x600px e 111 KB, chegaria a 104 KB em JPEG se eu apenas comprimisse (**7% de ganho**). Mas como estamos falando de mobile, vou redimensionar e comprimir. **Em JPEG ela teria 61.7 KB (45% menor) ou 38.6 KB.**

Aplicando o mesmo processo a uma imagem com 1920x540px e 268 KB, chegaríamos a **40.3 KB em JPEG (85% menor) ou 30.5 KB em WebP.**

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/gremio/imgs/squoosh){:target="_blank"}.

A compressão já está sendo feita. Um passo além seria o carregamento atrasado das imagens (lazy load) e o uso de imagens específicas para mobile com `srcset` ou `picture`.

## Resultado

1. Grêmio - 9.9s
1. Flamengo - 11.5s
1. Fortaleza - 13.2s
1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Fluminense - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. CSA - 27.9s
1. Botafogo - 28s *
1. Goiás - 30.3s
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

O Grêmio assume a liderança com 1.6s de folga para o Flamengo, com uma margem mínima para melhorar de 1.5s, graças àquele script bloqueando o render. Além disso, é o único que baixa da casa dos 2 dígitos.

<div class="criterios-br bom">
  <ul>
<li>gzip</li>
<li>HTTPS</li>
<li>Compressão de imagens</li>
<li>HTML minificado</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Minificação parcial</li>
<li>Sem cache</li>
<li>HTTP/1</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Fontes de ícones</li>
<li>Scripts bloqueiam renderização</li>
  </ul></div>

# Internacional

[Site](http://www.internacional.com.br/home/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_M5_c92e0ae45d1201194bdfdd4da0a223c9/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/internacional/imgs/filmstrip-first-view-run-1.png" class="lazy">
</div>

Após 4.2s de tela em branco, a renderização começa mas não avança praticamente nada até os 13s. Não bastasse isso, a **thread principal está bloqueada até 17.2s**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/internacional/imgs/first-view-run-1.png" class="lazy">
</div>

Aqui as fontes são a primeira coisa escrita no HTML, mas só são baixadas na linha 19, devido ao tempo de conexão. Melhor que isso, só se tivesse `preload` junto, o que adiantaria o download delas que só ocorre na requisição 34.

Praticamente todos scripts e CSS estão minificados, **com exceção de um arquivo de estilo com 6700 linhas**. No geral, **64% do código não é usado, ou 1.9 MB**.

Todo esse código não usado (1.3 MB só de JS) segura uma otimização: todos os scripts estão no `head` e alguns usam `defer`. Suponho que os 5 scripts usando `defer` tenham sido analisados e os demais não o usam devido a alguma interação que deve ser executada de forma síncrona. 

<img data-src="/assets/dist/images/brasileiro/internacional/imgs/script.png" class="lazy">

Mas como eu sou curioso, resolvi executar o JavaScript Profiler no CDT e descobri que o responsável pelo bloqueio da thread é o script `widget_v2.293.js`, que **pesa 246 KB gzipado mas descomprimido vai a 1 MB**! Você pode vê-lo sendo baixado na linha 52 (final 290.js). Este script é responsável por colocar uma janela de chat ao vivo no rodapé.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/internacional/imgs/filmstrip-second-view-run-3.png" class="lazy">
</div>

A partir de 2.1s a tela branca dá lugar à lenta e vagarosa renderização. A interação acontece aos **12.3s, quando a thread é desbloqueada**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/internacional/imgs/second-view-run-3.png" class="lazy">
</div>

Como são muitas requisições externas, o navegador ainda tem muito contato a fazer na segunda visita, o que é amenizado pelo cache do site, que apesar de curto (4 horas), existe. O mesmo problema de bloqueio do render acontece aqui, provavelmente pelo mesmo script.

## Custo

São 6 MB baixados (4.3 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 2,68 por mês, quase 2 dias de internet.

## Imagens

O Internacional faz uso do carregamento atrasado de imagens (lazy loading), o que reduz o tamanho da página e a quantidade de dados além da compressão de algumas imagens.

Uma imagem sem compressão, de 856x420px pesando 519 KB em PNG, teria, comprimida, 66.7 KB em JPEG (87% menor) ou 41.6 KB em WebP.

Outra foto, agora com 710x420px e 68.7 KB, aparentemente já comprimida, chegaria a 59.6 KB em JPEG (13% menor) ou 38.9 KB em WebP.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/internacional/imgs/squoosh){:target="_blank"}.

Para o tratamento de imagens ficar completo, só falta o uso de imagens específicas para mobile com `srcset` ou `picture`.

## Resultado

1. Grêmio - 9.9s
1. Flamengo - 11.5s
1. Fortaleza - 13.2s
1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Fluminense - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. CSA - 27.9s
1. Botafogo - 28s *
1. Internacional - 29.5s
1. Goiás - 30.3s
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

Uma coisa precisa ser discutida: a possibilidade de uma interação por chat, **vale mais do que a interação com página em si**? Afinal, se a pessoa não esperar 15s até a página destravar, ela não vai usar o chat. Corrigindo só este script, o Internacional **pode melhorar em pelo menos 13s**.

<div class="criterios-br bom">
  <ul>
<li>gzip</li>
<li>defer</li>
<li>Lazy loading</li>
<li>Compressão parcial de imagens</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Cache muito curto</li>
<li>HTTP</li>
<li>Compressão de imagens</li>
<li>Minificação parcial</li>
<li>HTTP/1</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de fontes</li>
<li>Fontes de ícones</li>
<li>Scripts bloqueiam renderização</li>
  </ul></div>

# Palmeiras

[Site](http://www.palmeiras.com.br/home/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_B3_5bf545f43ebcd29db33a020dc16bd2cf/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/palmeiras/imgs/filmstrip-first-view-run-1.png" class="lazy">
</div>

**Após 3.6s de tela branca** a renderização começa, porém, o **site não é responsivo**. Como interativo vou considerar **o primeiro estado em que o texto é legível, em 8.2s**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/palmeiras/imgs/first-view-run-1.png" class="lazy">
</div>

O WebPageTest registrou 376 requisições, que é o que eu vou considerar, porém **o CDT no desktop capturou 479, 139 delas scripts**.

<img data-src="/assets/dist/images/brasileiro/palmeiras/imgs/ads.png" class="lazy">

Em determinado ponto, 10 **scripts de anúncios** são carregados e executados. Iguais a estes existem outros entre as requisições.

A maioria dos scripts e CSS **não estão minificados**, porém o conteúdo vem comprimido pelo gzip. Os arquivo de JavaScript estão posicionados no `head`, porém sem `defer`, o que atrapalha a renderização, além do fato de que alguns geram outras requisições, que baixam e executam outros scripts.

Em determinado momento (linha 205 com um script imenso), até o player da Globo é baixado, contendo 425.6 KB comprimido e 1.4 MB no final das contas.

As fontes (10 variações de Open Sans) são requisitadas por um `link` a parte e poderiam se beneficiar do `preload`.

Ao todo, são 91 conexões e reconexões entre navegador e servidores.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/palmeiras/imgs/filmstrip-second-view-run-2.png" class="lazy">
</div>

Ainda que, aparentemente, o site pareça completo aos 6.6s, **a thread principal está bloqueada até os 25.3s**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/palmeiras/imgs/second-view-run-2.png" class="lazy">
</div>

Ainda que o cache do site seja simbólico (2 horas), os arquivos próprios estão sendo servidos a partir dele, só que ainda tem 127 conexões a serem feitas, em sua **maioria para servidores de anúncio**.

Os quase 140 scripts baixados anteriormente, agora bloqueiam a thread por praticamente 22s.

A estratégia que o Palmeiras adotou para promover o próprio conteúdo é usar ferramentas de terceiros (Google Ads), o que cobra um preço exorbitante.

Praticamente todos os outros clubes promovem planos de associação ou produtos de suas lojas, porém o fazem usando banners e sliders próprios, o que permite que a imagem seja veiculada mesmo com ad blockers.

## Custo

São 21.1 MB baixados (17.2 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 9,43 por mês, mais de 6 dias de internet.

## Imagens

A imagem de um dos jogadores do carousel mede 581x760px pesando 617 KB em PNG. **Comprimida teria 294 KB em PNG (52% menor) ou 33.7 KB em WebP.**

Uma imagem de uma notícia mede1498x843px pesando 691 KB. Após a compressão fica com 68.4 KB em JPEG (90% menor) ou 42.1 KB em WebP.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/palmeiras/imgs/squoosh){:target="_blank"}.

Compressão, carregamento atrasado (lazy load) e um possível carregamento de imagens em tamanhos específicos para mobile ajudariam a diminuir a carga das imagens neste site.

## Resultado

1. Grêmio - 9.9s
1. Flamengo - 11.5s
1. Fortaleza - 13.2s
1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Fluminense - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. CSA - 27.9s
1. Botafogo - 28s *
1. Internacional - 29.5s
1. Goiás - 30.3s
1. Palmeiras - 33.5s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

No desktop, com Ad Blocker, foram 190 requisições a menos. Então minha dica de otimização para o site do Palmeiras é essa, usem bloqueador de anúncios ao visitar.

<div class="criterios-br bom">
  <ul>
<li>gzip</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Cache muito curto</li>
<li>HTTP</li>
<li>Compressão de imagens</li>
<li>sem minificação</li>
<li>HTTP/1</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Fontes de ícones</li>
<li>Scripts bloqueiam renderização</li>
<li>Muitas imagens</li>
<li>Excesso de requisições</li>
  </ul></div>

# Santos

[Site](https://www.santosfc.com.br/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_7C_5c3c4af266a88a08604240edcb71bb01/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/santos/imgs/filmstrip-first-view-run-1.png" class="lazy">
</div>

Após **5s de nada na tela**, a renderização começa. Os elementos vão sendo jogados de um lado para o outro, conforme os scripts vão sendo executados, até que aos 9.2s o slider do cabeçalho carrega.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/santos/imgs/first-view-run-1.png" class="lazy">
</div>

O CSS carregado logo no começo está **parcialmente minificado** e junto com os scripts totalizam **76% de código não utilizado**. Além disso, duas versões do Bootstrap são carregadas.

É possível ver o degrau entre os scripts carregados no `head` e os ao final do `body`, na linha 16. Seria possível ganhar quase 1 segundo no último download se todos estivessem no `head` com `defer`.

O site não usa nenhuma fonte externa... para si próprio, pois **o widget do Spotify baixa 165 KB em duas versões da fonte Circular**.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/santos/imgs/filmstrip-second-view-run-3.png" class="lazy">
</div>

Passados 2.7s de tela em branco, parece que o site está pronto, porém, **a thread principal fica bloqueada até 3.8s**

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/santos/imgs/second-view-run-3.png" class="lazy">
</div>

Ainda que o site não defina o cache, o navegador entra em ação e salva o dia, restando apenas algumas conexões, entre elas com o Spotify, **mais uma vez baixando suas fontes**.

A execução simultânea de diversos scripts (plugins do jQuery) travam a thread e bloqueiam a interação.

## Custo

São 3.5 MB baixados (2.8 MB de imagens). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 1,56 por mês, praticamente 1 dia de internet.

## Imagens

A imagem de uma jogadora mede 700x700px pesando 75.3 KB em PNG. **Comprimida teria 58.4 KB em JPEG (22% menor) ou 33.8 KB em WebP.**

Um banner que mede 951x130px e pesa 1.09 MB. Após a compressão fica com 24.4 KB em JPEG (98% menor) ou 17.4 KB em WebP.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/santos/imgs/squoosh){:target="_blank"}.

Poderia aproveitar a compressão e carregamento atrasado das imagens para reduzir a carga de dados transferidos.

## Resultado

1. Grêmio - 9.9s
1. Flamengo - 11.5s
1. Santos - 13s
1. Fortaleza - 13.2s
1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Fluminense - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. CSA - 27.9s
1. Botafogo - 28s *
1. Internacional - 29.5s
1. Goiás - 30.3s
1. Palmeiras - 33.5s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

Atrasar a execução de scripts que não interferem no visual e interatividade da página poderia ajudar com a experiência do usuário aqui, alguns exemplos são scripts que mudam a dinâmica do scroll e o próprio Spotify. Quero conhecer o santista que chega no site do Santos só pra ouvir música.

<div class="criterios-br bom">
  <ul>
<li>gzip</li>
<li>HTTPS</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Sem cache</li>
<li>Compressão de imagens</li>
<li>Minificação parcial</li>
<li>HTTP/1</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>CSS duplicado</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Scripts bloqueiam renderização</li>
  </ul></div>

# São Paulo

[Site](http://www.saopaulofc.net/spfc){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_MZ_aa46261095fcc9e57f215f6c9fa729a3/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/sao-paulo/imgs/filmstrip-first-view-run-2.png" class="lazy">
</div>

Após **3.3s de tela em branco**, a página, que **não é responsiva**, começa a renderizar. Em 10.8s temos o texto do primeiro item do slider, o que seria a primeira possibilidade de interação.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/sao-paulo/imgs/first-view-run-2.png" class="lazy">
</div>

Seria. Mas os diversos scripts executando (traços/linhas horizontais rosa) **atrasam a renderização e bloqueiam a thread principal diversas vezes**, sendo o período mais longo entre 15 e 23s. Aos **22.6s termina o bloqueio**.

A maior parte do CSS e JS carregados **não está minificada**, porém o **gzip** ajuda na compressão, que poderia ser melhor. Destes, **56% não é utilizado (1.3MB)**.

Uma conclusão que cheguei, tendo analisado 19 site até agora é que **scripts externos não estão preocupados com a perfomance do SEU site**. Por *script externo* me refiro principalmente a widgets, como as linhas 5, 65 e da 152 em diante (o Facebook carrega 15 scripts ali). Veja que eles estão constantemente em execução, **procupados com que eles próprios sejam performáticos**, não necessariamente o ambiente ao redor.

No desktop, aliás, de 322 requisições, 78 são do Facebook, sendo 48 scripts, pesando 1.8 MB e totalizando 10.5 MB após a descompressão. Com **15.1 MB de scripts** descomprimidos no total, o resultado não poderia ser diferente.

Trazer estes scripts para o `head` e usar o máximo possível de `defer` poderia ajudar no desbloqueio da renderização, mas de qualquer forma, no momento que eles entrassem em execução, **o bloqueio aconteceria da mesma forma**.

Apenas uma fonte é carregada pelo site (outra é solicitado pelo YouTube), e demora para começar a carregar pelo fato de sua requisição estar **dentro de um CSS** e depender de sua análise. Um `link` externo já resolveria.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/sao-paulo/imgs/filmstrip-second-view-run-2.png" class="lazy">
</div>

Após **1.2s de tela em branco**, a renderização está praticamente pronta aos 9.1s.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/sao-paulo/imgs/second-view-run-2.png" class="lazy">
</div>

Porém, a thread está bloqueada desde o começo, e **só é liberada aos 15.5s**. Além disso, apesar do cache do site fazer seu trabalho, a imensidão de conteúdo externo precisa ser recarregada.

Os mesmos scripts que bloqueavam a thread na primeira visita bloqueiam aqui. Uma solução possível para manter os widgets de redes sociais e não ter tanto impacto na performance, seria **atrasar o carregamento deles para quando entrassem na tela** (como o que eu faço aqui no blog com a seção de comentários no rodapé). Só que no mobile não faria diferença pois, *wait for it*, o site inteiro já está carregado na tela.

## Custo

São 7 MB baixados (1.7 MB de imagens e 3.3 MB de scripts). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 3,13 por mês, ou 2 dias de internet.

## Imagens

Um dos principais banners mede 680x350px pesando 128 KB. **Comprimida teria 69.6 KB em JPEG (45% menor) ou 46.3 KB em WebP.**

A foto dos jogadores mede 1000x420px e pesa 50.8 KB. Após a compressão fica com 32.5 KB em JPEG (36% menor) ou 20.5 KB em WebP.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/sao-paulo/imgs/squoosh){:target="_blank"}.

Aparentemente existe uma compressão, que deve ser **por parte do trabalho de exportação das imagens**, visto que outros clubes que usam compressão, como Grêmio e Internacional, conseguimos reduções pequenas, geralmente abaixo dos 10%, enquanto que aqui, ainda que bem abaixo dos 80-90% que encontramos na maioria dos clubes, ainda fica acima do que seria uma compressão gerada por um algorítmo ótimo.

## Resultado

1. Grêmio - 9.9s
1. Flamengo - 11.5s
1. Santos - 13s
1. Fortaleza - 13.2s
1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Fluminense - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. CSA - 27.9s
1. Botafogo - 28s *
1. Internacional - 29.5s
1. Goiás - 30.3s
1. Palmeiras - 33.5s *
1. São Paulo - 38.1s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

As soluções que apontei acima, como o atraso do carregamento dos widgets sociais só teria efeito se estes elementos estivessem fora da tela, num site responsivo.

<div class="criterios-br bom">
  <ul>
<li>gzip</li>
<li>Cache</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Compressão de imagens parcial</li>
<li>HTTP</li>
<li>Sem minificação</li>
<li>HTTP/1</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Scripts bloqueiam renderização</li>
<li>Excesso de requisições</li>
  </ul></div>

# Vasco

[Site](http://www.vasco.com.br/site/){:target="_blank"}. [WebPageTest](https://www.webpagetest.org/result/190422_KP_2ee025c613c3832900fab106799f6052/){:target="_blank"}.

## Primeiro tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/vasco/imgs/filmstrip-first-view-run-2.png" class="lazy">
</div>

São **5.2s de tela em branco**, enquanto que só aos **8.3s as fontes carregam**.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/vasco/imgs/first-view-run-2.png" class="lazy">
</div>

Enquanto que o Cruzeiro sofria com o FOUT (Flash of Unstyled Text), o Vasco sofre com o **FOIT (Flash of Invisible Text)**. Este último acontece nesse caso pois a requisição da fonte principal acaba em 404 (linhas vermelhas), o que obriga o navegador a baixar a próxima fonte, que tem uma **péssima compressão** (`ttf`) e consequentemente leva mais tempo para baixar.

A solução poderia ser simplesmente carregar a fonte mais rápido (evitar o 404 já resolveria) ou usar a [FontFace API](https://developer.mozilla.org/en-US/docs/Web/API/FontFace/load){:target="_blank"} e o evento de `load` para carregar um fonte qualquer antes da definitiva. Além disso, são **5 fontes baixadas no total**, pesando 132 KB.

A maioria dos arquivos CSS e JS além de **subutilizados** (68% é inútil) **não estão minificados**.

É possível perceber ainda o bloqueio que os scripts causam no download de outros arquivos (além do bloqueio do render, claro) pelos degraus no relatório. Ainda que todos estejam no `head`, muitos poderiam beneficiar a renderização se não fossem carregados imediatamente (usar `defer`).

Uma solução inteligente usada aqui, foi servir o **conteúdo estático a partir de um servidor com HTTP/2 e SSL**. Isso fica bem claro pelas conexões com o cadeado na frente, que vêm desse servidor e adianta bastante o trabalho de download, sem afetar o servidor principal que continua com HTTP/1.

## Segundo tempo

<div class="filmstrip">
  <img data-src="/assets/dist/images/brasileiro/vasco/imgs/filmstrip-second-view-run-2.png" class="lazy">
</div>

**Até 1.9s tudo o que vemos é nada**, que é quando a thread principal trava e assim fica até os 5s.

<div class="expand-report">
  <img data-src="/assets/dist/images/brasileiro/vasco/imgs/second-view-run-2.png" class="lazy">
</div>

O cache do site faz um bom trabalho e apenas conexões externas precisam ser refeitas. Ainda assim, scripts bloqueiam a thread principal por 3s, além de ela ficar bem ocupada (tecnicamente estaria ocupada) por mais 1s, o que pode refletir em instabilidade na experiência.

Como veremos a seguir, o site já faz uso de carregamento atrasado para outros recursos e poderia fazer o mesmo com as galerias, por exemplo, e priorizar o carousel principal, que aparentemente é uma das últimas coisas a carregar.

## Custo

São 2 MB baixados (1 MB de imagens e 800 KB de scripts). Num plano de 100 MB a R$ 1,49/dia, acessar este site uma vez por dia custaria R$ 0,89 por mês, praticamente meio dia de internet.

## Imagens

O site faz uso de carregamento atrasado de imagens (lazy load), principalmente da galeria, e muitas imagens passaram por compressão, como fotos de jogadores, porém, outras imagens como o banner do cabeçalho aparentemente não.

A foto de um jogador mede 280x320px pesando 82.3 KB em PNG. **Comprimida teria 80.8 KB em PNG (2% menor) ou 8.47 KB em WebP.**

Enquanto que o banner do topo mede 1920x220px e pesa 39 KB. Após a compressão fica com 11.3 KB em JPEG (71% menor) ou 5.54 KB em WebP.

[Veja os resultados](https://github.com/estevanmaito/brasileirao-perf-2019/tree/master/clubes/vasco/imgs/squoosh){:target="_blank"}.

## Resultado

1. Grêmio - 9.9s
1. Flamengo - 11.5s
1. Santos - 13s
1. Fortaleza - 13.2s
1. Vasco - 13.3s
1. Cruzeiro - 13.4s
1. Ceará - 14.9s *
1. Atlético - 15.3s
1. Fluminense - 15.3s
1. Athletico - 17.4s
1. Chapecoense - 18.4s
1. Corinthians - 25.9s
1. CSA - 27.9s
1. Botafogo - 28s *
1. Internacional - 29.5s
1. Goiás - 30.3s
1. Palmeiras - 33.5s *
1. São Paulo - 38.1s *
1. Bahia - 56.8s
1. Avaí - 61.6s

Não é responsivo *

O Vasco garante um quinto lugar amargo, pois não fosse o carregamento das fontes, o site estaria pronto alguns segundos antes. Não sei se seria o suficiente para tomar o segundo lugar do Flamengo, mas com certeza o terceiro.

<div class="criterios-br bom">
  <ul>
<li>gzip</li>
<li>Cache</li>
<li>HTTP/2</li>
<li>Lazy loading</li>
  </ul></div>

<div class="criterios-br ruim">
  <ul>
<li>Compressão de imagens parcial</li>
<li>HTTP</li>
<li>Sem minificação</li>
<li>JS desnecessário</li>
<li>CSS desnecessário</li>
<li>Download atrasado de JS</li>
<li>Download atrasado de fontes</li>
<li>Fontes sem compressão</li>
<li>Scripts bloqueiam renderização</li>
  </ul></div>