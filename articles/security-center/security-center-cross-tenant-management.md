---
title: Több-bérlős felügyelet a Azure Security Centerban | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be a több-bérlős felügyeletet az Azure-beli delegált erőforrás-kezelési szolgáltatással Security Center több bérlő biztonsági állapotának kezeléséhez.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: d76ecc55c2e156199b2db789721946fd1f36de27
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922905"
---
# <a name="cross-tenant-management-in-security-center"></a>Több-bérlős felügyelet a Security Center

A több-bérlős felügyelet lehetővé teszi, hogy az Azure-beli [delegált erőforrás-kezelést](../lighthouse/concepts/azure-delegated-resource-management.md) kihasználva megtekintse és felügyelje Security Center több bérlő biztonsági állapotát. Több bérlő hatékony kezelése egyetlen nézetből, anélkül, hogy be kellene jelentkeznie az egyes bérlői címtárba.

- A szolgáltatók több ügyfél számára is kezelhetik az erőforrások biztonsági állapotát, a saját bérlőn belül.

- A több Bérlővel rendelkező szervezetek biztonsági csoportjai egyetlen helyről tekinthetik meg és kezelhetik biztonsági állapotát.

## <a name="set-up-cross-tenant-management"></a>Bérlők közötti felügyelet beállítása

Az Azure-beli delegált erőforrás-kezelés az Azure Lighthouse egyik fő összetevője. A több-bérlős felügyelet beállítása a felügyelt bérlők erőforrásaihoz való hozzáférés delegálásával a saját bérlőhöz az Azure Lighthouse dokumentációjában ismertetett utasítások alapján: Azure-beli [delegált erőforrás-kezelés](../lighthouse/concepts/azure-delegated-resource-management.md).


## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Hogyan működik a több-bérlős felügyelet Security Center

Az előfizetések több bérlőn is megtekinthetők és kezelhetők ugyanúgy, mint a több előfizetés egyetlen bérlőn való kezeléséhez.

A felső menüsorban kattintson a szűrő ikonra, és válassza ki az előfizetéseket minden bérlő címtárában, amelyet meg szeretne tekinteni.

  ![Bérlők szűrése](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

A nézetek és a műveletek alapvetően azonosak. Néhány példa:

- **Biztonsági házirendek kezelése**: az egyik nézetből számos erőforrás biztonsági állapotát kezelheti a [szabályzatokkal](tutorial-security-policy.md), a biztonsági javaslatokkal kapcsolatos műveleteket, valamint a biztonsági adatok gyűjtését és kezelését.
- A **biztonságos pontszám és a megfelelőségi helyzet javítása**: a bérlők közötti láthatósággal megtekintheti az összes bérlő teljes biztonsági állapotát, valamint azt, hogy miként és hogyan lehet legjobban javítani a [biztonságos pontszámot](secure-score-security-controls.md) és a [megfelelőségi testhelyzetet](security-center-compliance-dashboard.md) .
- **Szervizeléssel kapcsolatos javaslatok**: a különböző bérlők számos erőforrására [vonatkozó javaslatok](security-center-recommendations.md) figyelése és szervizelése egyszerre. Ezután azonnal megoldhatja azokat a biztonsági réseket, amelyek a legnagyobb kockázatot jelentik az összes bérlő esetében.
- **Riasztások kezelése**: [riasztások](security-center-alerts-overview.md) észlelése a különböző bérlők között. Tegyen lépéseket olyan erőforrásokra, amelyek nem felelnek meg a gyakorlatban használható [szervizelési lépéseknek](security-center-managing-and-responding-alerts.md).

- A **speciális Cloud Defense-funkciók kezelése és egyebek**: a veszélyforrások elleni védelem különböző szolgáltatásainak kezelése, például az igény szerinti [(JIT)](security-center-just-in-time.md)virtuálisgép-hozzáférés, az [adaptív hálózatok megerősítése](security-center-adaptive-network-hardening.md), az [adaptív alkalmazások vezérlése](security-center-adaptive-application.md)stb.
 
## <a name="next-steps"></a>Következő lépések
Ez a cikk bemutatja, hogyan működik a több-bérlős felügyelet a Security Centerban. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Megerősítheti a biztonsági helyzeteket Azure Security Centerekkel](security-center-monitoring.md) – megismerheti az Azure-erőforrások állapotának monitorozását.
* [Azure Security Center GYIK](faq-general.md) – gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Ismerje meg az Azure Lighthouse nagyvállalati forgatókönyveit – megismerheti,](../lighthouse/concepts/enterprise.md) hogy az Azure Lighthouse hogyan egyszerűsítheti le a több-bérlős felügyeletet egy olyan vállalaton belül, amely több Azure ad-bérlőt használ.