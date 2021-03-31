---
title: Hogyan észlelhetők a vizsgálatok során az eltávolított eszközök?
description: Ez a cikk azt ismerteti, hogy az Azure-beli hatáskörébe tartozó fiókok hogyan észlelik a törölt eszközöket a vizsgálatok során.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553370"
---
# <a name="how-scans-detect-deleted-assets"></a>Hogyan észlelhetők a vizsgálatok során az eltávolított eszközök?

Ez a cikk azt ismerteti, hogyan használja az Azure hatáskörébe a vizsgálati eredményeket a törölt eszközök észleléséhez.

## <a name="background-info"></a>Háttér-információk

Az Azure-beli hatáskörébe katalógusa csak akkor ismeri fel az adattár állapotát, ha megvizsgálja. Ahhoz, hogy a katalógus megtudja, hogy a fájl, tábla vagy tároló törölve lett-e, összehasonlítja a legutóbbi vizsgálat kimenetét az aktuális vizsgálat kimenetével. Tegyük fel például, hogy az utolsó alkalommal, amikor beolvasott egy Azure Data Lake Storage Gen2 fiókot, egy *Mappa1* nevű mappát tartalmazott. Ha a rendszer újból megvizsgálja ugyanazt a fiókot, a *Mappa1* hiányzik. Ezért a katalógus azt feltételezi, hogy a mappa törölve lett.

## <a name="detecting-deleted-files"></a>Törölt fájlok észlelése

A hiányzó fájlok észlelésének logikája ugyanazon felhasználó és a különböző felhasználók által több vizsgálatra is használható. Tegyük fel például, hogy a felhasználó egy egyszeri vizsgálatot futtat egy Data Lake Storage Gen2 adattáron az A, B és C mappákban. Később egy másik felhasználó ugyanabban a fiókban egy eltérő egyszeri vizsgálatot futtat a C, D és E mappákban ugyanazon az adattáron. Mivel a C mappa kétszer lett megvizsgálva, a katalógus ellenőrzi a lehetséges törléseket. Az A, B, D és E mappákat azonban csak egyszer vizsgálták meg, és a katalógus nem ellenőrzi őket a törölt eszközökön.

A törölt fájlok a katalógusból való törléséhez fontos a rendszeres vizsgálatok futtatása. A vizsgálat időköze fontos, mivel a katalógus nem képes a törölt eszközök észlelésére, amíg egy másik vizsgálat nem fut. Így ha egy adott áruházban havonta egyszer futtat vizsgálatot, a katalógus nem képes észlelni a tárolt törölt adategységeket, amíg a következő vizsgálatot nem futtatja egy hónap múlva.

Ha a nagyméretű adattárakat (például Data Lake Storage Gen2) sorolja fel, több módon is (beleértve az enumerálási hibákat és az eldobott eseményeket) az információk hiányoznak. Előfordulhat, hogy egy adott vizsgálat elmulasztja egy fájl létrehozását vagy törlését. Így ha a katalógus nem biztos benne, hogy a fájlt törölték, azt nem törli a katalógusból. Ez a stratégia azt jelenti, hogy hibák jelentkezhetnek, ha a beolvasott adattárban nem található fájl még létezik a katalógusban. Bizonyos esetekben előfordulhat, hogy az adattárakat két vagy három alkalommal kell beszkennelni, mielőtt bizonyos törölt eszközöket elkap.

## <a name="next-steps"></a>Következő lépések

Az Azure hatáskörébe-katalógusok használatának megkezdéséhez lásd: gyors üzembe helyezési [fiók létrehozása](create-catalog-portal.md).
