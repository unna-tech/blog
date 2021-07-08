---
layout: post
current: post
navigation: 'True'
class: post-template
cover: /assets/images/backend-cover-01.jpg
placeholder: >-
  data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDABALDA4MChAODQ4SERATGCgaGBYWGDEjJR0oOjM9PDkzODdASFxOQERXRTc4UG1RV19iZ2hnPk1xeXBkeFxlZ2P/2wBDARESEhgVGC8aGi9jQjhCY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2P/wgARCAAQABgDAREAAhEBAxEB/8QAFgABAQEAAAAAAAAAAAAAAAAABQEA/8QAFgEBAQEAAAAAAAAAAAAAAAAAAAEC/9oADAMBAAIQAxAAAAAZuhaZYHp//8QAHxAAAgECBwAAAAAAAAAAAAAAAAQCAQMSFSNUYqLR/9oACAEBAAE/ALShdUwjcoWjOH9z0p4Mtssr6zs58CUT/8QAFBEBAAAAAAAAAAAAAAAAAAAAIP/aAAgBAgEBPwBf/8QAFBEBAAAAAAAAAAAAAAAAAAAAIP/aAAgBAwEBPwBf/9k=
title: Testes com react + redux
description: testando
date: '2019-04-16 09:30:49'
author: estevanmaito
category: javascript
tags:
  - react
  - redux
---
TL;DR - Se você está usando o método `mount` do Enzyme para testar seus componentes no React, as chances são grandes de você poder trocar pelo `shallow`, com uma pequena adaptação e menos código.

## Contexto

A aplicação desse exemplo, um carrinho de compras, é pequena em número de componentes, mas um pouco complexa, em grande parte devido ao Redux. O objetivo dela é servir de base para um workshop que eu pretendo criar sobre React, Redux e testes, e levar para o Youtube nas próximas semanas. Nessa aplicação eu simulo um ambiente que poderia ser usado por uma base de código muito maior, com vários reducers, actions creators, 100% de test coverage e etc e no meio de tudo isso eu busco seguir as melhores práticas possíveis qu se encaixariam em um projeto de porte muito maior.

## Problema

Dependendo do ponto de vista, você pode não ver isso como um problema, afinal, os testes continuam rodando, mas a diferença entre os métodos `mount` (que renderiza completamente o DOM pro componente montado MAIS os filhos) e `shallow` (renderiza apenas o componente atual, isolado) pode comprometer a performance dos testes em uma base de testes maior (enquanto eu escrevo, são 44 testes, executados em 6.726s)
































