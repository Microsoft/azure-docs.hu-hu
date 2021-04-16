---
title: Magas rendelkezésre állás és vészhelyreállítás
titleSuffix: Azure Digital Twins
description: Ismerteti az Azure és Azure Digital Twins funkcióit, amelyek segítségével vészhelyreállítási képességekkel és magas rendelkezésre állséggel elérhető Azure IoT-megoldásokat építhet ki.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 41edef58910fe2b772831ef083e5aca8bb52a321
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482268"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Twins magas rendelkezésre állás és vészhelyreállítás

A rugalmas IoT-megoldások egyik legfontosabb szempontja az üzletmenet-folytonosság és a vészhelyreállítás. A magas rendelkezésre **állásra (HA)** és vészhelyreállításra **(DR)** tervezéssel meghatározhatja és megvalósíthatja a megoldáshoz megfelelő üzemidőre vonatkozó célokat.

Ez a cikk a rendelkezésre állást és a dr. funkciókat ismerteti, amelyeket kifejezetten a Azure Digital Twins kínál.

Azure Digital Twins a következő szolgáltatásbeállításokat támogatja:
* *Régión belüli hatékon* belüli – Beépített redundancia a szolgáltatás üzemidejű biztosításához
* *Régiók közötti dr.* – Feladatátvétel földrajzi párral párosított Azure-régióba váratlan adatközponthiba esetén

Az Ajánlott eljárások [](#best-practices) című szakaszban általános Azure-útmutatót is kaphat a ha/DR tervezéshez.

## <a name="intra-region-ha"></a>Régión belüli hatékony
 
Azure Digital Twins régión belüli hatékonyt biztosít a szolgáltatáson belüli redundanciák megvalósításával. Ezt az üzemidő szolgáltatási [SLA-ja](https://azure.microsoft.com/support/legal/sla/digital-twins) is tükrözi. **A nagy szolgáltatásokat igénybe Azure Digital Twins megoldás fejlesztőinek nem kell további munkát fordítaniuk.** Bár Azure Digital Twins a számítási feladatok viszonylag magas üzemidő-garanciát kínálnak, az átmeneti hibák is várhatók, mint bármely elosztott számítási platform esetén. Az átmeneti hibák megoldásához a megfelelő újrapróbálkozási szabályzatokat be kell építeni a felhőalkalmazással kommunikáló összetevőkbe.

## <a name="cross-region-dr"></a>Régiók közötti dr.

Előfordulhatnak olyan ritka helyzetek, amikor az adatközpont hosszabb kimaradásokat tapasztal áramkimaradások vagy más események miatt a régióban. Az ilyen események ritkák, és az ilyen hibák során a fent leírt, régión belüli hatékon belüli képesség nem biztos, hogy segít. Azure Digital Twins a Microsoft által kezdeményezett feladatátvételsel foglalkozik.

**A Microsoft által kezdeményezett** feladatátvételt ritka esetekben a Microsoft az összes Azure Digital Twins példány feladatátvételét egy érintett régióból a megfelelő geopáros régióba. Ez a folyamat egy alapértelmezett beállítás (amely nem teszi lehetővé a felhasználók számára a lemondást), és nem igényel beavatkozást a felhasználótól. A Microsoft fenntartja a jogot annak meghatározására, hogy mikor fogja ezt a lehetőséget gyakorolni. Ez a mechanizmus nem tartalmaz felhasználói jóváhagyást, mielőtt a felhasználó példánya átveszi az adatokat.

>[!NOTE]
> Egyes Azure-szolgáltatások egy további lehetőséget is biztosítanak az ügyfél által kezdeményezett feladatátvételnek, amely lehetővé teszi az ügyfelek számára, hogy csak a saját példányukhoz kezdeményezze a feladatátvételt, például egy DR-próba futtatását. Ezt a mechanizmust **jelenleg nem támogatja** a Azure Digital Twins. 

## <a name="monitor-service-health"></a>Monitorozhatja a szolgáltatás állapotát.

Mivel Azure Digital Twins példányok átveszése és helyreállítása folyamatban van, a folyamatot az Azure Service Health segítségével [figyelheti.](../service-health/service-health-overview.md) Service Health nyomon követi az Azure-szolgáltatások állapotát a különböző régiókban és előfizetéseken, és megosztja a szolgáltatásra hatást gyakoroló kommunikációt a szolgáltatáskimaradásokkal és leállásokkal kapcsolatban.

A feladatátvételi események Service Health jelzik, mikor áll le és mikor áll le a szolgáltatás, és mikor történik a biztonságimentés.

A Service Health megtekintése...
1. Lépjen [a Service Health](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) a Azure Portal (ezt a hivatkozást használhatja, vagy megkeresheti a portál keresősávjában).
1. A bal oldali menüben váltson az *Állapotelőzmények oldalra.*
1. Keresse meg a *probléma nevét* a **Azure Digital Twins,** majd jelölje ki.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Az Állapotelőzmények Azure Portal képernyőképe. Az elmúlt néhány napban számos probléma szerepel, és a &quot;Azure Digital Twins – Nyugat-Európa – Enyhítve&quot; probléma ki van emelve." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. A kimaradással kapcsolatos általános információkért tekintse meg *az* Összegzés lapot.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Az Állapotelőzmények lapon az Összegzés lap van kiemelve. A lap általános információkat jelenít meg, például az érintett erőforrást, annak régióját és előfizetését." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. A problémával kapcsolatos további információkért és frissítésekért tekintse meg a *Problémafrissítések* lapot.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Az Állapotelőzmények lapon ki van emelve a Problémafrissítések lap. A lapon több olyan bejegyzés is megjelenik, amelyek az egy nappal ezelőtti aktuális állapotot mutatják." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Vegye figyelembe, hogy az eszközben megjelenő információk nem egyetlen Azure Digital-példányra vonatkoznak. Miután a Service Health használatával megértette, hogy mi történik az Azure Digital Twins-szolgáltatás egy adott régióban vagy előfizetésben, a [Resource Health](troubleshoot-resource-health.md) eszköz használatával egy lépéssel tovább figyelheti a szolgáltatást, hogy részletesen lefúrjon adott példányokhoz, és hogy ezek hatással vannak-e az adott példányra.

## <a name="best-practices"></a>Ajánlott eljárások

A ha/DR-hez ajánlott eljárásokért tekintse meg a témakör alábbi Azure-útmutatóit: 
* Az [*Azure Üzletmenet-folytonosság műszaki útmutatója*](/azure/architecture/framework/resiliency/overview) egy általános keretrendszert ír le, amely segít az üzletmenet folytonosságának és a vészhelyreállításnak a megoldásában. 
* Az [*Azure-alkalmazások*](/azure/architecture/framework/resiliency/backup-and-recovery) vészhelyreállításról és magas rendelkezésre állásról készült tanulmánya architektúra-útmutatót nyújt az Azure-alkalmazások magas rendelkezésre állás (HA) és vészhelyreállítás (DR) elérésére vonatkozó stratégiáiról.

## <a name="next-steps"></a>Következő lépések 

További információ a megoldásokkal kapcsolatos Azure Digital Twins:
 
* [*Mi az az Azure Digital Twins?*](overview.md)
* [*Rövid útmutató: Mintaforgatókönyv megismerés*](quickstart-azure-digital-twins-explorer.md)