---
title: Hibrid környezetek Azure Monitorának engedélyezése
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Monitor for VMs egy vagy több virtuális gépet tartalmazó hibrid felhőalapú környezetben.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: d56b1ed7b4923b054ad6864b713fc2a26d95f7e2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619865"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-virtual-machine"></a>Hibrid virtuális gépek Azure Monitor for VMsának engedélyezése
Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Monitor for VMs az Azure-on kívüli virtuális gépekhez, beleértve a helyszíni és más felhőalapú környezeteket is.

> [!IMPORTANT]
> A hibrid virtuális gépek engedélyezésének ajánlott módja az [Azure-arc](../../azure-arc/servers/overview.md) engedélyezése a kiszolgálókhoz, hogy a virtuális gépek engedélyezve legyenek Azure monitor for VMS az Azure-beli virtuális gépekhez hasonló folyamatok használatával. Ez a cikk a hibrid virtuális gépek előkészítését ismerteti, ha úgy dönt, hogy nem használja az Azure-ívet.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre és konfiguráljon egy log Analytics munkaterületet](../insights/vminsights-configure-workspace.md).
- A [támogatott operációs rendszerekkel](../insights/vminsights-enable-overview.md#supported-operating-systems) biztosíthatja, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport operációs rendszere támogatott legyen. 


## <a name="overview"></a>Áttekintés
Az Azure-on kívüli virtuális gépekhez azonos Log Analytics ügynökre és függőségi ügynökre van szükség, amely az Azure-beli virtuális gépekhez használatos. Mivel a virtuálisgép-bővítmények nem használhatók az ügynökök telepítéséhez, manuálisan kell telepítenie azokat a vendég operációs rendszerbe, vagy más módszerrel kell őket telepíteni. 

A Log Analytics ügynök telepítésével kapcsolatos részletekért lásd: [Windows rendszerű számítógépek Összekapcsolása Azure monitor](../agents/agent-windows.md) vagy Linux rendszerű [számítógépek összekapcsolása Azure monitor](../agents/agent-linux.md) . A függőségi ügynök részletei ebben a cikkben találhatók. 

## <a name="firewall-requirements"></a>A tűzfalra vonatkozó követelmények
A Log Analytics ügynökre vonatkozó tűzfalszabályok a [log Analytics ügynök áttekintése című témakörben](../agents/log-analytics-agent.md#network-requirements)találhatók. A Azure Monitor for VMs Map-függőségi ügynök nem küld magába adatokat, és nem igényel semmilyen módosítást a tűzfalakon vagy a portokon. A leképezési adatokat mindig a Log Analytics ügynök továbbítja a Azure Monitor szolgáltatáshoz közvetlenül vagy az [Operations Management Suite-átjárón](../../azure-monitor/agents/gateway.md) keresztül, ha az informatikai biztonsági szabályzatok nem engedélyezik a hálózaton lévő számítógépek számára az internethez való kapcsolódást.


## <a name="dependency-agent"></a>Függőségi ügynök

>[!NOTE]
>Az ebben a szakaszban ismertetett információk a [Service Map megoldásra](../insights/service-map.md)is érvényesek.  

A függőségi ügynököt a következő helyekről töltheti le:

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.7.12710 | CA29CC328F991D7301FD0360F4F56DF78275545BB8CDA853679899CA885E96F0  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.7.12710 | 98380DBEB2E2A5848F2202BC22422C68B20B62090C1BFC1DECAB37ED5451ED8C |


## <a name="install-the-dependency-agent-on-windows"></a>A függőségi ügynök telepítése Windows rendszeren

A függőségi ügynököt manuálisan is telepítheti a Windows rendszerű számítógépeken `InstallDependencyAgent-Windows.exe` . Ha bármilyen lehetőség nélkül futtatja ezt a végrehajtható fájlt, elindul egy telepítővarázsló, amelyet követve interaktív módon telepítheti az ügynököt. Az ügynök telepítéséhez vagy eltávolításához *rendszergazdai* jogosultságok szükségesek a vendég operációs rendszeren.

A következő táblázat azokat a paramétereket mutatja be, amelyeket az ügynöknek a parancssorból való beállítása támogat.

| Paraméter | Leírás |
|:--|:--|
| /? | A parancssori kapcsolók listáját adja vissza. |
| /S | A csendes telepítést felhasználói beavatkozás nélkül hajtja végre. |

A telepítőprogramnak a paraméterrel való futtatásához például írja be a következőt: `/?` **InstallDependencyAgent-Windows.exe/?**.

A Windows-függőségi ügynök fájljai alapértelmezés szerint telepítve vannak a *C:\Program Files\Microsoft Dependency Agent* . Ha a telepítés befejezése után a függőségi ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. A naplózási könyvtár a *%ProgramFiles%\Microsoft függőségi Agent\logs*.

### <a name="powershell-script"></a>PowerShell-parancsprogram
Az ügynök letöltéséhez és telepítéséhez használja az alábbi PowerShell-parancsfájlt:

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```


## <a name="install-the-dependency-agent-on-linux"></a>A függőségi ügynök telepítése Linux rendszeren

A függőségi ügynök Linux-kiszolgálókra van telepítve a *InstallDependencyAgent-Linux64. bin* fájlból, amely egy önkicsomagoló bináris elemet tartalmazó rendszerhéj-parancsfájl. A fájlt futtathatja, `sh` vagy saját maga is végrehajthat végrehajtási engedélyeket.

>[!NOTE]
> Az ügynök telepítéséhez vagy eltávolításához gyökérszintű hozzáférés szükséges.
>

| Paraméter | Leírás |
|:--|:--|
| – Súgó | A parancssori kapcsolók listájának lekérése. |
| -s | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |
| – Keresés | Győződjön meg arról, hogy az engedélyek és az operációs rendszer nem telepíti az ügynököt. |

A telepítőprogramnak a paraméterrel való futtatásához például `-help` írja be a **InstallDependencyAgent-Linux64. bin-help** parancsot. Telepítse a Linux függőségi ügynököt root-ként a parancs futtatásával `sh InstallDependencyAgent-Linux64.bin` .

Ha a függőségi ügynök nem indul el, ellenőrizze a naplókat a hibák részletes ismertetéséhez. Linux-ügynökök esetén a */var/opt/Microsoft/Dependency-Agent/log* a naplózási könyvtár.

A függőségi ügynök fájljai a következő könyvtárakba kerülnek:

| Fájlok | Hely |
|:--|:--|
| Alapvető fájlok | /opt/microsoft/dependency-agent |
| Naplófájlok | /var/opt/microsoft/dependency-agent/log |
| Konfigurációs fájlok | /etc/opt/microsoft/dependency-agent/config |
| Szolgáltatás végrehajtható fájljai | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Bináris tárolófájlok | /var/opt/microsoft/dependency-agent/storage |

### <a name="shell-script"></a>Rendszerhéj-parancsfájl 
Az ügynök letöltéséhez és telepítéséhez használja a következő minta rendszerhéj-parancsfájlt:

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Célállapot-konfiguráció

Ha a függőségi ügynököt a kívánt állapot-konfigurációval (DSC) szeretné telepíteni, használja a xPSDesiredStateConfiguration modult a következő mintakód használatával:

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```



## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="vm-doesnt-appear-on-the-map"></a>A virtuális gép nem jelenik meg a térképen

Ha a függőségi ügynök telepítése sikeres volt, de a számítógép nem jelenik meg a térképen, a következő lépésekkel diagnosztizálhatja a problémát.

1. Sikeres volt a függőségi ügynök telepítése? Ennek ellenőrzéséhez tekintse meg, hogy a szolgáltatás telepítve van és fut-e.

    **Windows**: keresse meg a "Microsoft függőségi ügynök" nevű szolgáltatást.

    **Linux**: keresse meg a "Microsoft-függőség – ügynök" futó folyamatot.

2. Ön a [log Analytics ingyenes díjszabási szintjére](../insights/solutions.md)vonatkozik? Az ingyenes csomag legfeljebb öt egyedi számítógépet tesz lehetővé. A további számítógépek nem jelennek meg a térképen, még akkor is, ha az előző öt már nem küld adatokat.

3. A számítógép napló-és teljesítményszámláló-adatokat küld Azure Monitor naplókba? Hajtsa végre a következő lekérdezést a számítógépen:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Egy vagy több eredményt adott vissza? Frissek az adatok? Ha igen, a Log Analytics-ügynök megfelelően működik, és kommunikál a szolgáltatással. Ha nem, ellenőrizze az ügynököt a kiszolgálón: [log Analytics ügynök a Windows hibaelhárításához](../agents/agent-windows-troubleshoot.md) , vagy [log Analytics ügynök a Linux rendszerhez – hibaelhárítás](../agents/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>A számítógép megjelenik a térképen, de nem rendelkezik folyamatokkal

Ha látja a kiszolgálót a térképen, de nem rendelkezik folyamat-vagy adatszolgáltatással, akkor ez azt jelzi, hogy a függőségi ügynök telepítve van és fut, de a kernel-illesztőprogram nem töltődött be.

Ellenőrizze a C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log fájlt (Windows) vagy a /var/opt/microsoft/dependency-agent/log/service.log fájlt (Linux). A fájl utolsó sorai jelzik, hogy a kernel miért nem töltődött be. Előfordulhat például, hogy a Linux nem támogatja a kernelt, ha frissítette a kernelt.


## <a name="next-steps"></a>Következő lépések

Most, hogy a figyelés engedélyezve van a virtuális gépek számára, ezek az információk a Azure Monitor for VMssal való elemzéshez érhetők el.

- A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép megtekintése](vminsights-maps.md).

- Az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md)a szűk keresztmetszetek és a virtuális gépek teljesítményének teljes kihasználtsága alapján:.