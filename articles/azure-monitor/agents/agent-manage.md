---
title: Az Azure Log Analytics-ügynök kezelése
description: Ez a cikk azokat a különböző felügyeleti feladatokat ismerteti, amelyeket általában a gépen üzembe helyezett Log Analytics Windows vagy Linux rendszerű ügynök életciklusa során fog elvégezni.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: ca36c35d859e651c0d949f4b7fbb28137d01af90
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734961"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Windows és Linux rendszerhez készült Log Analytics-ügynök kezelése és karbantartása

A Log Analytics Windows-vagy Linux-ügynöknek a Azure Monitor-ben történő kezdeti telepítése után előfordulhat, hogy újra kell konfigurálnia az ügynököt, frissítenie kell, vagy el kell távolítania a számítógépről, ha elérte a nyugdíjazási fázist életciklusa során. Ezeket a rutin karbantartási feladatokat manuálisan vagy Automation használatával egyszerűen kezelheti, ami csökkenti a működési hibákat és a költségeket is.

## <a name="upgrading-agent"></a>Ügynök frissítése

A Windows és a Linux rendszerhez készült Log Analytics ügynök manuálisan vagy automatikusan is frissíthető a legújabb kiadásra, attól függően, hogy milyen környezetben fut a virtuális gép. Az ügynök frissítésére a következő módszerek használhatók.

| Környezet | Telepítési módszer | Frissítési módszer |
|--------|----------|-------------|
| Azure VM | Log Analytics Agent VM-bővítmény Windows/Linux rendszerhez | A rendszer alapértelmezés szerint automatikusan frissíti az ügynököt, kivéve, ha úgy állította be a Azure Resource Manager sablont, hogy a *autoUpgradeMinorVersion* tulajdonság **hamis** értékre állításával letiltható. |
| Egyéni Azure VM-rendszerképek | Log Analytics-ügynök manuális telepítése Windows/Linux rendszerhez | A virtuális gépeknek az ügynök legújabb verziójára való frissítését a Windows Installer-csomagot vagy a Linux rendszerű önkicsomagoló és telepíthető rendszerhéj-parancsfájlt futtató parancssorból kell elvégezni.|
| Nem Azure-beli virtuális gépek | Log Analytics-ügynök manuális telepítése Windows/Linux rendszerhez | A virtuális gépeknek az ügynök legújabb verziójára való frissítését a Windows Installer-csomagot vagy a Linux rendszerű önkicsomagoló és telepíthető rendszerhéj-parancsfájlt futtató parancssorból kell elvégezni. |

### <a name="upgrade-windows-agent"></a>Windows-ügynök frissítése 

Ha egy Windows rendszerű virtuális gépen lévő ügynököt a Log Analytics virtuálisgép-bővítmény használatával nem telepített legújabb verzióra szeretné frissíteni, akkor a parancssorból, parancsfájlból vagy más automatizálási megoldásból vagy a MMASetup- \<platform\> . msi telepítővarázsló segítségével futtathatja.  

Az alábbi lépések végrehajtásával letöltheti a Windows-ügynök legújabb verzióját a Log Analytics munkaterületről.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek** lehetőséget.

3. Log Analytics munkaterületek listájában válassza ki a munkaterületet.

4. A Log Analytics munkaterületen válassza a **Speciális beállítások**, majd a **csatlakoztatott források**, végül pedig a **Windows-kiszolgálók** elemet.

5. A Windows- **kiszolgálók** lapon válassza ki a Windows- **ügynök** megfelelő verzióját a letöltéshez a Windows operációs rendszer processzor-architektúrája alapján.

>[!NOTE]
>A Windows Log Analytics ügynökének frissítése során nem támogatja a munkaterületek konfigurálását vagy újrakonfigurálását a jelentéshez. Az ügynök konfigurálásához a [munkaterület hozzáadása vagy eltávolítása](#adding-or-removing-a-workspace)részben felsorolt támogatott módszerek egyikét kell követnie.
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>A frissítés a telepítővarázsló használatával

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. A telepítővarázsló elindításához futtassa a **MMASetup- \<platform\> . exe fájlt** .

3. A telepítővarázsló első lapján kattintson a **tovább** gombra.

4. A **Microsoft monitoring Agent telepítése** **párbeszédpanelen kattintson az Elfogadom gombra a** licencszerződés elfogadásához.

5. A **Microsoft Monitoring Agent beállítása** párbeszédpanelen kattintson a **Frissítés** lehetőségre. Az állapotlapon megjelenik a frissítés állapota.

6. Ha a **Microsoft monitoring Agent konfigurálása sikeresen befejeződött.** lap jelenik meg, kattintson a **Befejezés** gombra.

#### <a name="to-upgrade-from-the-command-line"></a>Frissítés a parancssorból

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Az ügynök telepítési fájljainak kibontásához emelt szintű parancssorból futtassa a parancsot, `MMASetup-<platform>.exe /c` és kérni fogja a fájlok kinyerésének elérési útját. Azt is megteheti, hogy az argumentumok átadásával megadhatja az elérési utat `MMASetup-<platform>.exe /c /t:<Full Path>` .

3. Futtassa a következő parancsot, amelyben a D:\ a frissítési naplófájl helye.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Linux-ügynök frissítése 

A korábbi verziókról (>1.0.0-47) való frissítés támogatott. Ha a paranccsal végrehajtja a telepítést, az a `--upgrade` legújabb verzióra frissíti az ügynök összes összetevőjét.

Futtassa az alábbi parancsot az ügynök frissítéséhez.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Munkaterület hozzáadása vagy eltávolítása

### <a name="windows-agent"></a>Windows-ügynök
Az ebben a szakaszban ismertetett lépések akkor szükségesek, ha nem csak a Windows-ügynököt szeretné egy másik munkaterületre bejelenteni, vagy egy munkaterületet törölni a konfigurációból, de akkor is, ha úgy szeretné konfigurálni az ügynököt, hogy több munkaterületre is jelentsen (általában több-önkiszolgáló). Ha úgy konfigurálja a Windows-ügynököt, hogy több munkaterületnek jelentsen, csak az ügynök kezdeti beállítása után és az alább ismertetett módszerekkel hajtható végre.    

#### <a name="update-settings-from-control-panel"></a>Beállítások frissítése a Vezérlőpultról

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Nyissa meg a **Vezérlőpultot**.

3. Válassza a **Microsoft monitoring Agent** lehetőséget, majd kattintson az **Azure log Analytics** fülre.

4. Ha eltávolít egy munkaterületet, jelölje ki, majd kattintson az **Eltávolítás** gombra. Ismételje meg ezt a lépést minden olyan munkaterületen, amelyhez az ügynököt le szeretné állítani.

5. Ha hozzáad egy munkaterületet, kattintson a **Hozzáadás** gombra, és a **log Analytics munkaterület hozzáadása** párbeszédpanelen illessze be a munkaterület-azonosítót és a munkaterület kulcsát (elsődleges kulcs). Ha a gépnek egy Azure Government-felhőbeli Log Analytics-munkaterületnek kell jelentenie, válassza az Azure US Government lehetőséget az Azure Cloud legördülő listából.

6. Kattintson az **OK** gombra a módosítások mentéséhez.

#### <a name="remove-a-workspace-using-powershell"></a>Munkaterület eltávolítása a PowerShell használatával

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Munkaterület hozzáadása az Azure Commercial szolgáltatásban a PowerShell használatával

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Munkaterület hozzáadása az Azure-ban az USA kormánya számára a PowerShell használatával

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Ha korábban már használta a parancssort vagy a parancsfájlt az ügynök telepítésére vagy konfigurálására, a `EnableAzureOperationalInsights` és a helyére váltott `AddCloudWorkspace` `RemoveCloudWorkspace` .
>

### <a name="linux-agent"></a>Linux-ügynök
A következő lépések bemutatják, hogyan lehet újrakonfigurálni a Linux-ügynököt, ha úgy dönt, hogy egy másik munkaterülettel regisztrálja, vagy egy munkaterületet távolít el a konfigurációból.

1. A következő parancs futtatásával ellenőrizheti, hogy regisztrálva van-e egy munkaterületen:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    A következő példához hasonló állapotot kell visszaadnia:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Fontos, hogy az állapot azt is megjeleníti, hogy az ügynök fut, ellenkező esetben az ügynök újrakonfigurálásának következő lépései nem lesznek sikeresek.

2. Ha már regisztrálva van egy munkaterületen, távolítsa el a regisztrált munkaterületet a következő parancs futtatásával. Ellenkező esetben, ha nincs regisztrálva, folytassa a következő lépéssel.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Ha egy másik munkaterülethez szeretne regisztrálni, futtassa a következő parancsot:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. A módosítások érvénybe léptetéséhez futtassa a következő parancsot:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    A következő példához hasonló állapotot kell visszaadnia:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

A módosítások életbe léptetéséhez nem kell újraindítani az ügynök szolgáltatását.

## <a name="update-proxy-settings"></a>Proxybeállítások frissítése
Ha úgy szeretné konfigurálni az ügynököt, hogy az üzembe helyezés után proxykiszolgálón vagy [log Analytics átjárón](./gateway.md) keresztül kommunikáljon a szolgáltatással, a feladat elvégzéséhez használja az alábbi módszerek egyikét.

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="update-settings-using-control-panel"></a>Beállítások frissítése a Vezérlőpult használatával

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Nyissa meg a **Vezérlőpultot**.

3. Válassza a **Microsoft monitoring Agent** lehetőséget, majd kattintson a **Proxybeállítások** fülre.

4. Kattintson a **Proxykiszolgáló használata** elemre, és adja meg a proxykiszolgáló vagy átjáró URL-címét és portszámát. Ha a proxykiszolgáló vagy a Log Analytics-átjáró hitelesítést igényel, írja be a felhasználónevet és jelszót a hitelesítéshez, majd kattintson az **OK** gombra.

#### <a name="update-settings-using-powershell"></a>Beállítások frissítése a PowerShell használatával

Másolja az alábbi PowerShell-kódot, frissítse a környezetére vonatkozó információkkal, és mentse egy PS1 fájlnévkiterjesztéssel. Futtassa a szkriptet minden olyan számítógépen, amely közvetlenül csatlakozik a Log Analytics munkaterülethez Azure Monitorban.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Linux-ügynök
Hajtsa végre az alábbi lépéseket, ha a linuxos számítógépeknek proxykiszolgálón vagy Log Analytics átjárón keresztül kell kommunikálnia. A proxykonfiguráció értékének szintaxisa a következő: `[protocol://][user:password@]proxyhost[:port]`. A *proxyhost* tulajdonság a proxykiszolgáló teljes tartománynevét vagy IP-címét fogadja el.

1. Szerkessze az `/etc/opt/microsoft/omsagent/proxy.conf` fájlt a következő parancsok futtatásával, és módosítsa az értékeket a saját konkrét beállításaira.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Indítsa újra az ügynököt a következő parancs futtatásával:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Ügynök eltávolítása
A következő eljárások egyikével távolíthatja el a Windows-vagy Linux-ügynököt a parancssor vagy a telepítővarázsló használatával.

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="uninstall-from-control-panel"></a>Eltávolítás a Vezérlőpultról
1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. A **Vezérlőpulton** kattintson a **programok és szolgáltatások** elemre.

3. A **programok és szolgáltatások** területen kattintson a **Microsoft monitoring Agent** elemre, majd az **Eltávolítás** elemre, végül pedig az **Igen** gombra.

>[!NOTE]
>Az ügynök telepítővarázslója a **MMASetup- \<platform\> . exe fájlra** duplán kattintva is futtatható, amely a Azure Portal munkaterületéről tölthető le.

#### <a name="uninstall-from-the-command-line"></a>Eltávolítás a parancssorból
Az ügynök letöltött fájlja egy, a IExpress-mel létrehozott, önálló telepítési csomag. Az ügynök és a támogató fájlok telepítőprogramja a csomagban található, és a megfelelő eltávolításhoz a következő példában látható parancssor használatával kell kinyerni.

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Az ügynök telepítési fájljainak kibontásához emelt szintű parancssorból futtassa a parancsot, `extract MMASetup-<platform>.exe` és kérni fogja a fájlok kinyerésének elérési útját. Azt is megteheti, hogy az argumentumok átadásával megadhatja az elérési utat `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>` . A IExpress által támogatott parancssori kapcsolókról további információt a [IExpress parancssori kapcsolói](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/197147) című témakörben talál, majd az igényeinek megfelelően frissítheti a példát.

3. A parancssorba írja be a következőt: `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb` .

### <a name="linux-agent"></a>Linux-ügynök
Az ügynök eltávolításához futtassa az alábbi parancsot a Linux rendszerű számítógépen. A *--purge* argumentum teljesen eltávolítja az ügynököt és annak konfigurációját.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Az ügynök konfigurálása Operations Manager felügyeleti csoportnak való jelentésre

### <a name="windows-agent"></a>Windows-ügynök
A következő lépések végrehajtásával konfigurálhatja a Windows Log Analytics ügynökét, hogy jelentést készítsen egy System Center Operations Manager felügyeleti csoportnak.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Nyissa meg a **Vezérlőpultot**.

3. Kattintson a **Microsoft monitoring Agent** elemre, majd a **Operations Manager** fülre.

4. Ha a Operations Manager-kiszolgálók Active Directory-integrációval rendelkeznek, kattintson **a felügyeleti csoport hozzárendeléseinek automatikus frissítése a AD DS** lehetőségre.

5. Kattintson a **Hozzáadás** gombra a **felügyeleti csoport hozzáadása** párbeszédpanel megnyitásához.

6. A **felügyeleti csoport neve** mezőbe írja be a felügyeleti csoport nevét.

7. Az **elsődleges felügyeleti kiszolgáló** mezőbe írja be az elsődleges felügyeleti kiszolgáló számítógépnevét.

8. A **felügyeleti kiszolgáló portja** mezőbe írja be a TCP-port számát.

9. Az **ügynök műveleti fiókja** területen válassza a helyi rendszer fiókot vagy a helyi tartományi fiókot.

10. A **felügyeleti csoport hozzáadása** párbeszédpanel bezárásához kattintson az **OK** gombra, majd kattintson az **OK** gombra a **Microsoft monitoring Agent tulajdonságai** párbeszédpanel bezárásához.

### <a name="linux-agent"></a>Linux-ügynök
A következő lépések végrehajtásával konfigurálhatja a Linux rendszerhez készült Log Analytics-ügynököt, hogy az System Center Operations Manager felügyeleti csoportnak jelentsen.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. A fájl szerkesztése `/etc/opt/omi/conf/omiserver.conf`

2. Győződjön meg arról, hogy a kezdetű sor `httpsport=` a 1270-es portot határozza meg. Például: `httpsport=1270`

3. A (z) rendszer újraindítása: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Következő lépések

- Ha problémák merülnek fel a Linux-ügynök telepítésekor vagy felügyeletekor, tekintse át [a Linux-ügynök hibaelhárítását ismertető témakört](agent-linux-troubleshoot.md) .

- Ha problémák merülnek fel a Windows-ügynök telepítésekor vagy felügyeletekor, tekintse át [a Windows-ügynök hibaelhárítását ismertető témakört](agent-windows-troubleshoot.md) .