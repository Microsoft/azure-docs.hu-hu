---
title: Tömeges beszúrások optimalizálása – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan optimalizálható a tömeges beszúrási műveletek egy Azure Database for PostgreSQL egyetlen kiszolgálón.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 4d10f06577738364e3f4a0ea40221d37ebfb31c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86116284"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Tömeges beszúrások optimalizálása és átmeneti adatmennyiség használata egy Azure Database for PostgreSQL – egyetlen kiszolgálón 
Ez a cikk azt ismerteti, hogyan optimalizálható a tömeges beszúrási műveletek és hogyan használhatók az átmeneti adatok egy Azure Database for PostgreSQL kiszolgálón.

## <a name="use-unlogged-tables"></a>Nem naplózott táblák használata
Ha olyan munkaterhelési műveletekkel rendelkezik, amelyek átmeneti adatokat tartalmaznak, vagy nagyméretű adatkészleteket szúrnak be ömlesztve, érdemes lehet a nem naplózott táblákat használni.

A nem naplózott táblák egy PostgreSQL-szolgáltatás, amely hatékonyan használható a tömeges beszúrások optimalizálására. A PostgreSQL Write-Ahead naplózást (WAL) használ. A szolgáltatás alapértelmezés szerint az atomi és a tartósságot biztosítja. A sav tulajdonságait az atomenergia, a konzisztencia, az elkülönítés és a tartósság teszi elérhetővé. 

A nem naplózott táblázatba való Beszúrás azt jelenti, hogy a PostgreSQL a tranzakciónaplóba való írás nélkül beszúrja a naplóba, ami maga az I/O művelet. Ennek eredményeképpen ezek a táblázatok lényegesen gyorsabbak, mint a hagyományos táblák.

Nem naplózott tábla létrehozásához használja az alábbi beállításokat:
- Hozzon létre egy új, nem naplózott táblát a szintaxis használatával `CREATE UNLOGGED TABLE <tableName>` .
- Egy meglévő naplózott táblázat átalakítása nem naplózott táblára a szintaxis használatával `ALTER TABLE <tableName> SET UNLOGGED` .  

A folyamat megfordításához használja a szintaxist `ALTER TABLE <tableName> SET LOGGED` .

## <a name="unlogged-table-tradeoff"></a>Nem naplózott tábla kompromisszuma
A nem naplózott táblák nem rendelkeznek összeomlás-biztonsággal. Egy nem naplózott tábla automatikusan csonkítva lesz egy összeomlás után, vagy egy nem tiszta leállítás után. A nem naplózott tábla tartalmát szintén nem replikálja a rendszer készenléti kiszolgálókra. A nem naplózott táblán létrehozott indexek is automatikusan visszakerülnek a naplóba. Az INSERT művelet befejeződése után alakítsa át a táblát úgy, hogy a Beszúrás tartós legyen.

Egyes ügyfelek munkaterhelései körülbelül 15%-os teljesítménybeli javulást tapasztaltak, ha nem naplózott táblákat használtak.

## <a name="next-steps"></a>Következő lépések
Tekintse át az átmeneti adatok és a nagyméretű tömeges beszúrások felhasználásának munkaterhelését. Tekintse meg a következő PostgreSQL-dokumentációt:
 
- [Táblázatos SQL-parancsok létrehozása](https://www.postgresql.org/docs/current/static/sql-createtable.html)
