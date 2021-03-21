---
title: Elastic Database eszközök szószedete
description: A rugalmas adatbázis-eszközökhöz használt használati feltételek magyarázata
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 166e365a4ead8ad6d0f7e543c081161ebff5f027
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330461"
---
# <a name="elastic-database-tools-glossary"></a>Elastic Database eszközök szószedete
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A [Elastic Database eszközökhöz](elastic-scale-introduction.md)a következő kifejezések vannak meghatározva. Az eszközök a szegmenses [térképek](elastic-scale-shard-map-management.md)felügyeletére szolgálnak, és tartalmazzák az [ügyféloldali kódtárat](elastic-database-client-library.md), a [felosztott egyesítési eszközt](elastic-scale-overview-split-and-merge.md), a [rugalmas készleteket](elastic-pool-overview.md)és a [lekérdezéseket](elastic-query-overview.md). 

Ezek a kifejezések a szegmensek [Elastic Database eszközökkel való hozzáadásával](elastic-scale-add-a-shard.md) , valamint [a recoverymanager osztállyal osztály használatával határozzák meg a szilánkok térképével kapcsolatos problémákat](elastic-database-recovery-manager.md).

![Rugalmas skálázási feltételek][1]

**Adatbázis**: Azure SQL Database található adatbázis. 

**Adatfüggő útválasztás**: az a funkció, amely lehetővé teszi, hogy egy alkalmazás egy adott szegmenshez kapcsolódjon. Tekintse meg [az Adatfüggő útválasztást](elastic-scale-data-dependent-routing.md). Összehasonlítás **[több szegmenses lekérdezéssel](elastic-scale-multishard-querying.md)**.

**Globális** szegmenses Térkép: a horizontális Felskálázási kulcsok és a hozzájuk tartozó szilánkok közötti **Térkép.** A globális szegmenses térképet a rendszer a szegmens **map Managerben** tárolja. Összehasonlítás a **helyi szegmenses térképsel**.

Szegmenses **Térkép listázása**: egy szegmenses Térkép, amelyben a horizontális Felskálázási kulcsok külön vannak leképezve. Összehasonlítás a **tartományhoz** tartozó felosztási térképsel.   

**Helyi** szegmenses Térkép: egy szegmensen tárolva a helyi szegmens Térkép a szegmensen található shardletek leképezéseit tartalmazza.

**Több szegmenses lekérdezés**: a lekérdezések több szegmensre való kibocsátásának lehetősége; az eredmények készleteit a UNION összes szemantika (más néven "kipróbálható lekérdezés") használatával kapjuk vissza. Összehasonlítás az **Adatfüggő útválasztással**.

**Több-bérlős** és **egybérlős**: ez egy egybérlős adatbázist és egy több-bérlős adatbázist mutat be:

![Képernyőkép, amely egy egybérlős adatbázist és egy több-bérlős adatbázist mutat be.](./media/elastic-scale-glossary/multi-single-simple.png)

Itt láthatja **a több** -bérlős adatbázisok szétválasztását. 

![Egy-és több-bérlős adatbázisok](./media/elastic-scale-glossary/shards-single-multi.png)

**Tartományhoz tartozó szegmensek térképe**: egy olyan szegmenses Térkép, amelyben a szegmens elosztási stratégia több összefüggő értéken alapul. 

**Hivatkozási táblázatok**: nem szétszórt, de a szegmensekben replikált táblák. A zip-kódok például egy hivatkozási táblában tárolhatók. 

Szegmens **: a** Azure SQL Database egy adatbázisa, amely egy szilánkokra bontott adatkészletből tárolja az adatok adatait. 

**Szilánk rugalmassága**: **horizontális skálázás** és **vertikális skálázás** is elvégezhető.

Többszintű **táblázatok**: horizontálisan tagolt táblázatok, azaz amelyek az adatok szétosztása a szegmensek között a horizontálisan használt értékek alapján történik. 

Horizontális Felskálázási **kulcs**: egy oszlop értéke, amely meghatározza, hogy az adatszegmensek hogyan oszlanak el. Az érték típusa a következők egyike lehet: **int**, **bigint**, **varbinary** vagy **uniqueidentifier**. 

Szegmens **készlet**: a szegmenses Térkép kezelőjében azonos szegmenses térképhez rendelt szegmensek gyűjteménye.  

**Shardletbe**: a szegmensben lévő horizontális Felskálázási kulcs egyetlen értékével társított összes adatmennyiség. A shardletbe az adatáthelyezés legkisebb egysége, amely a felosztott táblák újraterjesztésekor lehetséges. 

Szegmenses **Térkép**: a horizontális Felskálázási kulcsok és a hozzájuk tartozó szegmensek közötti leképezések halmaza.

Szegmenses **Térkép kezelője**: olyan felügyeleti objektum és adattár, amely egy vagy több szegmenshez tartozó szegmensi leképezéseket, szilánkokat és leképezéseket tartalmaz.

![Az ábrán egy shardmaps_globalhoz, shards_globalhoz és shard_mappings_globalhoz társított szegmenses Térkép-kezelő látható.][2]

## <a name="verbs"></a>Parancsok
**Horizontális skálázás**: az alábbi ábrán látható szegmensek kibővítésének vagy eltávolításának elvégzése a szegmensek egy szegmensbe való felvételével.

![Horizontális és vertikális skálázás][3]

**Egyesítés**: a shardletek két szegmensből egy szegmensbe való mozgatásával, és ennek megfelelően frissíti a szegmenses térképet.

**Shardletbe Move**: egyetlen shardletbe áthelyezése egy másik szegmensbe. 

Szegmens: horizontálisan particionálja az azonos módon strukturált adategységeket több adatbázis között egy vízszintű kulcs alapján.

**Felosztás**: több shardletek áthelyezése az egyik szegmensből egy másikba (jellemzően új) szegmensbe. A horizontális Felskálázási kulcsot a felhasználó a felosztott pontként kapja meg.

**Vertikális skálázás**: az egyes szegmensek számítási méretének méretezése (vagy le). Például ha a standard és a prémium közötti szegmenst szeretné módosítani (ami több számítási erőforrást eredményez). 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-glossary/glossary.png
[2]: ./media/elastic-scale-glossary/mappings.png
[3]: ./media/elastic-scale-glossary/h_versus_vert.png

