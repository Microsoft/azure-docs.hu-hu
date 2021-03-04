---
title: Azure Security Center GYIK – általános kérdések
description: Gyakori kérdések a Azure Security Centerről, a fenyegetések megelőzésére, észlelésére és reagálására szolgáló termékről
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 3a8429d9dc6820b1f79c49d325872b61833f988d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095544"
---
# <a name="faq---general-questions-about-azure-security-center"></a>GYIK – általános kérdések Azure Security Center

## <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?
Azure Security Center segít megakadályozni, észlelni és reagálni a fenyegetésekre az erőforrások biztonságának jobb láthatóságával és szabályozásával. A biztonsági megoldások átfogó ökoszisztémájának alkalmazásával az összes előfizetésére vonatkozóan integrált biztonságfelügyeletet és szabályzatkezelést biztosít, és segít felderíteni az egyébként nehezen észlelhető fenyegetéseket.

Security Center a Log Analytics ügynök használatával gyűjti és tárolja az adatokat. Részletes információk: [adatgyűjtés Azure Security Centerban](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>Hogyan lekérni Azure Security Center?
A Azure Security Center engedélyezve van a Microsoft Azure-előfizetéssel, és a [Azure Portal](https://azure.microsoft.com/features/azure-portal/)érhető el. A hozzáféréshez [Jelentkezzen be a portálra](https://portal.azure.com), válassza a **Tallózás** lehetőséget, majd görgessen a **Security Center** elemre.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Mely Azure-erőforrásokat figyeli a Azure Security Center?
Azure Security Center a következő Azure-erőforrásokat figyeli:

* Virtuális gépek (VM-EK) (beleértve a [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Virtuálisgép-méretezési csoportok
* Azure-előfizetéssel integrált partneri megoldások, például webalkalmazási tűzfal virtuális gépeken és App Service Environment
* [A termék áttekintésében szereplő számos Azure-beli Pásti-szolgáltatás](features-paas.md)


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hogyan tekinthetem meg az Azure-erőforrások aktuális biztonsági állapotát?
A **Security Center áttekintő** oldal a környezet általános biztonsági állapotát mutatja számítás, hálózatkezelés, tárterület & adatok és alkalmazások szerint lebontva. Minden erőforrástípus tartalmaz egy mutatót, amely az azonosított biztonsági réseket mutatja be. A csempére kattintva megjelenik a Security Center által azonosított biztonsági problémák listája, valamint az előfizetésében lévő erőforrások leltára.



## <a name="what-is-a-security-initiative"></a>Mi az a biztonsági kezdeményezés?
A biztonsági kezdeményezés meghatározza a megadott előfizetésben lévő erőforrásokhoz ajánlott vezérlők (szabályzatok) készletét. A Azure Security Center az Azure-előfizetésekhez tartozó kezdeményezéseket a vállalat biztonsági követelményeinek, az alkalmazások típusának és az egyes előfizetésekben található adattartalomnak megfelelően rendelheti hozzá.

A biztonsági szabályzatok Azure Security Center meghajtó biztonsági javaslatai és figyelése. További információ a [biztonsági szabályzatokról, a kezdeményezésekről és a javaslatokról](security-policy-concept.md):.


## <a name="who-can-modify-a-security-policy"></a>Kik módosíthatják a biztonsági házirendeket?
A biztonsági szabályzatok módosításához **biztonsági rendszergazdának** vagy az előfizetés **tulajdonosának** kell lennie.

A biztonsági szabályzatok konfigurálásával kapcsolatos további információkért lásd: [biztonsági házirendek beállítása Azure Security Centerban](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Mi az a biztonsági javaslat?
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. A potenciális biztonsági rések észlelése esetén javaslatok jönnek létre. A javaslatok végigvezetik a szükséges vezérlő konfigurálásának lépésein. Példák:

* Kártevő szoftver kiépítés a kártékony szoftverek azonosításához és eltávolításához
* [Hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md) és szabályok a virtuális gépek forgalmának vezérléséhez
* Webalkalmazási tűzfal üzembe helyezése a webalkalmazásokra irányuló támadások elleni védelem érdekében
* Hiányzó rendszerfrissítések telepítése
* Az operációs rendszer azon konfigurációinak kezelése, amelyek nem felelnek meg a javasolt alapkonfigurációknak

Itt csak a biztonsági házirendekben engedélyezett javaslatok jelennek meg.


## <a name="what-triggers-a-security-alert"></a>Mi váltja fel a biztonsági riasztást?
Azure Security Center automatikusan gyűjti, elemzi és megtagadja az Azure-erőforrások, a hálózat és a partneri megoldások, például az antimalware és a tűzfalak naplózási adatait. Fenyegetések észlelése esetén a központ biztonsági riasztást hoz létre. Példák fenyegetés észlelésére:

* Feltört virtuális gépek, amelyek kártékonyként azonosított IP-címekkel kommunikálnak
* A Windows hibajelentés használatával észlelt speciális kártevők
* Virtuális gépek elleni, a teljes kipróbálás módszerén alapuló támadások
* Biztonsági riasztások olyan integrált partneri biztonsági megoldásokból, mint például a kártevők elleni védelem vagy a webalkalmazási tűzfalak


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Mi a különbség az észlelt fenyegetések és a riasztások között a Microsoft Security Response Center és a Azure Security Center között?
A Microsoft Security Response Center (MSRC) az Azure-hálózat és-infrastruktúra biztonsági figyelését végzi, és a fenyegetésekkel kapcsolatos intelligenciát és a harmadik felektől érkező visszaéléseket fogad. Ha a MSRC tisztában van azzal, hogy az ügyféladatokat egy törvénytelen vagy jogosulatlan fél használja, vagy az Azure nem felel meg az elfogadható használat feltételeinek, akkor a biztonsági incidens kezelője értesíti az ügyfelet. Az értesítés általában akkor fordul elő, ha e-mailt küld a Azure Security Center vagy az Azure-előfizetés tulajdonosa által megadott biztonsági partnereknek, ha nincs biztonsági kapcsolat megadva.

Security Center egy Azure-szolgáltatás, amely folyamatosan figyeli az ügyfél Azure-környezetét, és az elemzést alkalmazza a potenciálisan kártékony tevékenységek széles körének automatikus észlelésére. Ezek az észlelések biztonsági riasztásként jelennek meg a Security Center irányítópulton.