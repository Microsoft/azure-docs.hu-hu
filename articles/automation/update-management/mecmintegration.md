---
title: Azure Automation Update Management integrálása a Microsoft Endpoint Configuration Manager
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Microsoft Endpoint Configuration Manager és a Update Management a szoftverfrissítések központi telepítése a Manager-ügyfelekre.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: a848c7c15bf786ba26b8a1fdb1dab41b9aa20b8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575779"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>Update Management integrálása a Microsoft Endpoint Configuration Manager

A Microsoft Endpoint Configuration Managerban a számítógépek, kiszolgálók és mobileszközök felügyeletére használt ügyfelek a szoftverfrissítések felügyeleti (SUM) ciklusának részeként a szoftverfrissítések felügyeletének erősségét és érettségét is kihasználják.

A felügyelt Windows-kiszolgálókat jelentéssel és frissítéssel állíthatja be és frissítheti a Microsoft Endpoint Configuration Managerban, valamint a befejezett frissítések telepítésének részletes állapotát [Update Management](overview.md)használatával. Ha a Microsoft Endpoint Configuration Managert használja a frissítési megfelelőségi jelentéskészítéshez, de nem a Windows-kiszolgálókkal való frissítési központi telepítések kezelésére, akkor folytathatja a jelentéskészítést a Microsoft Endpoint Configuration Manager, amíg a biztonsági frissítéseket Azure Automation Update Management kezeli.

>[!NOTE]
>Míg Update Management támogatja a Windows Server 2008 R2 frissítési értékelését és javítását, a nem támogatja az operációs rendszer futtatásával Configuration Manager Microsoft Endpoint által felügyelt ügyfeleket.

## <a name="prerequisites"></a>Előfeltételek

* Az Automation-fiókhoz [Azure Automation Update Management](overview.md) kell hozzáadnia.
* A Microsoft Endpoint Configuration Manager-környezet által jelenleg felügyelt Windows Server-kiszolgálóknak is jelentést kell készíteniük a Log Analytics munkaterületre, amely szintén Update Management engedélyezve van.
* Ez a funkció engedélyezve van a Microsoft Endpoint Configuration Manager az aktuális ág 1606-es és újabb verziója. Ha integrálni szeretné a Microsoft-végpontot Configuration Manager központi adminisztrációs helyet vagy egy önálló elsődleges helyet, amelyen Azure Monitor naplók és az importálási gyűjtemények szerepelnek, tekintse át a [kapcsolódás Configuration Manager a Azure monitor naplókat](../../azure-monitor/logs/collect-sccm.md)  
* A Windows-ügynököket úgy kell konfigurálni, hogy Windows Server Update Services-(WSUS-) kiszolgálóval kommunikáljanak, Microsoft Update vagy ha nem kapnak biztonsági frissítéseket a Microsoft Endpoint Configuration Managertól, a Windows-ügynököt is be kell állítani.

Az Azure IaaS-ben üzemeltetett ügyfelek kezelése a meglévő Microsoft Endpoint Configuration Manager-környezettel elsősorban az Azure-adatközpontok és az infrastruktúra közötti kapcsolattól függ. Ez a kapcsolat hatással van minden olyan tervezési módosításra, amelyet a szükséges módosítások támogatásához szükséges lehet a Microsoft-végponton Configuration Manager infrastruktúrához és a kapcsolódó díjakhoz. Annak megismeréséhez, hogy milyen tervezési szempontokat kell értékelnie a továbblépés előtt, tekintse meg az [Azure-beli Configuration Managerrel kapcsolatos gyakori kérdéseket](/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Szoftverfrissítések kezelése a Microsoft Endpoint Configuration Manager

Hajtsa végre az alábbi lépéseket, ha továbbra is szeretné kezelni a frissítések központi telepítését a Microsoft Endpoint Configuration Managerról. Azure Automation csatlakozik a Microsoft Endpoint Configuration Manager-hoz, hogy a Log Analytics munkaterülethez csatlakoztatott ügyfélszámítógépeken is alkalmazza a frissítéseket. Ha a központi telepítést a Microsoft Endpoint Configuration Manager felügyeli, a tartalom frissítése az ügyfélszámítógép gyorsítótárából is elérhető.

1. Hozzon létre egy szoftverfrissítés-telepítést a Microsoft Endpoint Configuration Manager hierarchia legfelső szintű helyéről a [szoftverfrissítések központi telepítése](/configmgr/sum/deploy-use/deploy-software-updates)című témakörben ismertetett eljárással. Az egyetlen beállítás, amelyet egy standard telepítéstől eltérően kell konfigurálnia, a **Ne telepítse a szoftverfrissítéseket** kiválasztása, amely a telepítőcsomag letöltésének viselkedését vezérli. Ezt a viselkedést Update Management felügyelheti egy ütemezett frissítési telepítés létrehozásával a következő lépésben.

2. A Azure Automation területen válassza a **Update Management** lehetőséget. Hozzon létre egy új központi telepítést a [frissítés központi telepítésének létrehozása](deploy-updates.md#schedule-an-update-deployment) című témakörben ismertetett lépéseket követve, és válassza az **importált csoportok** lehetőséget a **típus** legördülő menüben a megfelelő Microsoft Endpoint Configuration Manager gyűjtemény kiválasztásához. Tartsa szem előtt az alábbi fontos szempontokat:

    a. Ha egy karbantartási időszak van meghatározva a kiválasztott Microsoft Endpoint Configuration Manager-eszköz gyűjteményben, a gyűjtemény tagjai az ütemezett telepítésben megadott **időtartam** beállítás helyett tiszteletben tartsák.

    b. A cél gyűjtemény tagjainak csatlakozniuk kell az internethez (vagy közvetlenül, egy proxykiszolgálón keresztül vagy az Log Analytics átjárón keresztül).

A frissítés telepítésének a Azure Automationon keresztül történő befejezése után a kiválasztott számítógépcsoport tagjai lesznek a helyi ügyfél-gyorsítótárból beütemezett időpontban települő frissítések. [A frissítéstelepítés állapotát bármikor megtekintheti](deploy-updates.md#check-deployment-status) a telepítés eredményeinek monitorozásához.

## <a name="manage-software-updates-from-azure-automation"></a>Szoftverfrissítések kezelése Azure Automationról

A Microsoft Endpoint Configuration Manager ügyfelekhez tartozó Windows Server-alapú virtuális gépek frissítéseinek kezeléséhez konfigurálnia kell az ügyfél-házirendet, hogy letiltsa a Update Management által felügyelt összes ügyfél Szoftverfrissítések felügyelete szolgáltatását. Alapértelmezés szerint az ügyfélbeállítások minden eszközt megcéloznak a hierarchiában. A házirend-beállítással és annak konfigurálásával kapcsolatos további információkért tekintse át az [ügyfélbeállítások konfigurálása a Configuration Manager-ben](/configmgr/core/clients/deploy/configure-client-settings)című témakört.

A konfigurációs módosítás elvégzése után hozzon létre egy új központi telepítést a [frissítés központi telepítésének létrehozása](deploy-updates.md#schedule-an-update-deployment) című témakörben ismertetett lépéseket követve, és válassza a **típus** legördülő menüből az **importált csoportok** lehetőséget a megfelelő Microsoft Endpoint Configuration Manager gyűjtemény kiválasztásához.

## <a name="next-steps"></a>Következő lépések

Az integrációs ütemterv beállításával kapcsolatban lásd: [frissítések központi telepítésének ütemezett](deploy-updates.md#schedule-an-update-deployment)beállítása.
