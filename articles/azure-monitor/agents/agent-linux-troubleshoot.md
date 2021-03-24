---
title: Az Azure Log Analytics Linux-ügynök hibáinak megoldása | Microsoft Docs
description: A Azure Monitor Log Analytics-ügynökével kapcsolatos leggyakoribb problémák tüneteinek, okainak és megoldásának leírása.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 6c262e75bfc3fe9c7fb5ce3e00b42b0ff6cbe3bb
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952949"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>A Linuxhoz készült Log Analytics-ügynökkel kapcsolatos hibák elhárítása 

Ez a cikk segítséget nyújt a Linux rendszerhez készült Log Analytics-ügynökkel kapcsolatos hibák elhárításához Azure Monitorban, és javaslatot tesz a lehetséges megoldásokra.

Ha a fenti lépések egyike sem működik, a következő támogatási csatornák is elérhetők:

* A Premier szintű támogatási csomaggal rendelkező ügyfelek a [Premier](https://premier.microsoft.com/)szintű támogatási kérést is megnyithatják.
* Az Azure-támogatási szerződéssel rendelkező ügyfelek támogatási kérelmet is megnyithatnak [a Azure Portal](https://azure.microsoft.com/support/options/).
* A kisegítő problémák diagnosztizálásával kapcsolatos [hibaelhárítási útmutatót](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Fájl [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Látogasson el a Log Analytics visszajelzési oldalra, és tekintse át az elküldött ötleteket és hibákat, [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) vagy egy újat. 

## <a name="log-analytics-troubleshooting-tool"></a>Log Analytics hibaelhárítási eszköz

A Log Analytics Agent Linux hibaelhárítási eszköze egy olyan parancsfájl, amely a Log Analytics-ügynökkel kapcsolatos problémák keresésében és diagnosztizálásában segít. A telepítéskor a rendszer automatikusan tartalmazza az ügynököt. Az eszköz futtatása a probléma diagnosztizálásának első lépése.

### <a name="how-to-use"></a>Használat
A hibaelhárítási eszköz futtatásához illessze be a következő parancsot egy terminál ablakába egy olyan gépen, amelyen a Log Analytics ügynök található: `sudo /opt/microsoft/omsagent/bin/troubleshooter`

### <a name="manual-installation"></a>Manuális telepítés
A hibaelhárítási eszköz automatikusan belekerül a Log Analytics ügynök telepítésére. Ha azonban a telepítés bármilyen módon meghiúsul, az alábbi lépéseket követve manuálisan is telepítheti azt.

1. Másolja a hibakereső csomagot a gépre: `wget https://raw.github.com/microsoft/OMS-Agent-for-Linux/master/source/code/troubleshooter/omsagent_tst.tar.gz`
2. Csomagolja ki a csomagot: `tar -xzvf omsagent_tst.tar.gz`
3. A manuális telepítés futtatása: `sudo ./install_tst`

### <a name="scenarios-covered"></a>Érintett forgatókönyvek
Alább látható a hibaelhárítási eszköz által ellenőrzött forgatókönyvek listája:

1. Az ügynök állapota nem kifogástalan, a szívverés nem működik megfelelően
2. Az ügynök nem indul el, nem tud kapcsolódni a log analitikus szolgáltatásokhoz
3. Az ügynök syslog nem működik
4. Az ügynök magas CPU-/memóriahasználat
5. Telepítési problémákkal rendelkező ügynök
6. Az ügynök egyéni naplói nem működnek
7. Ügynökök naplóinak gyűjtése

További részletekért tekintse meg a [GitHub dokumentációját](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting-Tool.md).

 >[!NOTE]
 >Ha probléma merül fel, futtassa a log Collector eszközt. A naplók kezdetben nagy mértékben segítik a támogatási csapatot a probléma gyorsabb megoldásában.

## <a name="purge-and-re-install-the-linux-agent"></a>A Linux-ügynök kiürítése és Re-Install

Láttuk, hogy az ügynök tiszta újratelepítése megoldja a legtöbb problémát. Valójában ez a támogatás első olyan javaslata, amellyel az ügynököt uncurropted-állapotba veheti a támogatási csapattól. A hibakereső futtatása, a napló gyűjtése és a tiszta újratelepítési kísérlet megkönnyíti a problémák megoldását.

1. Töltse le a kiürítési parancsfájlt:
- `$ wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/purge_omsagent.sh`
2. Futtassa a kiürítési parancsfájlt (sudo engedélyekkel):
- `$ sudo sh purge_omsagent.sh`

## <a name="important-log-locations-and-log-collector-tool"></a>Fontos naplózási helyszínek és a log Collector eszköz

 Fájl | Elérési út
 ---- | -----
 A Linux-naplófájl Log Analytics ügynöke | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Log Analytics ügynök konfigurációs naplófájlja | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Javasoljuk, hogy a log Collector eszköz használatával szerezze be a hibaelhárításhoz vagy a GitHub-probléma elküldése előtt a fontos naplókat. Az eszközről és annak futtatásáról [itt](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)olvashat bővebben.

## <a name="important-configuration-files"></a>Fontos konfigurációs fájlok

 Kategória | Fájl helye
 ----- | -----
 Rendszernapló | `/etc/syslog-ng/syslog-ng.conf`vagy `/etc/rsyslog.conf``/etc/rsyslog.d/95-omsagent.conf`
 Teljesítmény, Nagios, Zabbix, Log Analytics kimenet és általános ügynök | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 További konfigurációk | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >A teljesítményszámlálók és a syslog konfigurációs fájljainak szerkesztése felül van írva, ha a gyűjtemény az [adatok menüben log Analytics speciális beállítások](../agents/agent-data-sources.md#configuring-data-sources) elemre van állítva a munkaterület Azure Portal. Az összes ügynök konfigurációjának letiltásához tiltsa le a gyűjteményt Log Analytics **Speciális beállítások** közül, vagy egyetlen ügynöknél futtassa a következőt:  
> `sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable && sudo rm /etc/opt/omi/conf/omsconfig/configuration/Current.mof* /etc/opt/omi/conf/omsconfig/configuration/Pending.mof*`

## <a name="installation-error-codes"></a>Telepítési hibakódok

| Hibakód | Értelmezés |
| --- | --- |
| NOT_DEFINED | Mivel a szükséges függőségek nincsenek telepítve, a auoms naplózott beépülő modul nem lesz telepítve. | Sikertelen volt a auoms telepítése, a csomag naplózása megtörtént. |
| 2 | Érvénytelen beállítás van megadva a rendszerhéj-csomagban. Futtatás `sudo sh ./omsagent-*.universal*.sh --help` használatra |
| 3 | A rendszerhéj-csomagban nincs megadva lehetőség. Futtatás `sudo sh ./omsagent-*.universal*.sh --help` a használathoz. |
| 4 | Érvénytelen a csomag típusa vagy a proxybeállítások érvénytelenek; omsagent –*rpm*. sh csomagok csak rpm-alapú rendszereken telepíthetők, és a omsagent-*deb*. sh csomagok csak a Debian-alapú rendszereken telepíthetők. Javasoljuk, hogy a [legújabb kiadásban](../vm/quick-collect-linux-computer.md#install-the-agent-for-linux)használja az univerzális telepítőt. Tekintse át a proxybeállítások ellenőrzését is. |
| 5 | A rendszerhéj-csomagot root-ként kell végrehajtani, vagy 403 hiba történt az előkészítés során. Futtassa a parancsot a használatával `sudo` . |
| 6 | Érvénytelen a csomag architektúrája, vagy hiba történt a bevezetéskor visszaadott 200-es hiba miatt. a omsagent-*x64.sh csomagok csak 64 bites rendszerekre telepíthetők, és a omsagent-* x86.sh csomagok csak 32 bites rendszereken telepíthetők. Töltse le a megfelelő csomagot az architektúrához a [legújabb kiadásban](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Nem sikerült telepíteni a OMS-csomagot. Tekintse át a parancs kimenetét a gyökérszintű hiba miatt. |
| 18 | Nem sikerült telepíteni a OMSConfig-csomagot. Tekintse át a parancs kimenetét a gyökérszintű hiba miatt. |
| 19 | Nem sikerült telepíteni a a "a" csomagot. Tekintse át a parancs kimenetét a gyökérszintű hiba miatt. |
| 20 | Az SCX-csomag telepítése nem sikerült. Tekintse át a parancs kimenetét a gyökérszintű hiba miatt. |
| 21 | A szolgáltatói készletek telepítése nem sikerült. Tekintse át a parancs kimenetét a gyökérszintű hiba miatt. |
| 22 | Nem sikerült telepíteni a csomagban lévő csomagot. A hiba okát a parancs kimenetében tekintheti meg |
| 23 | Már telepítve van az SCX-vagy a-adatcsomag. `--upgrade` `--install` A rendszerhéj-csomag telepítése helyett használja a parancsot. |
| 30 | Belső kötegi hiba. A kimenetből származó részleteket tartalmazó [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 55 | Nem támogatott OpenSSL-verzió, vagy nem lehet kapcsolódni Azure Monitor vagy a dpkg-hoz zárolt vagy hiányzó curl program. |
| 61 | Hiányzik a Python ctypes könyvtára. Telepítse a Python ctypes könyvtárat vagy csomagot (Python-ctypes). |
| 62 | Hiányzó tar-program, telepítse a Tar-t. |
| 63 | Hiányzó sed-program, a sed telepítése. |
| 64 | Hiányzó curl-program, a curl telepítése. |
| 65 | Hiányzó GPG program, telepítse a GPG-t. |

## <a name="onboarding-error-codes"></a>Bevezetési hibakódok

| Hibakód | Értelmezés |
| --- | --- |
| 2 | A omsadmin parancsfájl érvénytelen beállítást adott meg. Futtatás `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` a használathoz. |
| 3 | A omsadmin parancsfájlhoz megadott konfiguráció érvénytelen. Futtatás `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` a használathoz. |
| 4 | A omsadmin parancsfájlhoz megadott proxy érvénytelen. Ellenőrizze a proxyt, és tekintse [meg a http-proxy használatára vonatkozó dokumentációt](./log-analytics-agent.md#firewall-requirements). |
| 5 | 403 HTTP-hiba érkezett a következőtől: Azure Monitor. A részletekért tekintse meg a omsadmin parancsfájl teljes kimenetét. |
| 6 | Nem 200 HTTP-hiba érkezett a következőtől: Azure Monitor. A részletekért tekintse meg a omsadmin parancsfájl teljes kimenetét. |
| 7 | Nem lehet csatlakozni a Azure Monitorhoz. A részletekért tekintse meg a omsadmin parancsfájl teljes kimenetét. |
| 8 | Hiba történt Log Analytics munkaterület bevezetéséhez. A részletekért tekintse meg a omsadmin parancsfájl teljes kimenetét. |
| 30 | Belső parancsfájl hibája. A kimenetből származó részleteket tartalmazó [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 31 | Hiba történt az ügynök AZONOSÍTÓjának létrehozásakor. A kimenetből származó részleteket tartalmazó [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 32 | Hiba történt a tanúsítványok létrehozásakor. A részletekért tekintse meg a omsadmin parancsfájl teljes kimenetét. |
| 33 | Hiba történt a omsconfig metaconfiguration létrehozásakor. A kimenetből származó részleteket tartalmazó [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 34 | A Metaconfiguration létrehozási parancsfájlja nincs jelen. Próbálja megismételni a bevezetést `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>` . |

## <a name="enable-debug-logging"></a>Hibakeresési naplózás engedélyezése
### <a name="oms-output-plugin-debug"></a>OMS kimeneti beépülő modul hibakeresése
 A speciálisan lehetővé teszi a beépülő modulok naplózási szintjeinek megadását, így különböző naplózási szinteket adhat meg a bemenetekhez és kimenetekhez. Ha más naplózási szintet szeretne megadni a OMS kimenetéhez, szerkessze az általános ügynök konfigurációját a következő helyen: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .  

 A OMS kimeneti beépülő modulban a konfigurációs fájl vége előtt módosítsa a `log_level` tulajdonságot a következőre `info` `debug` :

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

A hibakeresési naplózás lehetővé teszi a kötegelt feltöltések megtekintését Azure Monitor típus, az adatelemek száma és a küldési idő szerint elválasztva:

*Példa hibakeresést támogató naplóra:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Részletes kimenet
A OMS kimeneti beépülő modul használata helyett közvetlenül a `stdout` (log Analytics ügynök for Linux-naplófájlban látható) adatokat is kiállíthatja.

A Log Analytics általános ügynök konfigurációs fájljában a (z) lapon `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` adja meg a OMS kimeneti beépülő modulját az `#` egyes sorok elé:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

A kimeneti beépülő modul alatt törölje a következő szakasz megjegyzését az `#` egyes sorok előtt:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Probléma: nem lehet csatlakozni a proxyn keresztül Azure Monitor

### <a name="probable-causes"></a>Lehetséges okok
* Az előkészítés során megadott proxy helytelen
* A Azure Monitor és Azure Automation szolgáltatási végpontok nem szerepelnek az adatközpont jóváhagyott listáján 

### <a name="resolution"></a>Feloldás
1. A következő parancs futtatásával Azure Monitor a Linuxra Log Analytics-ügynökkel `-v` . Lehetővé teszi az ügynök részletes kimenetét, amely a proxyn keresztül csatlakozik a Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Tekintse át a [Proxybeállítások frissítése](agent-manage.md#update-proxy-settings) szakaszt, és ellenőrizze, hogy megfelelően konfigurálta-e az ügynököt egy proxykiszolgálón keresztül történő kommunikációhoz.    

3. Ellenőrizze, hogy a Azure Monitor [hálózati tűzfal követelményei](./log-analytics-agent.md#firewall-requirements) listán szereplő végpontok megfelelően vannak-e hozzáadva az engedélyezési listához. Ha Azure Automation használ, a szükséges hálózati konfigurációs lépések a fentiekben is összekapcsolhatók.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Probléma: 403-as hibaüzenet jelenik meg a bevezetési kísérlet során

### <a name="probable-causes"></a>Lehetséges okok
* A dátum és az idő nem megfelelő a Linux-kiszolgálón 
* A munkaterület-azonosító és a használt munkaterület-kulcs helytelen.

### <a name="resolution"></a>Feloldás

1. A Linux-kiszolgálón található idő megadásával adja meg a parancs dátumát. Ha az idő +/-15 perc az aktuális időponttól, akkor a bevezetés sikertelen lesz. A frissítés kijavítani a Linux-kiszolgáló dátumát és/vagy időzónáját. 
2. Ellenőrizze, hogy telepítette-e a Linux rendszerhez készült Log Analytics-ügynök legújabb verzióját.  A legújabb verzió mostantól értesíti, ha az idő eldöntése okozza a bevezetési hibát.
3. A jelen cikk korábbi részében ismertetett telepítési utasításokat követve állítsa be a megfelelő munkaterület-azonosítót és-munkaterület-kulcsot.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Probléma: egy 500-es és 404-as hibaüzenet jelenik meg a naplófájlban közvetlenül a bevezetést követően
Ez egy ismert probléma, amely a Linux-adatok első feltöltésével Log Analytics munkaterületre történik. Ez nincs hatással az elküldött vagy a szolgáltatási élményre.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Probléma: a omiagent a 100%-os CPU használatával jelenik meg

### <a name="probable-causes"></a>Lehetséges okok
Az NSS-PEM-csomag [v 1.0.3 -5. el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) -beli regressziója súlyos teljesítménnyel kapcsolatos problémát okozott, melyet a vörös hajú/CentOS 7. x disztribúcióban láttunk. A probléma további megismeréséhez tekintse meg a következő dokumentációt: hiba [1667121 teljesítmény-regresszió a libcurl-ben](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

A teljesítménnyel kapcsolatos hibák nem minden időben történnek, és nagyon nehéz reprodukálni őket. Ha ilyen problémát tapasztal a omiagent, használja a szkript omiHighCPUDiagnostics.sh, amely összegyűjti a omiagent verem-nyomkövetését, amikor egy bizonyos küszöbértéket meghalad.

1. A parancsfájl letöltése <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Diagnosztika futtatása 24 órán át 30%-os CPU-küszöbértékkel <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. A hívási verem omiagent_trace fájlban lesz kiszámítva, ha sok curl és NSS függvény hívása jelenik meg, kövesse az alábbi megoldási lépéseket.

### <a name="resolution-step-by-step"></a>Megoldás (lépésről lépésre)

1. Frissítse az NSS-PEM csomagot a következőre: [v 1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Ha az NSS-PEM nem érhető el a frissítéshez (többnyire a CentOS-on történik), akkor a curl a 7.29.0-46 értékre vált. Ha tévedésben futtatja a "yum update" parancsot, akkor a curl a 7.29.0-51-re frissül, és a probléma újra megtörténik. <br/>
`sudo yum downgrade curl libcurl`

3. Újraindítási a (z): <br/>
`sudo scxadmin -restart`


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Probléma: nem jelenik meg a továbbított syslog-üzenetek 

### <a name="probable-causes"></a>Lehetséges okok
* A Linux-kiszolgálón alkalmazott konfiguráció nem engedélyezi az ellátott létesítmények és/vagy naplózási szintek gyűjtését
* A syslog nem kerül megfelelően a Linux-kiszolgáló felé
* A másodpercenként továbbított üzenetek száma túl nagy a Linux rendszerű Log Analytics-ügynök alapkonfigurációjának kezeléséhez.

### <a name="resolution"></a>Feloldás
* Ellenőrizze, hogy a syslog Log Analytics munkaterületén található konfiguráció rendelkezik-e az összes szolgáltatással és a megfelelő naplózási szinttel. [A syslog-gyűjtemény konfigurálásának áttekintése a Azure Portal](data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Ellenőrizze, hogy a natív syslog üzenetküldési démonok ( `rsyslog` , `syslog-ng` ) képesek-e fogadni a továbbított üzeneteket
* Ellenőrizze a tűzfal beállításait a syslog-kiszolgálón, hogy az üzenetek ne legyenek letiltva.
* Syslog-üzenet szimulálása Log Analytics parancs használatával `logger`
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Probléma: a rendszer a omsagent-naplófájlban már használatban lévő errno-címeket fogadja
Ha `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` a omsagent. log naplófájlban látható.

### <a name="probable-causes"></a>Lehetséges okok
Ez a hiba azt jelzi, hogy a Linux diagnosztikai bővítmény (LAD) a Log Analytics linuxos virtuálisgép-bővítménnyel együtt települ, és ugyanazt a portot használja a syslog-adatgyűjtéshez, mint a omsagent.

### <a name="resolution"></a>Feloldás
1. Root-ként hajtsa végre a következő parancsokat (vegye figyelembe, hogy 25224 egy példa, és lehetséges, hogy a környezetében a LAD által használt eltérő portszám jelenik meg):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Ezután szerkesztenie kell a helyes `rsyslogd` vagy a `syslog_ng` konfigurációs fájlt, és módosítania kell a Lad-hez kapcsolódó konfigurációt, hogy az a 25229-es portra írjon.

2. Ha a virtuális gép fut `rsyslogd` , a módosítandó fájl a `/etc/rsyslog.d/95-omsagent.conf` következő: (ha létezik, más `/etc/rsyslog` ). Ha a virtuális gép fut `syslog_ng` , a módosítandó fájl a következő: `/etc/syslog-ng/syslog-ng.conf` .
3. Indítsa újra a omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart` .
4. Indítsa újra a syslog szolgáltatást.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Probléma: a omsagent nem távolítható el a kiürítési beállítás használatával.

### <a name="probable-causes"></a>Lehetséges okok

* A Linux diagnosztikai bővítmény telepítve van
* A Linux diagnosztikai bővítmény telepítése és eltávolítása megtörtént, de továbbra is hibaüzenet jelenik meg a MDSD által használt omsagent, és nem távolítható el.

### <a name="resolution"></a>Feloldás
1. Távolítsa el a Linux diagnosztikai bővítményt (LAD).
2. Távolítsa el a Linux diagnosztikai bővítmény fájljait a gépről, ha azok a következő helyen találhatók: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` és `/var/opt/microsoft/omsagent/LAD/` .

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Probléma: a Nagios-adatértékek nem láthatók 

### <a name="probable-causes"></a>Lehetséges okok
* A Omsagent-felhasználónak nincs engedélye a Nagios-naplófájl olvasására
* A Nagios-forrás és-szűrő nem lett eltávolítva a omsagent. conf fájlból

### <a name="resolution"></a>Feloldás
1. Adja hozzá a omsagent-felhasználót a Nagios-fájl olvasásához a következő [utasítások](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)követésével.
2. A Linux rendszerhez készült általános konfigurációs fájljának Log Analytics ügynökében `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` ellenőrizze  , hogy a Nagios forrása és a szűrő sincs-e megadva.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Probléma: nem jelenik meg Linux-alapú 

### <a name="probable-causes"></a>Lehetséges okok
* A Azure Monitor bevezetése nem sikerült
* A Azure Monitorhoz való kapcsolódás le van tiltva
* A virtuális gép újraindult
* A program a következőhöz képest manuálisan frissített egy újabb verzióra, mint amit a Log Analytics Agent Linux-csomaggal telepített.
* A OMS-ügynök zárolva van.
* A DSC-erőforrás naplók *osztálya nem található* hibát a `omsconfig.log` naplófájlban
* Log Analytics-ügynök biztonsági mentése
* A DSC-naplók *jelenlegi konfigurációja nem létezik. Futtassa Start-DscConfiguration parancsot a-Path paraméterrel egy konfigurációs fájl megadásához, és először hozzon létre egy aktuális konfigurációt.* a `omsconfig.log` naplófájlban nem található naplóbejegyzés a `PerformRequiredConfigurationChecks` műveletekről.

### <a name="resolution"></a>Feloldás
1. Telepítse az összes függőséget, például a naplózott csomagot.
2. Ellenőrizze, hogy sikerült-e bevezetést Azure Monitor a következő fájl létezésének ellenőrzésével: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` .  Ha nem, akkor a omsadmin.sh parancssori [utasítások](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)használatával.
4. Ha proxyt használ, ellenőrizze a fenti proxy-hibaelhárítási lépéseket.
5. Bizonyos Azure-beli terjesztési rendszerek esetében a nem indul el a virtuális gép újraindítása után. Ez azt eredményezi, hogy nem fog megjelenni a megoldással kapcsolatos változáskövetési vagy UpdateManagement. A megkerülő megoldás az, ha a következőt `sudo /opt/omi/bin/service_control restart` használja:
6. Azt követően, hogy az új verzióra történő frissítés után a rendszer manuálisan újraindította a-csomagot, manuálisan kell újraindítani a Log Analytics-ügynök működésének folytatásához. Erre a lépésre akkor van szükség, ha a rendszer nem indítja el automatikusan a következő adatdisztribúciókat: Futtassa `sudo /opt/omi/bin/service_control restart` újra a következőt:.
* Bizonyos helyzetekben a képpontok zárolva lesznek. A OMS-ügynök letiltott állapotra vár, amely az összes adatgyűjtést blokkolja. A OMS-ügynök folyamata futni fog, de nincs olyan tevékenység, amely nem jelent meg új naplófájlokat (például elküldett szívveréseket) `omsagent.log` . `sudo /opt/omi/bin/service_control restart`Az ügynök helyreállításához indítsa újra a következőt:.
7. Ha a DSC-erőforrás *osztály nem található* a omsconfig. log naplófájlban, futtassa a következőt: `sudo /opt/omi/bin/service_control restart` .
8. Bizonyos esetekben, ha a Linux Log Analytics-ügynöke nem tud kommunikálni Azure Monitor, az ügynökön lévő adat a teljes puffer méretére lesz mentve: 50 MB. Az ügynököt újra kell indítani a következő parancs futtatásával `/opt/microsoft/omsagent/bin/service_control restart` .

    >[!NOTE]
    >Ezt a problémát az ügynök 1.1.0-28-as vagy újabb verziójában rögzítették
    >

* Ha a `omsconfig.log` naplófájl nem jelzi, hogy a `PerformRequiredConfigurationChecks` műveletek rendszeresen futnak a rendszeren, előfordulhat, hogy probléma van a cron-feladatokkal vagy szolgáltatással. Győződjön meg arról, hogy a cron-feladatot a alatt található `/etc/cron.d/OMSConsistencyInvoker` . Ha szükséges, futtassa a következő parancsokat a cron-feladatok létrehozásához:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Győződjön meg arról is, hogy a cron szolgáltatás fut. A- `service cron status` t a Debian, az Ubuntu, a SUSE vagy `service crond status` a RHEL, a CentOS Oracle Linux használatával is használhatja a szolgáltatás állapotának vizsgálatához. Ha a szolgáltatás nem létezik, telepítheti a bináris fájlokat, és elindíthatja a szolgáltatást a következő paranccsal:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Probléma: Ha a portálon a syslog vagy a Linux teljesítményszámlálók számára konfigurálja a gyűjteményt, a beállítások nincsenek alkalmazva.

### <a name="probable-causes"></a>Lehetséges okok
* A Linux rendszerhez készült Log Analytics-ügynök nem vette át a legújabb konfigurációt
* A portálon módosult beállítások nem lettek alkalmazva

### <a name="resolution"></a>Feloldás
**Háttér:** `omsconfig` a Linux-alapú konfigurációs ügynök Log Analytics ügynöke, amely öt percenként keresi az új portál-oldali konfigurációt. Ezt a konfigurációt a rendszer a/etc/opt/Microsoft/omsagent/conf/omsagent.conf. címen található linuxos konfigurációs fájlok Log Analytics ügynökére alkalmazza.

* Bizonyos esetekben előfordulhat, hogy a linuxos konfigurációs ügynök Log Analytics ügynöke nem tud kommunikálni a portál konfigurációs szolgáltatásával, ami a legújabb konfigurációt nem alkalmazza.
  1. A vagy a futtatásával győződjön meg arról, hogy az `omsconfig` ügynök telepítve van `dpkg --list omsconfig` `rpm -qi omsconfig` .  Ha nincs telepítve, telepítse újra a Linux rendszerhez készült Log Analytics-ügynök legújabb verzióját.

  2. A következő parancs futtatásával győződjön meg arról, hogy az `omsconfig` ügynök tud kommunikálni Azure Monitorval `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` . Ez a parancs visszaadja az ügynök által a szolgáltatástól kapott konfigurációt, beleértve a syslog-beállításokat, a Linux-teljesítményszámlálókat és az egyéni naplókat. Ha a parancs végrehajtása sikertelen, futtassa a következő parancsot `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . Ez a parancs arra kényszeríti a omsconfig-ügynököt, hogy beszéljen Azure Monitor és beolvassa a legújabb konfigurációt.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Probléma: nem jelenik meg egyéni naplózási érték 

### <a name="probable-causes"></a>Lehetséges okok
* A Azure Monitor bevezetése nem sikerült.
* A beállítás **a következő konfigurációt alkalmazza a Linux-kiszolgálókra,** nem lett kiválasztva.
* a omsconfig nem vette át a legújabb egyéni naplózási konfigurációt a szolgáltatásból.
* A Linux-felhasználók Log Analytics ügynöke nem `omsagent` tud hozzáférni az egyéni naplóhoz engedélyek miatt vagy nem található.  A következő hibák jelenhetnek meg:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Ismert probléma az Log Analytics Agent for Linux 1.1.0-217-es verziójában rögzített versenyhelyzet esetében

### <a name="resolution"></a>Feloldás
1. Ellenőrizze, hogy a bevezetési Azure Monitor sikeres volt-e a következő fájl létezésének ellenőrzésével: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` . Ha nem, a következők egyike:  

  1. A omsadmin.sh parancssori utasítások használatával történő [újratelepítésre](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
  2. A Azure Portal **Speciális beállítások** területén ellenőrizze, hogy engedélyezve van-e a **következő konfiguráció alkalmazása a Linux-kiszolgálókon** beállítás.  

2. A következő parancs futtatásával győződjön meg arról, hogy az `omsconfig` ügynök tud kommunikálni Azure Monitorval `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` .  Ez a parancs visszaadja az ügynök által a szolgáltatástól kapott konfigurációt, beleértve a syslog-beállításokat, a Linux-teljesítményszámlálókat és az egyéni naplókat. Ha a parancs végrehajtása sikertelen, futtassa a következő parancsot `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . Ez a parancs arra kényszeríti a omsconfig-ügynököt, hogy beszéljen Azure Monitor és beolvassa a legújabb konfigurációt.

**Háttér:** A rendszerjogosultságú felhasználóként futó Linux-Log Analytics ügynök helyett `root` az ügynök `omsagent` felhasználóként fut. A legtöbb esetben a felhasználóknak explicit engedélyeket kell adni ahhoz, hogy bizonyos fájlokat el lehessen olvasni. Ha engedélyt szeretne adni a `omsagent` felhasználónak, futtassa a következő parancsokat:

1. A `omsagent` felhasználó hozzáadása adott csoporthoz `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Univerzális olvasási hozzáférés biztosítása a szükséges fájlhoz `sudo chmod -R ugo+rx <FILE DIRECTORY>`

A Race feltétellel kapcsolatos ismert probléma a 1.1.0-217-nél korábbi Linux-verzió Log Analytics ügynöke. A legújabb ügynök frissítése után futtassa a következő parancsot a kimeneti beépülő modul legújabb verziójának beszerzéséhez `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Probléma: új munkaterületre próbál újra bejelentkezni
Amikor egy ügynököt egy új munkaterületre próbál újra bevezetni, a Log Analytics-ügynök konfigurációját a telepítés előtt meg kell tisztítani. Ha törölni szeretné a régi konfigurációt az ügynöktől, futtassa a rendszerhéj-csomagot a következővel: `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Vagy

```
sudo sh ./onboard_agent.sh --purge
```

A következő lehetőség használatával folytathatja a bevezetést `--purge`

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Log Analytics ügynök bővítményét a Azure Portal hibás állapottal van megjelölve: a kiépítés sikertelen volt.

### <a name="probable-causes"></a>Lehetséges okok
* Log Analytics ügynök el lett távolítva az operációs rendszerből
* Log Analytics ügynök szolgáltatás nem működik, le van tiltva vagy nincs konfigurálva

### <a name="resolution"></a>Feloldás 
A probléma megoldásához hajtsa végre az alábbi lépéseket.
1. Távolítsa el a bővítményt a Azure Portalból.
2. Telepítse az ügynököt az [utasításokat](../vm/quick-collect-linux-computer.md)követve.
3. Indítsa újra az ügynököt a következő parancs futtatásával: `sudo /opt/microsoft/omsagent/bin/service_control restart` .
* Várjon néhány percet, és a kiépítés állapota **sikeresen kiépítve** értékre változik.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Probléma: a Log Analytics ügynök igény szerinti frissítése

### <a name="probable-causes"></a>Lehetséges okok

A gazdagép Log Analytics ügynökének csomagjai elavultak.

### <a name="resolution"></a>Feloldás 
A probléma megoldásához hajtsa végre az alábbi lépéseket.

1. Keresse meg a [lapon](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/)a legújabb verziót.
2. Telepítési parancsfájl letöltése (1.4.2-124 példa verziója):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. A csomagokat a végrehajtásával frissítheti `sudo sh ./omsagent-*.universal.x64.sh --upgrade` .
