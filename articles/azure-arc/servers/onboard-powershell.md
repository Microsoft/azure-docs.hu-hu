---
title: Hibrid gépek csatlakoztatása az Azure-hoz a PowerShell használatával
description: Ebből a cikkből megtudhatja, hogyan telepítheti az ügynököt, és csatlakoztathat egy gépet az Azure-hoz az engedélyezett Azure Arc használatával. Ezt a PowerShell használatával is meg lehet tenni.
ms.date: 10/28/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d6963a53ac14c9d6727a8d53e781bc8b8389b76e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831616"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Hibrid gépek csatlakoztatása az Azure-hoz a PowerShell használatával

A virtuális gépekkel Azure Arc manuális lépésekkel engedélyezheti őket egy vagy több Windows vagy Linux rendszerű gépen a környezetben. Másik lehetőségként a [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) PowerShell-parancsmaggal letöltheti a Csatlakoztatott gép ügynököt, telepítheti az ügynököt, és regisztrálhatja a gépet a Azure Arc. A parancsmag letölti a Windows-ügynökcsomagot (Windows Installer) a Microsoft letöltőközpontból, a Linux-ügynökcsomagot pedig a Microsoft csomagtárból.

Ehhez a módszerhez rendszergazdai jogosultsággal kell rendelkeznie a számítógépen az ügynök telepítéséhez és konfigurálásához. Linux rendszeren a gyökérfiókkal, Windows rendszeren pedig a Helyi rendszergazdák csoport tagja. Ezt a folyamatot interaktívan vagy távolról is befejezheti egy Windows-kiszolgálón a PowerShell távoli [eljárásának használatával.](/powershell/scripting/learn/ps101/08-powershell-remoting)

Mielőtt hozzá is kezd, tekintse át az [előfeltételeket,](agent-overview.md#prerequisites) és ellenőrizze, hogy az előfizetése és az erőforrásai megfelelnek-e a követelményeknek. További információ a támogatott régiókról és egyéb kapcsolódó szempontokról: [Támogatott Azure-régiók.](overview.md#supported-regions)

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

- Egy gép Azure PowerShell. Útmutatásért lásd: [Install and configure Azure PowerShell.](/powershell/azure/)

A PowerShell használatával kezelheti a virtuálisgép-bővítményeket az engedélyezett Azure Arc felügyelt hibrid kiszolgálókon. A PowerShell használata előtt telepítse a `Az.ConnectedMachine` modult. Futtassa a következő parancsot az engedélyezett kiszolgálón Azure Arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

A telepítés befejezése után a következő üzenet jelenik meg:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Az ügynök telepítése és csatlakozás az Azure-hoz

1. Nyisson meg egy PowerShell-konzolt emelt szintű jogosultságokkal.

2. Jelentkezzen be az Azure-ba a parancs `Connect-AzAccount` futtatásával.

3. A Connected Machine-ügynök telepítéséhez használja `Connect-AzConnectedMachine` a et a `-Name` , a és a `-ResourceGroupName` `-Location` paraméterekkel. A paraméterrel felülbírálhatja az alapértelmezett előfizetést a bejelentkezés után létrehozott `-SubscriptionId` Azure-környezet eredményeként. Futtassa az alábbi parancsok egyikét:

    * A Connected Machine-ügynöknek az Azure-ral közvetlenül kommunikáló célgépre való telepítéséhez futtassa a következőt:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * A Connected Machine-ügynök proxykiszolgálón keresztül kommunikáló célgépre való telepítéséhez futtassa a következőt:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Ha az ügynök nem indul el a telepítés befejezése után, tekintse meg a naplókat a hiba részletes információiért. Windows rendszeren ellenőrizze a következő fájlt: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. Linux rendszeren ellenőrizze a következő fájlt: */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Telepítés és csatlakozás PowerShell-eltolás használatával

Így konfigurálhatja egy vagy több Windows-kiszolgálót olyan kiszolgálókkal, amelyeken engedélyezve vannak a Azure Arc. Engedélyeznie kell a PowerShell távoli távelérését a távoli gépen. Ehhez az `Enable-PSRemoting` parancsmagot használhatja.

1. Nyisson meg egy PowerShell-konzolt rendszergazdaként.

2. Jelentkezzen be az Azure-ba a parancs `Connect-AzAccount` futtatásával.

3. A Connected Machine-ügynök telepítéséhez használja `Connect-AzConnectedMachine` a következőt: `-ResourceGroupName` , és `-Location` paraméterek. Az Azure-erőforrások nevei automatikusan az egyes kiszolgálók állomásnevét használják. A paraméterrel felülbírálhatja az alapértelmezett előfizetést a bejelentkezés után létrehozott `-SubscriptionId` Azure-környezet eredményeként.

    * Futtassa a következő parancsot a Csatlakoztatottgép-ügynök telepítéséhez a célgépre, amely közvetlenül tud kommunikálni az Azure-ral:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Ha egyszerre több távoli gépre is telepíteni szeretne egy Csatlakoztatottgép-ügynököt, adja hozzá a távoli gépek nevének vesszővel elválasztott listáját.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    Az alábbi példa az egyetlen gépet célzó parancs eredményeit mutatja be:
    
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

Miután telepíti és konfigurálja az ügynököt az Azure Arc-kompatibilis kiszolgálókon való regisztrálásra, Azure Portal a kiszolgáló sikeres csatlakozásának ellenőrzéséhez. Tekintse meg a gépet a [Azure Portal.](https://portal.azure.com)

![A Kiszolgálók irányítópult képernyőképe, amely egy sikeres kiszolgálókapcsolatot mutat.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Következő lépések

* Szükség esetén tekintse meg [a Csatlakoztatott gép ügynök hibaelhárítása útmutatót.](troubleshoot-agent-onboard.md)

* Ismerje meg, hogyan kezelheti a gépet a [Azure Policy.](../../governance/policy/overview.md) Használhatja a [](../../governance/policy/concepts/guest-configuration.md)virtuálisgép-vendégkonfigurációt, ellenőrizheti, hogy a gép a várt Log Analytics-munkaterületnek jelent-e, és engedélyezheti a virtuális gépekkel Azure Monitor [monitorozást.](../../azure-monitor/vm/vminsights-enable-policy.md)

* További információ a [Log Analytics-ügynökről:](../../azure-monitor/agents/log-analytics-agent.md). A Windowshoz és Linuxhoz használható Log Analytics-ügynökre akkor van szükség, ha az operációs rendszer és a számítási feladatok figyelési adatait szeretné gyűjteni, vagy runbookokkal vagy olyan szolgáltatásokkal szeretné kezelni Azure Automation forgatókönyveket vagy szolgáltatásokat, mint Update Management. Ez az ügynök más Azure-szolgáltatások, például a [Azure Security Center.](../../security-center/security-center-introduction.md)
