---
title: Adatmegőrzési szabályzat – áttekintés – Azure SQL Edge
description: Tudnivalók az Azure SQL Edge adatmegőrzési szabályzatáról
keywords: SQL Edge, adatmegőrzés
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: bb059a946c03f41e5b65944eec67070f84ee6b08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90976343"
---
# <a name="data-retention-overview"></a>Adatmegőrzés áttekintése

A csatlakoztatott IoT-eszközökről származó adatok gyűjtése és tárolása fontos szerepet játszik a működési és üzleti információk megszerzésében. Az ezekből az eszközökről származó adatok mennyisége miatt azonban fontos, hogy a szervezetek alaposan tervezzék meg a megőrizni kívánt adatok mennyiségét, és hogy milyen részletességgel rendelkeznek. Miközben minden részletességi adat megtartása szükséges, nem mindig praktikus. Emellett a megőrzött adatmennyiséget a IoT vagy a peremhálózati eszközökön elérhető tárterület mérete korlátozza. 

Az Azure SQL Edge adatbázis-rendszergazdák meghatározhatják az adatmegőrzési szabályzatot az SQL Edge-adatbázison és a mögöttes táblákon. Az adatmegőrzési házirend meghatározása után a háttérben futó rendszerfeladat a felhasználói táblákból származó elavult (régi) adatok törlését fogja futtatni. 

> [!Note]
> Az adatok a táblából való kitisztítása után nem helyreállítható. A törölt adatok helyreállításának egyetlen lehetséges módja, ha egy régebbi biztonsági másolatból állítja vissza az adatbázist.

Gyorsútmutatók:

- [Adatmegőrzési szabályzatok engedélyezése és letiltása](data-retention-enable-disable.md)
- [Korábbi adatok kezelése adatmegőrzési házirenddel](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>Az adatmegőrzés működése

Az adatmegőrzés konfigurálásához DDL-utasításokat használhat. További információért [engedélyezze és tiltsa le az adatmegőrzési házirendeket](data-retention-enable-disable.md). Az elavult rekordok automatikus törléséhez először engedélyeznie kell az adatmegőrzést mind az adatbázison, mind pedig az adatbázisban, amelyet törölni szeretne. 

Ha az adatmegőrzést egy táblához konfigurálta, a háttérben futó feladat egy tábla elavult rekordjainak azonosítására és a rekordok törlésére szolgál. Ha valamilyen okból kifolyólag a feladatok automatikus karbantartása nem fut, vagy nem tud lépést tartani a törléssel, akkor a manuális törlési művelet elvégezhető ezeken a táblákon. Az automatikus és a manuális karbantartással kapcsolatos további információkért tekintse meg az [automatikus és a manuális karbantartást](data-retention-cleanup.md).

## <a name="limitations-and-restrictions"></a>Korlátozások és korlátozások

- Ha engedélyezve van az adatok megőrzése, a automatikusan le lesz tiltva, ha az adatbázist egy teljes biztonsági másolatból állítja vissza, vagy újra csatolja. 
- Az adatmegőrzés nem engedélyezhető egy ideiglenes előzmény tábla esetében
- Az adatmegőrzési szűrő colomn nem módosítható. Az oszlop módosításához tiltsa le az adatok megőrzését a táblán.  

## <a name="next-steps"></a>Következő lépések

- [Machine learning és mesterséges intelligencia a ONNX az SQL Edge-ben](onnx-overview.md).
- [Végpontok közötti IoT-megoldás létrehozása az SQL Edge használatával IoT Edge segítségével](tutorial-deploy-azure-resources.md).
- [Adatfolyamok az Azure SQL Edge-ben](stream-data.md)
