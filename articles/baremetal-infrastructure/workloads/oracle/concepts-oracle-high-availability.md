---
title: Magas rendelkezésre állás és vészhelyreállítás az Oracle-hez BareMetalon
description: Ismerje meg a magas rendelkezésre állást és a vészhelyreállítást Azure-beli Oracle BareMetal-infrastruktúrához.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: ab0337622eaa8c1368760fcbcd28533dacb3adce
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590260"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>Magas rendelkezésre állás és vészhelyreállítás az Oracle-hez BareMetalon

Ebben a cikkben a magas rendelkezésre állás és a vészhelyreállítás alapjaival fogunk átesni. Ezután bemutatjuk, hogyan érhet el magas rendelkezésre állást és vészhelyreállítást Oracle-környezetben az BareMetal-infrastruktúrán.

## <a name="high-availability-vs-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás

Mind a magas rendelkezésre állás, mind a vészhelyreállítás biztosítja a lefedettséget, de különböző típusú hibák esetén. Az alkalmazás különböző funkcióit és Oracle Database.

A magas rendelkezésre állás lehetővé teszi, hogy a rendszer több hibát is leküzdsen anélkül, hogy ez hatással lenne az alkalmazás felhasználói élményére. A magas rendelkezésre állású rendszerek gyakori jellemzői a következők:

- Redundáns hardver, amely egyetlen meghibásodási ponttal sem rendelkezik.
- Automatikus helyreállítás nem kritikus hibákból, például hibás lemezmeghajtókból vagy hibás hálózati kábelekből.
- A hardverek és szoftverek dobási képessége anélkül változik, hogy észrevehető hatással lenne a feldolgozásra.
- A helyreállítási időre vonatkozó célkitűzések (RTO) és a helyreállítási időkorrekta (RPO) céljainak elérése vagy túllépése.

A magas rendelkezésre álláshoz leggyakrabban használt Oracle-szolgáltatás az [Oracle Real Application Clusters (RAC).](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92)

A vészhelyreállítás megvédi a helyreállíthatatlan helyi hibáktól, amelyek az elsődleges magas rendelkezésre állási stratégiát sértenék. Az Oracle ökoszisztémájában ezt az adatbázis-replikáció, más néven [Oracle Data Guard biztosítja.](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590)

## <a name="next-steps"></a>Következő lépések

További információ az Oracle magas rendelkezésre állású funkcióiról:

> [!div class="nextstepaction"]
> [Magas rendelkezésre állású funkciók Oracle-hez az BareMetal-infrastruktúrán](high-availability-features.md)
