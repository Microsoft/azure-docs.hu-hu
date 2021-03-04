---
title: Függvények a Azure Monitor log lekérdezésekben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a függvények egy másik, Azure Monitorban található napló lekérdezésének meghívásához.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 9b2466bbe061dc147d020104b13da197840fe40a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030783"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Függvények használata Azure Monitor log-lekérdezésekben

Ha egy másik lekérdezéssel rendelkező naplózási lekérdezést szeretne használni, akkor azt függvényként mentheti. Ez lehetővé teszi, hogy leegyszerűsítse az összetett lekérdezéseket részekre bontással, és lehetővé teszi, hogy a közös kódokat több lekérdezéssel is felhasználhassa.

## <a name="create-a-function"></a>Függvény létrehozása

A **Save (Mentés** ) gombra kattintva hozzon létre egy Azure Portal log Analyticstel rendelkező függvényt, majd adja meg az adatokat a következő táblázatban.

| Beállítás | Leírás |
|:---|:---|
| Név           | A lekérdezés megjelenített neve a **query Explorerben**. |
| Mentés másként        | Függvény |
| Függvény aliasa | A függvény más lekérdezésekben való használatának rövid neve. A nem tartalmazhat szóközt, és egyedinek kell lennie. |
| Kategória       | A **lekérdezési Explorerben** mentett lekérdezések és függvények rendszerezésének kategóriája. |




## <a name="use-a-function"></a>Függvény használata
Használjon függvényt egy másik lekérdezésben szereplő aliassal együtt. Más táblákhoz hasonlóan is használható.

## <a name="function-parameters"></a>Függvény paraméterei 
Paramétereket adhat hozzá egy függvényhez, így bizonyos változók értékeit megadhatja a híváskor. Egy Resource Manager-sablon használatával az egyetlen lehetőség, hogy a függvényt a paraméterekkel együtt hozza létre. Példaként tekintse meg a [Resource Manager-sablonok mintáit a Azure monitor napló lekérdezéséhez](./resource-manager-log-queries.md#parameterized-function) .

## <a name="example"></a>Példa
A következő minta lekérdezés az elmúlt nap során jelentett összes hiányzó biztonsági frissítést visszaadja. Mentse ezt a lekérdezést függvényként az alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Hozzon létre egy másik lekérdezést, és hivatkozzon a _security_updates_last_day_ függvényre az SQL-hez kapcsolódó szükséges biztonsági frissítések kereséséhez.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Következő lépések
A Azure Monitor log-lekérdezések írásához további leckéket talál:

- [Sztringműveletek](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)
- [Dátum és idő típusú adatokkal végzett műveletek](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#date-and-time-operations)
- [Aggregátumfüggvények](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations)
- [Speciális aggregátumok](/azure/data-explorer/write-queries#advanced-aggregations)
- [JSON és adatstruktúrák](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#json-and-data-structures)
- [Illesztések](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)
- [Diagramok](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#charts)