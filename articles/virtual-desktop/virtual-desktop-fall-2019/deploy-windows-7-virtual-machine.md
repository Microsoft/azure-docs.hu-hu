---
title: Windows 7 rendszerű virtuális gép üzembe helyezése (klasszikus) – Azure
description: Windows 7 rendszerű virtuális gép konfigurálása és üzembe helyezése Windows rendszerű virtuális asztali Windows virtuális asztalon (klasszikus).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 259e49fbdd6a0eb392ddf6a3cd3c318798cfabd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88005063"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop-classic"></a>Windows 7 rendszerű virtuális gép üzembe helyezése Windowsos virtuális asztalon (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha a Windows virtuális asztal jelenlegi verziójában bemutatott Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretné felügyelni, tekintse meg [ezt a cikket](../deploy-windows-7-virtual-machine.md).

Windows 7 rendszerű virtuális gép (VM) Windows rendszerű virtuális asztalon való üzembe helyezésének folyamata némileg eltér a Windows újabb verzióit futtató virtuális gépeknél. Ebből az útmutatóból megtudhatja, hogyan helyezheti üzembe a Windows 7 rendszert.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, kövesse a [gazdagép létrehozása a PowerShell](create-host-pools-powershell-2019.md) használatával című témakör utasításait a gazdagépek létrehozásához. Ezután kövesse a [gazdagépek létrehozása az Azure Marketplace-ben](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group) című témakör utasításait, hogy egy vagy több felhasználót rendeljen az asztali alkalmazás csoportjához.

## <a name="configure-a-windows-7-virtual-machine"></a>Windows 7 rendszerű virtuális gép konfigurálása

Ha elvégezte az előfeltételeket, készen áll a Windows 7 rendszerű virtuális gép konfigurálására a Windows Virtual Desktopban.

Windows 7 rendszerű virtuális gép beállítása Windows rendszerű virtuális asztalon:

1. Jelentkezzen be a Azure Portalba, és keresse meg a Windows 7 Enterprise rendszerképet, vagy töltse fel a saját testreszabott Windows 7 Enterprise-(x64-) rendszerképét.
2. Helyezzen üzembe egy vagy több virtuális gépet a Windows 7 Enterprise rendszerű gazdagép operációs rendszereként. Győződjön meg arról, hogy a virtuális gépek engedélyezik RDP protokoll (RDP) (a TCP/3389 portot).
3. Kapcsolódjon a Windows 7 Enterprise-gazdagéphez az RDP használatával, és hitelesítse magát a telepítés konfigurálása során megadott hitelesítő adatokkal.
4. Adja hozzá azt a fiókot, amelyet a gazdagéphez a "Távoli asztal user" csoport RDP-vel való csatlakozásakor használt. Ha ezt nem teszi meg, előfordulhat, hogy nem tud csatlakozni a virtuális géphez a Active Directory tartományhoz való csatlakoztatása után.
5. Nyissa meg a Windows Updatet a virtuális gépen.
6. Telepítse az összes Windows-frissítést a fontos kategóriába.
7. Telepítse az összes Windows-frissítést a választható kategóriába (kivéve a nyelvi csomagokat). Ezzel telepíti a RDP protokoll 8,0 frissítést ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)), amelyre el kell végeznie ezeket az utasításokat.
8. Nyissa meg a Helyicsoportházirend-szerkesztő, és navigáljon a **Számítógép konfigurációja**  >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **Távoli asztali szolgáltatások**  >  **Távoli asztal munkamenet-gazdagép**  >  **távoli munkamenet-környezete** elemre.
9. Engedélyezze a RDP protokoll 8,0 szabályzatot.
10. Csatlakoztassa a virtuális gépet a Active Directory tartományhoz.
11. Indítsa újra a virtuális gépet a következő parancs futtatásával:

     ```cmd
     shutdown /r /t 0
     ```

12. A regisztrációs jogkivonat beszerzéséhez kövesse az [itt](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) található utasításokat.
13. [Töltse le a Windows 7 rendszerhez készült Windows Virtual Desktop Agent ügynököt](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Töltse le a Windows virtuális asztali ügynök kezelőjét a Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)rendszerhez.
15. Nyissa meg a Windows rendszerű virtuális asztali ügynök telepítőjét, és kövesse az utasításokat. Ha a rendszer kéri, adja meg a 12. lépésben létrehozott regisztrációs kulcsot.
16. Nyissa meg a Windows rendszerű virtuális asztali ügynök kezelőjét, és kövesse az utasításokat.
17. Ha szeretné, tiltsa le a TCP/3389 portot a virtuális géphez való közvetlen RDP protokoll-hozzáférés eltávolításához.
18. Szükség esetén győződjön meg arról, hogy a .NET-keretrendszer legalább 4.7.2-verzióval rendelkezik. Ez különösen akkor fontos, ha egyéni rendszerképet hoz létre.

## <a name="next-steps"></a>Következő lépések

A Windows rendszerű virtuális asztal üzembe helyezése most már készen áll a használatra. A kezdéshez [töltse le a Windows rendszerű virtuális asztali ügyfél legújabb verzióját](https://aka.ms/wvd/clients/windows) .

Az ismert problémák és a Windows 7 rendszerhez készült hibaelhárítási utasítások listáját a Windows rendszerű virtuális asztalon tekintse meg a hibaelhárítási cikket a Windows 7 rendszerű virtuális [gépek hibaelhárítása a Windows rendszerű virtuális asztalon](troubleshoot-windows-7-vm.md)című cikkben.
