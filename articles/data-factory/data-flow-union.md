---
title: Union-átalakítás a leképezési adatfolyamban
description: Azure Data Factory leképezési adatfolyam új ág-átalakítás
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/27/2020
ms.openlocfilehash: e61c53cabc8f784ba9f0a3e78b5de01be134b0cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367843"
---
# <a name="union-transformation-in-mapping-data-flow"></a>Union-átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Union több adatfolyamot egyesít egy, a streamek SQL Union szolgáltatásával, amely az Unió átalakításának új kimenete lesz. Az egyes bemeneti adatfolyamokból származó összes séma össze lesz foglalva az adatfolyamattal, anélkül, hogy hozzá kellene adni egy csatlakoztatási kulcsot.

Az egyes konfigurált sorok melletti "+" ikonra kattintva a "+" ikont is egyesítheti a beállítások táblában, beleértve a forrásadatok és az adatfolyamok meglévő átalakításait is.

Íme egy rövid videó, amely az ADF leképezési adatforgalmában lévő Union-átalakítási folyamaton keresztül érhető el:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![Unió átalakítása](media/data-flow/union.png "Union")

Ebben az esetben kombinálhatja a különböző forrásokból származó eltérő metaadatokat (ebben a példában három különböző forrásfájl), és egyetlen streambe kombinálhatja őket:

![A Union Transformation áttekintése](media/data-flow/union111.png "1. Unió")

Ennek eléréséhez vegyen fel további sorokat a Union-beállításokba úgy, hogy a hozzáadni kívánt forrást is tartalmazza. Közös keresési vagy csatlakoztatási kulcsra nincs szükség:

![A Union átalakítási beállításai](media/data-flow/unionsettings.png "Union-beállítások")

Ha a szervezete után kiválasztja az átalakítást, akkor átnevezheti a nem fejléces forrásokból származó átfedésben lévő mezőket vagy mezőket. A "vizsgálat" gombra kattintva tekintheti meg az alábbi három különböző forrásból származó, 132-es összesített metaadatokat:

![Az Unió átalakítása végleges](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>Név és pozíció

Ha az "Unió neve" lehetőséget választja, minden oszlop értéke bekerül a megfelelő oszlopba az egyes forrásokból, egy új összefűzött metaadat-sémával.

Ha a "Union by position" lehetőséget választja, minden egyes oszlop értéke bekerül az eredeti pozícióba az egyes forrásokból, ami egy új, összesített adatstreamet eredményez, amelyben az egyes forrásokból származó adatok ugyanahhoz az adatfolyamhoz lesznek hozzáadva:

![Union-kimenet](media/data-flow/unionoutput.png "Union-kimenet")

## <a name="next-steps"></a>Következő lépések

Fedezze fel a hasonló átalakításokat, beleértve a [csatlakozást](data-flow-join.md) és a [meglévőket](data-flow-exists.md).
