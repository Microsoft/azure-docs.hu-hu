---
title: Az Azure arc-kompatibilis kiszolgálók ügynökének kezelése
description: Ez a cikk azokat a különböző felügyeleti feladatokat ismerteti, amelyeket általában az Azure arc-kompatibilis kiszolgálók csatlakoztatott számítógép-ügynök életciklusa során fog elvégezni.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 36ae081f939cbf865db7755a2f766a7ccd87d619
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587621"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>A csatlakoztatott gép ügynökének kezelése és karbantartása

Előfordulhat, hogy az Azure arc-kompatibilis kiszolgálók Windows vagy Linux rendszerhez csatlakoztatott számítógép-ügynökének kezdeti telepítése után újra kell konfigurálnia az ügynököt, frissítenie kell, vagy el kell távolítania a számítógépről. Ezeket a rutin karbantartási feladatokat manuálisan vagy Automation használatával egyszerűen kezelheti, ami csökkenti a működési hibákat és a költségeket is.

## <a name="before-uninstalling-agent"></a>Az ügynök eltávolítása előtt

Mielőtt eltávolítja a csatlakoztatott számítógép ügynökét az ív-kompatibilis kiszolgálóról, vegye figyelembe a következőket az Azure-számlához hozzáadott váratlan problémák és költségek elkerüléséhez:

* Ha az Azure-beli virtuálisgép-bővítményeket egy engedélyezett kiszolgálóra telepítette, és eltávolítja a csatlakoztatott számítógép-ügynököt, vagy törli az adott erőforrás-csoportban lévő ív-kompatibilis kiszolgálót jelképező erőforrást, akkor ezek a bővítmények továbbra is futnak, és végrehajtják a normál működést.

* Ha törli az arc-kompatibilis kiszolgálót jelképező erőforrást az erőforráscsoporthoz, de nem távolítja el a virtuálisgép-bővítményeket, a gép újraregisztrálása után nem fogja tudni felügyelni a telepített virtuálisgép-bővítményeket.

Azon kiszolgálók vagy gépek esetében, amelyekhez már nem szeretne felügyelni az Azure arc-kompatibilis kiszolgálókat, az alábbi lépéseket kell követnie a kezelés sikeres leállítása érdekében:

1. Távolítsa el a virtuálisgép-bővítményeket a gépről vagy a kiszolgálóról. Az alábbi lépéseket kell megadnia.

2. A következő módszerek egyikével válassza le a gépet az Azure-ív használatával:

    * `azcmagent disconnect`Parancs futtatása a gépen vagy a kiszolgálón.

    * A Azure Portal kiválasztott regisztrált ív-kiszolgálóról a felső sávon található **Törlés** lehetőség kiválasztásával.

    * Az [Azure CLI](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) vagy a [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource)használatával. A `ResourceType` paraméter használata esetén `Microsoft.HybridCompute/machines` .

3. [Távolítsa el az ügynököt](#remove-the-agent) a gépről vagy a kiszolgálóról az alábbi lépéseket követve.

## <a name="renaming-a-machine"></a>Gép átnevezése

Ha megváltoztatja az Azure arc-kompatibilis kiszolgálókhoz csatlakozó Linux vagy Windows rendszerű gép nevét, az új név nem ismerhető fel automatikusan, mert az Azure-beli erőforrás neve nem változtatható meg. A többi Azure-erőforráshoz hasonlóan törölnie kell az erőforrást, majd újra létre kell hoznia az új név használatához.

Az ív használatára képes kiszolgálók esetében a gép átnevezése előtt el kell távolítania a virtuálisgép-bővítményeket a továbblépés előtt.

> [!NOTE]
> Amíg a telepített bővítmények továbbra is futnak, és az eljárás befejezése után normál műveletet hajtanak végre, nem fogja tudni kezelni őket. Ha megpróbálja újratelepíteni a bővítményeket a gépen, akkor kiszámíthatatlan működést tapasztalhat.

> [!WARNING]
> Javasoljuk, hogy ne nevezze át a gép számítógépnevét, és csak akkor hajtsa végre ezt az eljárást, ha feltétlenül szükséges.

1. Naplózza a gépen telepített virtuálisgép-bővítményeket, és jegyezze fel a konfigurációt az [Azure CLI](manage-vm-extensions-cli.md#list-extensions-installed) használatával vagy a [Azure PowerShell](manage-vm-extensions-powershell.md#list-extensions-installed)használatával.

2. Távolítsa el a [Azure Portalról](manage-vm-extensions-portal.md#uninstall-extension)telepített virtuálisgép-bővítményeket az [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension)használatával vagy a [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension)használatával.

3. A **azcmagent** eszköz és a [leválasztási](manage-agent.md#disconnect) paraméter használatával válassza le a gépet az Azure arc-ról, és törölje a gépi erőforrást az Azure-ból. A gép az arc-kompatibilis kiszolgálókról való leválasztása nem távolítja el a csatlakoztatott számítógép-ügynököt, és a folyamat részeként nem kell eltávolítania az ügynököt. Ezt manuálisan is futtathatja, ha interaktív módon jelentkezett be, vagy automatizálja ugyanazt a szolgáltatásnevet, amelyet több ügynök bevezetéséhez vagy egy Microsoft Identity platform [hozzáférési jogkivonatához](../../active-directory/develop/access-tokens.md)használ. Ha nem használ egyszerű szolgáltatásnevet a gép Azure arc-kompatibilis kiszolgálókhoz való regisztrálásához, tekintse meg az alábbi [cikket](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) egy egyszerű szolgáltatásnév létrehozásához.

4. Nevezze át a gépek számítógépének nevét.

5. Regisztrálja újra a csatlakoztatott gép ügynököt az ív használatára képes kiszolgálókon. Futtassa az `azcmagent` eszközt a [csatlakozási](manage-agent.md#connect) paraméterrel, és fejezze be ezt a lépést.

6. Telepítse újra azokat a virtuálisgép-bővítményeket, amelyeket eredetileg az arc-kompatibilis kiszolgálókról telepítettek a gépre. Ha az Azure Monitor for VMs (elemzés) ügynököt vagy az Log Analytics ügynököt egy Azure-szabályzattal telepítette, az ügynököket a következő [értékelési ciklust követően újra](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)üzembe helyezi a rendszer.

## <a name="upgrading-agent"></a>Ügynök frissítése

Az Azure-beli csatlakoztatott gépi ügynök rendszeresen frissül a hibajavítások, a stabilitási fejlesztések és az új funkciók kezeléséhez. [Azure Advisor](../../advisor/advisor-overview.md) azonosítja azokat az erőforrásokat, amelyek nem a Machine Agent legújabb verzióját használják, és azt javasolja, hogy frissítsen a legújabb verzióra. Akkor értesíti Önt, ha kijelöli az ív-kompatibilis kiszolgálót az **Áttekintés** oldalon egy szalagcím beírásával, vagy ha az Advisort a Azure Portal keresztül éri el.

A Windows és a Linux rendszerhez készült Azure-beli csatlakoztatott gépi ügynök a követelményektől függően manuálisan vagy automatikusan is frissíthető a legújabb kiadásra.

Az alábbi táblázat az ügynök frissítésének végrehajtásához támogatott metódusokat ismerteti.

| Operációs rendszer | Frissítési módszer |
|------------------|----------------|
| Windows | Manuálisan<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [Zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum használatával](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Windows-ügynök

A Windows rendszerhez készült csatlakoztatott számítógép-ügynök frissítési csomagja a következő helyről érhető el:

* Microsoft Update

* [Microsoft Update katalógus](https://www.catalog.update.microsoft.com/Home.aspx)

* [Windows agent Windows Installer csomag](https://aka.ms/AzureConnectedMachineAgent) a Microsoft letöltőközpontból.

Az ügynök a szoftverfrissítési felügyeleti folyamat támogatásának különböző módszerein keresztül frissíthető. A Microsoft Updatetól való beszerzésen kívül a parancssorból, egy parancsfájlból vagy más automatizálási megoldásból, illetve a végrehajtásával a felhasználói felület varázslóból manuálisan is letöltheti és futtathatja a parancsot `AzureConnectedMachine.msi` .

> [!NOTE]
> * Az ügynök frissítéséhez *rendszergazdai* engedélyekkel kell rendelkeznie.
> * A manuális frissítéshez először le kell töltenie és át kell másolnia a telepítőcsomagot egy mappába a célkiszolgálón vagy egy megosztott hálózati mappából. 

Ha nem ismeri a Windows Installer csomagok parancssori kapcsolóit, tekintse át az [msiexec standard parancssori kapcsolóit](/windows/win32/msi/standard-installer-command-line-options) és az [msiexec parancssori kapcsolókat](/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>A frissítés a telepítővarázsló használatával

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. **AzureConnectedMachineAgent.msi** végrehajtása a telepítővarázsló elindításához.

A telepítővarázsló felfedi, hogy létezik-e korábbi verzió, és hogy automatikusan végrehajtja-e az ügynök frissítését. Ha a frissítés befejeződött, a telepítővarázsló automatikusan bezáródik.

#### <a name="to-upgrade-from-the-command-line"></a>Frissítés a parancssorból

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Ha csendesen szeretné frissíteni az ügynököt, és létre kell hoznia egy telepítési naplófájlt a `C:\Support\Logs` mappában, futtassa a következő parancsot.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux-ügynök

Ha egy Linux rendszerű gépen szeretné frissíteni az ügynököt a legújabb verzióra, két parancsot kell magában foglalnia. Egy parancs a helyi csomag indexének frissítésére a Tárházak legújabb elérhető csomagjainak listájával, valamint egy parancs a helyi csomag frissítéséhez.

A legújabb ügynök-csomagot letöltheti a Microsoft [Package adattárból](https://packages.microsoft.com/).

> [!NOTE]
> Az ügynök frissítéséhez *rendszergazdai* jogosultságokkal vagy olyan fiókkal kell rendelkeznie, amely a sudo használatával emelt szintű jogokkal rendelkezik.

#### <a name="upgrade-ubuntu"></a>Ubuntu frissítése

1. Ha frissíteni szeretné a helyi csomag indexét a Tárházak legújabb módosításaival, futtassa a következő parancsot:

    ```bash
    apt update
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    apt upgrade
    ```

Az [apt](https://help.ubuntu.com/lts/serverguide/apt.html) parancs műveleteit, például a csomagok telepítését és eltávolítását a rendszer naplózza a `/var/log/dpkg.log` naplófájlba.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>A Red Hat/CentOS/Amazon Linux frissítése

1. Ha frissíteni szeretné a helyi csomag indexét a Tárházak legújabb módosításaival, futtassa a következő parancsot:

    ```bash
    yum check-update
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    yum update
    ```

A [yum](https://access.redhat.com/articles/yum-cheat-sheet) parancs műveleteit – például a csomagok telepítését és eltávolítását – a rendszer naplózza a `/var/log/yum.log` naplófájlba. 

#### <a name="upgrade-suse-linux-enterprise"></a>SUSE Linux Enterprise frissítése

1. Ha frissíteni szeretné a helyi csomag indexét a Tárházak legújabb módosításaival, futtassa a következő parancsot:

    ```bash
    zypper refresh
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    zypper update
    ```

A [Zypper](https://en.opensuse.org/Portal:Zypper) parancs műveleteit, például a csomagok telepítését és eltávolítását, a rendszer naplózza a `/var/log/zypper.log` naplófájlban.

## <a name="about-the-azcmagent-tool"></a>Tudnivalók a Azcmagent eszközről

A Azcmagent eszköz (Azcmagent.exe) használatával konfigurálható az Azure arc-kompatibilis kiszolgálók csatlakoztatott gépi ügynöke a telepítés során, vagy a telepítés után módosíthatja az ügynök kezdeti konfigurációját. A Azcmagent.exe parancssori paramétereket biztosít az ügynök testreszabásához és az állapotának megtekintéséhez:

* **Kapcsolódás** – a gép és az Azure-ív összekapcsolásához

* **Leválasztás** – a gép leválasztása az Azure-ív használatával

* **Megjelenítheti** az ügynök állapotát és a hozzá tartozó konfigurációs tulajdonságokat (erőforráscsoport-név, előfizetés-azonosító, verzió stb.), amely segíthet az ügynökkel kapcsolatos problémák elhárításában. Adja `-j` meg a paramétert az eredmények JSON formátumban való kimenetéhez.

* **Naplók** – létrehoz egy. zip fájlt a naplófájlokat tartalmazó aktuális könyvtárban, amely segítséget nyújt a hibaelhárításhoz.

* **Verzió** – megjeleníti a csatlakoztatott gép ügynökének verzióját.

* **-h vagy--help** – megjeleníti az elérhető parancssori paramétereket

    Ha például meg szeretné tekinteni a **kapcsolódási** paraméter részletes súgóját, írja be a következőt: `azcmagent connect -h` . 

* **-v vagy--verbose** – részletes naplózás engedélyezése

A **kapcsolódást** és a **leválasztást** manuálisan is elvégezheti, ha interaktív módon jelentkezett be, vagy automatizálja ugyanazt a szolgáltatásnevet, amelyet több ügynök bevezetéséhez vagy egy Microsoft Identity platform [hozzáférési jogkivonatához](../../active-directory/develop/access-tokens.md)használ. Ha nem használ egyszerű szolgáltatásnevet a gép Azure arc-kompatibilis kiszolgálókhoz való regisztrálásához, tekintse meg az alábbi [cikket](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) egy egyszerű szolgáltatásnév létrehozásához.

>[!NOTE]
>A **azcmagent** futtatásához *rendszergazdai* jogosultságokkal kell rendelkeznie a Linux rendszerű gépeken.

### <a name="connect"></a>Csatlakozás

Ez a paraméter egy olyan erőforrást határoz meg Azure Resource Manager, amely a gépet az Azure-ban hozza létre. Az erőforrás a megadott előfizetésben és erőforráscsoporthoz van megadva, és a gép adatait a beállítás által megadott Azure-régióban tárolja a rendszer `--location` . Ha nincs megadva, az alapértelmezett erőforrás neve a gép állomásneve.

A rendszer a gép rendszerszintű identitásához tartozó tanúsítványt letölti és helyileg tárolja. A lépés elvégzése után az Azure Connected Machine Metadata Service és a vendég konfigurációs ügynök megkezdi a szinkronizálást az Azure arc-kompatibilis kiszolgálókkal.

Egy egyszerű szolgáltatásnév használatával történő kapcsolódáshoz futtassa a következő parancsot:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Hozzáférési jogkivonat használatával történő kapcsolódáshoz futtassa a következő parancsot:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Ha a rendszergazda jogú bejelentkezett hitelesítő adataival szeretne csatlakozni (interaktív), futtassa a következő parancsot:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Leválasztás

Ez a paraméter egy olyan erőforrást határoz meg Azure Resource Manager, amely a gépet jelképezi az Azure-ban. Nem távolítja el az ügynököt a gépről, külön távolítsa el az ügynököt. Ha a gép le van választva, ha újra szeretné regisztrálni az Azure arc-kompatibilis kiszolgálókon, akkor az `azcmagent connect` Azure-ban létrehoz egy új erőforrást.

> [!NOTE]
> Ha telepített egy vagy több Azure-beli virtuálisgép-bővítményt az ív használatára képes kiszolgálóra, és törli a regisztrációját az Azure-ban, a bővítmények továbbra is települnek. Fontos megérteni, hogy a telepített bővítménytől függően aktívan végrehajtja a funkcióját. A kivonni kívánt vagy az arc-kompatibilis kiszolgálók által már nem felügyelt gépeknek először el kell távolítaniuk a bővítményeket az Azure-beli regisztráció eltávolítása előtt.

Az egyszerű szolgáltatásnév használatával történő leválasztáshoz futtassa a következő parancsot:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Hozzáférési jogkivonat használatával történő leválasztáshoz futtassa a következő parancsot:

`azcmagent disconnect --access-token <accessToken>`

Az emelt szintű bejelentkezett hitelesítő adatokkal (interaktív) való leválasztáshoz futtassa a következő parancsot:

`azcmagent disconnect`

## <a name="remove-the-agent"></a>Az ügynök eltávolítása

Az alábbi módszerek egyikével távolíthatja el a Windows vagy Linux rendszerű csatlakoztatott gépi ügynököt a gépről. Az ügynök eltávolítása nem törli az ív-kompatibilis kiszolgálókkal rendelkező gép regisztrációját, vagy eltávolíthatja a telepített Azure virtuálisgép-bővítményeket. Törölje a gép regisztrációját, és távolítsa el a telepített virtuálisgép-bővítményeket, ha már nincs szükség a gép Azure-ban való felügyeletére, és ezeket a lépéseket az ügynök eltávolítása előtt kell végrehajtani.

### <a name="windows-agent"></a>Windows-ügynök

A következő módszerek mindegyike eltávolítja az ügynököt, de nem távolítja el a *C:\Program Files\AzureConnectedMachineAgent* mappát a gépen.

#### <a name="uninstall-from-control-panel"></a>Eltávolítás a Vezérlőpultról

1. A Windows-ügynök számítógépről való eltávolításához tegye a következőket:

    a. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.  
    b. A **Vezérlőpulton** válassza a **programok és szolgáltatások** lehetőséget.  
    c. A **programok és szolgáltatások** területen válassza **Az Azure Connected Machine Agent ügynököt**, válassza az **Eltávolítás** lehetőséget, majd válassza az **Igen** lehetőséget.  

    >[!NOTE]
    > Az ügynök telepítővarázslója úgy is futtatható, ha duplán kattint a **AzureConnectedMachineAgent.msi** telepítőcsomag csomagra.

#### <a name="uninstall-from-the-command-line"></a>Eltávolítás a parancssorból

Ha manuálisan szeretné eltávolítani az ügynököt a parancssorból, vagy automatikus metódust (például egy parancsfájlt) szeretne használni, használja az alábbi példát. Először le kell kérnie a termékkód beolvasását, amely az alkalmazáscsomag elsődleges azonosítója, az operációs rendszerből. Az Eltávolítás a Msiexec.exe parancssor használatával történik `msiexec /x {Product Code}` .

1. Nyissa meg a Beállításszerkesztőt.

2. A beállításkulcs szakaszban `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` Keresse meg és másolja a termékkód GUID azonosítóját.

3. Ezután az alábbi példákkal távolíthatja el az ügynököt az msiexec használatával:

   * A parancssorból írja be a következőt:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Ugyanezeket a lépéseket a PowerShell használatával is végrehajthatja:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux-ügynök

> [!NOTE]
> Az ügynök eltávolításához *rendszergazdai* jogosultságokkal vagy olyan fiókkal kell rendelkeznie, amely a sudo használatával emelt szintű jogokkal rendelkezik.

A Linux-ügynök eltávolításához a használni kívánt parancs a Linux operációs rendszertől függ.

- Ubuntu esetében futtassa a következő parancsot:

    ```bash
    sudo apt purge azcmagent
    ```

- A RHEL, a CentOS és az Amazon Linux esetében futtassa a következő parancsot:

    ```bash
    sudo yum remove azcmagent
    ```

- A SLES futtassa a következő parancsot:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Számítógép regisztrációjának törlése

Ha azt tervezi, hogy leállítja a gép felügyeletét az Azure-ban támogató szolgáltatásokkal, hajtsa végre a következő lépéseket a gép az ív-kompatibilis kiszolgálókkal való regisztrációjának megszüntetéséhez. Ezeket a lépéseket megelőzően vagy azt követően is elvégezheti, hogy eltávolította a csatlakoztatott gépi ügynököt a gépről.

1. Nyissa meg az Azure arc-kompatibilis kiszolgálókat a [Azure Portal](https://aka.ms/hybridmachineportal).

2. Válassza ki a gépet a listából, válassza a három pontot (**..**.), majd válassza a **Törlés** lehetőséget.

## <a name="update-or-remove-proxy-settings"></a>Proxybeállítások frissítése vagy eltávolítása

Ha úgy szeretné konfigurálni az ügynököt, hogy proxykiszolgálón keresztül kommunikáljon a szolgáltatással, vagy távolítsa el ezt a konfigurációt az üzembe helyezés után, vagy használja az alábbi módszerek egyikét a feladat elvégzéséhez.

> [!NOTE]
> Az ív használatára képes kiszolgálók nem támogatják [log Analytics átjáró](../../azure-monitor/agents/gateway.md) proxyként való használatát a csatlakoztatott gépi ügynökhöz.
>

### <a name="windows"></a>Windows

A proxykiszolgáló környezeti változójának beállításához futtassa a következő parancsot:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Ha úgy szeretné konfigurálni az ügynököt, hogy a proxykiszolgálón keresztül kommunikáljon, futtassa a következő parancsot a proxykiszolgáló környezeti változó eltávolításához és az ügynök szolgáltatás újraindításához:

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

A proxykiszolgáló beállításához futtassa a következő parancsot abból a címtárból, amelyet az ügynök telepítési csomagja letöltött:

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Ha úgy szeretné konfigurálni az ügynököt, hogy a proxykiszolgáló használatával leállítsa a kommunikációt, futtassa a következő parancsot a proxy konfigurációjának eltávolításához:

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Következő lépések

* A hibaelhárítási információ a [csatlakoztatott gép ügynökének hibaelhárítása című útmutatóban](troubleshoot-agent-onboard.md)található.

* Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/vm/vminsights-enable-policy.md)és sok más további műveletet.

* További információ a [log Analytics-ügynökről](../../azure-monitor/agents/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics ügynökre akkor van szükség, ha az operációs rendszer és a számítási feladatok figyelésére szolgáló adatokat szeretné összegyűjteni, az Automation-runbookok vagy-szolgáltatásokkal, például a Update Managementekkel, vagy más Azure-szolgáltatások, például a [Azure Security Center](../../security-center/security-center-introduction.md)használatával