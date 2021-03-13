---
title: A részletes olvasó indítás gombjának szerkesztése
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a teljes olvasót indító gombot.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 03/08/2021
ms.author: metang
ms.openlocfilehash: d60e37a437cacda8afbe88a901089f9478a53c16
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608614"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>A megölelő olvasó gomb testreszabása

Ez a cikk bemutatja, hogyan szabhatja testre a teljes olvasót, hogy illeszkedjen az alkalmazás igényeihez.

## <a name="add-the-immersive-reader-button"></a>A magával ejtő olvasó gomb hozzáadása

A lebilincselő olvasó SDK alapértelmezett stílust biztosít a magával ragadó olvasót indító gombhoz. A `immersive-reader-button` Class attribútum használatával engedélyezze ezt a stílust.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>A gomb stílusának testreszabása

A `data-button-style` gomb stílusának beállításához használja az attribútumot. A megengedett értékek: `icon` , `text` és `iconAndText` . Az alapértelmezett érték `icon`.

### <a name="icon-button"></a>Ikon gomb

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Ez a következőt jeleníti meg:

![Ez a megjelenített szöveg gomb](./media/button-icon.png)

### <a name="text-button"></a>Text (szöveg) gomb

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Ez a következőt jeleníti meg:

![Ez a megjelenített, lebilincselő olvasó gomb.](./media/button-text.png)

### <a name="icon-and-text-button"></a>Ikon és szöveg gomb

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Ez a következőt jeleníti meg:

![Ikon gomb](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>A gomb szövegének testreszabása

Konfigurálja a gomb nyelvét és az ALT szövegét az `data-locale` attribútum használatával. Az alapértelmezett nyelv az angol.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Az ikon méretének testreszabása

A lebilincselő olvasó ikonjának mérete az attribútum használatával konfigurálható `data-icon-px-size` . Ezzel beállítja az ikon méretét képpontban megadva. Az alapértelmezett méret a 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [olvasói SDK-referenciát](./reference.md)