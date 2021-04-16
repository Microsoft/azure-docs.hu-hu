---
title: Külső alkalmazásokkal és alkalmazásokkal kapcsolatos kompatibilitási Azure Synapse Analytics
description: Ismert problémákat ismertet, amelyek külső alkalmazások által a Azure Synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: sql
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: 3bad9d6464b41ff1b7ad03147d3a48c50c787cb0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568114"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Külső alkalmazásokkal és alkalmazásokkal kapcsolatos kompatibilitási Azure Synapse Analytics

A dedikált SQL SERVER alkalmazások zökkenőmentesen működnek Azure Synapse dedikált SQL-készletekkel. Bizonyos esetekben azonban előfordulhat, hogy a SQL Server gyakran használt funkciók és nyelvi elemek nem érhetők el a Azure Synapse, vagy másképp viselkednek.

Ez a cikk azokat az ismert problémákat sorolja fel, amelyek akkor előfordulhatnak, amikor külső alkalmazásokat Azure Synapse Analytics. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tableau-hiba: "A tranzakció befejezésére tett kísérlet meghiúsult. Nem található megfelelő tranzakció"

Egy dedikált SQL-készlet 10.0.11038.0-s verziójától kezdődően egyes, tárolt eljáráshívásokat illesztő Tableau-lekérdezések meghiúsulhatnak a következő hibaüzenettel: "**[Microsoft][ODBC Driver 17 for SQL Server][SQL Server]111214; Azure Synapse A tranzakció befejezésére tett kísérlet meghiúsult. Nem található megfelelő tranzakció.**"

### <a name="cause"></a>Ok

Ez a probléma a Azure Synapse SQL-készletben, amelyet az ODBC- és JDBC-illesztőprogramok automatikusan hívott új rendszer tárolt eljárásainak bevezetése okoz. A rendszer által tárolt eljárások egyike a nyitott tranzakciók megszakítását okozhatja, ha a végrehajtás meghiúsul. Ez a probléma az ügyfélalkalmazás logikájának függvényében fordulhat elő.

### <a name="solution"></a>Megoldás
A Tableau dedikált SQL-készletekhez csatlakoztatott tableau Azure Synapse az FMTONLY kapcsolót YES (IGEN) állapotúra kell állítaniuk az SQL-kapcsolatban. A Tableau Asztali és Tableau-kiszolgálóhoz egy Tableau adatforrás testreszabási (TDC) fájlt kell használnia annak biztosításához, hogy a Tableau átadja ezt a paramétert az illesztőnek.  

> [!NOTE] 
> A Microsoft nem nyújt támogatást külső eszközök számára. Bár teszteltük, hogy ez a megoldás működik-e a Tableau Desktop 2020.3.2-es verzióval, ezt az áthidaló megoldást a saját kapacitásán kell használnia.
>

* [A Tableau Desktop TDC-fájllal való globális testreszabásával kapcsolatban tekintse meg a Tableau Desktop dokumentációját.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [A Tableau-kiszolgálón található TDC-fájlokkal való globális testreszabással kapcsolatban lásd: Using a . TDC-fájl Tableau-kiszolgálóval.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

Az alábbi példában egy Tableau TDC-fájl látható, amely az FMTONLY=YES paramétert átadja az SQL kapcsolati sztringnek:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
A TDC-fájlok használatával kapcsolatos további részletekért forduljon a Tableau ügyfélszolgálatához. 

## <a name="see-also"></a>Lásd még

* [T-SQL nyelvi elemek dedikált SQL-készlethez a Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [A T-SQL-utasítások támogatottak a dedikált SQL-készlethez a Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-statements.md)