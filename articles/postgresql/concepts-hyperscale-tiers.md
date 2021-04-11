---
title: Alapszintű előzetes verzió – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: A Azure Database for PostgreSQL-nagy kapacitású (Citus) egycsomópontos alapszintű csomagja
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c8cad5eb0983715a7cc7c18249243e93a2c498d7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023987"
---
# <a name="basic-tier-preview"></a>Alapszintű csomag (előzetes verzió)

> [!IMPORTANT]
> A nagy kapacitású (Citus) alapszintű csomag jelenleg előzetes verzióban érhető el.  Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
>
> Megtekintheti a [nagy kapacitású (Citus) előzetes](hyperscale-preview-features.md)verziójának új funkcióinak teljes listáját.

A Azure Database for PostgreSQL-nagy kapacitású (Citus) alapszintű csomagja egy egyszerű módszer egy olyan kis kiszolgálócsoport létrehozására, amelyet később is méretezheti. Míg a standard szintű kiszolgálócsoportok egy koordinátori csomóponttal rendelkeznek, és legalább két feldolgozó csomóponttal rendelkeznek, az alapszintű csomag egyetlen adatbázis-csomóponton fut le.

A kevesebb csomópontot használva az alapszintű csomag a standard szint összes funkcióját tartalmazza. A standard szinthez hasonlóan a szolgáltatás a magas rendelkezésre állást, az olvasási replikákat és az oszlopos Table Storage-t is támogatja.

## <a name="choosing-basic-vs-standard-tier"></a>Az alapszintű vs standard csomag kiválasztása

Az alapszintű csomag gazdaságos és kényelmes üzembe helyezési lehetőséget kínál a kezdeti fejlesztéshez, teszteléshez és folyamatos integrációhoz. Egyetlen adatbázis-csomópontot használ, és ugyanazt az SQL API-t jeleníti meg, mint a standard szint. Az alapszintű és a későbbi [diplomával rendelkező alkalmazásokat a standard](howto-hyperscale-scale-grow.md#add-worker-nodes) csomaggal tesztelheti, és biztos lehet benne, hogy az illesztőfelület változatlan marad.

Az alapszintű csomag az éles környezetben is kisebb számítási feladatokhoz is alkalmas (ha az előzetes verzióról az általánosan elérhetővé válik). *A kiszolgáló* virtuális mag számának növelésével lehetőség van az alapszintű vertikális skálázásra.

Ha a nagyobb méretre van szükség, használja a standard szintet. A legkisebb megengedett kiszolgálócsoport egyetlen koordinátor-csomóponttal és két feldolgozóval rendelkezik. A használati eset alapján több csomópontot is használhat, a [kezdeti méretezési](howto-hyperscale-scale-initial.md) útmutatóban leírtak szerint.

## <a name="next-steps"></a>Következő lépések

* [Az alapszintű csomag kiépítése](quickstart-create-hyperscale-basic-tier.md)
* Ha készen áll, tekintse meg az alapszintű csomagról a standard szinthez való [diplomát](howto-hyperscale-scale-grow.md#add-worker-nodes) ismertető témakört.
* Az [oszlopos tárolás](concepts-hyperscale-columnar.md) beállítás az alapszintű és a standard szinten is elérhető
