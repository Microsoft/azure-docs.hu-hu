---
title: Előtér-keretrendszerek konfigurálása az Azure statikus Web Apps előzetes verziójával
description: Az Azure statikus Web Appshoz szükséges népszerű előtér-keretrendszerekre vonatkozó beállítások
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 14564b0591ef0146131b3f9324556b613e25daac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97901232"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Előtér-keretrendszerek és-kódtárak konfigurálása az Azure statikus Web Apps előzetes verziójával

Az Azure statikus Web Apps a megfelelő konfigurációs értékeket kell megadnia az előtér-keretrendszer vagy a könyvtár [létrehozási konfigurációs fájljában](github-actions-workflow.md) .

## <a name="configuration"></a>Konfiguráció

A következő táblázat felsorolja a keretrendszerek és<sup>a kódtárak</sup>egy sorozatának beállításait.

A táblázat oszlopainak szándékát a következő elemek magyarázzák:

- **Kimeneti hely**: a (z) értékét listázza `output_location` , amely az [Alkalmazásfájlok beépített verzióihoz tartozó mappa](github-actions-workflow.md#build-and-deploy).

- **Egyéni Build parancs**: Ha a keretrendszer a vagy rendszertől eltérő parancsot igényel `npm run build` `npm run azure:build` , [Egyéni Build-parancsot](github-actions-workflow.md#custom-build-commands)is meghatározhat.

| Keretrendszer | Alkalmazás-összetevő helye | Egyéni Build parancs |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | n/a <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Szögletes univerzális](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | n.a. |
| [Backbone.js](https://backbonejs.org/) | `/` | n.a. |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | n.a. |
| [Ember](https://emberjs.com/) | `dist` | n.a. |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Pislákoló](https://glimmerjs.com/) | `dist` | n.a. |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | n.a. |
| [Hyperapp](https://hyperapp.dev/) | `/` | n.a. |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | n.a. |
| [jQuery](https://jquery.com/) | `/` | n.a. |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | n.a. |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | n.a. |
| [Marko](https://markojs.com/) | `public` | n.a. |
| [Meteor](https://www.meteor.com/) | `bundle` | n.a. |
| [Mithril](https://mithril.js.org/) | `dist` | n.a. |
| [Polimer](https://www.polymer-project.org/) | `build/default` | n.a. |
| [Preact](https://preactjs.com/) | `build` | n.a. |
| [React](https://reactjs.org/) | `build` | n.a. |
| [Rajzsablonon](https://stenciljs.com/) | `www` | n.a. |
| [Svelte](https://svelte.dev/) | `public` | n.a. |
| [Three.js](https://threejs.org/) | `/` | n.a. |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | n.a. |
| [Vue.js](https://vuejs.org/) | `dist` | n.a. |

<sup>1</sup> a fenti táblázat nem az Azure statikus web Appsokkal működő keretrendszerek és könyvtárak teljes listáját jelenti.

<sup>2</sup> nem alkalmazható

## <a name="next-steps"></a>Következő lépések

- [Buildelés és munkafolyamat-konfiguráció](github-actions-workflow.md)
