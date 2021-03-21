---
title: A Windows rendszerű virtuális asztali hibaelhárítás áttekintése – Azure
description: Áttekintés a Windows rendszerű virtuális asztali környezet beállítása során felmerülő problémák elhárításához.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4d4bdc4fa15f634b36f12a650b70b9ffd89b40e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539104"
---
# <a name="troubleshooting-overview-feedback-and-support-for-windows-virtual-desktop"></a>Hibaelhárítás – áttekintés, visszajelzés és a Windows rendszerű virtuális asztalok támogatása

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).

Ez a cikk áttekintést nyújt a Windows rendszerű virtuális asztali környezet beállítása során felmerülő problémákról, és a problémák megoldásához nyújt lehetőségeket.

## <a name="report-issues"></a>Problémák bejelentése

Ha Azure Resource Manager-integrációval kapcsolatos problémákat szeretne jelenteni a Windows rendszerű virtuális asztali környezethez, látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). A technikai Közösség segítségével megismerheti az ajánlott eljárásokat, illetve az új funkciókra vonatkozó javaslatokat és szavazhat.

Ha olyan bejegyzést tesz, amely segítséget kér vagy új szolgáltatást javasol, a lehető legrészletesebben írja le a témakört. A részletes információk segítséget nyújthatnak más felhasználóknak a kérdés megválaszolásához vagy az Ön által szavazásra javasolt funkció megismeréséhez.

## <a name="escalation-tracks"></a>Eszkalációs sávok

Mielőtt bármit végrehajtaná, győződjön meg róla, hogy az Azure-beli szolgáltatás megfelelően fut-e, és [Azure Service Health](https://azure.microsoft.com/features/service-health/) ellenőrizze az Azure-beli [állapot lapot](https://status.azure.com/status) .

A következő táblázat segítségével azonosíthatja és megoldhatja a környezetek Távoli asztal-ügyfél használatával történő beállításakor felmerülő problémákat. A környezet beállítása után az új [diagnosztikai szolgáltatás](diagnostics-role-service.md) segítségével azonosíthatja a gyakori forgatókönyvek problémáit.

| **Kérdés**                                                            | **Javasolt megoldás**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Session Host Pool Azure Virtual Network (VNET) és Express Route-beállítások               | [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), majd válassza ki a megfelelő szolgáltatást (a hálózatkezelés kategóriában). |
| A munkamenet-gazdagép virtuális gép (VM) létrehozása, ha Azure Resource Manager a Windows rendszerű virtuális asztallal elérhető sablonok nincsenek használatban | [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), majd válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz. <br> <br> A Windows rendszerű virtuális asztal szolgáltatásban elérhető Azure Resource Manager sablonokkal kapcsolatos problémákért lásd: a gazdagép-készletek [létrehozásának](troubleshoot-set-up-issues.md)Azure Resource Manager sablon hibái szakasza. |
| Windows rendszerű virtuális asztali munkamenetgazda-környezet kezelése a Azure Portal    | [Nyisson meg egy Azure-támogatási kérelmet](https://azure.microsoft.com/support/create-ticket/). <br> <br> A Távoli asztali szolgáltatások/Windows rendszerű virtuális asztali PowerShell használatakor a felügyeleti problémákért lásd: a [Windows virtuális asztali PowerShell](troubleshoot-powershell.md) vagy [egy Azure-támogatási kérelem megnyitása](https://azure.microsoft.com/support/create-ticket/), válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, válassza a **konfiguráció és kezelés** lehetőséget a probléma típusa beállításnál, majd válassza a probléma altípushoz a **környezet konfigurálása a PowerShell használatával** című témakört. |
| A Windows rendszerű virtuális asztali konfiguráció kezelése a gazdagépek és az alkalmazások csoportjaihoz kötve (alkalmazás-csoportok)      | Tekintse meg a [Windows rendszerű virtuális asztali PowerShellt](troubleshoot-powershell.md), vagy [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), válassza a szolgáltatáshoz a **Windows virtuális asztal** lehetőséget, majd válassza ki a megfelelő problémát.|
| FSLogix-profilok tárolóinak üzembe helyezése és kezelése | Tekintse meg a [FSLogix termékekkel kapcsolatos hibaelhárítási útmutatót](/fslogix/fslogix-trouble-shooting-ht/) , és ha ez nem oldja meg a problémát, [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, válassza a **FSLogix** lehetőséget, majd válassza ki a megfelelő problémát altípust. |
| A távoli asztali ügyfelek működése a Start menüben                                                 | Lásd: [az távoli asztal-ügyfél hibaelhárítása](troubleshoot-client.md) , és ha ez nem oldja meg a problémát,  [Nyisson meg egy Azure-támogatási kérelmet](https://azure.microsoft.com/support/create-ticket/), válassza a szolgáltatáshoz a **Windows virtuális asztal** lehetőséget, majd válassza a probléma típusa **Távoli asztal ügyfelek** lehetőséget.  <br> <br> Hálózati hiba esetén a felhasználóknak kapcsolatba kell lépniük a hálózati rendszergazdával. |
| Csatlakoztatva, de nincs hírcsatorna                                                                 | A felhasználó kapcsolódási hibáinak megoldása, de a [Windows rendszerű virtuális asztali szolgáltatás kapcsolatainak](troubleshoot-service-connection.md)egyike [sem jelenik meg (nincs hírcsatorna)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) szakasza. <br> <br> Ha a felhasználók egy alkalmazáshoz vannak rendelve,  [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, majd válassza a probléma típusa **Távoli asztal ügyfelek** lehetőséget. |
| A hírcsatornák felderítésével kapcsolatos problémák a hálózat miatt                                            | A felhasználóknak kapcsolatba kell lépniük a hálózati rendszergazdával. |
| Ügyfelek csatlakoztatása                                                                    | Tekintse meg a [Windows rendszerű virtuális asztali szolgáltatások kapcsolatait](troubleshoot-service-connection.md) , és ha ez nem oldja meg a problémát, tekintse meg a [munkamenet-gazdagép virtuális gép konfigurációját](troubleshoot-vm-configuration.md) |
| A távoli alkalmazások vagy az asztal rugalmassága                                      | Ha a problémák egy adott alkalmazáshoz vagy termékhez vannak kötve, forduljon a termékért felelős csapathoz. |
| Licencelési üzenetek vagy hibák                                                          | Ha a problémák egy adott alkalmazáshoz vagy termékhez vannak kötve, forduljon a termékért felelős csapathoz. |
| Harmadik féltől származó hitelesítési módszerekkel vagy eszközökkel kapcsolatos problémák | Győződjön meg arról, hogy a harmadik féltől származó szolgáltató támogatja a Windows rendszerű virtuális asztali környezeteket, és az ismert problémákkal kapcsolatban megközelíti őket. |
| Problémák a Windows rendszerű virtuális asztali Log Analytics használatával | A diagnosztikai sémával kapcsolatos problémák esetén [Nyisson meg egy Azure-támogatási kérelmet](https://azure.microsoft.com/support/create-ticket/).<br><br>A Log Analytics lekérdezések, vizualizációk vagy egyéb problémák esetén válassza ki a megfelelő problémát a Log Analytics alatt. |
| M365-alkalmazásokat használó problémák | Forduljon a M365 felügyeleti központhoz a [M365 felügyeleti központ súgójának](/microsoft-365/admin/contact-support-for-business-products/)egyikével. |

## <a name="next-steps"></a>Következő lépések

- A gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg az [alkalmazáskészlet létrehozása](troubleshoot-set-up-issues.md)című témakört.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A Windows rendszerű virtuális asztali ügynökkel vagy a munkamenet-kapcsolattal kapcsolatos problémák elhárításához lásd: a [Windows rendszerű virtuális asztali ügynökkel kapcsolatos gyakori problémák elhárítása](troubleshoot-agent.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali szolgáltatások kapcsolatai](troubleshoot-service-connection.md)című témakört.
- Távoli asztal-ügyfelekkel kapcsolatos problémák elhárításához tekintse meg [a távoli asztal-ügyfél hibaelhárítása](troubleshoot-client.md) című témakört.
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerű virtuális asztali környezet](environment-setup.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](../azure-resource-manager/management/view-activity-logs.md).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](../azure-resource-manager/templates/deployment-history.md).
