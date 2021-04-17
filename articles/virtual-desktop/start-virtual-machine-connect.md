---
title: Virtuális gép csatlakoztatásának kezdete – Azure
description: A virtuális gép indítási funkció konfigurálása a csatlakozáshoz.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: af95cf5d3e4112c717d653062f186797d48fb515
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389808"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Virtuális gép létrehozása csatlakozáson (előzetes verzió)

> [!IMPORTANT]
> A Start VM on Connect funkció jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Start Virtual Machine (VM) on Connect (preview) (Virtuális gép létrehozása csatlakozáskor (előzetes verzió) funkcióval költségtakarékoskodhat, ha nem használja a virtuális gépeket. Ha újra használnia kell a virtuális gépet, mindössze annyit kell megtennie, hogy ismét bekapcsolja a virtuális gépeket.

>[!NOTE]
>Windows Virtual Desktop (klasszikus) nem támogatja ezt a funkciót.

## <a name="requirements-and-limitations"></a>Követelmények és korlátozások

Személyes gazdagépkészletek esetén csak a Start VM on Connect (Virtuális gép csatlakoztatása) funkciót engedélyezheti. További információ a személyes gazdagépkészletről: Windows Virtual Desktop [környezet.](environment-setup.md#host-pools)

A következő távoli asztali ügyfelek támogatják a Start VM on Connect szolgáltatást:

- [A webes ügyfél](connect-web.md)
- [A Windows-ügyfél (1.2748-as vagy újabb verzió)](connect-windows-7-10.md)

A frissítésekkel és az ügyféltámogatással kapcsolatos közleményeket a [Tech Community fórumán ellenőrizheti.](https://aka.ms/wvdtc)

A Azure Government felhő jelenleg nem támogatja a virtuális gép csatlakoztatását.

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Egyéni szerepkör létrehozása a virtuális gép csatlakozáson való indításhoz

Mielőtt konfigurálni tudja a Virtuális gép létrehozása csatlakozáson funkciót, hozzá kell rendelnie a virtuális géphez egy egyéni RBAC(szerepköralapú hozzáférés-vezérlés) szerepkört. Ezzel a szerepkörsel Windows Virtual Desktop kezelheti az előfizetésében a virtuális gépeket. Ezzel a szerepkörsel bekapcsolhatja a virtuális gépeket, ellenőrizheti azok állapotát, és diagnosztikai adatokat is jelenthet. Ha többet szeretne tudni az egyes szerepkörök szerepiről, nézze meg az [egyéni Azure-szerepköröket.](../role-based-access-control/custom-roles.md)

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Ha a virtuális gép Azure Portal szeretne egyéni szerepkört hozzárendelni a Start VM on Connecthez:

1. Nyissa meg a Azure Portal, és nyissa meg az **Előfizetések lehetőséget.**

2. Válassza a **Hozzáférés-vezérlés (IAM) lehetőséget, és** **válassza az Egyéni szerepkör hozzáadása lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Hozzáférés-vezérlés (IAM) Hozzáadás gombjának legördülő menüjéről. Az "Egyéni szerepkör hozzáadása" pirossal van kiemelve.](media/add-custom-role.png)

3. Ezután nevezze el az egyéni szerepkört, és adjon meg egy leírást. Javasoljuk, hogy a nevet a következőnek nevezze el: "start VM on connect".

4. Az **Engedélyek lapon** adja hozzá a következő engedélyeket ahhoz az előfizetéshez, amelyhez a szerepkört hozzárendeli: 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read

5. Ha elkészült, kattintson az **OK gombra.**

Ezután hozzá kell rendelnie a szerepkört, hogy hozzáférést biztosítson a Windows Virtual Desktop.

Az egyéni szerepkör hozzárendelése:

1. A **Hozzáférés-vezérlés (IAM) lapon válassza a** **Szerepkör-hozzárendelések hozzáadása lehetőséget.**

2. Válassza ki az előbb létrehozott szerepkört.

3. A keresősávba írja be a következőt: , majd válassza a **Windows Virtual Desktop.**

      >[!NOTE]
      >Ha üzembe helyezett egy alkalmazást (klasszikus), két Windows Virtual Desktop is láthat. Rendelje hozzá a szerepkört mindkét látható alkalmazáshoz.
      >
      > [!div class="mx-imgBorder"]
      > ![Képernyőkép a Hozzáférés-vezérlés (IAM) lapról. A keresősávban a Windows Virtual Desktop és Windows Virtual Desktop (klasszikus) piros színnel vannak kiemelve.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Egyéni szerepkör létrehozása JSON-fájlsablonnal

Ha JSON-fájlt használ az egyéni szerepkör létrehozásához, az alábbi példa egy használható alapszintű sablont mutat be. Az előfizetés-azonosító értékét cserélje le arra az előfizetés-azonosítóra, amelyhez hozzá szeretné rendelni a szerepkört.

```json
{
    "properties": {
        "roleName": "start VM on connect",
        "description": "Friendly description.",
        "assignableScopes": [
            "/subscriptions/<SubscriptionID>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/virtualMachines/start/action",
                    "Microsoft.Compute/virtualMachines/read"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="configure-the-start-vm-on-connect-feature"></a>A Start VM on Connect szolgáltatás konfigurálása

Most, hogy hozzárendelt egy szerepkört az előfizetéshez, itt az ideje, hogy konfigurálja a Virtuális gép start on Connect funkcióját!

### <a name="deployment-considerations"></a>Telepítési szempontok 

A Start VM on Connect egy gazdagépkészlet-beállítás. Ha csak a felhasználók egy kiválasztott csoportját szeretné használni, győződjön meg arról, hogy csak a hozzáadni kívánt felhasználókhoz rendeli hozzá a szükséges szerepkört.

>[!IMPORTANT]
> Ez a funkció csak meglévő gazdagépkészletek esetén konfigurálható. Ez a funkció új gazdagépkészlet létrehozásakor nem érhető el.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A virtuális gép Azure Portal a Virtuális gép csatlakoztatása beállításhoz:

1. Nyissa meg a böngészőt, és nyissa [meg a Azure Portal.](https://portal.azure.com)

2. A Azure Portal a **Windows Virtual Desktop.**

3. Válassza **a Gazdagépkészletek** lehetőséget, majd keresse meg azt a gazdagépkészletet, amely azokat a személyes asztali számítógépeket tartalmazza, amelyekhez a szerepkört rendelte.

   >[!NOTE]
   > A szolgáltatásban konfigurált gazdagépkészletnek közvetlen szerepkör-hozzárendeléssel rendelkezik személyes asztalokkal. Ha a gazdagépkészletben található asztalok nincsenek megfelelően konfigurálva, a konfigurációs folyamat nem fog működni.

4. A gazdagépkészletben válassza a Tulajdonságok **lehetőséget.** A **Start VM on connect (Virtuális gép létrehozása csatlakozáskor) területen** válassza az **Igen** lehetőséget, majd kattintson a **Mentés** gombra a beállítás azonnali alkalmazáshoz.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Tulajdonságok ablak. A Start VM on connect (Virtuális gép indítás csatlakozáson) lehetőség pirossal van kiemelve.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>A PowerShell használata

Ha ezt a beállítást a PowerShell-lel szeretné konfigurálni, győződjön meg arról, hogy rendelkezik a konfigurálni kívánt erőforráscsoport és gazdagépkészletek nevével. Emellett telepítenie kell a [Azure PowerShell modult (2.1.0-s](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0)vagy újabb verzió).

A Virtuális gép start on Connect konfigurálása a PowerShell használatával:

1. Nyisson meg egy PowerShell-parancsablakot.

2. Futtassa a következő parancsmagot a virtuális gép csatlakozáson való indításának engedélyezéséhez:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Futtassa a következő parancsmagot a virtuális gép csatlakozáson való indításának letiltásához:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Felhasználó felület

A tipikus munkamenetekben a felhasználók számára a felszabadított virtuális gépekhez való csatlakozáshoz szükséges idő növekszik, mert a virtuális gépnek időre van szüksége az újra bekapcsoláshoz, akárcsak egy fizikai számítógép bekapcsolása. A Távoli asztal-ügyfél rendelkezik egy jelzővel, amely tudatja a felhasználóval, hogy a számítógép be van kapcsolva a csatlakozás közben.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a funkcióval probléma merül fel, javasoljuk, hogy a Windows Virtual Desktop [diagnosztikai](diagnostics-log-analytics.md) szolgáltatás használatával ellenőrizze a problémákat. Ha hibaüzenetet kap, ügyeljen arra, hogy nagy figyelmet fordítson az üzenet tartalmára, és másolja le a hiba nevét valahová referenciaként.

A problémák megoldásához [Azure Monitor Windows Virtual Desktop](azure-monitor.md) is használhatja a Windows Virtual Desktop javaslatokat.

## <a name="next-steps"></a>Következő lépések

Ha olyan problémát talál, amely miatt a hibaelhárítási dokumentáció vagy a diagnosztikai funkció nem oldotta meg a problémát, tekintse meg a virtuális gép indításáról a csatlakozással kapcsolatos gyakori [kérdéseket.](start-virtual-machine-connect-faq.md)