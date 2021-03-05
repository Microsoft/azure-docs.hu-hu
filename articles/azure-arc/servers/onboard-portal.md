---
title: Hibrid gépek összekötése az Azure-ba a Azure Portal
description: Ebből a cikkből megtudhatja, hogyan telepítheti az ügynököt, és hogyan csatlakoztathatók a gépek az Azure-ba az Azure arc-kompatibilis kiszolgálók használatával a Azure Portal.
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: d7a89db7b8a42476a312a8f9a96c5ad230b140a2
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183148"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Hibrid gépek összekötése az Azure-ba a Azure Portal

Az Azure arc-kompatibilis kiszolgálókat egy vagy több Windows-vagy Linux-gépen is engedélyezheti a környezetben a lépések manuális elvégzésével. Vagy használhat automatizált metódust egy általunk biztosított sablon-parancsfájl futtatásával. Ez a szkript automatizálja mindkét ügynök letöltését és telepítését.

Ehhez a módszerhez rendszergazdai jogosultságokkal kell rendelkeznie a gépen az ügynök telepítéséhez és konfigurálásához. Linux rendszeren a legfelső szintű fiók, a Windows rendszeren pedig a helyi Rendszergazdák csoport tagja.

Mielőtt elkezdené, tekintse át az [előfeltételeket](agent-overview.md#prerequisites) , és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek. A támogatott régiókkal és az egyéb kapcsolódó megfontolásokkal kapcsolatos információkért lásd: [támogatott Azure-régiók](overview.md#supported-regions).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="generate-the-installation-script-from-the-azure-portal"></a>A telepítési parancsfájl létrehozása a Azure Portal

A letöltés és telepítés automatizálására szolgáló parancsfájl, valamint az Azure arc-kapcsolat létesítése a Azure Portal érhető el. A folyamat befejezéséhez hajtsa végre a következő lépéseket:

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com).

1. A **kiszolgálók – Azure arc** lapon válassza a **Hozzáadás** lehetőséget a bal felső sarokban.

1. A **módszer kiválasztása** lapon jelölje be a **kiszolgálók hozzáadása interaktív parancsfájl** csempével lehetőséget, majd válassza a **parancsfájl létrehozása** lehetőséget.

1. A **parancsfájl létrehozása** lapon válassza ki azt az előfizetést és erőforráscsoportot, ahol a gépet az Azure-ban szeretné felügyelni. Válasszon ki egy Azure-helyet, ahol a rendszer a számítógép metaadatait tárolja. Ez a hely lehet azonos vagy eltérő, mint az erőforráscsoport helye.

1. Az **Előfeltételek** lapon tekintse át az információkat, majd kattintson a **Tovább gombra: erőforrás részletei**.

1. Az **erőforrás részletei** lapon adja meg a következőket:

    1. Az **erőforráscsoport** legördülő listában válassza ki azt az erőforráscsoportot, amelyből a gépet felügyelni kívánja.
    1. A **régió** legördülő listában válassza ki az Azure-régiót a kiszolgálók metaadatainak tárolásához.
    1. Az **operációs rendszer** legördülő listában válassza ki azt az operációs rendszert, amelyen a parancsfájl konfigurálva van.
    1. Ha a gép egy proxykiszolgálón keresztül kommunikál az internethez, adja meg a proxykiszolgáló IP-címét, vagy azt a nevet és portszámot, amelyet a gép használni fog a proxykiszolgálóhoz való kommunikációhoz. Adja meg az értéket a formátumban `http://<proxyURL>:<proxyport>` .
    1. Kattintson a **Tovább gombra: címkék**.

1. A **címkék** lapon tekintse át a javasolt alapértelmezett **fizikai hely címkéit** , és adjon meg egy értéket, vagy adjon meg egy vagy több **Egyéni címkét** a szabványok támogatásához.

1. Válassza **a tovább lehetőséget: parancsfájl letöltése és futtatása**.

1. A **letöltési és futtatási parancsfájl** lapon tekintse át az összegzési információkat, majd kattintson a **Letöltés** gombra. Ha továbbra is módosításokat kell végeznie, válassza az **előző** lehetőséget.

## <a name="install-and-validate-the-agent-on-windows"></a>Az ügynök telepítése és ellenőrzése Windows rendszeren

### <a name="install-manually"></a>Manuális telepítés

A csatlakoztatott gépi ügynököt manuálisan is telepítheti a Windows Installer csomag *AzureConnectedMachineAgent.msi* futtatásával. A [Windows agent Windows Installer csomag](https://aka.ms/AzureConnectedMachineAgent) legújabb verzióját a Microsoft letöltőközpontból töltheti le.

>[!NOTE]
>* Az ügynök telepítéséhez vagy eltávolításához *rendszergazdai* jogosultságokkal kell rendelkeznie.
>* Először le kell töltenie és át kell másolnia a telepítőcsomagot egy mappába a célkiszolgálón vagy egy megosztott hálózati mappából. Ha bármilyen lehetőség nélkül futtatja a telepítőcsomagot, elindul egy telepítővarázsló, amelyet követve interaktív módon telepítheti az ügynököt.

Ha a gépnek egy proxykiszolgálón keresztül kell kommunikálnia a szolgáltatással, az ügynök telepítése után futtatnia kell egy, az alábbi lépésekben ismertetett parancsot. Ezzel a paranccsal állítható be a proxykiszolgáló rendszerkörnyezet-változója `https_proxy` .

Ha nem ismeri a Windows Installer csomagok parancssori kapcsolóit, tekintse át az [msiexec standard parancssori kapcsolóit](/windows/win32/msi/standard-installer-command-line-options) és az [msiexec parancssori kapcsolókat](/windows/win32/msi/command-line-options).

Például futtassa a telepítőprogramot a `/?` paraméterrel, és tekintse át a Súgó és a gyors hivatkozás lehetőséget.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. Ha csendes módban szeretné telepíteni az ügynököt, és létre kell hoznia egy telepítési naplófájlt a `C:\Support\Logs` létező mappában, futtassa a következő parancsot.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    Ha a telepítés befejezése után az ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. A *%ProgramData%\AzureConnectedMachineAgent\log* a naplózási könyvtár.

2. Ha a gépnek proxykiszolgálón keresztül kell kommunikálnia, a proxykiszolgáló környezeti változó beállításához futtassa a következő parancsot:

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >Az ügynök nem támogatja a proxy hitelesítés beállítását ebben az előzetes verzióban.
    >

3. Az ügynököt a telepítése után konfigurálnia kell az Azure Arc szolgáltatással való kommunikációhoz a következő parancs futtatásával:

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>Telepítés parancsfájl-metódussal

1. Jelentkezzen be a kiszolgálóra.

1. Nyisson meg egy emelt szintű PowerShell-parancssort.

    >[!NOTE]
    >A parancsfájl csak a Windows PowerShell 64 bites verziójáról támogatja a futtatást.
    >

1. Váltson arra a mappára vagy megosztásra, amelyre a parancsfájlt másolta, majd futtassa azt a kiszolgálón a parancsfájl futtatásával `./OnboardingScript.ps1` .

Ha a telepítés befejezése után az ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. A *%ProgramData%\AzureConnectedMachineAgent\log* a naplózási könyvtár.

## <a name="install-and-validate-the-agent-on-linux"></a>Az ügynök telepítése és ellenőrzése Linuxon

A Linux rendszerhez csatlakoztatott számítógép-ügynök a terjesztés előnyben részesített csomag formátumban van megadva (. RPM vagy. DEB), amely a Microsoft [Package adattárában](https://packages.microsoft.com/)található. A [rendszerhéj-parancsfájl `Install_linux_azcmagent.sh` köteg](https://aka.ms/azcmagent) a következő műveleteket hajtja végre:

* Úgy konfigurálja a gazdagépet, hogy letöltse az ügynök csomagját a packages.microsoft.com.

* Telepíti a hibrid erőforrás-szolgáltatói csomagot.

Igény szerint az ügynököt a (z) paraméterrel is konfigurálhatja a proxy adataival `--proxy "{proxy-url}:{proxy-port}"` .

A parancsfájl a támogatott és nem támogatott disztribúciók azonosítására szolgáló logikát is tartalmaz, és ellenőrzi a telepítés végrehajtásához szükséges engedélyeket.

A következő példa letölti az ügynököt, és telepíti azt:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

1. A következő parancsok futtatásával töltheti le és telepítheti az ügynököt, beleértve azt a paramétert is, hogy `--proxy` az ügynököt a proxykiszolgálón keresztüli kommunikációra konfigurálja.

    ```bash
    # Download the installation package.
    wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

    # Install the connected machine agent.
    bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
    ```

2. Az ügynököt a telepítése után konfigurálnia kell az Azure Arc szolgáltatással való kommunikációhoz a következő parancs futtatásával:

    ```bash
    azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --cloud "cloudName"
    if [ $? = 0 ]; then echo "\033[33mTo view your onboarded server(s), navigate to https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2Fmachines\033[m"; fi
    ```

### <a name="install-with-the-scripted-method"></a>Telepítés parancsfájl-metódussal

1. Jelentkezzen be a kiszolgálóra egy rendszergazdai jogosultságokkal rendelkező fiókkal.

1. Váltson arra a mappára vagy megosztásra, amelyre a parancsfájlt másolta, majd futtassa azt a kiszolgálón a parancsfájl futtatásával `./OnboardingScript.sh` .

Ha a telepítés befejezése után az ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. A naplózási könyvtár a *var/opt/azcmagent/log*.

## <a name="verify-the-connection-with-azure-arc"></a>Az Azure Arc csatlakozásának ellenőrzése

Miután telepítette az ügynököt, és konfigurálta az Azure arc-kompatibilis kiszolgálókhoz való csatlakozáshoz, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakozott-e. A gépet megtekintheti az [Azure Portalon](https://aka.ms/hybridmachineportal).

![Sikeres kiszolgálói kapcsolatok](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Következő lépések

- A hibaelhárítási információ a [csatlakoztatott gép ügynökének hibaelhárítása című útmutatóban](troubleshoot-agent-onboard.md)található.

- Megtudhatja, hogyan kezelheti a gépet a [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a [virtuális gépekkel Azure monitor](../../azure-monitor/vm/vminsights-enable-policy.md)és még sok minden más.

- További információ a [log Analytics-ügynökről](../../azure-monitor/agents/log-analytics-agent.md). A Windows és a Linux rendszerhez készült Log Analytics ügynökre akkor van szükség, ha az operációs rendszer és a számítási feladatok figyelésére szolgáló adatokat Azure Monitor for VMs használatával szeretné összegyűjteni, az Automation-runbookok vagy-szolgáltatásokkal, például a Update Managementekkel, vagy más Azure-szolgáltatásokkal (például [Azure Security Center](../../security-center/security-center-introduction.md)