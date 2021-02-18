---
title: Azure Monitor munkafüzet szövegének vizualizációi
description: Ismerkedjen meg az összes Azure Monitor munkafüzet szöveges vizualizációval.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614978"
---
# <a name="text-visualizations"></a>Szövegvizualizációk

A munkafüzetek lehetővé teszik a szerzők számára, hogy szöveges blokkokat foglaljanak bele a munkafüzetekbe. A szöveg lehet a telemetria emberi elemzése, amely segítséget nyújt a felhasználóknak az adatok értelmezésében, a szakaszok fejlécében stb.

[![Képernyőkép a Apdex](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

A szöveg egy Markdown vezérlőn keresztül lesz hozzáadva, amely teljes formázási vezérlést biztosít. Ide tartoznak a különböző címsor-és betűstílusok, a hiperhivatkozások, a táblázatok stb.

Szerkesztési mód:

![Képernyőkép a szerkesztési módban lévő szöveges lépésekről.](./media/workbooks-text-visualizations/text-edit-mode.png)

Előnézet mód:

![Képernyőkép az Előnézet lapon található szerkesztési módban lévő szöveges lépésről.](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>Szövegbeviteli vezérlőelem hozzáadása

1. Az eszköztár **szerkesztése** elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **text (szöveg hozzáadása** ) hivatkozás használatával adjon hozzá egy szövegbeviteli vezérlőt a munkafüzethez.
3. Adja hozzá a Markdown a szerkesztő mezőben.
4. A *szöveg stílusa* beállítással válthat az egyszerű Markdown és a Markdown között a Azure Portal szokásos adatai/figyelmeztetés/siker/hiba stílusa alapján.
5. A **Preview (előnézet** ) lapon megtekintheti, hogyan fog kinézni a tartalom. A szerkesztés során az előnézet egy görgetősávon belül megjeleníti a tartalmat a méret korlátozása érdekében. futásidőben azonban a Markdown-tartalom kibontásával kitöltheti a szükséges lemezterületet, görgetősáv nélkül.
6. Kattintson a **kész szerkesztés** gombra a lépés szerkesztésének befejezéséhez.

> [!TIP]
> Ez a [Markdown-Cheat-adatlap](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) a különböző formázási beállítások megismerésére használható.

## <a name="text-styles"></a>Szövegstílusok

A szöveges lépéshez a következő szövegstílusok érhetők el:

| Stílus     | Magyarázat                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | Nincs alkalmazva további formázás.                                                      |
| `info`    | A portál "info" stílusa,  `ℹ` vagy hasonló ikon és általában kék háttér.      |
| `error`   | A portál "hiba" stílusa `❌` vagy hasonló ikon és általában piros háttér.     |
| `success` | A portál "sikeres" stílusa, `✔` vagy egy hasonló ikon és általában zöld háttér.  |
| `upsell`  | A portál "upsell" stílusa egy `🚀` vagy több hasonló ikonnal és általában lila háttérrel. |
| `warning` | A portál "figyelmeztetés" stílusa `⚠` vagy hasonló ikon és általában kék háttér.   |

Egy adott stílus kiválogatása helyett a stílus forrásaként is kiválaszthat egy szöveges paramétert. A paraméter értékének a fenti szöveges értékek egyikének kell lennie. Egy érték vagy ismeretlen érték hiánya stílusként lesz kezelve `plain` .

Példa az információs stílusra:

![Képernyőkép arról, hogy az információs stílus hogyan néz ki.](./media/workbooks-text-visualizations/text-preview-info-style.png)

A figyelmeztetés stílusa például:

![Képernyőkép arról, hogy a figyelmeztetés stílusa hogyan néz ki.](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan hozhat létre [diagramot a munkafüzetekben](workbooks-chart-visualizations.md).
* Megtudhatja, hogyan hozhat létre [rácsvonalat a munkafüzetekben](workbooks-grid-visualizations.md).
