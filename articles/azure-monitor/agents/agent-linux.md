---
title: Log Analytics-ügynök telepítése Linux rendszerű számítógépeken
description: Ez a cikk azt ismerteti, hogyan csatlakoztathat más felhőkben vagy helyszínen üzemeltetett Linux rendszerű számítógépeket Azure Monitor a Linuxhoz való Log Analytics-ügynökkel.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 23597804a34a9bc409db179010569024aa472016
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725925"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Log Analytics-ügynök telepítése Linux rendszerű számítógépeken
Ez a cikk részletesen beszerzi a Log Analytics-ügynök Linux rendszerű számítógépekre való telepítését a következő módszerekkel:

* [Telepítse a Linux-ügynököt a GitHubon](#install-the-agent-using-wrapper-script) üzemeltetett burkoló szkript használatával. Ez az ajánlott módszer az ügynök telepítésére és frissítésére, ha a számítógép közvetlenül vagy proxykiszolgálón keresztül kapcsolódik az internethez.
* [Manuálisan töltse le és telepítse az](#install-the-agent-manually) ügynököt. Erre akkor van szükség, ha a Linux rendszerű számítógép nem rendelkezik internetkapcsolattal, és a Log Analytics-átjárón keresztül Azure Monitor vagy Azure Automation kommunikál az [-nal.](./gateway.md) 

>[!IMPORTANT]
> A cikkben ismertetett telepítési módszereket általában helyszíni vagy más felhőbeli virtuális gépekhez használják. Az [Azure-beli](./log-analytics-agent.md#installation-options) virtuális gépekhez használható hatékonyabb lehetőségekért lásd: Telepítési lehetőségek.



## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A [Log Analytics Azure Monitor által](agents-overview.md#supported-operating-systems) támogatott Linux-disztribúciók listájáért lásd az Azure Monitor-ügynökök áttekintését.

>[!NOTE]
>Az OpenSSL 1.1.0 csak x86_x64 platformokon (64 bites) és az 1.x-esnél korábbi OpenSSL-ben nem támogatott egyetlen platformon sem.

>[!NOTE]
>A Log Analytics Linux-ügynök tárolókban való futtatása nem támogatott. Ha tárolókat kell figyelnie, [](../containers/containers.md) használja a Docker-gazdagépek tárolómonitorozási megoldását vagy a Kuberneteshez való [Container Insights](../containers/container-insights-overview.md) megoldást.

A 2018 augusztusa után kiadott verzióktól kezdve a következő módosításokat támogatjuk a támogatási modellben:  

* Csak a kiszolgálóverziók támogatottak, az ügyfél nem.  
* Összpontosítson az Azure Linux által támogatott [disztribúciókra.](../../virtual-machines/linux/endorsed-distros.md) Vegye figyelembe, hogy az új disztribúció/verzió Azure Linux által támogatottként való használata és a Log Analytics Linux-ügynök támogatása között némi késés lehet.
* A felsorolt főverziók minden kisebb kiadást támogatnak.
* Azok a verziók, amelyek a gyártó támogatási időszakának végének megfelelőek, nem támogatottak.
* Csak a virtuálisgép-rendszerképeket támogatja; A tárolók, még a hivatalos disztribúció-közzétevők rendszerképeiből származó tárolók sem támogatottak.
* Az AMI új verziói nem támogatottak.  
* Alapértelmezés szerint csak az OpenSSL 1.x verziót futtató verziók támogatottak.

>[!NOTE]
>Ha olyan disztribúciót vagy verziót használ, amely jelenleg nem támogatott, és nem igazodik a támogatási modellünkhöz, javasoljuk, hogy ágazásként használja ezt az adattárt, és annak tudatában, hogy a Microsoft ügyfélszolgálata nem nyújt segítséget az el hamisított ügynökverziókhoz.

### <a name="python-requirement"></a>Python-követelmény

Az ügynök 1.13.27-es verziójától kezdődően a Linux-ügynök a Python 2-es és 3-as verzióját is támogatja. Javasoljuk, hogy mindig a legújabb ügynököt használja. 

Ha az ügynök egy régebbi verzióját használja, a virtuális gépnek alapértelmezés szerint Python 2-t kell használnia. Ha a virtuális gép olyan disztribúciót használ, amely alapértelmezés szerint nem tartalmazza a Python 2-t, telepítenie kell. A következő mintaparancsok különböző disztribúciókra telepítik a Python 2-t.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - Suse: `zypper install -y python2`

A python2 végrehajtható fájlnak a python aliasa *kell, hogy legyen.* Az alias beállításának egyik módszere a következő:

1. A meglévő aliasok eltávolításához futtassa a következő parancsot.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Futtassa a következő parancsot az alias létrehozásához.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Támogatott Linux-alapú megsokkolás
Az OMS-ügynök korlátozott testreszabási és támogatási támogatást nyújt a Linuxhoz.

Jelenleg a következők támogatottak: 
- Fips

Nem támogatottak az alábbiak:
- Cis
- Selinux

Tervben van a CIS- és a SELINUX-alapú biztonsági támogatás az [Azure Monitoring Agenthez.](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) Az OMS-ügynök nem támogatja és nem tervezi a további megsokeredő és testre szabási módszereket.  

## <a name="agent-prerequisites"></a>Ügynök előfeltételei

Az alábbi táblázat azokat a támogatott [Linux-disztribúciókhoz szükséges](#supported-operating-systems) csomagokat emeli ki, amelyekre az ügynök telepítve lesz.

|Szükséges csomag |Description |Minimális verzió |
|-----------------|------------|----------------|
|Bc |    GNU C kódtár | 2.5-12 
|Openssl    | OpenSSL-kódtárak | 1.0.x vagy 1.1.x |
|Curl | cURL webes ügyfél | 7.15.5 |
|Python | | 2.7 vagy 3.6+
|Python-ctypes | | 
|PAM | Cserélhető hitelesítési modulok | | 

>[!NOTE]
>A rendszernapló-üzenetek gyűjtéséhez rsyslog vagy syslog-ng szükséges. Az alapértelmezett syslog démon a Red Hat Enterprise Linux, CentOS és Oracle Linux verzió (sysklog) 5-ös verzióján nem támogatott a syslog eseménygyűjtéshez. Az ilyen disztribúciók ezen verziójából származó rendszernapló-adatok gyűjtéséhez telepíteni és konfigurálni kell az rsyslog démont a sysklog lecserélésre.

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények
A Linux-ügynök hálózati követelményeiről a [Log Analytics-ügynök](./log-analytics-agent.md#network-requirements) áttekintésében olvashat.

## <a name="agent-install-package"></a>Ügynök telepítőcsomagja

A Linuxhoz használható Log Analytics-ügynök több csomagból áll. A kiadási fájl a következő csomagokat tartalmazza, amelyek a parancshéjcsomag paraméterrel való `--extract` futtatásával érhetők el:

**Csomag** | **Verzió** | **Leírás**
----------- | ----------- | --------------
omsagent | 1.13.9 | A Linuxhoz használható Log Analytics-ügynök
omsconfig | 1.1.1 | A Log Analytics-ügynök konfigurációs ügynöke
Omi | 1.6.4 | Open Management Infrastructure (OMI) – egy egyszerűsített CIM-kiszolgáló. *Vegye figyelembe, hogy az OMI gyökér szintű hozzáférést igényel a szolgáltatás működéséhez szükséges cron-feladat futtatásához*
Scx | 1.6.4 | OMI CIM-szolgáltatók az operációs rendszer teljesítménymetrikákhoz
apache-cimprov | 1.0.1 | Apache HTTP Server teljesítményfigyelési szolgáltató OMI-hez. Csak akkor van telepítve, ha a rendszer Apache HTTP Servert észlel.
mysql-cimprov | 1.0.1 | A MySQL Server teljesítményfigyelési szolgáltatója az OMI-hez. Csak akkor van telepítve, ha a rendszer MySQL-/MariaDB-kiszolgálót észlel.
docker-cimprov | 1.0.0 | OMI-hez való Docker-szolgáltató. Csak akkor van telepítve, ha a Docker észlelhető.

### <a name="agent-installation-details"></a>Az ügynök telepítésének részletei

A Linux-csomagokhoz használható Log Analytics-ügynök telepítése után a rendszer az alábbi, a teljes rendszerre kiterjedő konfigurációs módosításokat alkalmazza. Ezek az összetevők az omsagent csomag eltávolításakor törlődnek.

* A rendszer létrehoz egy nem kiemelt jogosultságú `omsagent` felhasználót: A démon ezen a hitelesítő adaton fut. 
* A sudoers *include fájl* a fájlban jön `/etc/sudoers.d/omsagent` létre. Ez engedélyezi a syslog és `omsagent` az omsagent démonok újraindítását. Ha a sudo *include* direktívák nem támogatottak a sudo telepített verziójában, a rendszer a következőbe írja a bejegyzéseket: `/etc/sudoers` .
* A rendszernapló konfigurációja úgy módosul, hogy az események egy részkészletét továbbítja az ügynöknek. További információ: [Syslog-adatgyűjtés konfigurálása.](data-sources-syslog.md)

A figyelt Linux-számítógépeken az ügynök a következőként szerepel: `omsagent` . `omsconfig` A a Linuxhoz használható Log Analytics-ügynök, amely 5 percenként új portáloldali konfigurációt keres. Az új és frissített konfiguráció a helyen található ügynökkonfigurációs fájlokra lesz `/etc/opt/microsoft/omsagent/conf/omsagent.conf` alkalmazva.

## <a name="install-the-agent-using-wrapper-script"></a>Az ügynök telepítése burkoló parancsfájl használatával

A következő lépésekkel konfigurálhatja az ügynök beállítását az Azure-beli Log Analyticshez és az Azure Government-felhőhöz a Linux rendszerű számítógépeken közvetlenül vagy proxykiszolgálón keresztül kommunikáló burkoló szkript használatával a GitHubon üzemeltetett ügynök letöltéséhez és az ügynök telepítéséhez.  

Ha a Linux rendszerű számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, ez a konfiguráció a parancssorban is meg lehet adni a `-p [protocol://][user:password@]proxyhost[:port]` használatával. A *protokolltulajdonság* elfogadja a vagy a protokollt, a proxyhost tulajdonság pedig a proxykiszolgáló teljes tartománynevét vagy `http` `https` IP-címét.  

Például: `https://proxy01.contoso.com:30443`

Ha a hitelesítés mindkét esetben szükséges, meg kell adnia a felhasználónevet és a jelszót. Például: `https://user01:password@proxy01.contoso.com:30443`

1. A Linux rendszerű számítógép Log Analytics-munkaterülethez való csatlakozásra való konfiguráláshoz futtassa a következő parancsot, amely tartalmazza a munkaterület azonosítóját és elsődleges kulcsát. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    A következő parancs tartalmazza a proxyparamétert és a példaszintaxist, ha a `-p` proxykiszolgáló hitelesítést igényel:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Ha a Linux rendszerű számítógépet a felhőben található Log Analytics-munkaterülethez való csatlakozásra Azure Government konfigurálni, futtassa a következő parancsot, és adja meg a korábban kimáselt munkaterület-azonosítót és elsődleges kulcsot. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    A következő parancs tartalmazza a proxyparamétert és a példaszintaxist, ha a `-p` proxykiszolgáló hitelesítést igényel:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Indítsa újra az ügynököt a következő parancs futtatásával: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>Az ügynök manuális telepítése

A Linuxhoz használható Log Analytics-ügynök egy önkicsomagolható és telepíthető héjszkprogramcsomagban található. Ez a csomag Debian- és RPM-csomagokat tartalmaz az egyes ügynökösszetevőkhöz, és közvetlenül telepíthető vagy kinyerhető az egyes csomagok lekérésekor. Az x64- és egy x86-architektúrákhoz egy csomag áll rendelkezésre. 

> [!NOTE]
> Azure-beli virtuális gépek esetén javasoljuk, hogy telepítse rájuk az ügynököt a [Linuxhoz](../../virtual-machines/extensions/oms-linux.md) használható Azure Log Analytics virtuálisgép-bővítmény használatával. 

1. [Töltse](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) le és töltse le a megfelelő csomagot (x64 vagy x86) linuxos virtuális gépére vagy fizikai számítógépére scp/sftp használatával.

2. Telepítse a csomagot a argumentum `--install` használatával. A Log Analytics-munkaterületre való bevezetéshez a telepítés során adja meg a korábban `-w <WorkspaceID>` másolt és `-s <workspaceKey>` paramétereket.

    >[!NOTE]
    >A argumentumot akkor kell használnia, ha bármely függő csomag, például az `--upgrade` omi, az scx, az omsconfig vagy azok régebbi verziói telepítve vannak, ahogyan az a System Center Operations Manager Agent for Linux esetében is történik. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. A Linux-ügynök Log Analytics-átjárón keresztüli log Analytics-munkaterületre való telepítéséhez és ahhoz való csatlakozáshoz futtassa a következő parancsot, amely tartalmazza a proxy, a munkaterület azonosítóját és a munkaterület kulcsparaméterét. Ez a konfiguráció a parancssorban a megadva van `-p [protocol://][user:password@]proxyhost[:port]` megadva. A *proxyhost* tulajdonság elfogadja a Log Analytics-átjárókiszolgáló teljes tartománynevét vagy IP-címét.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Ha hitelesítésre van szükség, meg kell adnia a felhasználónevet és a jelszót. Például: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Ha a Linux rendszerű számítógépet egy felhőbeli Log Analytics-munkaterülethez Azure Government konfigurálni, futtassa a következő parancsot, és adja meg a korábban kimáselt munkaterület-azonosítót és elsődleges kulcsot.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Ha telepíteni szeretné az ügynökcsomagokat, és úgy szeretné konfigurálni, hogy egy későbbi időpontban egy adott Log Analytics-munkaterületnek jelentsen, futtassa a következő parancsot:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Ha az ügynök telepítése nélkül szeretné kinyerni az ügynökcsomagokat a csomagból, futtassa a következő parancsot:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Frissítés egy korábbi kiadásról

Az 1.0.0-47-es verziótól kezdődően az előző verzióra való frissítés minden kiadásban támogatott. Hajtsa végre a telepítést a paraméterrel az ügynök összes összetevőnek a `--upgrade` legújabb verzióra való frissítéséhez.

## <a name="cache-information"></a>Gyorsítótár-információk
A Linuxhoz használt Log Analytics-ügynök adatait a rendszer a helyi gépen a *%STATE_DIR_WS%/out_oms_common*.buffer* szinten gyorsítótárazza, mielőtt a rendszer elküldi őket a Azure Monitor. Az egyéni naplóadatok pufferelése *%STATE_DIR_WS%/out_oms_blob*.buffer*. Egyes megoldások és adattípusok elérési útja [eltérő lehet.](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)

Az ügynök 20 másodpercenként megkísérli a feltöltést. Ha sikertelen, akkor exponenciálisan növekvő másodpercig vár a sikerességig: 30 másodperccel a második kísérlet előtt, 60 másodperccel a harmadik előtt, 120 másodperc... és így tovább, legfeljebb 16 percet az újraküldetek között, amíg sikeresen újra nem csatlakozik. Az ügynök egy adott adattömbben legfeljebb 6 alkalommal próbálkozik újra, mielőtt elvetné és továbblép a következőre. Ez addig folytatódik, amíg az ügynök újra fel nem töltődhet. Ez azt jelenti, hogy az adatok körülbelül 30 percig pufferelhetnek, mielőtt elvetik őket.

A gyorsítótár alapértelmezett mérete 10 MB, de az [omsagent.conf fájlban módosítható.](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)


## <a name="next-steps"></a>Következő lépések

- A [Windowshoz és Linuxhoz](agent-manage.md) használható Log Analytics-ügynök kezelése és karbantartása cikkből megtudhatja, hogyan konfigurálhatja újra, frissítheti vagy távolíthatja el az ügynököt a virtuális gépről.

- Ha [az ügynök telepítése vagy](agent-linux-troubleshoot.md) kezelése során problémákba ütközik, tekintse át a Linux-ügynök hibaelhárítását ismertető témakört.
