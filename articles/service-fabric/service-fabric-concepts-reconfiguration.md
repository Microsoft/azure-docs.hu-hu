---
title: Újrakonfigurálás az Azure Service Fabric
description: Ismerje meg az állapot-nyilvántartó szolgáltatás replikáinak konfigurációit, valamint azt, hogy a módosítás során a rendszer hogyan dolgozza fel az újrakonfigurálási Service Fabric a konzisztencia és a rendelkezésre állás fenntartásához.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75609995"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Újrakonfigurálás az Azure Service Fabric
A *konfiguráció* egy állapot-nyilvántartó szolgáltatás partíciójának replikái és szerepköreiként van definiálva.

Az *újrakonfigurálás* az egyik konfiguráció másik konfigurációba való áthelyezésének folyamata. Egy állapot-nyilvántartó szolgáltatás partíciójának replikáját módosítja. A régi konfiguráció neve *korábbi konfiguráció (PC)*, az új konfiguráció pedig az *aktuális konfiguráció (CC)*. Az Azure-ban az újrakonfigurálási protokoll Service Fabric megőrzi az egységességet, és fenntartja a rendelkezésre állást a replikakészlet változásai során.

Feladatátvételi felügyelő újrakonfigurálást kezdeményez a rendszeren lévő különböző eseményekre adott válaszként. Ha például az elsődleges művelet meghiúsul, akkor a rendszer újrakonfigurálást kezdeményez egy aktív másodlagos elsődlegesre való előléptetéséhez. Egy másik példa az alkalmazások frissítéseire is reagál, amikor szükség lehet arra, hogy az elsődlegeset egy másik csomópontra helyezze át a csomópont frissítése érdekében.

## <a name="reconfiguration-types"></a>Újrakonfigurálási típusok
Az újrakonfigurálások két típusba sorolhatók:

- Újrakonfigurálások, ahol az elsődleges változó:
    - **Feladatátvétel**: a feladatátvételek a futó elsődleges hiba miatti újrakonfigurálások.
    - **SwapPrimary**: a swap-konfigurációk olyan újrakonfigurálások, ahol Service Fabric a futó elsődlegest egy csomópontról egy másikra kell áthelyeznie, általában a terheléselosztásra vagy a frissítésre válaszul.

- Újrakonfigurálások, amelyek esetében az elsődleges nem változik.

## <a name="reconfiguration-phases"></a>Újrakonfigurálási fázisok
Az újrakonfigurálás több fázisban is folytatódik:

- **Phase0**: Ez a fázis olyan swap-elsődleges újrakonfigurációknál fordul elő, ahol az aktuális elsődleges az új elsődleges és az aktív másodlagos állapotra vált.

- **Phase1**: Ez a fázis olyan újrakonfigurálások során fordul elő, ahol az elsődleges változó. Ebben a fázisban Service Fabric azonosítja a megfelelő elsődlegest az aktuális replikák között. Ez a fázis nem szükséges a swap-primer újrakonfigurálások során, mert az új elsődleges már ki van választva. 

- **Phase2**: ebben a fázisban Service Fabric biztosítja, hogy az összes érték az aktuális konfiguráció replikáinak többségében elérhető legyen.

Több más fázis is van, amelyek csak belső használatra szolgálnak.

## <a name="stuck-reconfigurations"></a>Beragadt újrakonfigurálások
Az újrakonfigurálás számos okból *elakadhat* . A leggyakoribb okok a következők:

- **Replikák**: egyes újrakonfigurálási fázisok esetében a konfigurációban szereplő replikák többségét meg kell adni.
- **Hálózati vagy kommunikációs problémák**: az újrakonfigurálások hálózati kapcsolatot igényelnek a különböző csomópontok között.
- **API-hibák**: az újrakonfigurálási protokoll megköveteli, hogy a szolgáltatás implementációi bizonyos API-kat fejezzenek be. Ha például egy megbízható szolgáltatásban nem tartja tiszteletben a lemondási tokent, a SwapPrimary újrakonfigurálást eredményez a beragadás érdekében.

A rendszerösszetevőktől, például a System.FM, a System. RA és a System. RAP szolgáltatásból származó állapotadatok használatával diagnosztizálhatja, hogy az újrakonfigurálás hol ragadt meg. A [rendszerállapot-jelentés lap](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) ismerteti ezeket az állapot-jelentéseket.

## <a name="next-steps"></a>Következő lépések
Service Fabric fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [A Reliable Services életciklusa – C#](service-fabric-reliable-services-lifecycle.md)
- [Rendszerállapot-jelentések](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Replikák és példányok](service-fabric-concepts-replica-lifecycle.md)
