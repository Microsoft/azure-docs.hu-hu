---
title: Syslog-adatforrások gyűjtése Log Analytics ügynökkel Azure Monitor
description: A syslog egy olyan eseménynaplózási protokoll, amely közös a Linux rendszerben. Ez a cikk ismerteti, hogyan konfigurálhatja a syslog-üzenetek gyűjteményét Log Analytics és a létrehozott rekordok részleteit.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: e82e74f4cd325444221bbd2e1c060b7cd2f5c6c7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036731"
---
# <a name="collect-syslog-data-sources-with-log-analytics-agent"></a>Syslog-adatforrások gyűjtése Log Analytics ügynökkel
A syslog egy olyan eseménynaplózási protokoll, amely közös a Linux rendszerben. Az alkalmazások elküldik a helyi gépen tárolt vagy a syslog-gyűjtőnek küldött üzeneteket. A Linux rendszerhez készült Log Analytics-ügynök telepítésekor a helyi syslog démont úgy konfigurálja, hogy továbbítsa az üzeneteket az ügynöknek. Az ügynök ezután elküldi az üzenetet, hogy Azure Monitor, ahol létrejön egy megfelelő rekord.  

> [!IMPORTANT]
> Ez a cikk ismerteti a syslog-események összegyűjtését a [log Analytics ügynökkel](./log-analytics-agent.md) , amely a Azure monitor által használt ügynökök egyike. Más ügynökök különböző adatokat gyűjtenek, és eltérően vannak konfigurálva. A rendelkezésre álló ügynökök és az összegyűjtött adatok listáját lásd: [Azure monitor ügynökök áttekintése](../agents/agents-overview.md) .

> [!NOTE]
> Azure Monitor támogatja a rsyslog vagy syslog-ng által küldött üzenetek gyűjteményét, ahol a rsyslog az alapértelmezett démon. A syslog-események gyűjteménye nem támogatja az alapértelmezett syslog démont a Red Hat Enterprise Linux, a CentOS és a Oracle Linux verzió (sysklog) 5. verziójában. A rendszernapló-adatok ezen disztribúciók ezen verziójából való összegyűjtéséhez a [rsyslog démont](http://rsyslog.com) telepíteni és konfigurálni kell a sysklog lecserélése érdekében.
>
>

![Syslog-gyűjtemény](media/data-sources-syslog/overview.png)

A syslog-gyűjtő a következő létesítményeket támogatja:

* egalizálás
* felhasználó!
* Levelezés
* démon
* Auth
* syslog
* LPR
* news
* UUCP
* cron
* authpriv
* ftp
* local0 – local7

Bármilyen más létesítmény esetében [konfigurálja az egyéni naplók adatforrását](data-sources-custom-logs.md) Azure monitor.
 
## <a name="configuring-syslog"></a>A syslog konfigurálása
A Linux rendszerhez készült Log Analytics-ügynök csak a konfigurációjában megadott létesítményekkel és megszakításokkal gyűjt eseményeket. A syslog-t a Azure Portal vagy a Linux-ügynökökön található konfigurációs fájlok kezelésével konfigurálhatja.

### <a name="configure-syslog-in-the-azure-portal"></a>A syslog konfigurálása a Azure Portalban
Konfigurálja a syslog-t az Log Analytics munkaterület [ügynök konfigurálása menüjében](../agents/agent-data-sources.md#configuring-data-sources) . Ezt a konfigurációt minden Linux-ügynök konfigurációs fájljába továbbítja a rendszer.

A **létesítmény hozzáadása** lehetőségre kattintva hozzáadhat egy új létesítményt. Minden egyes létesítmény esetében csak a kiválasztott részekkel rendelkező üzenetek lesznek összegyűjtve.  Tekintse át a gyűjteni kívánt adott létesítmény súlyosságát. Nem adhat meg további feltételeket az üzenetek szűréséhez.

[![A syslog konfigurálása](media/data-sources-syslog/configure.png)](media/data-sources-syslog/configure.png#lightbox)

Alapértelmezés szerint a rendszer az összes konfigurációs módosítást automatikusan leküldi az összes ügynöknek. Ha minden Linux-ügynökön manuálisan szeretné konfigurálni a syslog-t, törölje a jelet az *alábbi konfiguráció alkalmazása a saját gépekre* jelölőnégyzetből.

### <a name="configure-syslog-on-linux-agent"></a>A syslog konfigurálása Linux-ügynökön
Ha a [log Analytics-ügynök Linux-ügyfélre van telepítve](../vm/quick-collect-linux-computer.md), egy alapértelmezett syslog-konfigurációs fájlt telepít, amely meghatározza a begyűjtött üzenetek létesítményét és súlyosságát. A fájl módosításával módosíthatja a konfigurációt. A konfigurációs fájl különbözik attól függően, hogy az ügyfél melyik syslog démont telepítette.

> [!NOTE]
> Ha szerkeszti a syslog-konfigurációt, újra kell indítania a syslog démont a módosítások érvénybe léptetéséhez.
>
>

#### <a name="rsyslog"></a>rsyslog
A rsyslog konfigurációs fájlja a következő helyen található: **/etc/rsyslog.d/95-omsagent.conf**. Alább látható az alapértelmezett tartalma. Ezzel a beállítással a helyi ügynök által küldött syslog-üzeneteket a rendszer figyelmeztetési vagy magasabb szintű minden létesítmény számára gyűjti.

```config
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

A létesítmény eltávolításához távolítsa el a konfigurációs fájl szakaszát. A létesítmény bejegyzésének módosításával korlátozhatja az adott létesítmény számára összegyűjtött megszakításokat. Ha például a felhasználói létesítményt olyan üzenetekre szeretné korlátozni, amelyekben hiba vagy magasabb súlyosság van, akkor a konfigurációs fájl adott sorát a következőre kell módosítania:

```config
user.error    @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>syslog – ng
A syslog-ng konfigurációs fájlja a következő helyen található: **/etc/syslog-ng/syslog-ng.conf**.  Alább látható az alapértelmezett tartalma. Ez a művelet a helyi ügynöktől érkező syslog-üzeneteket gyűjti az összes létesítmény és az összes tartomány számára.   

```config
#
# Warnings (except iptables) in one file:
#
destination warn { file("/var/log/warn" fsync(yes)); };
log { source(src); filter(f_warn); destination(warn); };

#OMS_Destination
destination d_oms { udp("127.0.0.1" port(25224)); };

#OMS_facility = auth
filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
log { source(src); filter(f_auth_oms); destination(d_oms); };

#OMS_facility = authpriv
filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
log { source(src); filter(f_authpriv_oms); destination(d_oms); };

#OMS_facility = cron
filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
log { source(src); filter(f_cron_oms); destination(d_oms); };

#OMS_facility = daemon
filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
log { source(src); filter(f_daemon_oms); destination(d_oms); };

#OMS_facility = kern
filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
log { source(src); filter(f_kern_oms); destination(d_oms); };

#OMS_facility = local0
filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
log { source(src); filter(f_local0_oms); destination(d_oms); };

#OMS_facility = local1
filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
log { source(src); filter(f_local1_oms); destination(d_oms); };

#OMS_facility = mail
filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
log { source(src); filter(f_mail_oms); destination(d_oms); };

#OMS_facility = syslog
filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
log { source(src); filter(f_syslog_oms); destination(d_oms); };

#OMS_facility = user
filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

A létesítmény eltávolításához távolítsa el a konfigurációs fájl szakaszát. Ha korlátozni szeretné az adott létesítmény által összegyűjtött megszakításokat, távolítsa el őket a listáról.  Ha például a felhasználói létesítményt csak a riasztásra és a kritikus üzenetekre szeretné korlátozni, a konfigurációs fájl adott szakaszát a következőre kell módosítania:

```config
#OMS_facility = user
filter f_user_oms { level(alert,crit) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

### <a name="collecting-data-from-additional-syslog-ports"></a>Adatok gyűjtése további syslog-portokból
A Log Analytics ügynök a 25224-as porton a helyi ügyfélen figyeli a syslog-üzeneteket.  Az ügynök telepítésekor a rendszer az alapértelmezett syslog-konfigurációt alkalmazza, és a következő helyen található:

* Rsyslog `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

A portszámot úgy változtathatja meg, hogy két konfigurációs fájlt hoz létre: egy Fluent konfigurációs fájlt és egy rsyslog-vagy-syslog-ng fájlt a telepített syslog démontól függően.  

* A folyékonyan használt konfigurációs fájlnak egy új fájlnak kell lennie, amely a (z) helyen található, `/etc/opt/microsoft/omsagent/conf/omsagent.d` és a **port** bejegyzésben szereplő értéket cserélje le az egyéni portszámára.

    ```xml
    <source>
        type syslog
        port %SYSLOG_PORT%
        bind 127.0.0.1
        protocol_type udp
        tag oms.syslog
    </source>
    <filter oms.syslog.**>
        type filter_syslog
    ```

* A rsyslog hozzon létre egy új konfigurációs fájlt a következő helyen:, `/etc/rsyslog.d/` és cserélje le a% SYSLOG_PORT% értéket az egyéni portszámra.  

    > [!NOTE]
    > Ha módosítja ezt az értéket a konfigurációs fájlban `95-omsagent.conf` , akkor a rendszer felülírja, ha az ügynök alapértelmezett konfigurációt alkalmaz.
    >

    ```config
    # OMS Syslog collection for workspace %WORKSPACE_ID%
    kern.warning              @127.0.0.1:%SYSLOG_PORT%
    user.warning              @127.0.0.1:%SYSLOG_PORT%
    daemon.warning            @127.0.0.1:%SYSLOG_PORT%
    auth.warning              @127.0.0.1:%SYSLOG_PORT%
    ```

* A syslog-ng konfigurációt úgy kell módosítani, hogy átmásolja az alább látható példa konfigurációt, és hozzáadja az egyéni módosított beállításokat a syslog-ng. conf konfigurációs fájl végéhez a következő helyen: `/etc/syslog-ng/` . Ne **használja a** **(z)% WORKSPACE_ID% _oms** vagy a **(z)% WORKSPACE_ID_OMS** alapértelmezett címkét, adjon meg egy egyéni címkét a módosítások megkülönböztetése érdekében.  

    > [!NOTE]
    > Ha módosítja a konfigurációs fájl alapértelmezett értékeit, akkor azok felül lesznek írva, amikor az ügynök alapértelmezett konfigurációt alkalmaz.
    >

    ```config
    filter f_custom_filter { level(warning) and facility(auth; };
    destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
    log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };
    ```

A módosítások elvégzése után újra kell indítani a syslog és a Log Analytics Agent szolgáltatást, hogy a konfigurációs változások életbe léptetése megtörténjen.   

## <a name="syslog-record-properties"></a>Syslog-rekord tulajdonságai
A syslog-rekordok rendelkeznek **syslog** típussal, és rendelkeznek a következő táblázatban szereplő tulajdonságokkal.

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az a számítógép, amelyre az eseményt gyűjtötték. |
| Létesítmény |Meghatározza az üzenetet létrehozó rendszer részét. |
| HostIP |Az üzenetet küldő számítógép IP-címe. |
| HostName |Az üzenetet küldő számítógép neve. |
| SeverityLevel |Az esemény súlyossági szintje. |
| SyslogMessage |Az üzenet szövege. |
| ProcessID |Az üzenetet létrehozó folyamat azonosítója. |
| EventTime |Az esemény létrehozásának dátuma és időpontja. |

## <a name="log-queries-with-syslog-records"></a>Lekérdezések naplózása syslog-rekordokkal
Az alábbi táblázat a syslog-rekordokat lekérő lekérdezések különböző példáit mutatja be.

| Lekérdezés | Leírás |
|:--- |:--- |
| Rendszernapló |Minden syslog. |
| Syslog &#124;, ahol a SeverityLevel = = "hiba" |Minden syslog-rekord, amelynek súlyossága a hiba. |
| Syslog &#124; összefoglaló AggregatedValue = count () számítógépenként |A számítógépen a syslog-rekordok száma. |
| Syslog &#124; összefoglalja a AggregatedValue = count () szolgáltatást a létesítmény szerint |Syslog-rekordok száma a létesítmény alapján. |

## <a name="next-steps"></a>Következő lépések
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../logs/log-query-overview.md) .
* [Egyéni mezők](../logs/custom-fields.md) használatával elemezheti az adatokat a syslog-rekordokból az egyes mezőkbe.
* [Linux-ügynökök konfigurálása](../vm/quick-collect-linux-computer.md) más típusú adatok gyűjtéséhez.