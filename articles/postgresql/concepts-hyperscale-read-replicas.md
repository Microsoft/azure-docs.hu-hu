---
title: Replikák olvasása-Azure Database for PostgreSQL-nagy kapacitású (Citus)
description: Ez a cikk a Azure Database for PostgreSQL-nagy kapacitású (Citus) olvasási replika szolgáltatását ismerteti.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cf8c64bf3858f0d3b7d633b94bc7302fbe563f87
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023963"
---
# <a name="read-replicas-in-azure-database-for-postgresql---hyperscale-citus"></a>Replikák olvasása a Azure Database for PostgreSQL-nagy kapacitású (Citus)

> [!IMPORTANT]
> A nagy kapacitású-ben (Citus) jelenleg előzetes verzióban érhető el a replikák. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
>
> Megtekintheti a [nagy kapacitású (Citus) előzetes](hyperscale-preview-features.md)verziójának új funkcióinak teljes listáját.

Az olvasási replika funkció lehetővé teszi, hogy egy nagy kapacitású-(Citus-) kiszolgálócsoport adatait egy írásvédett kiszolgálói csoportba replikálja. A replikák **aszinkron módon** frissülnek a PostgreSQL fizikai replikációs technológiával. Az elsődleges kiszolgálóról korlátlan számú replikára lehet replikálni.

A replikák olyan új kiszolgálócsoportok, amelyeket a normál nagy kapacitású-(Citus-) kiszolgálócsoportokhöz hasonlóan kezel. Az egyes olvasási replikák esetében a virtuális mag és a Storage szolgáltatásban a kiépített számítási kapacitást a GB/hó értékben számítjuk fel.

Megtudhatja, hogyan [hozhat létre és kezelhet replikákat](howto-hyperscale-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Mikor használjon olvasási replikát

Az olvasási replika funkcióval javítható a nagy olvasási igényű számítási feladatok teljesítménye és méretezése. Az olvasási munkaterhelések elkülöníthetők a replikákkal, míg az írási munkaterhelések az elsődlegesre irányíthatók.

Gyakori forgatókönyv, hogy a BI-és analitikai munkaterhelések az olvasási replikát használják adatforrásként a jelentéskészítéshez.

Mivel a replikák csak olvashatók, nem csökkentik közvetlenül a írási kapacitás terheit az elsődlegesen.

### <a name="considerations"></a>Megfontolandó szempontok

A szolgáltatás olyan forgatókönyvekhez készült, ahol a replikálási késés elfogadható, és a lekérdezések kiszervezését jelenti. Nem azt jelenti, hogy szinkron replikációs forgatókönyvek esetén a replika adatok várhatóan naprakészek. Az elsődleges és a replika között mérhető késés lesz. A késés a munkaterheléstől, valamint az elsődleges és a replika közötti késéstől függően perc vagy akár óra is lehet.  A replikán lévő adatértékek végül konzisztensek az elsődlegesen tárolt értékekkel. Használja ezt a szolgáltatást olyan számítási feladatokhoz, amelyek alkalmasak erre a késésre. 

## <a name="create-a-replica"></a>Replika létrehozása

A replika létrehozása munkafolyamat indításakor létrejön egy üres nagy kapacitású-(Citus-) kiszolgálócsoport. Az új csoport az elsődleges kiszolgálócsoport-csoportba tartozó adattal van feltöltve. A létrehozási idő az elsődleges és az utolsó heti teljes biztonsági mentés óta eltelt idő mennyiségétől függ. Az idő néhány perctől akár több órára is terjedhet.

Az olvasási replika funkció PostgreSQL fizikai replikálást, nem logikai replikálást használ. Az alapértelmezett mód a replikálási tárolóhelyek használatával történő replikálás.
Ha szükséges, a rendszer naplózza a szállítást a felzárkózás érdekében.

Megtudhatja, hogyan [hozhat létre olvasási replikát a Azure Portalban](howto-hyperscale-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Kapcsolódás replikához

Replika létrehozásakor nem örökli a tűzfalszabályok az elsődleges kiszolgáló csoportjának szabályait. Ezeket a szabályokat a replika egymástól függetlenül kell beállítani.

A replika örökli a felügyeleti ("citus") fiókot az elsődleges kiszolgáló csoportjából.
Az összes felhasználói fiók replikálódik az olvasási replikára. Csak olvasási replikához csatlakozhat az elsődleges kiszolgálón elérhető felhasználói fiókok használatával.

A replika koordinátori csomópontjára a hostname és egy érvényes felhasználói fiók használatával kapcsolódhat, ahogyan azt egy normál nagy kapacitású-(Citus-) kiszolgálócsoport esetében tenné.
A **replika** és a rendszergazdai Felhasználónév **citus** nevű kiszolgáló esetén a psql használatával kapcsolódhat a replika koordinátori csomópontjára:

```bash
psql -h c.myreplica.postgres.database.azure.com -U citus@myreplica -d postgres
```

A parancssorba írja be a felhasználói fiókhoz tartozó jelszót.

## <a name="considerations"></a>Megfontolandó szempontok

Ez a szakasz az olvasási replika szolgáltatással kapcsolatos szempontokat foglalja össze.

### <a name="new-replicas"></a>Új replikák

Az olvasási replika új nagy kapacitású-(Citus-) kiszolgálócsoportként jön létre. Meglévő kiszolgálócsoport nem hozható létre replikába. Egy másik olvasási replika replikája nem hozható létre.

### <a name="replica-configuration"></a>Replika konfigurációja

A replikát a rendszer a számítási, tárolási és feldolgozó csomópont-beállítások alapján hozza létre elsődlegesként. A replika létrehozása után több beállítás is módosítható, beleértve a tárolási és a biztonsági mentés megőrzési idejét is. Más beállítások nem módosíthatók replikákban, például a tárolási méret és a munkavégző csomópontok száma.

Ne feledje, hogy a replikák elég erősek maradnak ahhoz, hogy megőrizze az elsődlegestől érkező változásokat. Ügyeljen arra, hogy a számítási teljesítmény a replikákban legyen felskálázás, ha az elsődlegesen a méretezést végzi.

A tűzfalszabályok és a paraméterek beállításai nem öröklődnek az elsődleges kiszolgálóról a replikára a replika létrehozásakor vagy azt követően.

### <a name="regions"></a>Régiók

A nagy kapacitású-(Citus-) kiszolgálócsoportok csak egyrégiós replikációt támogatnak.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portalban](howto-hyperscale-read-replicas-portal.md).
