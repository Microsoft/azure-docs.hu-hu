---
title: Log Analytics-ügynök telepítése Linux rendszerű számítógépeken
description: Ez a cikk azt ismerteti, hogyan csatlakoztathatók a más felhőben vagy a helyszínen üzemeltetett Linux rendszerű számítógépek a Log Analytics Azure Monitor-ügynökkel a Linux rendszerhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 9e0d9162c497ff035438b5a65c6f4500ce834860
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613557"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Log Analytics-ügynök telepítése Linux rendszerű számítógépeken
Ez a cikk részletesen ismerteti a Log Analytics-ügynök Linux rendszerű számítógépekre történő telepítését a következő módszerekkel:

* [Telepítse a Linux-ügynököt](#install-the-agent-using-wrapper-script) a githubon üzemeltetett burkoló parancsfájl használatával. Ez az ajánlott módszer az ügynök telepítésére és frissítésére, ha a számítógép közvetlenül vagy egy proxykiszolgálón keresztül kapcsolódik az internethez.
* [Manuálisan töltse le és telepítse](#install-the-agent-manually) az ügynököt. Erre akkor van szükség, ha a Linux rendszerű számítógép nem fér hozzá az internethez, és az [log Analytics-átjárón](../platform/gateway.md)keresztül kommunikál Azure Monitor vagy Azure Automation. 

>[!IMPORTANT]
> A cikkben ismertetett telepítési módszerek általában a helyszíni vagy más felhőkben található virtuális gépekhez használatosak. Az Azure Virtual Machines szolgáltatáshoz használható hatékonyabb beállításokért lásd a [telepítési beállításokat](../platform/log-analytics-agent.md#installation-options) .



## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Log Analytics-ügynök által támogatott Linux-disztribúciók listáját [Azure monitor ügynökök áttekintése](agents-overview.md#supported-operating-systems) című témakörben tekintheti meg.

>[!NOTE]
>Az OpenSSL 1.1.0 csak x86_x64 platformokon (64-bites) és 1. x-nél korábbi OpenSSL-platformon támogatott.

>[!NOTE]
>A Log Analytics Linux-ügynök tárolókban való futtatása nem támogatott. Ha meg kell figyelnie a tárolókat, használja a [tároló-figyelési megoldást](../insights/containers.md) a Docker-gazdagépekhez vagy [Azure monitor a Kubernetes tárolók](../insights/container-insights-overview.md) számára.

A 2018 augusztusa után kiadott verzióktól kezdve a következő módosításokat végezjük a támogatási modellen:  

* Csak a kiszolgálói verziók támogatottak, nem az ügyfél.  
* Koncentráljon az [Azure Linux által támogatott disztribúciók](../../virtual-machines/linux/endorsed-distros.md)bármelyikére. Vegye figyelembe, hogy az Azure Linux új disztribúciója/verziója esetében némi késéssel rendelkezik, és a Log Analytics Linux-ügynök támogatja.
* Minden másodlagos kiadás a felsorolt főbb verziók esetében támogatott.
* Nem támogatottak azok a verziók, amelyek átadták a gyártó támogatásának befejezési dátumát.
* Csak a VM-lemezképek támogatása; a tárolók, még azok is, amelyek a hivatalos distro kiadói lemezképből származnak, nem támogatottak.
* Az AMI új verziói nem támogatottak.  
* Az alapértelmezés szerint csak az SSL 1. x verziót futtató verziók támogatottak.

>[!NOTE]
>Ha olyan disztribúciót vagy verziót használ, amely jelenleg nem támogatott, és nem a támogatási modellhez igazodik, javasoljuk, hogy a tárházat elágazással lássa el, és tudomásul veszi, hogy a Microsoft támogatási szolgálata nem nyújt segítséget a villás ügynök verzióihoz.

### <a name="python-requirement"></a>Python-követelmény

Az ügynök verziójának 1.13.27 kezdődően a Linux-ügynök támogatja a Python 2-t és a 3-at is. Mindig javasoljuk a legújabb ügynök használatát. 

Ha az ügynök egy régebbi verzióját használja, akkor a virtuális gép alapértelmezés szerint a Python 2 értéket kell használnia. Ha a virtuális gép olyan disztribúciót használ, amely alapértelmezés szerint nem tartalmazza a Python 2-et, akkor telepítenie kell azt. A következő minta parancsok a Python 2 különböző disztribúciókban való telepítését teszik ki.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE `zypper install -y python2`

A python2 végrehajtható fájljának aliasnak kell lennie a *Pythonhoz*. Az alábbi két módszer használható az alias beállításához:

1. Futtassa a következő parancsot a meglévő aliasok eltávolításához.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. A következő parancs futtatásával hozza létre az aliast.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Támogatott Linux-megerősítés
A OMS-ügynök korlátozott testreszabási támogatást biztosít a Linux rendszerhez. 

Jelenleg a következők támogatottak: 
- FIPs

A következő szempontokat kell figyelembe venni, de még nem támogatottak:
- VIR
- SELINUX

Más korlátozási és testreszabási módszerek nem támogatottak és nem tervezettek a OMS-ügynök számára.  

## <a name="agent-prerequisites"></a>Ügynök előfeltételei

A következő táblázat a [támogatott Linux-disztribúciók](#supported-operating-systems) számára szükséges csomagokat mutatja be, amelyekre az ügynököt telepíteni fogja.

|Szükséges csomag |Description |Minimális verzió |
|-----------------|------------|----------------|
|Glibc |    GNU C könyvtár | 2.5-12 
|Openssl    | OpenSSL-kódtárak | 1.0. x vagy 1.1. x |
|Curl | cURL webes ügyfél | 7.15.5 |
|Python | | 2.6 + vagy 3.3 +
|Python – ctypes | | 
|PAM | Cserélhető hitelesítési modulok | | 

>[!NOTE]
>A syslog-üzenetek összegyűjtéséhez a rsyslog vagy a syslog-ng szükséges. A syslog-események gyűjteménye nem támogatja az alapértelmezett syslog démont a Red Hat Enterprise Linux, a CentOS és a Oracle Linux verzió (sysklog) 5. verziójában. A rendszernapló-adatok ezen disztribúciók ezen verziójából való összegyűjtéséhez a rsyslog démont telepíteni és konfigurálni kell a sysklog lecserélése érdekében.

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények
Lásd: [log Analytics ügynök áttekintése](../platform/log-analytics-agent.md#network-requirements) a Linux-ügynök hálózati követelményeiről.

## <a name="agent-install-package"></a>Ügynök telepítési csomagja

A Linux Log Analytics-ügynöke több csomagból áll. A kiadási fájl a következő csomagokat tartalmazza, amelyek a rendszerhéj-csomagnak a paraméterrel való futtatásával érhetők el `--extract` :

**Csomag** | **Verzió** | **Leírás**
----------- | ----------- | --------------
omsagent | 1.13.9 | A Linux rendszerhez készült Log Analytics-ügynök
omsconfig | 1.1.1 | A Log Analytics ügynökhöz tartozó konfigurációs ügynök
OMI | 1.6.4 | Nyissa meg a felügyeleti infrastruktúrát (a-t) – egy könnyű CIM-kiszolgálót. *Vegye figyelembe, hogy a "a" szolgáltatás működéséhez szükséges cron-feladatok futtatásához rendszergazdai hozzáférésre van szükség*
SCX | 1.6.4 | Az operációs rendszer teljesítményére vonatkozó mérőszámok az operációsrendszer-teljesítménymutatók számára
Apache – cimprov | 1.0.1 | Apache HTTP-kiszolgáló Teljesítményfigyelő szolgáltatója a következőhöz:. Csak akkor van telepítve, ha az Apache HTTP-kiszolgálót észlelte.
MySQL – cimprov | 1.0.1 | MySQL-kiszolgáló teljesítmény-figyelési szolgáltatója a következőhöz:. Csak akkor van telepítve, ha a rendszer a MySQL/MariaDB-kiszolgálót észleli.
Docker – cimprov | 1.0.0 | A következőhöz tartozó Docker-szolgáltató:. Csak akkor van telepítve, ha a Docker észlelve van.

### <a name="agent-installation-details"></a>Ügynök telepítésének részletei

A Linux-csomagok Log Analytics ügynökének telepítése után a rendszer a következő további rendszerszintű konfigurációs módosításokat alkalmazza. Ezek az összetevők el lesznek távolítva a omsagent-csomag eltávolításakor.

* A (z) nevű nem Kiemelt felhasználó `omsagent` létrejött. A démon ezen a hitelesítő adatokon fut. 
* A rendszer létrehoz egy sudo *-fájlt a* alkalmazásban `/etc/sudoers.d/omsagent` . Ez engedélyezi `omsagent` a syslog és a omsagent démonok újraindítását. Ha a sudo *include* utasítások nem támogatottak a sudo telepített verziójában, a rendszer ezeket a bejegyzéseket fogja írni a következőre: `/etc/sudoers` .
* A syslog-konfiguráció úgy módosul, hogy az események egy részhalmazát továbbítsa az ügynöknek. További információ: a [syslog-adatok gyűjtésének konfigurálása](data-sources-syslog.md).

A figyelt Linux rendszerű számítógépeken az ügynök szerepel a következőben: `omsagent` . `omsconfig` a Linux-alapú konfigurációs ügynök Log Analytics ügynöke, amely 5 percenként keresi az új portál oldal konfigurációját. Az új és frissített konfiguráció a (z) helyen található ügynök konfigurációs fájljaira lesz alkalmazva `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>Az ügynök telepítése burkoló parancsfájl használatával

A következő lépésekkel konfigurálhatja az ügynököt az Azure-ban Log Analyticshoz, és Azure Government a felhőt a Linux rendszerű számítógépek burkoló parancsfájljának használatával, amelyek közvetlenül vagy egy proxykiszolgálón keresztül kommunikálhatnak a GitHubon üzemeltetett ügynök letöltésével és az ügynök telepítésével.  

Ha a Linux rendszerű számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics, akkor ez a konfiguráció a parancssorban is megadható `-p [protocol://][user:password@]proxyhost[:port]` . A *Protocol* tulajdonság fogadja `http` vagy a `https` , és a *ProxyHost* tulajdonság fogadja a proxykiszolgáló teljes tartománynevét vagy IP-címét. 

Például: `https://proxy01.contoso.com:30443`

Ha mindkét esetben hitelesítésre van szükség, meg kell adnia a felhasználónevet és a jelszót. Például: `https://user01:password@proxy01.contoso.com:30443`

1. A Linux rendszerű számítógép Log Analytics munkaterülethez való kapcsolódásának konfigurálásához futtassa a következő parancsot a munkaterület-azonosító és az elsődleges kulcs megadásával. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    A következő parancs tartalmazza a `-p` proxy paramétert és a példa szintaxisát, ha a proxykiszolgáló hitelesítést igényel:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Ha úgy szeretné konfigurálni a Linux rendszerű számítógépet, hogy Azure Government felhőben Log Analytics munkaterülethez kapcsolódjon, futtassa a következő parancsot, amely a munkaterület-azonosítót és az elsődleges kulcsot a korábban másolta. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    A következő parancs tartalmazza a `-p` proxy paramétert és a példa szintaxisát, ha a proxykiszolgáló hitelesítést igényel:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Indítsa újra az ügynököt a következő parancs futtatásával: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>Az ügynök manuális telepítése

A Linux rendszerhez készült Log Analytics-ügynök egy önkicsomagoló és telepíthető rendszerhéj-parancsfájl-csomagban van megadva. Ez a csomag minden ügynök-összetevőhöz tartalmaz Debian és RPM csomagokat, amelyeket közvetlenül vagy kinyerve telepíthet az egyes csomagok lekérésére. Az x64-es és az x86-os architektúrához egy csomag van megadva. 

> [!NOTE]
> Az Azure-beli virtuális gépek esetében javasoljuk, hogy telepítse az ügynököt a Linux rendszerhez készült [azure log Analytics](../../virtual-machines/extensions/oms-linux.md) virtuálisgép-bővítmény használatával. 

1. [Töltse le](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) és vigye át a megfelelő köteget (x64 vagy x86) a Linux rendszerű virtuális gépre vagy fizikai számítógépre, SCP/SFTP használatával.

2. Telepítse a csomagot az argumentum használatával `--install` . Ha a telepítés során Log Analytics munkaterületre szeretne bejelentkezni, adja meg a `-w <WorkspaceID>` `-s <workspaceKey>` korábban átmásolt és paramétereket.

    >[!NOTE]
    >Az argumentumot akkor kell használnia `--upgrade` , ha a függő csomagok, például a omsconfig, az SCX vagy a régebbi verziói telepítve vannak, például ha már telepítve van a System Center Operations Manager-ügynök a Linux rendszerhez. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Ha úgy szeretné konfigurálni a Linux-ügynököt, hogy Log Analytics átjárón keresztül telepítsen és kapcsolódjon egy Log Analytics-munkaterülethez, futtassa a következő parancsot a proxy, a munkaterület-azonosító és a munkaterület-kulcs paramétereinek megadásával. Ez a konfiguráció a parancssorban is megadható a következővel: `-p [protocol://][user:password@]proxyhost[:port]` . A *ProxyHost* tulajdonság a log Analytics átjárókiszolgáló teljes tartománynevét vagy IP-címét fogadja el.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Ha hitelesítésre van szükség, meg kell adnia a felhasználónevet és a jelszót. Például: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Ha úgy szeretné konfigurálni a Linux rendszerű számítógépet, hogy Azure Government felhőben Log Analytics munkaterülethez kapcsolódjon, futtassa a következő parancsot, amely a munkaterület-azonosítót és az elsődleges kulcsot a korábban másolta.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Ha az ügynök csomagjait szeretné telepíteni, és úgy konfigurálja, hogy egy adott Log Analytics munkaterületre egy későbbi időpontban jelentsen, futtassa a következő parancsot:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Ha az ügynök csomagjait az ügynök telepítése nélkül szeretné kibontani a kötegből, futtassa a következő parancsot:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Frissítés korábbi kiadásról

A korábbi verzióról a 1.0.0-47 verziótól kezdődően minden kiadásban támogatott a frissítés. Végezze el a telepítést a `--upgrade` paraméterrel az ügynök összes összetevőjének a legújabb verzióra való frissítéséhez.

## <a name="cache-information"></a>Gyorsítótár-információk
A Linux rendszerhez készült Log Analytics-ügynöktől származó adatok gyorsítótárazása a *(z)% STATE_DIR_WS%/out_oms_common*. Buffer * helyen található helyi gépen történik, mielőtt elküldi őket a Azure monitornak. Az egyéni naplózási adatértékek a *(z)% STATE_DIR_WS%/out_oms_blob*. Buffer * pufferben vannak. Bizonyos [megoldások és adattípusok](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)esetében az elérési út eltérő lehet.

Az ügynök 20 másodpercenként próbálkozik a feltöltéssel. Ha ez nem sikerül, a rendszer az idő múlásával exponenciálisan növekszik, amíg a művelet sikertelen lesz: 30 másodperc a második kísérlet előtt, 60 másodperc a harmadik, 120 másodperc előtt... az újrapróbálkozások között akár 16 percet is igénybe vehet, amíg újra nem sikerül a csatlakozás. Az ügynök legfeljebb 6 alkalommal próbálkozik egy adott adathalmazban az Elvetés előtt és a következőre való áttérés előtt. Ez addig folytatódik, amíg az ügynök sikeresen fel nem tölthető. Ez azt jelenti, hogy az adatvesztés előtt körülbelül 30 percen belül lehet az adatpufferelés.

Az alapértelmezett gyorsítótár mérete 10 MB, de a [omsagent. conf fájlban](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)is módosítható.


## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Windows és Linux rendszerhez készült log Analytics ügynök felügyeletét és karbantartását](agent-manage.md) , hogy megtudja, hogyan lehet újrakonfigurálni, frissíteni vagy eltávolítani az ügynököt a virtuális gépről.

- Ha problémába ütközik az ügynök telepítésekor vagy felügyeletekor, tekintse át [a Linux-ügynök hibaelhárítását ismertető témakört](agent-linux-troubleshoot.md) .
