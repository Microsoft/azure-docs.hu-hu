---
title: Az Automation bemutatása az Azure Sentinelben | Microsoft Docs
description: Ez a cikk bemutatja az Azure Sentinel biztonsági előkészítési, automatizálási és reagálási (SZÁRNYALó) funkcióit, és leírja a hozzájuk tartozó összetevők – automatizálási szabályok és forgatókönyvek használatát.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609357"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Biztonsági előkészítés, automatizálás és reagálás (SZÁRNYALás) az Azure Sentinelben

Ez a cikk az Azure Sentinel biztonsági előkészítési, automatizálási és reagálási (SZÁRNYALó) funkcióit ismerteti, és bemutatja, hogyan használható az automatizálási szabályok és a forgatókönyvek a biztonsági fenyegetésekre reagálva növelik a SOC hatékonyságát, és időt és erőforrásokat takarítanak meg.

## <a name="azure-sentinel-as-a-soar-solution"></a>Az Azure Sentinel felszárnyaló megoldás

### <a name="the-problem"></a>A probléma

A SIEM/SOC csapatokat jellemzően a biztonsági riasztásokkal és az incidensekkel együtt rendszeresen, olyan köteteken használják, amelyeken a rendelkezésre álló személyek túlterheltek. Ez az eredmény túl gyakran előfordul olyan helyzetekben, amikor sok riasztást figyelmen kívül hagynak, és sok incidenst nem vizsgálnak meg, így a szervezet sebezhetővé válik a nem észlelt támadásokkal szemben.

### <a name="the-solution"></a>A megoldás

Az Azure Sentinel a biztonsági és az eseménykezelő (SIEM) rendszerek mellett a biztonsági előkészítés, az automatizálás és a reagálás (SZÁRNYALás) platformja is. Az egyik elsődleges célja, hogy automatizálja a biztonsági operatív központ és a személyzet (SOC/SecOps) által felvállalt ismétlődő és kiszámítható dúsítási, reagálási és szervizelési feladatokat, valamint a további részletes nyomozást és a speciális fenyegetések elleni vadászatot. Az Automation az Azure Sentinel néhány különböző formáját veszi igénybe az olyan automatizálási szabályoktól kezdve, amelyek központilag kezelik az incidensek kezelésének és reagálásának automatizálását, az előre meghatározott műveletsorokat futtató forgatókönyvekhez, amelyek hatékony és rugalmas, speciális automatizálást biztosítanak a veszélyforrások által felmerülő feladatokhoz.

## <a name="automation-rules"></a>Automatizálási szabályok

Az Automation-szabályok az Azure Sentinel új koncepciója. Ez a funkció lehetővé teszi a felhasználók számára, hogy központilag kezeljék az incidensek kezelésének automatizálását. Amellett, hogy az incidensekhez (nem csak az előtte lévő riasztásokhoz) rendel hozzá forgatókönyveket, az Automation-szabályok lehetővé teszik a több elemzési szabályra adott válaszok automatizálását, automatikus címkézését, hozzárendelését vagy bezárását, és a végrehajtott műveletek sorrendjét. Az automatizálási szabályok egyszerűsítik az automatizálási használatot az Azure Sentinelben, és lehetővé teszik az incidensek összehangolása folyamataihoz tartozó összetett munkafolyamatok egyszerűsítését.

További információ az [automatizálási szabályok részletes ismertetéséről](automate-incident-handling-with-automation-rules.md).

> [!IMPORTANT]
>
> - Az **Automation-szabályok** szolgáltatás jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

## <a name="playbooks"></a>Forgatókönyvek

A forgatókönyvek az Azure Sentinelből rutinként futtatható válasz-és szervizelési műveletek és logikák gyűjteményei. A forgatókönyvek segítségével automatizálhatja és összehangolhatja a fenyegetésre adott válaszait, integrálható a belső és külső rendszerekkel is, és beállítható úgy, hogy az adott riasztásokra vagy incidensekre adott válaszként automatikusan fusson, vagy egy elemzési szabály vagy egy Automation-szabály alapján. Az incidensek lapon manuálisan is futtatható a riasztásokra reagálva.

Az Azure Sentinelben elérhető forgatókönyvek az [Azure Logic apps](../logic-apps/logic-apps-overview.md)-ben létrehozott munkafolyamatokon alapulnak, amelyek segítségével a vállalaton belül a feladatok és munkafolyamatok ütemezhetők, automatizálható és koordinálható. Ez azt jelenti, hogy a forgatókönyvek kihasználhatják a Logic Apps integrációs és-előkészítési képességeinek, valamint a könnyen használható tervezési eszközöknek, valamint az 1. rétegbeli Azure-szolgáltatás skálázhatósági, megbízhatósági és szolgáltatási szintjének előnyeit.

A forgatókönyvek részletes [ismertetését](automate-responses-with-playbooks.md)itt találja.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogy az Azure Sentinel hogyan használja az automationt a SOC hatékonyabb és hatékony működéséhez.

- Az incidensek kezelésének automatizálásával kapcsolatos további információkért lásd: [az incidensek kezelésének automatizálása az Azure sentinelben](automate-incident-handling-with-automation-rules.md).
- További információ a speciális automatizálási lehetőségekről: a [veszélyforrások elleni reagálás automatizálása az Azure sentinelben](automate-responses-with-playbooks.md).
- Az Automation-szabályok és a forgatókönyvek megvalósításával kapcsolatos segítségért tekintse meg az [oktatóanyag: forgatókönyvek használata a fenyegetési válaszok automatizálásához az Azure sentinelben](tutorial-respond-threats-playbook.md)című témakört.
