---
title: Azure Monitor munkafüzetek – JSON-alapú adatátalakítás a JSONPath
description: A JSONPath használata Azure Monitor munkafüzetekben a lekérdezett végpont által visszaadott JSON-adatmennyiségnek a kívánt formátumra való átalakításához.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: 142853f0d5ed787d0b7aaee1bb118a001f144227
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722925"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>JSON-adatfájlok átalakítása a JSONPath használatával a munkafüzetekbe

A munkafüzetek számos forrásból származó adatok lekérdezésére képesek. Egyes végpontok, például a [Azure Resource Manager](../../azure-resource-manager/management/overview.md) vagy az egyéni végpontok, a JSON-ban lévő eredményeket adhatnak vissza. Ha a lekérdezett végpont által visszaadott JSON-adatmennyiség nem a kívánt formátumban van konfigurálva, a JSONPath az eredmények átalakítására is használható.

A JSONPath a JSON lekérdezési nyelve, amely az XML XPath-hoz hasonló. Az XPath-hoz hasonlóan a JSONPath lehetővé teszi, hogy a JSON-struktúraből kinyerje az adatgyűjtést és a szűrést.

A JSONPath-átalakítás használatával a munkafüzet szerzője táblázatos struktúrába konvertálhatja a JSON-t. A tábla ezután felhasználható a [munkafüzetek vizualizációinak](./workbooks-overview.md#visualizations)ábrázolására.

## <a name="using-jsonpath"></a>A JSONPath használata

1. Az eszköztár *szerkesztése* elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. *A Hozzáadás*  >  *lekérdezés* hozzáadása hivatkozásra kattintva hozzáadhat egy lekérdezés vezérlőelemet a munkafüzethez.
3. Válassza ki az adatforrást *JSON*-ként.
4. A JSON-szerkesztő használatával adja meg a következő JSON-kódrészletet
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

Tegyük fel, hogy a fenti JSON-objektumot az áruház leltárának ábrázolása adjuk meg. A feladatunk az áruházban elérhető könyvek táblázatának létrehozása a címeik, szerzők és díjak listázásával.

1. Válassza ki az *eredmény beállítások* lapot, és az eredmény formátumát állítsa a *JSON-útvonalra*.
2. Alkalmazza a következő JSON-elérésiút-beállításokat:

    JSON-elérésiút táblázata: `$.store.books` . Ez a mező a tábla gyökerének elérési útját jelöli. Ebben az esetben az áruház könyvének leltárát kell figyelni. A tábla elérési útja a JSON-t a könyv adataira szűri.

   | Oszlopok azonosítói | Oszlop JSON-elérési útja |
   |:-----------|:-----------------|
   | Cím      | `$.title`        |
   | Szerző     | `$.author`       |
   | Ár      | `$.price`        |

    Az oszlopok azonosítói az oszlopok fejlécei lesznek. A JSON-elérési utak mezői a tábla gyökeréből az oszlop értékére mutató útvonalat jelölik.

1. A fenti beállítások alkalmazása a *lekérdezés futtatása* gombra kattintva

![ Lekérdezési tétel szerkesztése JSON-adatforrással és a JSON-elérésiút eredményének formátumával](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Következő lépések
- [Munkafüzetek áttekintése](./workbooks-overview.md)
- [Csoportok Azure Monitor munkafüzetekben](workbooks-groups.md)