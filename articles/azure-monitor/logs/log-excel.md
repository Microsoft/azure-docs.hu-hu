---
title: A Log Analytics és az Excel integrálása
description: Log Analytics lekérdezés beszerzése az Excelben és az eredmények frissítése az Excelben.
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: f2834e9bd91ecbbf32e0321179c2359862a5b605
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041110"
---
# <a name="integrate-log-analytics-and-excel"></a>A Log Analytics és az Excel integrálása

Az M Query és a Log Analytics API használatával integrálhatja Azure Monitor Log Analytics és a Microsoft Excel alkalmazást. Ez az integráció lehetővé teszi legfeljebb 500 000 rekord küldését az Excel programba, feltéve, hogy az eredmények teljes mennyisége nem haladja meg a 61MiB.

> [!NOTE]
> Mivel az Excel egy helyi ügyfélalkalmazás, a helyi hardverek és szoftverek korlátozásai befolyásolják a teljesítményt és a nagy adatkészletek feldolgozásának képességét.

## <a name="create-your-m-query-in-log-analytics"></a>Az M-lekérdezés létrehozása Log Analytics 

1. **Hozzon létre és futtasson lekérdezést** a log Analyticsben, ahogy azt általában tenné. Ne aggódjon, ha a felhasználói felületen a 10 000-es rekordokra vonatkozó korlátozás szerepel.  Javasoljuk, hogy relatív dátumokat használjon – például a "ago" függvényt vagy a felhasználói felületi időt választót – az Excel frissíti a megfelelő adathalmazt.
  
2. **Lekérdezés exportálása** – Ha elégedett a lekérdezéssel és annak eredményeivel, exportálja a lekérdezést az m-re az *Exportálás* menü log Analytics **Exportálás Power bi (m lekérdezés)** menüjébe:

:::image type="content" source="media/log-excel/export-query.png" alt-text="Log Analytics lekérdezés az adatés exportálási lehetőséggel" border="true":::



Ezzel a lehetőséggel letölt egy. txt fájlt, amely tartalmazza az Excelben használható M kódot.

A fenti lekérdezés a következő M kód exportálását mutatja be. Íme egy példa a lekérdezéshez exportált M-kódra a példában:

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>Lekérdezés összekötése az Excelbe 

A lekérdezés importálásához. 

1. Nyissa meg a Microsoft Excelt. 
1. A menüszalagon lépjen az **adatmenüre** . Válassza **az adatlekérdezés** lehetőséget. **Más forrásokból** válassza az **üres lekérdezés** lehetőséget:
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Importálás az Excel programból üresen" border="true":::

1. A Power Query ablakban válassza a **speciális szerkesztő** elemet:

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Excel speciális lekérdezés-szerkesztő" border="true":::

 
1. Cserélje le a speciális szerkesztőben lévő szöveget a Log Analyticsból exportált lekérdezésre:

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="Üres lekérdezés létrehozása" border="true":::
 
1. Válassza a **kész**, majd a **betöltés és bezárás** lehetőséget. Az Excel a log Analytics API használatával hajtja végre a lekérdezést, és az eredményhalmaz ekkor megjelenik.
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Lekérdezés eredményei az Excelben" border="true":::

> [!Note]
> Ha a rekordok száma a vártnál kisebb, akkor az eredmények mennyisége túllépte a 61MiB korlátot. Próbálkozzon a `project` vagy `project-away` a lekérdezésével, hogy korlátozza az oszlopokat, amelyekre szüksége van.

##  <a name="refreshing--data"></a>Adatfrissítés

Az adatok közvetlenül az Excelből is frissíthetők. **Az Excel menüszalag adatmenü csoportjában** kattintson a **frissítés** gombra.
 
## <a name="next-steps"></a>Következő lépések

Az Excel külső adatforrásokkal való integrálásával kapcsolatos további információkért lásd: [adatok importálása külső adatforrásokból (Power Query)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)