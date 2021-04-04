---
title: A harmadik féltől származó alkalmazásokkal és az Azure szinapszis Analytics szolgáltatással való kompatibilitási problémák
description: A harmadik féltől származó alkalmazások által az Azure Szinapszisban talált ismert problémák ismertetése
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: a1031656eaa5125d07ae078773379270b26625e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98121379"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>A harmadik féltől származó alkalmazásokkal és az Azure szinapszis Analytics szolgáltatással való kompatibilitási problémák

Az SQL Server-hoz készült alkalmazások zökkenőmentesen együttműködnek az Azure szinapszis dedikált SQL-készletekkel. Bizonyos esetekben azonban előfordulhat, hogy a SQL Server gyakran használt szolgáltatások és nyelvi elemek nem érhetők el az Azure Szinapszisban, vagy eltérő módon működhetnek.

Ez a cikk felsorolja a harmadik féltől származó alkalmazások Azure szinapszis Analytics szolgáltatással való használata során felmerülő ismert problémákat. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tabló-hiba: "a tranzakció befejezésére tett kísérlet meghiúsult. Nem található megfelelő tranzakció. "

Az Azure szinapszis dedikált SQL Pool-verziójának 10.0.11038.0 kezdődően a tárolt eljárási hívásokat elkészítő tabló-lekérdezések sikertelenek lehetnek a következő hibaüzenettel: "**[Microsoft] [ODBC-illesztő 17 SQL Server] [SQL Server] 111214; Egy tranzakció befejezésére tett kísérlet sikertelen volt. Nem található megfelelő tranzakció.**"

### <a name="cause"></a>Ok

Ez az Azure szinapszis dedikált SQL-készlettel kapcsolatos probléma, amelyet az ODBC és a JDBC-illesztőprogramok által automatikusan hívott, új rendszertárolt eljárások bevezetése okozott. A rendszer által tárolt eljárások egyike azt eredményezheti, hogy a nyitott tranzakciók a végrehajtásuk meghiúsulása esetén megszakadnak. Ez a probléma az ügyfélalkalmazás logikájának függvényében fordulhat elő.

### <a name="solution"></a>Megoldás
Az ügyfelek ezt az adott problémát látják, amikor az Azure szinapszis dedikált SQL-készletekhez kapcsolódó tablót használ, az SQL-kapcsolat FMTONLY Igen értékre kell állítani. A tabló asztali és a tabló-kiszolgáló esetében a tabló adatforrás-testreszabási (TDC-) fájlját kell használnia annak biztosításához, hogy a tabló átadja ezt a paramétert az illesztőprogramnak.  

> [!NOTE] 
> A Microsoft nem nyújt támogatást harmadik féltől származó eszközökhöz. Habár teszteltük, hogy ez a megoldás együttműködik a tabló asztali 2020.3.2, ezt a megkerülő megoldást a saját kapacitására kell használni.
>

* [Ha szeretné megtudni, hogyan hozhatja ki a globális testreszabásokat egy TDC-fájllal a tabló Desktopban, tekintse meg a tabló asztali dokumentációját.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Ha szeretné megtudni, hogyan teheti meg a globális testreszabásokat egy TDC-fájllal a tabló-kiszolgálón, tekintse meg a következőt:. TDC-fájl a tabló-kiszolgálóval.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

Az alábbi példa egy olyan tabló TDC-fájlt mutat be, amely átadja a FMTONLY = YES paramétert az SQL-kapcsolódási karakterláncnak:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
A TDC-fájlok használatával kapcsolatos további információkért forduljon a tabló támogatásához. 

## <a name="see-also"></a>Lásd még

* [A T-SQL nyelvi elemei a dedikált SQL-készlethez az Azure szinapszis Analyticsben.](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [A T-SQL-utasítások támogatottak a dedikált SQL-készletekhez az Azure szinapszis Analyticsben.](./sql-data-warehouse-reference-tsql-statements.md)