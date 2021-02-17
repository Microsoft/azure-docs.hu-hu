---
title: Hibrid gépek összekötése az Azure-ba a PowerShell használatával
description: Ebből a cikkből megtudhatja, hogyan telepítheti az ügynököt, és hogyan csatlakoztatható egy gép az Azure-hoz az Azure arc-kompatibilis kiszolgálók használatával. Ezt a PowerShell használatával teheti meg.
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 07a00de9077378ce3e3f7a7578b66e93d1b04f2b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584937"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Hibrid gépek összekötése az Azure-ba a PowerShell használatával

Az Azure arc használatával engedélyezett kiszolgálók esetében manuális lépésekkel engedélyezheti azokat egy vagy több Windows-vagy Linux-gépen a környezetben. Azt is megteheti, hogy a [Connect-AzConnectedMachine PowerShell-](/powershell/module/az.connectedmachine/remove-azconnectedmachine) parancsmag használatával letölti a csatlakoztatott gépi ügynököt, telepíti az ügynököt, és regisztrálja a gépet az Azure arc segítségével. A parancsmag letölti a Windows-ügynök csomagját (Windows Installer) a Microsoft letöltőközpontból, valamint a Linux-ügynök csomagját a Microsoft Package adattárból.

Ehhez a módszerhez rendszergazdai jogosultságokkal kell rendelkeznie a gépen az ügynök telepítéséhez és konfigurálásához. Linux rendszeren a legfelső szintű fiók, a Windows rendszeren pedig a helyi Rendszergazdák csoport tagja. Ez a folyamat interaktív módon vagy távolról is elvégezhető a Windows Serveren a [PowerShell távelérés](/powershell/scripting/learn/ps101/08-powershell-remoting)használatával.

Az első lépések előtt tekintse át az [előfeltételeket](agent-overview.md#prerequisites) , és ellenőrizze, hogy az előfizetés és az erőforrások megfelelnek-e a követelményeknek. A támogatott régiókkal és az egyéb kapcsolódó megfontolásokkal kapcsolatos információkért lásd: [támogatott Azure-régiók](overview.md#supported-regions).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

- Azure PowerShell tartalmazó gép. Útmutatásért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).

A PowerShell használatával kezelheti a virtuálisgép-bővítményeket az Azure arc-kompatibilis kiszolgálók által kezelt hibrid kiszolgálókon. A PowerShell használata előtt telepítse a `Az.ConnectedMachine` modult. Futtassa az alábbi parancsot a kiszolgálón, amelyen engedélyezve van az Azure arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

A telepítés befejezésekor a következő üzenet jelenik meg:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Az ügynök telepítése és az Azure-hoz való kapcsolódás

1. Nyisson meg egy PowerShell-konzolt emelt szintű jogosultságokkal.

2. Jelentkezzen be az Azure-ba a parancs futtatásával `Connect-AzAccount` .

3. A csatlakoztatott számítógép ügynökének telepítéséhez használja `Connect-AzConnectedMachine` a, a `-Name` `-ResourceGroupName` és a `-Location` paramétereket. A `-SubscriptionId` paraméter használatával felülbírálhatja az alapértelmezett előfizetést a bejelentkezés után létrehozott Azure-környezet eredményeképpen. Futtassa a következő parancsok egyikét:

    * Az Azure-hoz közvetlenül kommunikáló célszámítógépen található csatlakoztatott számítógép-ügynök telepítéséhez futtassa a következőt:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * A csatlakoztatott számítógép ügynökének a proxykiszolgálón keresztül kommunikáló célszámítógépen való telepítéséhez futtassa a következőt:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Ha a telepítés befejezése után az ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. Windows rendszeren keresse meg a következő fájlt: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. Linux rendszeren keresse meg a következő fájlt: */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Telepítés és kapcsolat a PowerShell-távelérés használatával

Ebből a témakörből megtudhatja, hogyan konfigurálhat egy vagy több Windows-kiszolgálót az Azure arc használatával engedélyezett kiszolgálókkal. Engedélyeznie kell a PowerShell távelérést a távoli gépen. Ehhez az `Enable-PSRemoting` parancsmagot használhatja.

1. Nyisson meg egy PowerShell-konzolt rendszergazdaként.

2. Jelentkezzen be az Azure-ba a parancs futtatásával `Connect-AzAccount` .

3. A csatlakoztatott számítógép ügynökének telepítéséhez használja `Connect-AzConnectedMachine` a és a `-ResourceGroupName` `-Location` paramétereket. Az Azure-erőforrások neve automatikusan az egyes kiszolgálók állomásnevét fogja használni. A `-SubscriptionId` paraméter használatával felülbírálhatja az alapértelmezett előfizetést a bejelentkezés után létrehozott Azure-környezet eredményeképpen.

    * A következő parancs futtatásával telepítheti a csatlakoztatott gépi ügynököt a célszámítógépen, amely közvetlenül tud kommunikálni az Azure-ban:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Ha egyszerre több távoli gépen szeretné telepíteni a csatlakoztatott gépi ügynököt, vegyen fel egy listát a távoli gépek neveiről, amelyek mindegyike vesszővel elválasztva.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    Az alábbi példa egy adott gépet célzó parancs eredményét jeleníti meg:
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Az Azure Arc csatlakozásának ellenőrzése

Miután telepítette és konfigurálta az ügynököt az Azure arc-kompatibilis kiszolgálókhoz való regisztráláshoz, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakozott-e. Megtekintheti a gépet a [Azure Portalban](https://portal.azure.com).

![Képernyőkép a kiszolgálók irányítópultról, amely a kiszolgáló sikeres csatlakoztatását mutatja.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Következő lépések

* Ha szükséges, tekintse meg a [csatlakoztatott gép ügynökével kapcsolatos útmutatót](troubleshoot-agent-onboard.md).

* Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával. A virtuális gép [vendég konfigurációját](../../governance/policy/concepts/guest-configuration.md)használhatja, ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, és lehetővé teszi a figyelést a [Azure monitor virtuális gépekkel](../../azure-monitor/vm/vminsights-enable-policy.md).

* További információ a [log Analytics-ügynökről](../../azure-monitor/agents/log-analytics-agent.md). A Windows és a Linux rendszerhez készült Log Analytics ügynökre akkor van szükség, ha az operációs rendszer és a számítási feladatok figyelésére szolgáló adatokat szeretné összegyűjteni, vagy a Azure Automation runbookok vagy más funkciókkal, például a Update Management használatával kívánja kezelni Ez az ügynök más Azure-szolgáltatások, például a [Azure Security Center](../../security-center/security-center-introduction.md)használatához is szükséges.
