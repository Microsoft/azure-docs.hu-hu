---
title: Magas rendelkezésre állás és vészhelyreállítás
titleSuffix: Azure Digital Twins
description: Ismerteti az Azure és az Azure digitális Twins szolgáltatásait, amelyek segítségével a vész-helyreállítási képességekkel rendelkező, magasan elérhető Azure IoT-megoldások hozhatók létre.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3336a086fbe8f4291f752836a610cd80b773ec2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790816"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure digitális Twins – magas rendelkezésre állás és vész-helyreállítás

A rugalmas IoT megoldások szempontjából kulcsfontosságú terület az üzletmenet folytonossága és a vész-helyreállítás. A **magas rendelkezésre állású (ha)** és a vész- **helyreállítási (Dr)** rendszer tervezése lehetővé teszi a megoldáshoz szükséges hasznos üzemidői célok meghatározását és elérését.

Ez a cikk az Azure Digital Twins szolgáltatás által kínált HA és DR funkciókat ismerteti.

Az Azure Digital Twins a következő funkció-lehetőségeket támogatja:
* *Régión belüli ha* – beépített redundancia a szolgáltatás rendelkezésre állásának biztosításához
* *Régión* kívüli Dr – feladatátvétel egy geo-Párosítású Azure-régióba az adatközpont váratlan meghibásodása esetén

Az általános Azure-útmutatóhoz a HA/DR megtervezése című témakör [*ajánlott eljárásokat*](#best-practices) ismertető szakasza.

## <a name="intra-region-ha"></a>Régión belüli HA
 
Az Azure digitális Twins a szolgáltatáson belüli redundancia megvalósításán keresztül biztosít a régión belüli területet. Ezt a [szolgáltatás SLA](https://azure.microsoft.com/support/legal/sla/digital-twins) -ja mutatja a rendelkezésre álláshoz. **Egy Azure digitális Twins-megoldás fejlesztőinek nincs további teendője, hogy kihasználhassa ezeket a HA funkciókat.** Bár az Azure Digital Twins ésszerűen magas szintű üzemidőt biztosít, az átmeneti hibák várhatóak, ugyanúgy, mint bármely elosztott számítástechnikai platform esetében. A megfelelő újrapróbálkozási házirendeket be kell építeni a Felhőbeli alkalmazásokkal kommunikáló összetevőkbe az átmeneti hibák kezeléséhez.

## <a name="cross-region-dr"></a>Több régió – DR

Előfordulhat, hogy az adatközpont bizonyos ritkán fordul elő, ha áramkimaradások vagy a régió más eseményei miatt kibővített kimaradások állnak fenn. Ezek az események ritkán fordulnak elő, és az ilyen hibák miatt a fent ismertetett régión kívüli, HA-képesség nem segít. Az Azure digitális Twins ezt a Microsoft által kezdeményezett feladatátvételsel kezeli.

A Microsoft **által kezdeményezett feladatátvételt** ritka helyzetekben a Microsoft gyakorolja az érintett régióból származó összes Azure digitális Twins-példány feladatátvételére a megfelelő földrajzi helyzetű régióba. Ez a folyamat egy alapértelmezett beállítás (a felhasználók kizárása nélkül), és nem igényel beavatkozást a felhasználótól. A Microsoft fenntartja a jogot arra, hogy meghatározza, hogy a rendszer mikor gyakorolja ezt a lehetőséget. Ez a mechanizmus nem tartalmaz felhasználói beleegyező engedélyt a felhasználó példányának feladatátvétele előtt.

>[!NOTE]
> Bizonyos Azure-szolgáltatások egy, az **ügyfél által kezdeményezett feladatátvétel** nevű további lehetőséget is biztosítanak, amely lehetővé teszi az ügyfeleknek, hogy csak a példányuk számára kezdeményezzenek feladatátvételt, például egy Dr-részletezés futtatására. Az Azure Digital Twins jelenleg **nem támogatja** ezt a mechanizmust. 

## <a name="monitor-service-health"></a>Monitorozhatja a szolgáltatás állapotát.

Mivel az Azure digitális Twins-példányok feladatátvétele és helyreállítása megtörtént, a [Azure Service Health](../service-health/service-health-overview.md) eszköz használatával figyelheti a folyamatot. Service Health nyomon követi az Azure-szolgáltatások állapotát a különböző régiókban és előfizetésekben, és megosztja a szolgáltatással kapcsolatos kommunikációt az kimaradások és állásidők tekintetében.

Feladatátvételi esemény során a Service Health jelezheti, hogy mikor működik a szolgáltatás, és mikor kell biztonsági mentést készíteni.

Service Health események megtekintése...
1. Keresse meg [Service Health](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) a Azure Portalban (ezt a hivatkozást használhatja, vagy megkeresheti azt a portálon található keresősáv használatával).
1. A bal oldali menüben váltson az *állapotadatok* lapra.
1. Keresse meg a *probléma nevét* az **Azure digitális ikrektől** kezdve, és jelölje ki.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Képernyőkép a Azure Portal az állapotadatok oldaláról. Az elmúlt néhány nap számos problémáról van szó, és az &quot;Azure Digital Twins – Nyugat-Európa – enyhített&quot; nevű probléma ki van emelve." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Az kimaradással kapcsolatos általános információkért tekintse meg az *Összefoglalás* lapot.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Az állapotadatok lapon ki van emelve az összefoglalás lap. A lapon az általános információk láthatók, például az érintett erőforrás, a régiója és az előfizetése." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. A probléma időbeli változásával kapcsolatos további információkért és frissítésekhez tekintse meg a *probléma frissítései* lapot.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Az állapotadatok lapon a probléma frissítése lap ki van emelve. A lapon több bejegyzés jelenik meg, amely az aktuális állapotot jeleníti meg egy nappal ezelőtt." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Vegye figyelembe, hogy az eszközön megjelenő információk nem kizárólag egyetlen Azure digitális példányra vonatkoznak. Miután a Service Health használatával megértette, hogy mi történik az Azure Digital Twins szolgáltatással egy adott régióban vagy előfizetésben, a [Resource Health eszközzel](troubleshoot-resource-health.md) részletesen megtekintheti az adott példányokat, és láthatja, hogy érintettek-e.

## <a name="best-practices"></a>Ajánlott eljárások

A HA/DR-vel kapcsolatos ajánlott eljárásokért tekintse meg az alábbi Azure-útmutatást a témakörben: 
* Az [*Azure üzletmenet-folytonossági műszaki útmutatója*](/azure/architecture/framework/resiliency/overview) egy általános keretrendszert ismertet, amely segít az üzletmenet folytonosságának és a vész-helyreállításnak. 
* Az [*Azure-alkalmazások vész-helyreállítási és magas rendelkezésre állása*](/azure/architecture/framework/resiliency/backup-and-recovery) az Azure-alkalmazások magas rendelkezésre állást (ha) és a vész-helyreállítást (Dr) biztosító stratégiákra vonatkozó architektúrával kapcsolatos útmutatást nyújt.

## <a name="next-steps"></a>Következő lépések 

További információ az Azure Digital Twins-megoldások használatáról:
 
* [*Mi az az Azure Digital Twins?*](overview.md)
* [*Gyors útmutató: példa a forgatókönyvre*](quickstart-adt-explorer.md)