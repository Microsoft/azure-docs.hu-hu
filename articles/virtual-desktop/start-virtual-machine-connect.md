---
title: Virtuális gép csatlakoztatásának kezdete – Azure
description: A virtuális gép indítási funkció konfigurálása a csatlakozáshoz.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 161a4d578509a7752f9438ce8f05d599bdb54e93
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832283"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Virtuális gép létrehozása csatlakozáson (előzetes verzió)

> [!IMPORTANT]
> A Start VM on Connect funkció jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Start Virtual Machine (VM) on Connect (preview) (Virtuális gép létrehozása csatlakozáskor (előzetes verzió) funkció lehetővé teszi a költségek megtakarítását, mert lehetővé teszi a végfelhasználók számára, hogy csak akkor kapcsolják be a virtuális gépeiket, amikor szükségük van rájuk. Ezután kikapcsolhatja a virtuális gépeket, amikor nincs rájuk szükség.

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

2. A **Hozzáférés-vezérlés (IAM) alatt válassza** **az Egyéni szerepkör hozzáadása lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép egy legördülő menüről a Hozzáférés-vezérlés (IAM) Hozzáadás gombjával. Az "Egyéni szerepkör hozzáadása" piros színnel van kiemelve.](media/add-custom-role.png)

3. Ezután nevezze el az egyéni szerepkört, és adjon meg egy leírást. Javasoljuk, hogy nevezze el "start VM on connect" (Virtuális gép indítás csatlakozás után) névvel.

4. Az **Engedélyek lapon** adja hozzá a következő engedélyeket ahhoz az előfizetéshez, amelyhez a szerepkört hozzárendeli: 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read

5. Ha elkészült, kattintson az **OK gombra.**

Ezt követően hozzá kell rendelnie a szerepkört, hogy hozzáférést biztosítson a Windows Virtual Desktop.

Az egyéni szerepkör hozzárendelése:

1. A **Hozzáférés-vezérlés (IAM) lapon válassza a** **Szerepkör-hozzárendelések hozzáadása lehetőséget.**

2. Válassza ki az előbb létrehozott szerepkört.

3. A keresősávba írja be a következőt: , majd válassza a **Windows Virtual Desktop.**

      >[!NOTE]
      >Előfordulhat, hogy két alkalmazást lát, ha telepítette a Windows Virtual Desktop (klasszikus). Rendelje hozzá a szerepkört mindkét látható alkalmazáshoz.
      >
      > [!div class="mx-imgBorder"]
      > ![Képernyőkép a Hozzáférés-vezérlés (IAM) lapról. A keresősávban a Windows Virtual Desktop és Windows Virtual Desktop (klasszikus) piros színnel vannak kiemelve.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Egyéni szerepkör létrehozása JSON-fájlsablonnal

Ha JSON-fájlt használ az egyéni szerepkör létrehozásához, az alábbi példa egy használható alapszintű sablont mutat be. Cserélje le az előfizetés-azonosító értékét arra az előfizetés-azonosítóra, amelyhez hozzá szeretné rendelni a szerepkört.

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

## <a name="configure-the-start-vm-on-connect-feature"></a>A Virtuális gép start on Connect funkció konfigurálása

Most, hogy hozzárendelt egy szerepkört az előfizetéshez, itt az ideje, hogy konfigurálja a Start VM on Connect funkciót!

### <a name="deployment-considerations"></a>Telepítési szempontok 

A Start VM on Connect egy gazdagépkészlet-beállítás. Ha csak a felhasználók egy kiválasztott csoportját szeretné használni, győződjön meg arról, hogy csak a hozzáadni kívánt felhasználókhoz rendeli hozzá a szükséges szerepkört.

>[!IMPORTANT]
> Ez a funkció csak meglévő gazdagépkészletek esetén konfigurálható. Ez a funkció új gazdagépkészlet létrehozásakor nem érhető el.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A virtuális gép Azure Portal virtuális gép csatlakoztatáson való konfiguráláshoz használja a következőt:

1. Nyissa meg a böngészőt, és nyissa [meg a Azure Portal.](https://portal.azure.com)

2. A Azure Portal a **Windows Virtual Desktop.**

3. Válassza **a Gazdagépkészletek** lehetőséget, majd keresse meg azt a gazdagépkészletet, amely azokat a személyes asztalokat tartalmazza, amelyekhez a szerepkört rendelte.

   >[!NOTE]
   > A szolgáltatásban konfigurált gazdagépkészletnek személyes asztali számítógépekkel kell lennie, közvetlen szerepkör-hozzárendelésekkel. Ha a gazdagépkészlet asztalai nem megfelelően vannak konfigurálva, a konfigurációs folyamat nem fog működni.

4. A gazdagépkészletben válassza a **Tulajdonságok lehetőséget.** A Start VM on connect (Virtuális gép **létrehozása csatlakozáskor) területen** válassza az **Igen** lehetőséget, majd válassza a **Mentés** lehetőséget a beállítás azonnali alkalmazáshoz.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Tulajdonságok ablak. A Start VM on connect (Virtuális gép kezdése csatlakozáson) lehetőség pirossal van kiemelve.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>A PowerShell használata

Ha ezt a beállítást a PowerShell-lel szeretné konfigurálni, győződjön meg arról, hogy rendelkezik a konfigurálni kívánt erőforráscsoport és gazdagépkészletek nevével. Emellett telepítenie kell a [Azure PowerShell modult (2.1.0-s](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0)vagy újabb verzió).

Virtuális gép start on Connect konfigurálása a PowerShell használatával:

1. Nyisson meg egy PowerShell-parancsablakot.

2. Futtassa a következő parancsmagot a virtuális gép csatlakozáson való indításának engedélyezéséhez:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Futtassa a következő parancsmagot a Virtuális gép start on Connect letiltásához:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Felhasználó felület

A tipikus munkamenetekben a felszabadított virtuális gépekhez való csatlakozáshoz szükséges idő a felhasználók számára nő, mivel a virtuális gépnek időre van szüksége az újra bekapcsoláshoz, akárcsak egy fizikai számítógép bekapcsolása. A Távoli asztal ügyfél rendelkezik egy jelzővel, amely tudatja a felhasználóval, hogy a számítógép be van kapcsolva a csatlakozás közben.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a funkció bármilyen problémába belefut, javasoljuk, hogy a Windows Virtual Desktop [diagnosztikai](diagnostics-log-analytics.md) szolgáltatás használatával ellenőrizze a problémákat. Ha hibaüzenetet kap, figyeljen oda az üzenet tartalmára, és másolja le valahová a hiba nevét.

A problémák megoldásához [Azure Monitor Windows Virtual Desktop](azure-monitor.md) is javaslatokat kaphat.

## <a name="next-steps"></a>Következő lépések

Ha olyan problémát talál, amely a hibaelhárítási dokumentációban vagy a diagnosztikai funkcióban nem oldaná meg a problémát, tekintse meg a virtuális gép a csatlakozásról való indítással kapcsolatos gyakori [kérdéseket.](start-virtual-machine-connect-faq.md)
