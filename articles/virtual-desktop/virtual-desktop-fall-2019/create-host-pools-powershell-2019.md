---
title: Windows rendszerű virtuális asztali (klasszikus) alkalmazáskészlet létrehozása PowerShell – Azure
description: Gazdagépek létrehozása a Windows Virtual Desktopban (klasszikus) PowerShell-parancsmagokkal.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c035a7fbafe9b3a42fbd16e3f8377014010ddd49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88003541"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-powershell"></a>Gazdagép létrehozása a Windows Virtual Desktopban (klasszikus) a PowerShell-lel

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../create-host-pools-powershell.md).

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei a Windows rendszerű virtuális asztali bérlői környezetekben. Mindegyik gazdagép tartalmazhatja azt az alkalmazáscsoport-csoportot, amelyet a felhasználók a fizikai asztalon lévők használatával kezelhetnek.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>A PowerShell-ügyfél használata a gazdagépek létrehozásához

Először [töltse le és importálja a](/powershell/windows-virtual-desktop/overview/) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg.

Futtassa a következő parancsmagot a Windows rendszerű virtuális asztali környezetbe való bejelentkezéshez

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ezután futtassa ezt a parancsmagot egy új címkészlet létrehozásához a Windows rendszerű virtuális asztali bérlőben:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

A következő parancsmag futtatásával hozzon létre egy regisztrációs jogkivonatot, amely engedélyezi, hogy a munkamenet-állomás csatlakozzon a gazdagéphez, és mentse a helyi számítógép egy új fájljába. Megadhatja, hogy a regisztrációs jogkivonat mennyi ideig érvényes a-ExpirationHours paraméter használatával.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Ezután futtassa ezt a parancsmagot, hogy Azure Active Directory felhasználókat adjon hozzá az alapértelmezett asztali alkalmazás csoporthoz a gazdagéphez.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Az **Add-RdsAppGroupUser** parancsmag nem támogatja a biztonsági csoportok hozzáadását, és egyszerre csak egy felhasználót ad hozzá az alkalmazás csoporthoz. Ha több felhasználót szeretne hozzáadni az alkalmazás csoportjához, futtassa újra a parancsmagot a megfelelő egyszerű felhasználónevek használatával.

Futtassa a következő parancsmagot a regisztrációs jogkivonat egy változóba való exportálásához, amelyet később a [virtuális gépek a Windows rendszerű virtuális asztali készletbe való regisztrálásához](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)fog használni.

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Virtuális gépek létrehozása a gazdagéphez

Most létrehozhat egy Azure-beli virtuális gépet, amely csatlakoztatható a Windows rendszerű virtuális asztali készlethez.

Több módon is létrehozhat egy virtuális gépet:

- [Virtuális gép létrehozása Azure Gallery-rendszerképből](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Virtuális gép létrehozása felügyelt rendszerképből](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [Virtuális gép létrehozása nem felügyelt rendszerképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>Ha a Windows 7 operációs rendszert futtató virtuális gépet a gazdagép operációs rendszereként telepíti, a létrehozási és a telepítési folyamat némileg eltérő lesz. További részletek: Windows 7 rendszerű virtuális [gépek üzembe helyezése Windows rendszerű virtuális asztalon](deploy-windows-7-virtual-machine.md).

A munkamenet-gazdagép virtuális gépei létrehozása után Windows- [licencet alkalmazhat egy munkamenet-gazda](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) virtuális gépre, hogy a Windows vagy a Windows Server rendszerű virtuális gépeket egy másik licenc kifizetése nélkül futtassa.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>A virtuális gépek előkészítése a Windows rendszerű virtuális asztali ügynök telepítéséhez

A virtuális gépek előkészítéséhez a következő műveleteket kell elvégeznie, mielőtt telepítené a Windows rendszerű virtuális asztali ügynököket, és regisztrálja a virtuális gépeket a Windows rendszerű virtuális asztali alkalmazáskészletbe:

- A számítógépnek tartományhoz kell csatlakoznia. Ez lehetővé teszi, hogy a bejövő Windows rendszerű virtuális asztali felhasználók a Azure Active Directory fiókjából leképezhetők legyenek Active Directory-fiókjába, és sikeresen hozzáférhessenek a virtuális géphez.
- Ha a virtuális gép Windows Server operációs rendszert futtat, akkor telepítenie kell a Távoli asztal munkamenet-gazdagép (RDSH) szerepkört. A RDSH szerepkör lehetővé teszi a Windows rendszerű virtuális asztali ügynökök megfelelő telepítését.

A tartományhoz való csatlakozás sikeres végrehajtásához tegye a következőket az egyes virtuális gépeken:

1. [Kapcsolódjon a virtuális géphez](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. A virtuális gépen indítsa el a **Vezérlőpultot** , és válassza a **rendszer** elemet.
3. Válassza ki a **számítógép nevét**, válassza a **beállítások módosítása** lehetőséget, majd válassza a **módosítás...** lehetőséget.
4. Válassza a **tartomány** lehetőséget, majd adja meg a Active Directory tartományt a virtuális hálózaton.
5. A hitelesítést olyan tartományi fiókkal végezze el, amely jogosultságokkal rendelkezik a tartományhoz csatlakozó gépekhez.

    >[!NOTE]
    > Ha a virtuális gépeket egy Azure Active Directory Domain Services (Azure AD DS) környezethez csatlakoztatja, győződjön meg arról, hogy a tartományhoz való csatlakozás felhasználója a [HRE DC-rendszergazdák csoport](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)tagja is.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>A virtuális gépek regisztrálása a Windows rendszerű virtuális asztali gazdagépen

A virtuális gépek egy Windows rendszerű virtuális asztali készletbe való regisztrálása olyan egyszerű, mint a Windows rendszerű virtuális asztali ügynökök telepítése.

A Windows rendszerű virtuális asztali ügynökök regisztrálásához tegye a következőket az egyes virtuális gépeken:

1. [Kapcsolódjon a virtuális géphez](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. Töltse le és telepítse a Windows rendszerű virtuális asztali ügynököt.
   - Töltse le a [Windows rendszerű virtuális asztali ügynököt](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Kattintson a jobb gombbal a letöltött telepítőre, válassza a **Tulajdonságok**, majd a **Tiltás feloldása** elemet, majd kattintson **az OK gombra**. Ez lehetővé teszi a rendszer számára, hogy megbízzon a telepítőben.
   - Indítsa el a telepítőt. Ha a telepítő megkérdezi a regisztrációs jogkivonatot, adja meg az **export-RdsRegistrationInfo** parancsmagból kapott értéket.
3. Töltse le és telepítse a Windows rendszerű virtuális asztali ügynök rendszerbetöltőjét.
   - Töltse le a [Windows rendszerű virtuális asztali ügynök Rendszerbetöltőjét](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Kattintson a jobb gombbal a letöltött telepítőre, válassza a **Tulajdonságok**, majd a **Tiltás feloldása** elemet, majd kattintson **az OK gombra**. Ez lehetővé teszi a rendszer számára, hogy megbízzon a telepítőben.
   - Indítsa el a telepítőt.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali környezet biztonságossá tételéhez az Azure-ban javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows rendszerű virtuális asztal nem igényel olyan nyitott bejövő portot 3389, amellyel a felhasználók hozzáférhetnek a gazdagép-készlet virtuális gépei számára. Ha hibaelhárítási célból meg kell nyitnia a 3389-as portot, javasoljuk, hogy használja a virtuális gépek igény szerinti [elérését](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozott egy gazdagépet, feltöltheti azt a RemoteApps szolgáltatással. Ha többet szeretne megtudni az alkalmazások kezeléséről a Windows Virtual Desktopban, tekintse meg az alkalmazáscsoport kezelése oktatóanyagot.

> [!div class="nextstepaction"]
> [Alkalmazás-csoportok kezelése – oktatóanyag](../manage-app-groups.md)
