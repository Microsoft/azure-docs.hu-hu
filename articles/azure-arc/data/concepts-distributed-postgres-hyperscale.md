---
title: Fogalmak az adatterjesztéshez és a horizontális felskálázáshoz az arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport használatával
titleSuffix: Azure Arc enabled data services
description: Fogalmak az adatterjesztéshez az arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport használatával
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90939780"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>Fogalmak az adatterjesztéshez az arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport használatával

Ez a cikk azokat a főbb fogalmakat ismerteti, amelyek fontos szerepet játszanak az Azure arc-kompatibilis PostgreSQL-nagy kapacitású leginkább.
Az alább csatolt cikkek a Azure Database for PostgreSQL nagy kapacitású (Citus) kapcsolatos fogalmakat ismertetik. Ez ugyanaz a technológia, mint az Azure arc engedélyezve PostgreSQL nagy kapacitású, így ugyanazok a fogalmak és perspektívák érvényesek.

**Mi a különbség a közöttük?**
- _Azure Database for PostgreSQL – rugalmas skálázás (Citus)_

Ez az Azure-beli szolgáltatásként elérhető postgres-adatbázismotor nagy kapacitású formája. Ez a Citus-bővítmény, amely lehetővé teszi a nagy kapacitású élményt. Ebben az formátumban a szolgáltatás a Microsoft adatközpontokban fut, és a Microsoft működteti.

- _Azure arc engedélyezve PostgreSQL nagy kapacitású_

Ez az Azure arc-kompatibilis adatszolgáltatással elérhető postgres-adatbázismotor nagy kapacitású formája. Ezen az űrlapon ügyfeleink a rendszereket üzemeltető infrastruktúrát biztosítják, és működtetik azokat.

Az Azure arc-kompatibilis PostgreSQL-nagy kapacitású kapcsolatos főbb fogalmakat az alábbi táblázat foglalja össze:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>Csomópontok és táblák
Fontos tudni, hogy az Azure arc-kompatibilis postgres-nagy kapacitású leginkább az alábbi fogalmakat érdemes kihasználni:
- Speciális postgres-csomópontok az Azure arc-ban engedélyezve PostgreSQL nagy kapacitású: koordinátor és feldolgozók
- Táblák típusai: elosztott táblák, hivatkozási táblák és helyi táblák
- Szilánkok

További információ: [Azure Database for PostgreSQL-nagy kapacitású (Citus) csomópontokon és táblákon](../../postgresql/concepts-hyperscale-nodes.md). 

## <a name="determine-the-application-type"></a>Az alkalmazás típusának meghatározása
Az éppen felépített alkalmazás típusának egyértelműen azonosíthatónak kell lennie. Miért? Mivel a hatékony lekérdezések futtatása egy Azure arc-kompatibilis PostgreSQL-nagy kapacitású-kiszolgálócsoport esetében megköveteli, hogy a táblák megfelelően legyenek elosztva a kiszolgálók között. Az ajánlott terjesztés az alkalmazás típusától és a lekérdezési mintáktól függ. Az Azure arc-kompatibilis postgres-nagy kapacitású jól használható, széles körben használt alkalmazások:
- Több-bérlős alkalmazások
- Valós idejű alkalmazások

Az adatmodellezés első lépéseként meg kell határozni, hogy melyikük jobban hasonlít az alkalmazáshoz.

Az [alkalmazás típusának meghatározása](../../postgresql/concepts-hyperscale-app-type.md)című témakörben talál részleteket.


## <a name="choose-a-distribution-column"></a>Elosztási oszlop kiválasztása
Miért érdemes elosztott oszlopot választani?

Ez az egyik legfontosabb modellezési döntés. Az Azure arc-kompatibilis PostgreSQL-nagy kapacitású a sorok eloszlás oszlopának értéke alapján a szegmensekben lévő sorokat tárolja. A megfelelő választás a kapcsolódó adatokkal együtt ugyanazon fizikai csomópontokon csoportosítja a lekérdezéseket, így a lekérdezések gyorsak, és az összes SQL-funkció támogatását biztosítja. Helytelen választás miatt a rendszer lassan fut, és nem támogatja az összes csomóponton futó SQL-funkciót. Ez a cikk a két leggyakoribb nagy kapacitású forgatókönyvhöz nyújt terjesztési oszlopokra vonatkozó tippeket.

A részletek a [terjesztési oszlopok kiválasztása](../../postgresql/concepts-hyperscale-choose-distribution-column.md)szakaszban olvashatók.


## <a name="table-colocation"></a>Tábla közös elhelyezése

A közös elhelyezés a kapcsolódó információk ugyanazon csomópontokon történő tárolását ismerteti. A lekérdezések gyorsak lehetnek, ha az összes szükséges adatok hálózati forgalom nélkül elérhetők. A különböző csomópontokon található kapcsolódó adat-elhelyezés lehetővé teszi, hogy a lekérdezések hatékonyan, párhuzamosan fussanak az egyes csomópontokon.

Tekintse meg a részleteket a [táblázatos elhelyezésnél](../../postgresql/concepts-hyperscale-colocation.md).


## <a name="next-steps"></a>Következő lépések
- [További információ az Azure arc-kompatibilis PostgreSQL-nagy kapacitású létrehozásáról](create-postgresql-hyperscale-server-group.md)
- [További információ az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoportok az ív-adatvezérlőben létrehozott kibővítéséről](scale-out-postgresql-hyperscale-server-group.md)
- [További információ az Azure arc-kompatibilis Data Servicesról](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Az Azure arc áttekintése](https://aka.ms/azurearc)

