---
title: Az adatváltozások nyomon követése az Azure SQL Edge-ben
description: Tudnivalók a változások nyomon követéséről és az adatváltozások rögzítéséről az Azure SQL Edge-ben.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: dddaad3e171c757b353deb81ffcb77cfbe706340
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98108262"
---
# <a name="track-data-changes-in-azure-sql-edge"></a>Az adatváltozások nyomon követése az Azure SQL Edge-ben

Az Azure SQL Edge a két SQL Server funkciót támogatja, amelyek nyomon követik az adatbázisban tárolt adatváltozásokat: [változások követése](/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) és [adatváltozások rögzítése](/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Ezek a funkciók lehetővé teszik az alkalmazások számára, hogy meghatározzák az adatbázis felhasználói tábláiban végrehajtott adatmódosítási nyelvi módosításokat (INSERT, Update és DELETE művelet). Engedélyezheti az adatváltozások rögzítését és a változások nyomon követését ugyanazon az adatbázison. Nincs szükség különleges szempontokra.

Az adatbázisokban megváltoztatott Adatlekérdezési képesség fontos követelmény, hogy egyes alkalmazások hatékonyak legyenek. Az adatváltozások meghatározásához általában az alkalmazás fejlesztőknek eseményindítókat, időbélyeg-oszlopokat és további táblázatokat használó egyéni követési módszert kell alkalmazniuk az alkalmazásaikban. Ezeknek az alkalmazásoknak a létrehozása általában sok munkát igényel a megvalósításhoz, a séma frissítéseihez vezet, és gyakran nagy teljesítményű terhelést eredményez.

Ha egy IoT-megoldás esetében rendszeresen át kell helyeznie az adatait a szélétől egy felhőbe vagy adatközpontba, a Change Tracking nagyon hasznos lehet. A felhasználók gyorsan és hatékonyan le tudják kérdezni a legutóbbi szinkronizálás változásait, és feltöltik ezeket a változtatásokat a felhőbe vagy az adatközpontba. További részletekért lásd: [az adatváltozások rögzítésének vagy a változások követésének használatának előnyei](/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Ez a két funkció nem azonos. További információ: az [adatváltozások rögzítése és a változások követése közötti különbségek](/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Adatváltozások rögzítése

A szolgáltatás működésének részleteiért lásd: [az adatváltozások rögzítésének](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)áttekintése.

A szolgáltatás engedélyezésének és letiltásának megismeréséhez lásd: [az adatváltozások rögzítésének engedélyezése és letiltása](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server).

A szolgáltatás felügyeletéhez és figyeléséhez lásd: [az adatváltozások rögzítésének felügyelete és figyelése](/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server).

A módosított adatmennyiségek lekérdezéséről és használatáról további információt az [adatváltozások kezelésével foglalkozó](/sql/relational-databases/track-changes/work-with-change-data-sql-server)témakörben talál.

> [!NOTE]
> Az Azure SQL Edge nem támogatja az olyan adatrögzítési függvények módosítását, amelyek CLR-tól függenek.

## <a name="change-tracking"></a>Változások követése

A funkció működésének részletes ismertetését lásd: [a Change Tracking](/sql/relational-databases/track-changes/about-change-tracking-sql-server)ismertetése.

A szolgáltatás engedélyezésének és letiltásának megismeréséhez lásd: a [Változások követésének engedélyezése és letiltása](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server).

A szolgáltatás felügyeletéhez, figyeléséhez és kezeléséhez lásd: a [Változások követésének felügyelete és figyelése](/sql/relational-databases/track-changes/manage-change-tracking-sql-server).

A módosított adatmennyiségek lekérdezéséről és használatáról további információt az [adatváltozások kezelésével foglalkozó](/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)témakörben talál.

## <a name="temporal-tables"></a>Historikus táblák

Az Azure SQL Edge a SQL Server időbeli táblázatok funkcióját is támogatja. Ez a funkció (más néven *rendszerverzióval ellátott időszakos táblázatok*) beépített támogatást nyújt a táblázatban tárolt adatokkal kapcsolatos információk biztosításához. A funkció nem csupán az aktuális időpontnál helyes adatokról nyújt információt.

A rendszerverzióval ellátott időbeli táblázat egy olyan felhasználói tábla, amely az adatváltozások teljes előzményeinek megtartására szolgál, és lehetővé teszi az egyszerű időponton belüli elemzést. Az ilyen típusú ideiglenes táblát rendszerverzióval ellátott időbeli táblázatnak nevezzük, mert az egyes sorok érvényességi idejét a rendszer (azaz az adatbázismotor) kezeli.

Minden időbeli táblázat két explicit módon definiált oszlopot tartalmaz, amelyek mindegyike `datetime2` adattípussal rendelkezik. Ezeket az oszlopokat a rendszer *időszak* oszlopainak nevezzük. A rendszer ezeket az időtartam oszlopokat kizárólag az egyes sorok érvényességi idejének rögzítésére használja, amikor egy sor módosul.

Ezen időszak oszlopain kívül egy időbeli táblázat egy másik, tükrözött sémával rendelkező táblára mutató hivatkozást is tartalmaz. A rendszer ezt a táblázatot használja a sor előző verziójának automatikus tárolására, valahányszor az időbeli táblázat egy sora frissül vagy törölve lesz. Ezt a további táblázatot az *előzmény* táblázatnak nevezzük, míg az aktuális (tényleges) sor verzióját tároló főtábla az *aktuális* táblázat, vagy egyszerűen csak az időbeli tábla. Az időbeli tábla létrehozásakor a felhasználók megadhatnak egy meglévő History táblát (a sémának megfelelőnek kell lennie), vagy lehetővé kell tenniük a rendszer számára az alapértelmezett előzmények tábla létrehozását.

További információ: [időbeli táblák](/sql/relational-databases/tables/temporal-tables).

## <a name="next-steps"></a>Következő lépések

- [Adatfolyamok az Azure SQL Edge-ben ](stream-data.md)
- [Gépi tanulás és AI a ONNX az Azure SQL Edge-ben ](onnx-overview.md)
- [Replikáció konfigurálása az Azure SQL Edge-be](configure-replication.md)
- [Adatbázisok biztonsági mentése és visszaállítása az Azure SQL Edge-ben](backup-restore.md)
