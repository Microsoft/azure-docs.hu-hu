---
title: Magas rendelkezésre állás – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Magas rendelkezésre állás és vész-helyreállítási fogalmak
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 406b4f2ada665d65ee0452579e24744d1f7cfc63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91314853"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Magas rendelkezésre állás Azure Database for PostgreSQL – nagy kapacitású (Citus)

A magas rendelkezésre állás (HA) elkerüli az adatbázis leállását azáltal, hogy a csoport minden csomópontjának készenléti replikáit megtartja. Ha egy csomópont leáll, a nagy kapacitású (Citus) bekapcsolja a bejövő kapcsolatokat a meghibásodott csomópontról a készenléti állapotba. A feladatátvétel néhány percen belül megtörténik, és a támogatott csomópontok mindig friss adattal rendelkeznek a PostgreSQL szinkron adatfolyam-továbbítási replikálásával.

Ha a koordinátor csomóponton szeretné kihasználni a szolgáltatást, az adatbázis-alkalmazásoknak meg kell ismerniük és újra kell próbálnia a kapcsolatokat és a sikertelen tranzakciókat. Az újonnan előléptetett koordinátor ugyanazzal a kapcsolatok sztringtel lesz elérhető.

A helyreállítás három szakaszra bontható: észlelés, feladatátvétel és teljes helyreállítás.  A nagy kapacitású (Citus) rendszeres állapot-ellenőrzéseket futtat minden csomóponton, és négy sikertelen ellenőrzés után meghatározza, hogy a csomópont le van-e kapcsolva. A nagy kapacitású (Citus) támogatja a készenléti állapotot az elsődleges csomópont állapota (feladatátvétel) számára, és új készenléti állapotot biztosít.
A folyamatos átviteli replikáció megkezdődik, így az új csomópont naprakész állapotba kerül.  Ha minden adat replikálódik, a csomópont elérte a teljes helyreállítást.

### <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [engedélyezheti a magas rendelkezésre állást](howto-hyperscale-high-availability.md) egy nagy kapacitású-(Citus-) kiszolgálócsoport számára.
