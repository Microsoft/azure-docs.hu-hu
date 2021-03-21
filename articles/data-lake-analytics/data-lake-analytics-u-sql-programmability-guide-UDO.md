---
title: U-SQL UDO programozható útmutató a Azure Data Lake
description: Ismerje meg a U-SQL UDO programozható Azure Data Lake Analyticsét, hogy lehetővé tegye a jó USQL-parancsfájlok létrehozását.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512577"
---
# <a name="u-sql-user-defined-objects-overview"></a>A felhasználó által definiált U-SQL-objektumok áttekintése


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: felhasználó által definiált objektumok: UDO
Az U-SQL lehetővé teszi, hogy egyéni programozható objektumokat határozzon meg, amelyeket felhasználó által definiált objektumoknak vagy UDO-nek nevezünk.

A következő lista az U-SQL-ben UDO-t tartalmazza:

* Felhasználó által definiált kivonók
    * Sor kibontása sorba
    * Egyéni strukturált fájlokból származó adatok kinyerésének megvalósítására használatos

* Felhasználó által definiált előszerkesztők
    * Kimeneti sor soronként
    * Egyéni adattípusok vagy egyéni fájlformátumok kimenetére használatos

* Felhasználó által definiált processzorok
    * Egy sor készítése és egy sor létrehozása
    * Az oszlopok számának csökkentésére vagy új oszlopok előállítására szolgál, amelyek egy meglévő oszlopból származtatott értékekkel rendelkeznek.

* Felhasználó által definiált beadási típusok
    * Egy sor elkészítése és 0 – n sor létrehozása
    * KÜLSŐ/KERESZTtel használatos

* Felhasználó által definiált kombinálók
    * A sorhalmazok egyesítése – felhasználó által definiált illesztések

* Felhasználó által definiált szűkítők
    * N sor készítése és egy sor létrehozása
    * A sorok számának csökkentésére használatos

UDO általában a U-SQL-parancsfájlban explicit módon van meghívva a következő U-SQL-utasítások részeként:

* KINYERÉS
* KIMENETI
* FOLYAMAT
* EGYESÍTÉSE
* CSÖKKENTHETI

> [!NOTE]  
> A UDO-k 0,5 GB memóriát használnak fel.  Ez a memória-korlátozás nem vonatkozik a helyi végrehajtásokra.

## <a name="next-steps"></a>Következő lépések
* [A U-SQL programozható útmutatója – áttekintés](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programozható útmutató – UDT és UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)