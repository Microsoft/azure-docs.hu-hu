---
title: Windows rendszerű virtuális asztali alkalmazáskészlet létrehozása PowerShell – Azure
description: Hogyan hozhat létre egy gazdagépet a Windows rendszerű virtuális asztalon PowerShell-parancsmagokkal.
author: Heidilohr
ms.topic: how-to
ms.date: 10/02/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ec900f0537030d3ed0d1c875e8125806159bd51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251454"
---
# <a name="create-a-windows-virtual-desktop-host-pool-with-powershell"></a>Windows rendszerű virtuális asztali címkészlet létrehozása a PowerShell-lel

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei a Windows rendszerű virtuális asztali bérlői környezetekben. Mindegyik címkészlet több RemoteApp-csoporthoz, egy asztali alkalmazás csoporthoz és több munkamenet-gazdagéphez is társítható.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már követte a [PowerShell-modul beállítása](powershell-module.md)című témakör utasításait.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>A PowerShell-ügyfél használata a gazdagépek létrehozásához

Futtassa a következő parancsmagot a Windows rendszerű virtuális asztali környezetbe való bejelentkezéshez:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

Ez a parancsmag létrehozza a gazdagépet, a munkaterületet és az asztali alkalmazást. Emellett regisztrálja az asztali alkalmazás csoportját a munkaterületen. Létrehozhat egy munkaterületet ezzel a parancsmaggal, vagy használhat egy meglévő munkaterületet is.

A következő parancsmag futtatásával hozzon létre egy regisztrációs jogkivonatot, amely engedélyezi, hogy a munkamenet-állomás csatlakozzon a gazdagéphez, és mentse a helyi számítógép egy új fájljába. Megadhatja, hogy a regisztrációs jogkivonat mennyi ideig érvényes a-ExpirationHours paraméter használatával.

>[!NOTE]
>A jogkivonat lejárati dátuma nem lehet kevesebb, mint egy óra, és legfeljebb egy hónap. Ha a megadott korláton kívül *expirationtime tulajdonságok* , a parancsmag nem hozza létre a jogkivonatot.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Ha például olyan tokent szeretne létrehozni, amely két órán belül lejár, futtassa a következő parancsmagot:

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Ezután futtassa ezt a parancsmagot, hogy Azure Active Directory felhasználókat adjon hozzá az alapértelmezett asztali alkalmazás csoporthoz a gazdagéphez.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

A következő parancsmag futtatásával Azure Active Directory felhasználói csoportokat adhat hozzá az alapértelmezett asztali alkalmazás csoporthoz a gazdagépen:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Futtassa a következő parancsmagot a regisztrációs jogkivonat egy változóba való exportálásához, amelyet később a [virtuális gépek a Windows rendszerű virtuális asztali készletbe való regisztrálásához](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)fog használni.

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Virtuális gépek létrehozása a gazdagéphez

Most létrehozhat egy Azure-beli virtuális gépet, amely csatlakoztatható a Windows rendszerű virtuális asztali készlethez.

Több módon is létrehozhat egy virtuális gépet:

- [Virtuális gép létrehozása Azure Gallery-rendszerképből](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Virtuális gép létrehozása felügyelt rendszerképből](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Virtuális gép létrehozása nem felügyelt rendszerképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Ha a Windows 7 operációs rendszert futtató virtuális gépet a gazdagép operációs rendszereként telepíti, a létrehozási és a telepítési folyamat némileg eltérő lesz. További részletek: Windows 7 rendszerű virtuális [gépek üzembe helyezése Windows rendszerű virtuális asztalon](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

A munkamenet-gazdagép virtuális gépei létrehozása után Windows- [licencet alkalmazhat egy munkamenet-gazda](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) virtuális gépre, hogy a Windows vagy a Windows Server rendszerű virtuális gépeket egy másik licenc kifizetése nélkül futtassa.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>A virtuális gépek előkészítése a Windows rendszerű virtuális asztali ügynök telepítéséhez

A virtuális gépek előkészítéséhez a következő műveleteket kell elvégeznie, mielőtt telepítené a Windows rendszerű virtuális asztali ügynököket, és regisztrálja a virtuális gépeket a Windows rendszerű virtuális asztali alkalmazáskészletbe:

- A számítógépnek tartományhoz kell csatlakoznia. Ez lehetővé teszi, hogy a bejövő Windows rendszerű virtuális asztali felhasználók a Azure Active Directory fiókjából leképezhetők legyenek Active Directory-fiókjába, és sikeresen hozzáférhessenek a virtuális géphez.
- Ha a virtuális gép Windows Server operációs rendszert futtat, akkor telepítenie kell a Távoli asztal munkamenet-gazdagép (RDSH) szerepkört. A RDSH szerepkör lehetővé teszi a Windows rendszerű virtuális asztali ügynökök megfelelő telepítését.

A tartományhoz való csatlakozás sikeres végrehajtásához tegye a következőket az egyes virtuális gépeken:

1. [Kapcsolódjon a virtuális géphez](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. A virtuális gépen indítsa el a **Vezérlőpultot** , és válassza a **rendszer** elemet.
3. Válassza ki a **számítógép nevét**, válassza a **beállítások módosítása** lehetőséget, majd válassza a **módosítás...** lehetőséget.
4. Válassza a **tartomány** lehetőséget, majd adja meg a Active Directory tartományt a virtuális hálózaton.
5. A hitelesítést olyan tartományi fiókkal végezze el, amely jogosultságokkal rendelkezik a tartományhoz csatlakozó gépekhez.

    >[!NOTE]
    > Ha a virtuális gépeket egy Azure Active Directory Domain Services (Azure AD DS) környezethez csatlakoztatja, győződjön meg arról, hogy a tartományhoz való csatlakozás felhasználója a [HRE DC-rendszergazdák csoport](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)tagja is.

>[!IMPORTANT]
>Azt javasoljuk, hogy ne engedélyezzen olyan házirendeket vagy konfigurációkat, amelyek letiltják Windows Installer. Ha letiltja Windows Installer, a szolgáltatás nem tudja telepíteni az ügynök frissítéseit a munkamenet-gazdagépekre, és a munkamenet-gazdagépek nem fognak megfelelően működni.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>A virtuális gépek regisztrálása a Windows rendszerű virtuális asztali gazdagépen

A virtuális gépek egy Windows rendszerű virtuális asztali készletbe való regisztrálása olyan egyszerű, mint a Windows rendszerű virtuális asztali ügynökök telepítése.

A Windows rendszerű virtuális asztali ügynökök regisztrálásához tegye a következőket az egyes virtuális gépeken:

1. [Kapcsolódjon a virtuális géphez](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. Töltse le és telepítse a Windows rendszerű virtuális asztali ügynököt.
   - Töltse le a [Windows rendszerű virtuális asztali ügynököt](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Indítsa el a telepítőt. Ha a telepítő megkérdezi a regisztrációs tokent, adja meg a **Get-AzWvdRegistrationInfo** parancsmag által kapott értéket.
3. Töltse le és telepítse a Windows rendszerű virtuális asztali ügynök rendszerbetöltőjét.
   - Töltse le a [Windows rendszerű virtuális asztali ügynök Rendszerbetöltőjét](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Indítsa el a telepítőt.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali környezet biztonságossá tételéhez az Azure-ban javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows rendszerű virtuális asztal nem igényel olyan nyitott bejövő portot 3389, amellyel a felhasználók hozzáférhetnek a gazdagép-készlet virtuális gépei számára. Ha hibaelhárítási célból meg kell nyitnia a 3389-as portot, javasoljuk, hogy használja a virtuális gépek igény szerinti [elérését](../security-center/security-center-just-in-time.md). Javasoljuk továbbá, hogy a virtuális gépeket ne rendeljen nyilvános IP-címekhez.

## <a name="update-the-agent"></a>Az ügynök frissítése

Ha a következő esetek valamelyike van, frissítenie kell az ügynököt:

- Előzőleg regisztrált munkamenet-gazdagépet szeretne áttelepíteni egy új gazdagépre
- Frissítés után a munkamenet-gazdagép nem jelenik meg a gazdagép-készletben

Az ügynök frissítése:

1. Jelentkezzen be rendszergazdaként a virtuális gépre.
2. Lépjen a **szolgáltatások** elemre, majd állítsa le a **Rdagent** és **Távoli asztal ügynök** betöltési folyamatait.
3. Ezután keresse meg az ügynököt és a rendszerbetöltő rendszercsomagok. Ezek a **C:\DeployAgent** mappában vagy a telepítéskor mentett helyükön találhatók.
4. Keresse meg a következő fájlokat, és távolítsa el őket:
     
     - Microsoft. RDInfra. RDAgent. Installer-x64-verx. x. x
     - Microsoft. RDInfra. RDAgentBootLoader. Installer-x64

   A fájlok eltávolításához kattintson a jobb gombbal az egyes fájlokra, majd válassza az **Eltávolítás** lehetőséget.
5. Igény szerint a következő beállításjegyzék-beállításokat is eltávolíthatja:
     
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDInfraAgent
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDAgentBootLoader

6. Miután eltávolította ezeket az elemeket, az összes társítást el kell távolítania a régi gazdagép-készlettel. Ha szeretné újra regisztrálni a gazdagépet a szolgáltatásban, kövesse a [virtuális gépek regisztrálása a Windows rendszerű virtuális asztali alkalmazáskészletbe](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)című témakör utasításait.


## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozott egy gazdagépet, feltöltheti azt a RemoteApps szolgáltatással. Ha többet szeretne megtudni az alkalmazások kezeléséről a Windows Virtual Desktopban, tekintse meg az alkalmazáscsoport kezelése oktatóanyagot.

> [!div class="nextstepaction"]
> [Alkalmazás-csoportok kezelése – oktatóanyag](./manage-app-groups.md)
