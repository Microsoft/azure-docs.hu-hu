---
title: Megosztott metaadat-modell
description: Az Azure szinapszis Analytics lehetővé teszi, hogy a különböző munkaterület-számítási motorok adatbázisokat és táblákat osszanak meg a kiszolgáló nélküli Apache Spark készletek, a kiszolgáló nélküli SQL-készlet és a dedikált SQL-készletek között.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: b10b6f011fa7daee4094f0cc7b819d36127fedcd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460341"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Azure szinapszis Analytics megosztott metaadatok

Az Azure szinapszis Analytics lehetővé teszi, hogy a különböző munkaterület-számítási motorok adatbázisokat és táblákat osszanak meg kiszolgáló nélküli Apache Spark készletei és kiszolgáló nélküli SQL-készlete között.

A megosztás támogatja az úgynevezett modern adattárház-mintát, és lehetővé teszi a munkaterület SQL-motorjai számára a Spark használatával létrehozott adatbázisok és táblák elérését. Azt is lehetővé teszi, hogy az SQL-motorok olyan saját objektumokat hozzanak létre, amelyek nem a többi motorral vannak megosztva.

## <a name="support-the-modern-data-warehouse"></a>A modern adattárház támogatása

A megosztott metaadat-modell a következő módon támogatja a modern adattárház-mintát:

1. A rendszer az adatközpontból származó adatok előkészítését és szerkezetbe állítását hatékonyan végzi el, ha az előkészített (esetlegesen particionált) parketta-alapú táblákat a valószínűleg több adatbázisban tárolja.

2. A Spark által létrehozott adatbázisok és minden táblázat láthatóvá válik az Azure szinapszis-munkaterület Spark Pool-példányaiban, és bármely Spark-feladatból felhasználható. Ez a képesség az [engedélyek](#security-model-at-a-glance) hatálya alá tartozik, mivel a munkaterület összes Spark-készlete ugyanazt a mögöttes katalógus-metaadatokat használja.

3. A Spark által létrehozott adatbázisok és a parketta által támogatott táblázatok láthatóvá válnak a munkaterület kiszolgáló nélküli SQL-készletében. Az [adatbázisok](database.md) automatikusan létrejönnek a kiszolgáló nélküli SQL-készlet metaadataiban, és a Spark-feladatok által létrehozott [külső és felügyelt táblák](table.md) is elérhetők külső táblákként a kiszolgáló nélküli SQL-készlet metaadataiban a `dbo` megfelelő adatbázis sémájában. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Az objektum szinkronizálása aszinkron módon történik. Az objektumok csak néhány másodperc elteltével fognak megjelenni az SQL-környezetben. Amint megjelentek, lekérdezheti, de nem frissítheti és nem változtathatja meg azokat az SQL-motorokat, amelyek hozzáférnek hozzájuk.

## <a name="shared-metadata-objects"></a>Megosztott metaadat-objektumok

A Spark segítségével adatbázisok, külső táblák, felügyelt táblák és nézetek hozhatók létre. Mivel a Spark-nézetek megkövetelik, hogy a Spark-motor feldolgozza a definiált Spark SQL-utasítást, és nem dolgozható fel egy SQL-motor, csak az adatbázisok és a bennük tárolt külső és felügyelt táblák vannak megosztva a munkaterület SQL Engine használatával. A Spark-nézetek csak a Spark Pool-példányok között vannak megosztva.

## <a name="security-model-at-a-glance"></a>Biztonsági modell áttekintése

A Spark-adatbázisok és-táblák, valamint az SQL-motorban szinkronizált ábrázolások a mögöttes tárolási szinten vannak biztosítva. Ha a táblát bármely olyan motor kérdezi le, amelyet a lekérdezés küldője jogosult használni, a lekérdezés elküldő rendszerbiztonsági tagja átkerül az alapul szolgáló fájlokra. Az engedélyezés ellenőrzésére a fájlrendszer szintjén kerül sor.

További információ: az [Azure szinapszis Analytics megosztott adatbázisa](database.md).

## <a name="change-maintenance"></a>Karbantartás módosítása

Ha egy metaadat-objektumot törölnek vagy módosítanak a Sparktal, a módosítások bekerülnek a kiszolgáló nélküli SQL-készletbe, és propagálva lesznek. A szinkronizálás aszinkron módon történik, és a módosítások rövid késleltetés után az SQL-motorban is megjelennek.

## <a name="next-steps"></a>Következő lépések

- [További információ az Azure szinapszis Analytics megosztott metaadat-adatbázisairól](database.md)
- [További információ az Azure szinapszis Analytics megosztott metaadatait tartalmazó tábláiról](table.md)

