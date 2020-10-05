---
title: Engedélyek a Azure Security Centerban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Azure Security Center szerepköralapú hozzáférés-vezérléssel a felhasználók engedélyeinek kiosztásához és az egyes szerepkörökhöz engedélyezett műveletek azonosításához.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/02/2020
ms.author: memildin
ms.openlocfilehash: 2a66873d558495049c97b987dfb69bbf83b4e1d8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91439483"
---
# <a name="permissions-in-azure-security-center"></a>Engedélyek az Azure Security Centerben

Azure Security Center az [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)használja, amely [beépített szerepköröket](../role-based-access-control/built-in-roles.md) biztosít a felhasználókhoz, csoportokhoz és szolgáltatásokhoz az Azure-ban.

Security Center megvizsgálja az erőforrások konfigurációját a biztonsági problémák és a biztonsági rések azonosítása érdekében. A Security Centerban csak akkor jelenik meg az erőforrásokhoz kapcsolódó információ, ha a tulajdonos, közreműködő vagy olvasó szerepkörét rendelte hozzá ahhoz az előfizetéshez vagy erőforráscsoporthoz, amelyhez az erőforrás tartozik.

Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

* **Biztonsági olvasó**: az ehhez a szerepkörhöz tartozó felhasználó megtekinti a jogosultságokat a Security Center. A felhasználók megtekinthetik a javaslatokat, a riasztásokat, a biztonsági házirendeket és a biztonsági állapotokat, de nem módosíthatják azokat.
* **Biztonsági rendszergazda**: az ehhez a szerepkörhöz tartozó felhasználók ugyanazokkal a jogokkal rendelkeznek, mint a biztonsági olvasó, és frissíthetik a biztonsági házirendet, és elmulasztják a riasztások és javaslatok elutasítását.

> [!NOTE]
> A biztonsági szerepkörök, a biztonsági olvasó és a biztonsági rendszergazda csak Security Center rendelkezik hozzáféréssel. A biztonsági szerepkörök nem férnek hozzá az Azure más szolgáltatási területeihez, például a Storage, a web & Mobile vagy a eszközök internetes hálózatahoz.
>

## <a name="roles-and-allowed-actions"></a>Szerepkörök és engedélyezett műveletek

A következő táblázat a szerepköröket és az engedélyezett műveleteket mutatja Security Centerban.

|Művelet|Biztonsági olvasó/ <br> Olvasó |Biztonsági rendszergazda  |Erőforráscsoport közreműködői/ <br> Erőforráscsoport tulajdonosa  |Előfizetés közreműködői  |Előfizetés tulajdonosa  |
|:--- |:---:|:---:|:---:|:---:|:---:|
|Biztonsági házirend szerkesztése|-|✔|-|-|✔|
|Kezdeményezések hozzáadása/kiosztása (beleértve) szabályozási megfelelőségi szabványok)|-|-|-|-|✔|
|Az Azure Defender engedélyezése/letiltása|-|✔|-|-|✔|
|Automatikus kiépítés engedélyezése/letiltása|-|✔|-|✔|✔|
|Erőforrásokra vonatkozó biztonsági javaslatok alkalmazása</br> (és használjon [gyors javítást!](security-center-remediate-recommendations.md#quick-fix-remediation))|-|-|✔|✔|✔|
|Riasztások bezárása|-|✔|-|✔|✔|
|Riasztások és javaslatok megtekintése|✔|✔|✔|✔|✔|

> [!NOTE]
> Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Például rendelje hozzá az olvasó szerepkört azokhoz a felhasználókhoz, akik csak az erőforrások biztonsági állapotával kapcsolatos információkat szeretnének megtekinteni, de nem végeznek műveleteket, például javaslatok alkalmazása vagy szerkesztési szabályzatok.
>
>

## <a name="next-steps"></a>További lépések
Ez a cikk azt ismerteti, hogyan használja a Security Center a RBAC-t a felhasználók engedélyeinek hozzárendeléséhez és az egyes szerepkörökhöz engedélyezett műveletek azonosításához. Most, hogy már ismeri az előfizetés biztonsági állapotának figyeléséhez szükséges szerepkör-hozzárendeléseket, a biztonsági szabályzatok szerkesztését és a javaslatok alkalmazását, Ismerje meg a következőket:

- [Biztonsági szabályzatok beállítása a Security Centerben](tutorial-security-policy.md)
- [Biztonsági javaslatok kezelése Security Center](security-center-recommendations.md)
- [Azure-erőforrások biztonsági állapotának figyelése](security-center-monitoring.md)
- [Biztonsági riasztások kezelése és reagálás Security Center](security-center-managing-and-responding-alerts.md)
- [Partneri biztonsági megoldások figyelése](security-center-partner-solutions.md)
