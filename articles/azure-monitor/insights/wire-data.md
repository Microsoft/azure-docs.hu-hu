---
title: Adatátviteli megoldás a Azure Monitorban | Microsoft Docs
description: A vezetékes adatok összevont hálózati és teljesítményadatokat Log Analytics ügynökökkel rendelkező számítógépekről. A hálózati adatok és a naplóadatok összevonása segít az adatok összevetésében.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/29/2020
ms.openlocfilehash: 563104a82da3b6b2263fce46792cf4f627c8f6ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572343"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Wire Data 2.0 (előzetes verzió) megoldás a Azure Monitor

![Wire Data-szimbólum](media/wire-data/wire-data2-symbol.png)

A vezetékes adatok összevont hálózati és teljesítményadatokat gyűjtenek a Windowshoz csatlakoztatott és a Linux rendszerű számítógépekről a Log Analytics ügynökkel, beleértve azokat is, amelyeket a környezetében Operations Manager figyel. A hálózati adatok és az egyéb naplóadatok összevonása segít az adatok összevetésében.

A Log Analytics ügynökön kívül az adatátviteli megoldás a Microsoft függőségi ügynököket is használja, amelyeket az informatikai infrastruktúrában lévő számítógépekre telepít. A függőségi ügynökök monitorozzák a számítógépek által fogadott és küldött adatokat az [OSI-modell](https://en.wikipedia.org/wiki/OSI_model) szerinti 2. és 3. szintű hálózatokon, beleértve a különböző alkalmazott protokollokat és portokat. Ezután az ügynökök használatával küldi el az Azure Monitor.  

>[!NOTE]
>Az adatátviteli megoldást a [Service Map megoldás](../vm/service-map.md)váltotta fel.  Mindkettő a Log Analytics-ügynök és a függőségi ügynök használatával gyűjti össze a hálózati kapcsolatok adatait a Azure Monitorba. 
> 
>A vezetékes adatmegoldást használó meglévő ügyfelek továbbra is használhatják azt. Az áttelepítési ütemtervhez útmutatást teszünk közzé a Service Mapre való áttéréshez.
>
>Az új ügyfeleknek telepíteniük kell a [Service Map megoldást](../vm/service-map.md) vagy [Azure monitor for VMS](../vm/vminsights-overview.md).  Az adatService Map adatkészlete hasonló a vezetékes adatforgalomhoz.  Azure Monitor for VMs tartalmazza a Service Map adatkészletet, amely további teljesítményadatokat és szolgáltatásokkal rendelkezik az elemzéshez. 


Alapértelmezés szerint a Azure Monitor naplózza a CPU-, memória-, lemez-és hálózati teljesítményadatokat a Windows és a Linux rendszerbe épített számlálók, valamint a megadható egyéb teljesítményszámlálók adatait. A hálózati és egyéb adatok gyűjtése valós időben történik az egyes ügynökökre vonatkozóan, beleértve a számítógép által használt alhálózatokat és alkalmazásszintű protokollokat.  A Wire Data a hálózati adatokat az alkalmazások szintjén kezeli, nem a TCP átviteli réteg szintjén.  A megoldás nem veszi figyelembe az önálló ACK-kat és SYN-eket.  Ha a kézfogás befejeződött, onnantól a kapcsolat élőnek számít és Csatlakoztatva jelölést kap. A kapcsolat addig marad élő, amíg mindkét oldal egyetért a szoftvercsatorna nyitva tartásában, és az adatok átvitele oda-vissza lehetséges.  Ha az egyik oldalon lezárja a kapcsolatot, a rendszer leválasztott állapotként jelöli meg.  Ezért csak sikeresen elküldött csomagok által használt sávszélességet veszi számításba, az újraküldött vagy sikertelenül elküldött csomagok nem lesznek jelentve.

Ha már használta az [sFlow](http://www.sflow.org/)-t vagy valamilyen egyéb szoftvert a [Cisco NetFlow protokolljával](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), akkor a Wire Data statisztikái és adatai már ismerősek lesznek.

A beépített naplókeresési lekérdezések néhány típusa:

- Átviteli adatokat szolgáltató ügynökök
- Az átviteli adatokat szolgáltató ügynökök IP-címe
- Kimenő kommunikáció IP-címek szerint
- Az alkalmazásprotokollok által elküldött bájtok száma
- Egy alkalmazásszolgáltatás által elküldött bájtok száma
- A különböző protokollok által fogadott bájtok
- Az elküldött és fogadott bájtok teljes száma IP-verzió szerint
- A megbízhatóan mért kapcsolatok átlagos késése
- Számítógép-folyamatok, amelyek hálózati forgalmat kezdeményeztek vagy fogadtak
- Egy folyamat hálózati forgalmának mennyisége

Az átviteli adatok használatával történő kereséskor az adatokat szűrheti és csoportosíthatja, így megtekintheti a leggyakoribb ügynökök és a leggyakoribb protokollok információit. Emellett megtekintheti, hogy az egyes számítógépek (IP-címek vagy MAC-címek) mikor kommunikáltak egymással, mennyi ideig, és mennyi adatot küldtek. Tulajdonképpen metaadatokat tekinthet meg a hálózati forgalomról, a keresések alapján.

Mivel azonban metaadatokról van szó, ezek a részletes hibakereséshez nem feltétlenül lesznek hasznosak. A Azure Monitor adatátviteli szolgáltatás nem a hálózati adatmennyiség teljes rögzítése.  A szolgáltatást nem mélyre menő, csomagszintű hibakereséshez tervezték. Az ügynök használatának előnye más gyűjteményi módszerekhez képest, hogy nem kell telepítenie a berendezéseket, újra kell konfigurálnia a hálózati kapcsolókat, vagy bonyolult konfigurációkat kell végrehajtania. A Wire Data egy egyszerű ügynökalapú megoldás – Ön telepíti az ügynököt egy számítógépen, az ügynök pedig monitorozza a saját hálózati forgalmát. Egy másik előny, hogy a felhasználó monitorozhatja a felhőszolgáltatókon, tárhelyszolgáltatókon vagy a Microsoft Azure-ban futó számítási feladatokat is, vagyis olyan helyeken, ahol nem a hálóréteg tulajdonosa.

## <a name="connected-sources"></a>Összekapcsolt források

A Wire Data a Microsoft függőségi ügynöktől kapja az adatokat. A Dependency Agent az Log Analytics ügynöktől függ, hogy a kapcsolata Azure Monitor. Ez azt jelenti, hogy a kiszolgálónak telepítve kell lennie a Log Analytics ügynöknek, és konfigurálnia kell a függőségi ügynököt. A következő táblázat ismerteti a Wire Data megoldás által támogatott csatlakoztatott forrásokat:

| **Csatlakoztatott forrás** | **Támogatott** | **Leírás** |
| --- | --- | --- |
| Windows-ügynökök | Yes | A Wire Data adatok elemez és gyűjt a Windows rendszerű ügynökszámítógépekről. <br><br> A Windows rendszerhez készült [log Analytics ügynökön](../agents/agent-windows.md)kívül a Windows-ügynökök a Microsoft függőségi ügynököt is igénylik. A támogatott operációsrendszer-verziók teljes listáját megtekintheti a [támogatott operációs rendszerek](../vm/vminsights-enable-overview.md#supported-operating-systems) szakaszban. |
| Linux-ügynökök | Yes | A Wire Data adatokat elemez és gyűjt a Linux rendszerű ügynökszámítógépekről.<br><br> A [Linux rendszerhez készült log Analytics-ügynökön](../vm/quick-collect-linux-computer.md)kívül a Linux-ügynökök igénylik a Microsoft függőségi ügynököt. A támogatott operációsrendszer-verziók teljes listáját megtekintheti a [támogatott operációs rendszerek](../vm/vminsights-enable-overview.md#supported-operating-systems) szakaszban. |
| System Center Operations Manage felügyeleti csoport | Yes | A Wire Data adatokat elemez és gyűjt az olyan Windows- és Linux-ügynököktől, amelyek egy csatlakoztatott [System Center Operations Manager felügyeleti csoporthoz](../agents/om-agents.md) tartoznak. <br><br> Szükség van egy közvetlen kapcsolódásra a System Center Operations Manager ügynök számítógépéről Azure Monitorre. |
| Azure Storage-fiók | No | A Wire Data ügynökszámítógépekről gyűjt adatokat, így az Azure Storage-ből nem tud adatokat gyűjteni. |

Windows rendszeren a Microsoft monitoring Agent (MMA) szolgáltatást a System Center Operations Manager és a Azure Monitor egyaránt használja az adatok összegyűjtéséhez és elküldéséhez. A környezettől függően az ügynököt System Center Operations Manager ügynöknek, Log Analytics ügynöknek, az MMA-nak vagy a közvetlen ügynöknek nevezzük. System Center Operations Manager és Azure Monitor az MMA némileg eltérő verzióját nyújtják. Ezek a verziók az egyes jelentések System Center Operations Manager, Azure Monitor vagy mindkettőre használhatók.

Linux rendszeren a Linux rendszerhez készült Log Analytics-ügynök összegyűjti és elküldi az adatokat a Azure Monitornak. Az adatátviteli kiszolgálókon a Azure Monitorhoz közvetlenül csatlakoztatott, vagy System Center Operations Manager felügyeleti csoportokon keresztül Azure Monitorhoz csatlakozó kiszolgálókon is használhatók.

A függőségi ügynök nem küld magába adatokat, és nem igényel semmilyen módosítást a tűzfalakon vagy a portokon. A Log Analytics Azure Monitor ügynök mindig továbbítja az adatokat a vezetékes adatokban, közvetlenül vagy az Log Analytics-átjárón keresztül.

![ügynök diagram](./media/wire-data/agents.png)

Ha a Azure Monitorhoz csatlakoztatott felügyeleti csoporttal rendelkező System Center Operations Manager felhasználó:

- Nincs szükség további konfigurálásra, ha a System Center Operations Manager-ügynökök hozzáférhetnek az internethez a Azure Monitorhoz való csatlakozáshoz.
- A Log Analytics átjárót úgy kell konfigurálnia, hogy működjön a System Center Operations Manager, ha a System Center Operations Manager-ügynökök nem férnek hozzá a Azure Monitorhoz az interneten keresztül.

Ha a Windows vagy Linux rendszerű számítógépek nem tudnak közvetlenül csatlakozni a szolgáltatáshoz, úgy kell konfigurálnia a Log Analytics ügynököt, hogy az Log Analytics átjáró használatával kapcsolódjon a Azure Monitorhoz. A Log Analytics-átjárót a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=52666)töltheti le.

## <a name="prerequisites"></a>Előfeltételek

- Szükség van a [Betekintések és elemzés](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) megoldásra.
- Ha a Wire Data előző verzióját használja, először el kell távolítania azt. Az eredeti Wire Data megoldással rögzített adatok azonban továbbra is elérhetők lesznek a Wire Data 2.0-ban és a naplókeresésben.
- A függőségi ügynök telepítéséhez vagy eltávolításához rendszergazdai jogosultságok szükségesek.
- A függőségi ügynököt a 64 bites operációs rendszert futtató számítógépre kell telepíteni.

### <a name="operating-systems"></a>Operációs rendszerek

A következő részei a függőségi ügynök támogatott operációs rendszereit sorolja fel. A Wire Data semmilyen operációs rendszer esetében nem támogatja a 32 bites architektúrákat.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Asztali Windows

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Támogatott linuxos operációs rendszerek
A következő részben a Linux rendszeren futó függőségi ügynök támogatott operációs rendszereit sorolja fel.  

- Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
- A nem szabványos kernelű kiadások, például a PAE és a Xen nem támogatottak semmilyen Linux-disztribúció esetén. Például a "2.6.16.21-0,8-Xen" kiadási karakterlánccal rendelkező rendszer nem támogatott.
- Az egyéni kernelek, beleértve a standard kernelek újrafordításait, nem támogatottak.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 7,4 | 3.10.0 – 693 |
| 7,5 | 3.10.0 – 862 |
| 7.6 | 3.10.0 – 957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.9 | 2.6.32 – 696 |
| 6,10 | 2.6.32 – 754 |

##### <a name="centosplus"></a>CentOSPlus
| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6,10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| Ubuntu 18.04 | kernel 4,15.\*<br>4,18 * |
| Ubuntu-16.04.3 | kernel 4,15. * |
| 16,04 | 4,4.\*<br>4,8.\*<br>4,10.\*<br>4,11.\*<br>4,13.\* |
| 14,04 | 3,13.\*<br>4,4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
| 11 SP4 | 3,0. * |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
| 12 SP2 | 4,4. * |
| 12 SP3 | 4,4. * |

### <a name="dependency-agent-downloads"></a>Függőségi ügynök letöltése

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Konfiguráció

A Wire Data megoldásnak a munkaterületekhez való konfigurálásához végezze el az alábbi lépéseket:

1. Engedélyezze a Activity Log Analytics megoldást az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) -en, vagy használja a [figyelési megoldások hozzáadása a Solutions Galleryból](../insights/solutions.md)című témakörben ismertetett eljárást.
2. Telepítse a függőségi ügynököt minden olyan számítógépre, amelyen le szeretné kérni az adatgyűjtést. A függőségi ügynök nyomon követheti a kapcsolatokat az azonnali szomszédokkal, így előfordulhat, hogy nincs szüksége ügynökre minden számítógépen.

> [!NOTE]
> A Wire Data előző verzióját nem lehet hozzáadni új munkaterületekhez. Ha engedélyezve van az eredeti Wire Data megoldás, azt továbbra is használhatja. Azonban a Wire Data 2.0 használatához először el kell távolítani az eredeti verziót.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>A függőségi ügynök telepítése Windows rendszeren

Az ügynök telepítéséhez vagy eltávolításához rendszergazdai jogosultság szükséges.

A függőségi ügynök telepítve van a Windows rendszerű számítógépeken InstallDependencyAgent-Windows.exeon keresztül. Ha a végrehajtható fájlt bármilyen paraméter nélkül futtatja, akkor elindul egy varázsló, amelyet követve párbeszédes formában telepítheti az ügynököt.

A következő lépésekkel telepítheti a függőségi ügynököt a Windows rendszerű számítógépekre:

1. Telepítse a Log Analytics ügynököt az [adatok összegyűjtése a környezetben üzemeltetett Windows rendszerű számítógépekről](../agents/agent-windows.md)című témakör lépéseit követve.
2. Töltse le a Windows-függőségi ügynököt az előző szakaszban található hivatkozás használatával, majd futtassa a következő paranccsal: `InstallDependencyAgent-Windows.exe`
3. Az ügynök telepítéséhez kövesse a varázslót.
4. Ha a függőségi ügynök nem indul el, ellenőrizze a naplókat a hibák részletes ismertetéséhez. Windows-ügynökök esetén a naplózási könyvtár a következő: %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Windows parancssor

A parancssorból való telepítéshez a következő táblában leírt paraméterek használhatók. A telepítésjelzők listájának megtekintéséhez futtassa a telepítőt a /? jelzővel, a következő módon.

InstallDependencyAgent-Windows.exe /?

| **Zászló** | **Leírás** |
| --- | --- |
| <code>/?</code> | A parancssori kapcsolók listájának lekérése. |
| <code>/S</code> | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |

A Windows-függőségi ügynök fájljai alapértelmezés szerint a C:\Program Files\Microsoft függőségi ügynökbe kerülnek.

### <a name="install-the-dependency-agent-on-linux"></a>A függőségi ügynök telepítése Linux rendszeren

Az ügynök telepítéséhez vagy eltávolításához gyökérszintű hozzáférés szükséges.

A függőségi ügynök Linux rendszerű számítógépekre van telepítve a InstallDependencyAgent-Linux64. bin használatával, amely egy önkicsomagoló bináris fájlt tartalmazó rendszerhéj-parancsfájl. A fájlt futtathatja az _sh_ használatával, vagy hozzáadhat végrehajtási engedélyeket a fájlhoz.

Az alábbi lépésekkel telepítheti a függőségi ügynököt az egyes Linux rendszerű számítógépekre:

1. Telepítse a Log Analytics-ügynököt az [adatok gyűjtése a környezetben üzemeltetett Linux rendszerű számítógépekről](../vm/quick-collect-linux-computer.md#obtain-workspace-id-and-key)című témakör lépéseit követve.
2. Töltse le a Linux-függőségi ügynököt az előző szakaszban található hivatkozásra kattintva, majd a következő paranccsal telepítse root-ként: sh InstallDependencyAgent-Linux64. bin
3. Ha a függőségi ügynök nem indul el, ellenőrizze a naplókat a hibák részletes ismertetéséhez. A Linux-ügynökökön a naplókönyvtár a következő: /var/opt/microsoft/dependency-agent/log.

A telepítésjelzők listájának megtekintéséhez futtassa a `-help` jelzővel rendelkező telepítőprogramot az alábbiak szerint.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Zászló** | **Leírás** |
| --- | --- |
| <code>-help</code> | A parancssori kapcsolók listájának lekérése. |
| <code>-s</code> | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |
| <code>--check</code> | Az engedélyek és az operációs rendszer ellenőrzése, az ügynök telepítése nélkül. |

A függőségi ügynök fájljai a következő könyvtárakba kerülnek:

| **Fájlok** | **Hely** |
| --- | --- |
| Alapvető fájlok | /opt/microsoft/dependency-agent |
| Naplófájlok | /var/opt/microsoft/dependency-agent/log |
| Konfigurációs fájlok | /etc/opt/microsoft/dependency-agent/config |
| Szolgáltatás végrehajtható fájljai | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Bináris tárolófájlok | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Telepítési példaszkriptek

Ha a függőségi ügynököt egyszerre sok kiszolgálóra szeretné egyszerűen üzembe helyezni, akkor a parancsfájl használatát is lehetővé teszi. A következő parancsfájl-példákkal letöltheti és telepítheti a függőségi ügynököt Windows vagy Linux rendszeren.

#### <a name="powershell-script-for-windows"></a>PowerShell-szkript Windowshoz

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Héjszkript Linuxhoz

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Célállapot-konfiguráció

A függőségi ügynök a kívánt állapot-konfiguráción keresztüli üzembe helyezéséhez használhatja a xPSDesiredStateConfiguration modult és egy olyan kódot, amely a következőhöz hasonló:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>A függőségi ügynök eltávolítása

A következő fejezetei segítséget nyújtanak a függőségi ügynök eltávolításához.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>A függőségi ügynök eltávolítása Windows rendszeren

A rendszergazdák a Vezérlőpulton keresztül eltávolíthatják a Windows függőségi ügynököt.

A rendszergazdák az%Programfiles%\Microsoft függőségi Agent\Uninstall.exe is futtathatják a függőségi ügynök eltávolításához.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>A függőségi ügynök eltávolítása Linux rendszeren

A függőségi ügynök Linux rendszerből való teljes eltávolításához el kell távolítania magát az ügynököt és az összekötőt, amely automatikusan települ az ügynökkel. Az alábbi egyszerű parancs futtatásával mindkettőt eltávolíthatja:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Felügyeleti csomagok

Ha az átviteli adatok aktiválva vannak a Log Analytics-munkaterületen, a rendszer egy 300 KB méretű felügyeleti csomagot küld a munkaterület Windows-kiszolgálóinak. Ha System Center Operations Manager-ügynököt használ egy [csatlakoztatott felügyeleti csoportban](../agents/om-agents.md), a Függőségfigyelő felügyeleti csomag a System Center Operations Managerből lesz telepítve. Ha az ügynökök közvetlenül vannak csatlakoztatva, Azure Monitor biztosítja a felügyeleti csomagot.

A felügyeleti csomag neve Microsoft.IntelligencePacks.ApplicationDependencyMonitor. A következő helyre írja a rendszer: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. A felügyeleti csomag az alábbi adatforrást használja: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>A megoldás használata

A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

- A Wire Data megoldás a Windows Server 2012 R2, Windows 8.1 és újabb operációs rendszert futtató számítógépekről gyűjt adatokat.
- A Microsoft .NET-keretrendszer 4.0-s vagy újabb verziójával kell rendelkeznie azoknak a számítógépeknek, amelyekről átviteli adatokat szeretne gyűjteni.
- Adja hozzá az adatátviteli megoldást a Log Analytics munkaterülethez a [felügyeleti megoldások hozzáadása a Solutions Galleryból](../insights/solutions.md)című témakörben leírt eljárással. Nincs szükség további konfigurációra.
- Egy adott megoldás átviteli adatainak megtekintéséhez már rendelkeznie kell a megoldással a munkaterületen.

Miután telepítette az ügynököket és telepíti a megoldást, a munkaterületen megjelenik a Wire Data 2.0 csempéje.

![Wire Data csempe](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>A Wire Data 2.0 megoldás használata

Az Azure Portalon a Log Analytics-munkaterület **Áttekintés** területén kattintson a **Wire Data 2.0** csempére az Átviteli adatok irányítópult megnyitásához. Az irányítópulton az alábbi táblázatban felsorolt panelek találhatók. Minden panelen legfeljebb 10 olyan elem jelenik meg, amely megfelel a panel hatóköri és időtartományi kritériumainak. A panel alján található **Az összes megtekintése** elemre vagy a panel fejlécére kattintva az összes rekordot megjelenítő keresést végezhet a naplóban.

| **Panel** | **Leírás** |
| --- | --- |
| Hálózati forgalmat rögzítő ügynökök | A hálózati forgalmat rögzítő ügynökök számát és a forgalmat rögzítő első 10 számítógép listáját jeleníti meg. Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Kattintson a listában található egyik számítógépre egy olyan naplókeresés futtatásához, amely a rögzített bájtok számát adja vissza. |
| Helyi alhálózatok | Az ügynök által felderített helyi alhálózatok számát mutatja.  Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>. Ez az alhálózatokat és az egyes alhálózatokon átküldött bájtok számát jeleníti meg. Kattintson a listában található alhálózatra egy olyan naplókeresés futtatásához, amely az alhálózaton küldött bájtok teljes számát adja vissza. |
| Alkalmazásszintű protokollok | Az ügynökök által felderített, használatban lévő alkalmazásszintű protokollok számát jeleníti meg. Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Kattintson a protokollra egy olyan naplókeresés futtatásához, amely a protokoll használatával küldött bájtok számát adja vissza. |

![Átviteli adatok irányítópult](./media/wire-data/wire-data-dash.png)

A **Hálózati forgalmat rögzítő ügynökök** panellel megállapíthatja, mekkora hálózati sávszélességet használnak fel a számítógépek. A panel segítségével könnyen megtalálhatja a hálózat _legforgalmasabb_ számítógépét. Előfordulhat, hogy ezek a számítógépek túlterheltek, rendellenesen működnek vagy az átlagosnál több hálózati erőforrást használnak.

![Képernyőfelvétel: az ügynökök a hálózati forgalom panelt rögzítik a Wire Data 2.0 irányítópultján, amely az egyes számítógépek által felhasznált hálózati sávszélességet mutatja.](./media/wire-data/log-search-example01.png)

Ehhez hasonlóan a **Helyi alhálózatok** panel segítségével megállapíthatja, mekkora hálózati forgalom halad át az alhálózatokon. A felhasználók gyakran az alkalmazásaikhoz tartozó kritikus területek körül adnak meg alhálózatokat. Ez a panel ezekbe a területekbe kínál betekintést.

![Képernyőkép a Wire Data 2.0 irányítópult helyi alhálózatok paneljéről, amely az egyes LocalSubnet által felhasznált hálózati sávszélességet mutatja.](./media/wire-data/log-search-example02.png)

Az **Alkalmazásszintű protokollok** panel hasznos, mivel fontos lehet annak ismerete, hogy mely protokollok vannak használatban. Lehetséges például, hogy arra számít, hogy az SSH nincs használatban a hálózati környezetben. A panelen megjelenő információk ezt gyorsan megerősíthetik vagy megcáfolhatják.

![Képernyőkép a Wire Data 2.0-irányítópulton az alkalmazás szintű protokollok panelről, amely az egyes protokollok által felhasznált hálózati sávszélességet mutatja.](./media/wire-data/log-search-example03.png)

Ez akkor is hasznos, ha szeretné tudni, hogy a hálózati forgalom idővel növekszik vagy csökken. Például nem árt tudnia, vagy említésre méltónak találhatja, ha egy alkalmazás által továbbított adatok mennyisége növekszik.

## <a name="input-data"></a>Bemeneti adatok

A Wire Data az engedélyezett ügynökök használatával metaadatokat gyűjt a hálózati forgalomról. Minden ügynök körülbelül 15 másodpercenként küld adatokat.

## <a name="output-data"></a>Kimeneti adatok

A bemeneti adatok minden típusához létrejön egy _WireData_ típusú rekord. A WireData-rekordok tulajdonságai az alábbi táblázatban láthatók:

| Tulajdonság | Leírás |
|---|---|
| Computer | A számítógép neve, ahol az adatgyűjtés történt |
| TimeGenerated | A rekord létrehozásának időpontja |
| LocalIP | A helyi számítógép IP-címe |
| SessionState | Csatlakoztatva vagy leválasztva |
| ReceivedBytes | A fogadott bájtok mennyisége |
| ProtocolName | A használt hálózati protokoll neve |
| IPVersion | IP-cím verziója |
| Irány | Bemeneti vagy kimeneti |
| MaliciousIP | Ismert kártevő forrás IP-címe |
| Súlyosság | Gyanús kártevő súlyossága |
| RemoteIPCountry | A távoli IP-cím országa/régiója |
| ManagementGroupName | Az Operations Manager felügyeleti csoportjának neve |
| SourceSystem | A gyűjtött adatok forrása |
| SessionStartTime | A munkamenet kezdési időpontja |
| SessionEndTime | A munkamenet befejezési időpontja |
| LocalSubnet | Az alhálózat, ahol az adatgyűjtés történt |
| LocalPortNumber | Helyi portszám |
| RemoteIP | A távoli számítógép által használt távoli IP-cím |
| RemotePortNumber | A távoli IP-cím által használt portszám |
| SessionID | A két IP-cím közötti kommunikációs munkameneteket azonosító egyedi érték |
| SentBytes | Az elküldött bájtok száma |
| TotalBytes | A munkamenet során elküldött bájtok száma összesen |
| ApplicationProtocol | A használt hálózati protokoll típusa   |
| ProcessID | Windows-folyamat azonosítója |
| ProcessName | A folyamat elérési útja és neve |
| RemoteIPLongitude | IP-cím hosszúsági értéke |
| RemoteIPLatitude | IP-cím szélességi értéke |

## <a name="next-steps"></a>Következő lépések

- [Keresés a naplókban](../logs/log-query-overview.md) az átviteli adatokhoz kapcsolódó részletes keresési rekordok megtekintéséhez.

