---
layout:     page
menu-title: BEM
title:      Documentation > BEM
categories: [docs]
---

## QATools\QATools\BEM
Depends on `QATools\QATools\PageObject`.

According to BEM methodology (http://bem.info/) the following restrictions apply:

1. there can't be nested blocks
2. each element must be placed within a block

But single HTML node (or it's element) can be within different blocks at same time.

### Class Overview

#### Elements (namespace: `\QATools\QATools\BEM\Element`)

| Name | Description |
| ------------- | ------------- |
| `Element` | Represents a single element on a page, that must be placed within a Block. |
| `Block` | Base class for creating dedicated classes, that will encapsulate associated elements on a page. |

### Annotation Overview

| Name | Description |
| ------------- | ------------- |
| `@bem` | Unified annotation for both Block and Element. |

### Usage

1. create a subclass from `\QATools\QATools\BEM\Element\Block` class to for each block on a page
2. add class properties, that have `\QATools\QATools\BEM\Element\Element` in their `@var` annotation for each individual element in each block (or alternatively use `$this->getElements` method in the `Block` class)
3. create subclass from `\QATools\QATools\BEM\BEMPage` class
4. add class property with previously created block subclass name in it's `@var` annotation for each individual block

[Continue to Examples](/examples/03-BEM)
