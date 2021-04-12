---
title: A Windows Virtual Desktop (klasszikus) hibaelhárításának áttekintése – Azure
description: Áttekintés a Windows rendszerű virtuális asztali (klasszikus) bérlői környezet beállítása során felmerülő problémák elhárításához.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: efc96f03d93227cfffbb533bf1ce1786a0a47d14
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444156"
---
#  <a name="windows-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Windows rendszerű virtuális asztali (klasszikus) hibaelhárítás – áttekintés, visszajelzés és támogatás

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../troubleshoot-set-up-overview.md).

Ez a cikk áttekintést nyújt a Windows rendszerű virtuális asztali bérlői környezet beállításakor felmerülő problémákról, és a problémák megoldásához nyújt lehetőségeket.

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , és beszéljen a Windows Virtual Desktop szolgáltatásról a termék csapatával és az aktív közösség tagjaival.

## <a name="escalation-tracks"></a>Eszkalációs sávok

A következő táblázat segítségével azonosíthatja és megoldhatja a bérlői környezet Távoli asztal-ügyféllel való beállításakor felmerülő problémákat. A bérlő beállítása után az új [diagnosztikai szolgáltatás](diagnostics-role-service-2019.md) segítségével azonosíthatja a gyakori forgatókönyvekkel kapcsolatos problémákat.

>[!NOTE]
> Van egy technikai közösségi fórum, amelyről megtudhatja, hogy milyen problémák léptek fel a termék csapatával és az aktív közösség tagjaival kapcsolatban. A vitafórum elindításához látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) .

| **Probléma**                                                            | **Javasolt megoldás**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Windows rendszerű virtuális asztali bérlő létrehozása                                                    | Ha van Azure-leállás, [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/); egyéb esetben [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), válassza a szolgáltatáshoz tartozó **Windows virtuális asztal** lehetőséget, válassza a probléma típusának **központi telepítése** lehetőséget, majd válassza ki a problémát altípushoz tartozó **Windowsos virtuális asztali bérlő létrehozásakor felmerülő problémákat** .|
| A Marketplace-sablonok elérése Azure Portal       | Ha van Azure-leállás, [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/). <br> <br> Az Azure piactéren elérhető Windows rendszerű virtuális asztali sablonok szabadon hozzáférhetők.|
| Azure Resource Manager sablonok elérése a GitHubról                                  | Tekintse meg a [bérlői és a gazdagép-készlet létrehozása](troubleshoot-set-up-issues-2019.md)a [Windows rendszerű virtuális asztali munkamenetgazda virtuális gépek létrehozása](troubleshoot-set-up-issues-2019.md#creating-windows-virtual-desktop-session-host-vms) című szakaszát. Ha a probléma továbbra is megoldatlan, lépjen kapcsolatba a [GitHub támogatási csapatával](https://github.com/contact). <br> <br> Ha a hiba a GitHub-sablonhoz való hozzáférés után fordul elő, forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/create-ticket/).|
| Session Host Pool Azure Virtual Network (VNET) és Express Route-beállítások               | [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), majd válassza ki a megfelelő szolgáltatást (a hálózatkezelés kategóriában). |
| A munkamenet-gazdagép virtuális gép (VM) létrehozása, ha Azure Resource Manager a Windows rendszerű virtuális asztallal elérhető sablonok nincsenek használatban | [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), majd válassza a **Windows rendszerű virtuális gép** lehetőséget a szolgáltatáshoz. <br> <br> A Windows virtuális asztal szolgáltatásban elérhető Azure Resource Manager sablonokkal kapcsolatos problémákért lásd: a Windows virtuális asztali bérlő létrehozása szakasz a [bérlői és a gazdagép-készlet létrehozásához](troubleshoot-set-up-issues-2019.md). |
| Windows rendszerű virtuális asztali munkamenetgazda-környezet kezelése a Azure Portal    | [Nyisson meg egy Azure-támogatási kérelmet](https://azure.microsoft.com/support/create-ticket/). <br> <br> A Távoli asztali szolgáltatások/Windows rendszerű virtuális asztali PowerShell használatakor a felügyeleti problémákért lásd: a [Windows virtuális asztali PowerShell](troubleshoot-powershell-2019.md) vagy [egy Azure-támogatási kérelem megnyitása](https://azure.microsoft.com/support/create-ticket/), válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, válassza a **konfiguráció és kezelés** lehetőséget a probléma típusa beállításnál, majd válassza ki a probléma altípushoz tartozó **PowerShell használatával kapcsolatos problémákat** . |
| A Windows rendszerű virtuális asztali konfiguráció kezelése a gazdagépek és az alkalmazások csoportjaihoz kötve (alkalmazás-csoportok)      | Tekintse meg a [Windows rendszerű virtuális asztali PowerShellt](troubleshoot-powershell-2019.md), vagy [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), válassza a szolgáltatáshoz a **Windows virtuális asztal** lehetőséget, majd válassza ki a megfelelő problémát.|
| FSLogix-profilok tárolóinak üzembe helyezése és kezelése | Tekintse meg a [FSLogix termékekkel kapcsolatos hibaelhárítási útmutatót](/fslogix/fslogix-trouble-shooting-ht/) , és ha ez nem oldja meg a problémát, [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, válassza a **FSLogix** lehetőséget, majd válassza ki a megfelelő problémát altípust. |
| A távoli asztali ügyfelek működése a Start menüben                                                 | Lásd: [az távoli asztal-ügyfél hibaelhárítása](../troubleshoot-client.md) , és ha ez nem oldja meg a problémát,  [Nyisson meg egy Azure-támogatási kérelmet](https://azure.microsoft.com/support/create-ticket/), válassza a szolgáltatáshoz a **Windows virtuális asztal** lehetőséget, majd válassza a probléma típusa **Távoli asztal ügyfelek** lehetőséget.  <br> <br> Hálózati hiba esetén a felhasználóknak kapcsolatba kell lépniük a hálózati rendszergazdával. |
| Csatlakoztatva, de nincs hírcsatorna                                                                 | A felhasználó kapcsolódási hibáinak megoldása, de a [Windows rendszerű virtuális asztali szolgáltatás kapcsolatainak](troubleshoot-service-connection-2019.md)egyike [sem jelenik meg (nincs hírcsatorna)](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed) szakasza. <br> <br> Ha a felhasználók egy alkalmazáshoz vannak rendelve,  [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, majd válassza a probléma típusa **Távoli asztal ügyfelek** lehetőséget. |
| A hírcsatornák felderítésével kapcsolatos problémák a hálózat miatt                                            | A felhasználóknak kapcsolatba kell lépniük a hálózati rendszergazdával. |
| Ügyfelek csatlakoztatása                                                                    | Tekintse meg a [Windows rendszerű virtuális asztali szolgáltatások kapcsolatait](troubleshoot-service-connection-2019.md) , és ha ez nem oldja meg a problémát, tekintse meg a [munkamenet-gazdagép virtuális gép konfigurációját](troubleshoot-vm-configuration-2019.md) |
| A távoli alkalmazások vagy az asztal rugalmassága                                      | Ha a problémák egy adott alkalmazáshoz vagy termékhez vannak kötve, forduljon a termékért felelős csapathoz. |
| Licencelési üzenetek vagy hibák                                                          | Ha a problémák egy adott alkalmazáshoz vagy termékhez vannak kötve, forduljon a termékért felelős csapathoz. |
| Problémák a Windows rendszerű virtuális asztali eszközök GitHubon történő használatakor (Azure Resource Manager sablonok, diagnosztikai eszköz, felügyeleti eszköz) | A problémák jelentéséhez tekintse meg [a Távoli asztali szolgáltatások Azure Resource Manager sablonokat](https://github.com/Azure/RDS-Templates/blob/master/README.md) . |

## <a name="next-steps"></a>Következő lépések

- A bérlők és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [bérlői és az alkalmazáskészletek létrehozását](troubleshoot-set-up-issues-2019.md)ismertető részt.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration-2019.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali szolgáltatások kapcsolatai](troubleshoot-service-connection-2019.md)című témakört.
- Távoli asztal-ügyfelekkel kapcsolatos problémák elhárításához tekintse meg [a távoli asztal-ügyfél hibaelhárítása](../troubleshoot-client.md) című témakört.
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell-2019.md)című témakört.
- A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerű virtuális asztali környezet](environment-setup-2019.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](../../azure-resource-manager/management/view-activity-logs.md).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](../../azure-resource-manager/templates/deployment-history.md).
