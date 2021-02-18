---
title: Csatlakoztatott számítógép ügynökének telepítése a Windows PowerShell DSC használatával
description: Ebből a cikkből megtudhatja, hogyan csatlakoztathatók a gépek az Azure-hoz az Azure arc-kompatibilis kiszolgálók használatával a Windows PowerShell DSC használatával.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: c0ae9c97afe14559aa36c1b8387f07897aa4c43b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587637"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>A csatlakoztatott gép ügynökének telepítése a Windows PowerShell DSC használatával

A [Windows PowerShell desired State Configuration](/powershell/scripting/dsc/getting-started/winGettingStarted) (DSC) használatával automatizálhatja a szoftverek telepítését és konfigurációját a Windows rendszerű számítógépeken. Ez a cikk azt ismerteti, hogyan használható a DSC az Azure arc-kompatibilis kiszolgálók csatlakoztatott gépi ügynökének telepítéséhez hibrid Windows rendszerű gépeken.

## <a name="requirements"></a>Követelmények

- Windows PowerShell 4,0 vagy újabb verzió

- A [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc) DSC modul

- Egy egyszerű szolgáltatásnév, amely nem interaktív módon csatlakozik a gépekhez az Azure arc-kompatibilis kiszolgálókhoz. Ha nem hozott létre egy egyszerű szolgáltatásnevet az arc-kompatibilis kiszolgálókhoz, kövesse az [egyszerű szolgáltatás létrehozása](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) a bevezetéshez című szakaszban ismertetett lépéseket.

## <a name="install-the-connectedmachine-dsc-module"></a>A ConnectedMachine DSC moduljának telepítése

1. A modul manuális telepítéséhez töltse le a forráskódot, és bontsa ki a projekt könyvtára tartalmát a következőre: `$env:ProgramFiles\WindowsPowerShell\Modules folder` . Vagy futtassa a következő parancsot a PowerShell-galériából a PowerShellGet használatával történő telepítéshez (a PowerShell 5,0-ben):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. A telepítés megerősítéséhez futtassa a következő parancsot, és győződjön meg arról, hogy az Azure Connected Machine DSC-erőforrásai elérhetők.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   A kimenetben a következőhöz hasonlónak kell megjelennie:

   ![A csatlakoztatott Machine DSC-modul telepítési példájának megerősítése](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Az ügynök telepítése és az Azure-hoz való kapcsolódás

Az ebben a modulban található erőforrások úgy vannak kialakítva, hogy kezelhesse az Azure-beli csatlakoztatott gép ügynökének konfigurációját. A tartalmaz egy PowerShell-parancsfájlt is `AzureConnectedMachineAgent.ps1` , amely a `AzureConnectedMachineDsc\examples` mappában található. Közösségi erőforrásokat használ a letöltés és a telepítés automatizálásához, és kapcsolatot létesíteni az Azure arc használatával. Ez a szkript a [hibrid gépek az Azure-ba való összekapcsolásához a Azure Portal](onboard-portal.md) cikkben ismertetett hasonló lépéseket hajtja végre.

Ha a gépnek egy proxykiszolgálón keresztül kell kommunikálnia a szolgáltatással, az ügynök telepítése után futtatnia kell egy, az [itt](manage-agent.md#update-or-remove-proxy-settings)ismertetett parancsot. Ezzel beállítja a proxykiszolgáló rendszerkörnyezeti változóját `https_proxy` . A parancs manuális futtatása helyett a [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc) modul használatával elvégezheti ezt a lépést a DSC-vel.

>[!NOTE]
>A DSC futtatásának engedélyezéséhez a Windows rendszert úgy kell konfigurálni, hogy a localhost konfigurációjának futtatásakor is megkapja a PowerShell távoli parancsait. A környezet megfelelő konfigurálásához egyszerűen futtasson `Set-WsManQuickConfig -Force` egy emelt szintű PowerShell-terminált.
>

A konfigurációs dokumentumok (MOF-fájlok) a parancsmag használatával alkalmazhatók a gépre `Start-DscConfiguration` .

A következő paraméterek a használni kívánt PowerShell-szkripthez adhatók meg.

- `TenantId`: Az egyedi azonosító (GUID), amely az Azure AD dedikált példányát jelöli.

- `SubscriptionId`: Annak az Azure-előfizetésnek az előfizetés-azonosítója (GUID), amelyhez a gépeket szeretné használni.

- `ResourceGroup`: Az erőforráscsoport neve, ahová a csatlakoztatott gépek tartoznak.

- `Location`: Tekintse meg a [támogatott Azure-régiókat](overview.md#supported-regions). Ez a hely lehet azonos vagy eltérő, mint az erőforráscsoport helye.

- `Tags`: A csatlakoztatott gépi erőforrásra alkalmazni kívánt címkék karakterlánc-tömbje.

- `Credential`: A **ApplicationId** és- **jelszóval** rendelkező PowerShell hitelesítőadat-objektum, amellyel a gépeket egy [egyszerű szolgáltatásnév](onboard-service-principal.md)használatával regisztrálják a méretekben.

1. A PowerShell-konzolon Navigáljon arra a mappára, ahová a `.ps1` fájlt mentette.

2. Futtassa a következő PowerShell-parancsokat a MOF-dokumentum fordításához (a DSC-konfigurációk fordításával kapcsolatos információkért lásd: [DSC-konfigurációk](/powershell/scripting/dsc/configurations/configurations):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Ekkor létrejön egy `localhost.mof file` nevű új mappa `C:\dsc` .

Miután telepítette az ügynököt, és konfigurálta az Azure arc-kompatibilis kiszolgálókhoz való csatlakozáshoz, lépjen a Azure Portal a kiszolgáló sikeres csatlakoztatásának ellenőrzéséhez. A gépet megtekintheti az [Azure Portalon](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Hozzáadás meglévő konfigurációkhoz

Ezt az erőforrást hozzá lehet adni a meglévő DSC-konfigurációkhoz a gép végpontok közötti konfigurációjának ábrázolásához. Előfordulhat például, hogy hozzá szeretné adni ezt az erőforrást egy olyan konfigurációhoz, amely az operációs rendszer biztonságos beállításait állítja be.

A PowerShell-galéria [CompositeResource](https://www.powershellgallery.com/packages/compositeresource) modulja a konfiguráció [összetett erőforrásának](/powershell/scripting/dsc/resources/authoringResourceComposite) létrehozásához használható a konfigurációk összekapcsolásának további egyszerűsítése érdekében.

## <a name="next-steps"></a>Következő lépések

* A hibaelhárítási információ a [csatlakoztatott gép ügynökének hibaelhárítása című útmutatóban](troubleshoot-agent-onboard.md)található.

* Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/vm/vminsights-enable-policy.md)és sok más további műveletet.

* További információ a [log Analytics-ügynökről](../../azure-monitor/agents/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics-ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és munkaterheléseket, felügyelheti azt automatizálási runbookok vagy olyan megoldások használatával, mint például a Update Management, vagy más Azure-szolgáltatásokat is használhat, mint például a [Azure Security Center](../../security-center/security-center-introduction.md).