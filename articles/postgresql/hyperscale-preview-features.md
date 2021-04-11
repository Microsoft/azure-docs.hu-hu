---
title: A Azure Database for PostgreSQL-nagy kapacitású (Citus) előzetes verziójának funkciói
description: A jelenleg előzetes verzióban elérhető szolgáltatások frissített listája
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: 775785e1b5130499d69b269e72f5c774e9e5f3f9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024070"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>A PostgreSQL-nagy kapacitású (Citus) előzetes verziójának funkciói

A Azure Database for PostgreSQL-nagy kapacitású (Citus) előzetes verziókat biztosít a nem kiadott funkciókhoz. Az előzetes verziójú verziók szolgáltatói szerződés nélkül érhetők el, és nem ajánlottak éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="features-currently-in-preview"></a>A jelenleg előzetes verzióban elérhető funkciók

A jelenleg előzetes verzióban elérhető funkciók a következők:

* **[Oszlopos tárolás](concepts-hyperscale-columnar.md)**.
  A kiválasztott Tables (sorok helyett) oszlopok tárolása a lemezen contiguously. Támogatja a lemezes tömörítést. Elemzési és adattárház-számítási feladatokhoz jó.
* **[PostgreSQL 12 és 13](concepts-hyperscale-versions.md)**.
  Használja az adatbázis legújabb verzióját a kiszolgálói csoportban.
* **[Alapszintű csomag](concepts-hyperscale-tiers.md)**. Csak koordinátori csomópont és munkavégző csomópontok használatával futtasson egy kiszolgálócsoport. Gazdaságos módszer a kezdeti tesztelésre és fejlesztésre, valamint a kisméretű éles munkaterhelések kezelésére.
* **[Olvasási replikák](howto-hyperscale-read-replicas-portal.md)** (jelenleg azonos régió). Az elsődleges kiszolgálócsoport által végrehajtott módosítások megjelennek a replikájában, és a replikára irányuló lekérdezések nem eredményeznek további terhelést az eredetinél.
  A replikák hasznos eszközt biztosítanak a csak olvasási feladatok teljesítményének javításához.
* **[Felügyelt PgBouncer](concepts-hyperscale-limits.md#managed-pgbouncer-preview)**.
  Egy kapcsolati Pooler, amely lehetővé teszi, hogy sok ügyfél egyszerre kapcsolódjon a kiszolgálói csoporthoz, miközben korlátozza az aktív kapcsolatok számát. Megfelel a kapcsolódási kérelmeknek, miközben a koordinátori csomópont zökkenőmentesen fut.
* **[PgAudit](concepts-hyperscale-audit.md)**. Részletes munkamenet-és objektum-naplózási naplózást biztosít a PostgreSQL szabványos naplózási létesítményén keresztül. A szolgáltatás olyan naplókat hoz létre, amelyek szükségesek bizonyos kormányzati, pénzügyi vagy ISO-minősítési auditok továbbításához.

### <a name="available-regions-for-preview-features"></a>Elérhető régiók az előzetes verziójú funkciókhoz

Az pgAudit bővítmény a [nagy kapacitású (Citus) által támogatott összes régióban](concepts-hyperscale-configuration-options.md#regions)elérhető.
A többi előzetes verziójú funkció csak az **USA keleti** régiójában érhető el.

## <a name="does-my-server-group-have-access-to-preview-features"></a>Hozzáférnek a kiszolgálói csoport az előzetes verziójú funkciókhoz?

Annak megállapításához, hogy a nagy kapacitású-(Citus-) kiszolgálócsoport rendelkezik-e előzetes verziójú szolgáltatásokkal, navigáljon a kiszolgálócsoport **Áttekintés** lapjára a Azure Portal.
Ha a következő tulajdonságot látja: alapszintű **(előzetes verzió)** vagy **szint: Standard (előzetes verzió)** , akkor a kiszolgálócsoport hozzáfér az előzetes verziójú funkciókhoz.

### <a name="how-to-get-access"></a>Hozzáférés kérése

Új nagy kapacitású-(Citus-) kiszolgálócsoport létrehozásakor jelölje be az **előzetes verziójú szolgáltatások engedélyezése** jelölőnégyzetet.

## <a name="contact-us"></a>Kapcsolat

Ossza meg velünk tapasztalatait az előzetes verziójú funkciók használatával, e-mailben küldje el a [PostgreSQL-hez készült Azure db](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)-t.
(Ez az e-mail cím nem technikai támogatási csatorna. Technikai problémák esetén nyisson meg egy [támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).)
