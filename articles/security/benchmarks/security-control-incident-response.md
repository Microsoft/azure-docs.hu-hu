---
title: Azure Security Control – incidens válasza
description: Az Azure Security Control incidensre adott válasz
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81408421"
---
# <a name="security-control-incident-response"></a>Biztonsági ellenőrzés: incidens válasza

A szervezet információinak és hírnevének védelme az incidensek megoldási infrastruktúrájának (például a tervek, a meghatározott szerepkörök, a képzés, a kommunikáció, a felügyelet felügyelete) fejlesztésével és megvalósításával a támadás gyors feltárásához, majd a sérülés hatékony, a támadó jelenlétének megszüntetéséhez, valamint a hálózat és a rendszerek integritásának helyreállításához.

## <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Ügyfél |

Hozzon létre egy incidensre vonatkozó választ a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az észleléstől az incidens utáni felülvizsgálatig.  

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [A NIST számítógépes biztonsági incidensek kezelésének útmutatója a saját incidensekre vonatkozó válaszadási terv létrehozásához nyújtott támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 10,2 | 19,8 | Ügyfél |

A Security Center az egyes riasztásokhoz súlyosságot rendel, így rangsorolhatja, hogy a rendszer mely riasztásokat vizsgálja meg először. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt. 

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Címkék használata az erőforrások rendszerezéséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 10,3 | 19 | Ügyfél |

Az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet.

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 10,4 | 19,5 | Ügyfél |

A Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 10,5 | 19,6 | Ügyfél |

Az Azure Security Center-riasztások és-javaslatok exportálásával a folyamatos exportálás funkcióval azonosíthatja az Azure-erőforrásokkal kapcsolatos kockázatokat. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Riasztások továbbítása az Azure Sentinelbe](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 10,6 | 19 | Ügyfél |

A Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat az "Logic Apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>További lépések

- Tekintse meg a következő biztonsági vezérlőt: [behatolási tesztek és Red Team Exercises](security-control-penetration-tests-red-team-exercises.md)