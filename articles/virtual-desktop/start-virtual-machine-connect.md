---
title: A virtuális gép összekapcsolásának indítása – Azure
description: A virtuális gép elindítása a kapcsolódási szolgáltatásban.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d3ef8e3656051c4a99ab52a7b52a0d623fdf9ce2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303958"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Virtuális gép elindítása a kapcsolaton (előzetes verzió)

> [!IMPORTANT]
> A virtuális gép elindítása a kapcsolaton szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A virtuális gép indítása a csatlakozáskor (előzetes verzió) funkció lehetővé teszi a költségek megtakarítását azáltal, hogy felszabadítja a virtuális gépeket, ha nem használja őket. Ha újra kell használnia a virtuális gépet, mindössze annyit kell tennie, hogy újra bekapcsolja a virtuális gépeket.

>[!NOTE]
>A Windows virtuális asztal (klasszikus) nem támogatja ezt a funkciót.

## <a name="requirements-and-limitations"></a>Követelmények és korlátozások

A virtuális gép indítása a csatlakozási szolgáltatásban csak a személyes gazdagépek esetében engedélyezhető. Ha többet szeretne megtudni a személyes gazdagépekről, tekintse meg a [Windows rendszerű virtuális asztali környezet](environment-setup.md#host-pools)című témakört.

A következő távoli asztali ügyfelek támogatják a virtuális gép elindítása a csatlakozási szolgáltatásban:

- [A webes ügyfél](connect-web.md)
- [A Windows-ügyfél (1,2748-es vagy újabb verzió)](connect-windows-7-10.md)

A frissítésekkel és az ügyfelek támogatásával kapcsolatos közleményeket a [technikai közösségi fórumon](https://aka.ms/wvdtc)tekintheti meg.

A Azure Government felhő jelenleg nem támogatja a Start VM használatát a kapcsolaton.

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Egyéni szerepkör létrehozása a virtuális gép indításához a kapcsolaton

A virtuális gép elindítása a kapcsolódási szolgáltatásban beállítás megkezdése előtt hozzá kell rendelnie a virtuális gépet egy egyéni RBAC (szerepköralapú hozzáférés-vezérlési) szerepkörhöz. Ez a szerepkör lehetővé teszi, hogy a Windows virtuális asztal kezelje az előfizetésében lévő virtuális gépeket. Ezt a szerepkört a virtuális gépek bekapcsolására, az állapotuk vizsgálatára és a diagnosztikai adatok jelentésére is használhatja. Ha többet szeretne megtudni az egyes szerepkörökről, tekintse meg az [Egyéni Azure-szerepköröket](../role-based-access-control/custom-roles.md)ismertető részt.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Az Azure Portal használata egyéni szerepkör hozzárendeléséhez a Start virtuális géphez a kapcsolódáskor:

1. Nyissa meg a Azure Portal, és válassza az **előfizetések** lehetőséget.

2. Nyissa meg a **hozzáférés-vezérlés (iam)** elemet, és válassza **az egyéni szerepkör hozzáadása** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A hozzáférés-vezérlés (IAM) Hozzáadás gombjának legördülő menüjének képernyőképe. Az "egyéni szerepkör hozzáadása" piros színnel van kiemelve.](media/add-custom-role.png)

3. Ezután nevezze el az egyéni szerepkört, és adjon hozzá egy leírást. Javasoljuk, hogy a "virtuális gép elindítása a kapcsolaton" nevet.

4. Az **engedélyek** lapon adja hozzá a következő engedélyeket ahhoz az előfizetéshez, amelyhez a szerepkört hozzárendeli: 
 
   - Microsoft. számítás/virtualMachines/indítás/művelet
   - Microsoft. számítás/virtualMachines/olvasás

5. Ha elkészült, kattintson **az OK gombra**.

Ezt követően hozzá kell rendelnie a szerepkört ahhoz, hogy hozzáférést biztosítson a Windows rendszerű virtuális asztalhoz.

Az egyéni szerepkör hozzárendeléséhez:

1. A **hozzáférés-vezérlés (iam) lapon** válassza a **szerepkör-hozzárendelések hozzáadása** elemet.

2. Válassza ki az imént létrehozott szerepkört.

3. A keresősáv mezőben adja meg és válassza ki a **Windows rendszerű virtuális asztal** elemet.

      >[!NOTE]
      >Ha telepítette a Windows rendszerű virtuális asztalt (klasszikus), két alkalmazást is láthat. Rendelje hozzá a szerepkört mindkét olyan alkalmazáshoz, amelyet látni fog.
      >
      > [!div class="mx-imgBorder"]
      > ![A hozzáférés-vezérlés (IAM) lap képernyőképe. A keresési sávban a Windows rendszerű virtuális asztali és a Windowsos virtuális asztal (klasszikus) is piros színnel van kiemelve.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Egyéni szerepkör létrehozása JSON-fájllal sablonnal

Ha JSON-fájlt használ az egyéni szerepkör létrehozásához, a következő példa egy alapszintű sablont mutat be, amelyet használhat. Győződjön meg arról, hogy lecserélte az előfizetés-azonosító értékét arra az előfizetés-AZONOSÍTÓra, amelyhez hozzá szeretné rendelni a szerepkört.

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

## <a name="configure-the-start-vm-on-connect-feature"></a>A virtuális gép indítása a csatlakozási szolgáltatásban

Most, hogy hozzárendelte az előfizetését a szerepkörhöz, itt az ideje, hogy konfigurálja a Start virtuális gépet a kapcsolódási szolgáltatásban!

### <a name="deployment-considerations"></a>Telepítési szempontok 

A virtuális gép indítása a kapcsolaton egy címkészlet-beállítás. Ha csak a felhasználók csoportját szeretné használni a szolgáltatás használatához, akkor győződjön meg arról, hogy csak a szükséges szerepkört rendeli hozzá a hozzáadni kívánt felhasználókhoz.

>[!IMPORTANT]
> Ezt a funkciót csak a meglévő gazdagép-készletekben lehet konfigurálni. Ez a funkció új alkalmazáskészlet létrehozásakor nem érhető el.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A virtuális gép indítása a kapcsolódáskor a Azure Portal használatával:

1. Nyissa meg a böngészőt, és lépjen [a Azure Portal](https://portal.azure.com/?feature.startVMonConnect=true#home). Javasoljuk, hogy nyissa meg a Azure Portal InPrivate-ablakban.

2. A Azure Portal nyissa meg a **Windows rendszerű virtuális asztalt**.

3. Válassza ki a **gazdagép-készleteket**, majd keresse meg azt a gazdagépet, amely a szerepkörhöz hozzárendelt személyes asztalokat tartalmazza.

   >[!NOTE]
   > A szolgáltatásban konfigurált gazdagép-készletnek a közvetlen szerepkör-hozzárendelésekkel rendelkező személyes asztalokkal kell rendelkeznie. Ha a gazdagép-készletben lévő asztali számítógépek nem megfelelően vannak konfigurálva, a konfigurációs folyamat nem fog működni.

4. A gazdagép készletében válassza a **Tulajdonságok** lehetőséget. A **virtuális gép indítása a kapcsolódáskor** területen válassza az **Igen**, majd a **Mentés** lehetőséget a beállítás azonnali alkalmazásához.

    > [!div class="mx-imgBorder"]
    > ![A Tulajdonságok ablak képernyőképe. A virtuális gép elindítása a kapcsolaton beállítás piros színnel van kiemelve.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>A PowerShell használata

Ha ezt a beállítást a PowerShell-lel szeretné konfigurálni, győződjön meg arról, hogy rendelkezik a konfigurálni kívánt erőforráscsoport és alkalmazáskészletek nevével. Emellett telepítenie kell [a Azure PowerShell modult (2.1.0 vagy újabb verzió)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0).

A virtuális gép indításának beállítása a PowerShell használatával:

1. Nyisson meg egy PowerShell-parancssori ablakot.

2. Futtassa a következő parancsmagot a virtuális gép indításának engedélyezéséhez a kapcsolódáskor:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Futtassa a következő parancsmagot a virtuális gép indításának letiltásához a kapcsolódáskor:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Felhasználó felület

A tipikus munkamenetekben a felhasználók számára a lefoglalt virtuális géphez való kapcsolódáshoz szükséges idő növekszik, mivel a virtuális gépnek újra be kell kapcsolnia az időt, hasonlóan a fizikai számítógép bekapcsolásához. A Távoli asztal-ügyfél rendelkezik egy kijelzővel, amely lehetővé teszi, hogy a felhasználó tudja, hogy a számítógép be van kapcsolva a csatlakozás közben.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a szolgáltatás bármilyen problémába ütközik, javasoljuk, hogy a Windows rendszerű virtuális asztali [diagnosztika funkció](diagnostics-log-analytics.md) használatával ellenőrizze a problémákat. Ha hibaüzenetet kap, ügyeljen arra, hogy az üzenet tartalmának alapos figyelmet kapjon, és másolja le a hiba nevét valahol a hivatkozáshoz.

A Windows rendszerű [virtuális asztali Azure monitor](azure-monitor.md) is használhatja a problémák megoldásához szükséges javaslatok beszerzésére.

## <a name="next-steps"></a>Következő lépések

Ha olyan problémákba ütközik, amelyekkel a hibaelhárítási dokumentáció vagy a diagnosztikai funkció nem oldható fel, tekintse meg a [virtuális gép elindítása a csatlakozási GYIK](start-virtual-machine-connect-faq.md)-ben című témakört.