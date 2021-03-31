---
title: A Windows rendszerű virtuális asztali kapcsolat hibáinak megoldása – Azure
description: Problémák megoldása a Windows rendszerű virtuális asztali bérlői környezetben a szolgáltatási kapcsolatok beállítása során.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 42502864cfed177adfe487e9c59247579628fec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539069"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows rendszerű virtuális asztali szolgáltatások kapcsolatai

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).

Ez a cikk a Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák megoldásához használható.

## <a name="provide-feedback"></a>Visszajelzés küldése

Küldhet visszajelzést, és megvitathatja a Windows rendszerű virtuális asztali szolgáltatást a termék csapatával és a többi aktív közösségi taggal a [Windows Virtual Desktop technikai közösségében](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>A felhasználó csatlakozik, de semmi nem jelenik meg (nincs hírcsatorna)

A felhasználók elindíthatják Távoli asztal-ügyfeleket, és képesek hitelesíteni magukat, azonban a felhasználó nem lát ikonokat a webes felderítési hírcsatornában.

1. Győződjön meg arról, hogy a problémát jelentő felhasználó a következő parancssor használatával van hozzárendelve az alkalmazási csoportokhoz:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Győződjön meg arról, hogy a felhasználó a megfelelő hitelesítő adatokkal jelentkezik be.

3. Ha a webes ügyfél használatban van, ellenőrizze, hogy nincsenek-e gyorsítótárazott hitelesítő adatok.

4. Ha a felhasználó egy Azure Active Directory (AD) felhasználói csoport tagja, győződjön meg róla, hogy a felhasználói csoport a terjesztési csoport helyett biztonsági csoport. A Windows virtuális asztal nem támogatja az Azure AD terjesztési csoportokat.

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>A felhasználó elveszíti a meglévő hírcsatornát, és nem jelenik meg távoli erőforrás (nincs hírcsatorna)

Ez a hiba általában akkor jelenik meg, ha egy felhasználó áthelyezte az előfizetést egy Azure AD-bérlőből egy másikba. Ennek eredményeképpen a szolgáltatás elveszti a felhasználói hozzárendeléseinek nyomon követését, mivel ezek továbbra is a régi Azure AD-bérlőhöz vannak kötve.

Ennek megoldásához mindössze annyit kell tennie, hogy újra hozzárendeli a felhasználókat az alkalmazás-csoportjaihoz.

Ez akkor is előfordulhat, ha egy CSP-szolgáltató létrehozta az előfizetést, majd átviszi az ügyfelet. Az erőforrás-szolgáltató újbóli regisztrálásának feloldásához.

1. Jelentkezzen be az Azure Portalra.
2. Nyissa meg az **előfizetést**, majd válassza ki az előfizetését.
3. Az oldal bal oldalán található menüben válassza az **erőforrás-szolgáltató** elemet.
4. Keresse meg és válassza ki a **Microsoft. DesktopVirtualization** elemet, majd válassza az **ismételt regisztrálás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A Windows rendszerű virtuális asztali környezetek és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [környezet és az alkalmazáskészlet létrehozása](troubleshoot-set-up-issues.md)című témakört
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A Windows rendszerű virtuális asztali ügynökkel vagy a munkamenet-kapcsolattal kapcsolatos problémák elhárításához lásd: a [Windows rendszerű virtuális asztali ügynökkel kapcsolatos gyakori problémák elhárítása](troubleshoot-agent.md).
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
