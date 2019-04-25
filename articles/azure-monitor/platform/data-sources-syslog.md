---
title: Összegyűjtheti és elemezheti a Syslog-üzeneteket az Azure Monitor |} A Microsoft Docs
description: Syslog-esemény naplózása protokoll, amely Linux közös. Ez a cikk ismerteti a Syslog-üzeneteket gyűjteményét konfigurálása a Log Analytics és az általuk létrehozott rekordok részleteit.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: magoedte
ms.openlocfilehash: 41ea6222689516f224fc23ce6a658d17f7f81866
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60240859"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Syslog-adatforrások az Azure monitorban
Syslog-esemény naplózása protokoll, amely Linux közös. Alkalmazások küld üzeneteket, amelyek a helyi számítógépen tárolt vagy a Syslog-gyűjtő lett elküldve. A Linuxhoz készült Log Analytics-ügynök telepítve van, konfigurálja a továbbított üzenetek az ügynöknek a helyi Syslog démon. Az ügynök ezután elküldi az üzenetet az Azure Monitor, ahol létrejön egy megfelelő bejegyzés.  

> [!NOTE]
> Az Azure Monitor támogatja az üzeneteket a rsyslog vagy syslog-ng, ahol rsyslog-e az alapértelmezett démon gyűjteménye. Red Hat Enterprise Linux, CentOS és Oracle Linux-verzió (sysklog) 5-ös verzióját az alapértelmezett syslog démon nem támogatott a syslog-események gyűjtése. A syslog-adatok gyűjtésére ezek disztribúciók jelen verziója a [rsyslog démonnal](http://rsyslog.com) telepíteni és konfigurálni kell az sysklog helyett.
>
>

![A rendszernaplók gyűjtése](media/data-sources-syslog/overview.png)

A következő létesítmények a Syslog-gyűjtő támogatottak:

* kern
* Felhasználó
* levelezés
* Démon
* hitelesítés
* syslog
* LPR
* Hírek
* uucp
* cron
* authpriv
* ftp
* local0-local7

Az egyéb létesítmény [egy egyéni naplók-adatforrás konfigurálása](data-sources-custom-logs.md) az Azure monitorban.
 
## <a name="configuring-syslog"></a>Syslog konfigurálása
A Linuxhoz készült Log Analytics-ügynököt csak a létesítmények és súlyossági szinten pedig a konfigurációban megadott eseményeket gyűjt. Syslog konfigurálhatja az Azure Portalon keresztül, vagy konfigurációs fájlokat a Linux-ügynökök kezelése.

### <a name="configure-syslog-in-the-azure-portal"></a>Syslog konfigurálása az Azure Portalon
A Syslog beállítása a [adatok menü a Speciális beállítások](agent-data-sources.md#configuring-data-sources). Ez a konfiguráció minden egyes Linux-ügynök a konfigurációs fájlt érkeznek.

Írja be a nevét, majd egy új funkció is hozzáadhat **+**. Minden funkció esetében csak a kiválasztott súlyossági szinten pedig a üzenetek összegyűjtött.  Ellenőrizze a súlyossági szint esetén csak az adott létesítmény gyűjteni kívánt számára. Szűrő üzenetek további feltételeket nem tud biztosítani.

![Syslog konfigurálása](media/data-sources-syslog/configure.png)

Alapértelmezés szerint az összes konfigurációs módosítást automatikusan leküld az összes ügynököt. Ha azt szeretné, Syslog konfigurálása minden egyes Linux-ügynök manuálisan, törölje a jelet *alkalmaz az alábbi konfiguráció Linuxos gépeimre*.

### <a name="configure-syslog-on-linux-agent"></a>Syslog konfigurálása Linux-ügynök
Ha a [Log Analytics-ügynök telepítve van egy Linux-ügyfél](../../azure-monitor/learn/quick-collect-linux-computer.md), egy alapértelmezett syslog-konfigurációs fájl, amely meghatározza a létesítmény és az üzenetek gyűjtött súlyossági telepíti. Ehhez a fájlhoz, a konfiguráció módosítása módosíthatja. A konfigurációs fájl attól függően változik, a Syslog-démont, amely az ügyfél telepítve.

> [!NOTE]
> Ha szerkeszti a syslog-konfiguráció, a syslog démon a módosítások érvénybe léptetéséhez újra kell indítani.
>
>

#### <a name="rsyslog"></a>rsyslog
A konfigurációs fájl rsyslog **/etc/rsyslog.d/95-omsagent.conf**. Alább láthatók a saját alapértelmezett tartalmát. Ez a figyelmeztetés vagy magasabb szintű minden lehetőséget a helyi ügynök által küldött syslog-üzeneteket gyűjti.

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

Eltávolíthatja a létesítmény eltávolítása a konfigurációs fájl vonatkozó szakaszát. A súlyossági szint esetén csak az adott létesítmény módosítása az adott létesítmény bejegyzés által gyűjtött korlátozhatja. Például korlátozhatja a felhasználói üzenetek adatcserével rendelkező létesítményben egy hiba vagy magasabb szintű súlyosság kell módosítania a konfigurációs fájl a következő sort:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>Syslog-ng
A syslog-ng konfigurációs fájlja azon hely **/etc/syslog-ng/syslog-ng.conf**.  Alább láthatók a saját alapértelmezett tartalmát. Ez a syslog-üzeneteket küldött összes létesítmények és az összes súlyossági szinten pedig a helyi ügynök gyűjti.   

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

Eltávolíthatja a létesítmény eltávolítása a konfigurációs fájl vonatkozó szakaszát. Korlátozhatja a súlyossági szint esetén csak az adott létesítmény gyűjtött ehhez távolítsa el a listából.  Például korlátozhatja a felhasználói képessége csak figyelmeztetési és kritikus fontosságú üzenetek, ugyanúgy módosíthatja, hogy a konfigurációs fájl a következő szakaszban:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Adatgyűjtés a további Syslog-portok
Syslog-üzeneteket a helyi ügyfél 25224 porton figyeli a Log Analytics-ügynököket.  Az ügynök telepítve van, ha syslog alapértelmezett konfigurációja a alkalmazni, és a következő helyen található:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

A portszám két konfigurációs fájlok létrehozásával módosíthatja: egy FluentD konfigurációs fájl és a egy rsyslog – vagy – syslog-ng fájl függően a Syslog démon telepítette.  

* A FluentD konfigurációs fájl található az új fájlnak kell lennie: `/etc/opt/microsoft/omsagent/conf/omsagent.d` , és cserélje le az értéket a **port** bejegyzésben, az egyéni portszám.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* Rsyslog, hozzunk létre egy új konfigurációs fájlt: `/etc/rsyslog.d/` és az értéket % SYSLOG_PORT cserélje le az egyéni portszám.  

    > [!NOTE]
    > Ha módosítja ezt az értéket a konfigurációs fájlban `95-omsagent.conf`, azt felülírja, ha az ügynököt egy alapértelmezett konfigurációjának alkalmazására szolgál.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* A syslog-ng config módosítani kell az alábbi példa konfiguráció másolásával, és az egyéni módosított beállítások hozzáadása, a syslog-ng.conf konfigurációs fájl végén található `/etc/syslog-ng/`. Tegye **nem** használja az alapértelmezett címke **% WORKSPACE_ID % _oms** vagy **% WORKSPACE_ID_OMS**, a módosítások különböztetni egyéni címkék megadása.  

    > [!NOTE]
    > Ha módosítja az alapértelmezett értékeket a konfigurációs fájlban, ezek felülírja, ha az ügynök alapértelmezett konfigurációja a érvényes.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Miután befejezte a módosításokat, a Syslog- és a Log Analytics agent szolgáltatást újra kell indítani ahhoz, hogy a konfigurációs módosítások érvénybe léptetéséhez.   

## <a name="syslog-record-properties"></a>Syslog-rekord tulajdonságai
Syslog-rekord rendelkezik olyan típusú **Syslog** , és a tulajdonságait az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az esemény gyűjtötte a program a számítógép. |
| Létesítmény |A rendszer által generált üzenet részeként határozza meg. |
| HostIP |A rendszer az üzenetet küldő IP-címe. |
| Állomásnév |A rendszer az üzenetet küldő neve. |
| SeverityLevel |Az esemény súlyossági szintje. |
| SyslogMessage |Az üzenet szövege. |
| ProcessID |A folyamat által generált üzenet azonosítója. |
| eventTime |Dátum és idő, amelyik az esemény jött létre. |

## <a name="log-queries-with-syslog-records"></a>Syslog-rekord log lekérdezéseket.
Az alábbi táblázat a rekordok Syslog lekérő lekérdezések log különböző példákat.

| Lekérdezés | Leírás |
|:--- |:--- |
| Rendszernapló |Minden Syslog. |
| Syslog &#124; , err == "error" |Minden Syslog-rekord a hiba súlyossága |
| Syslog &#124; summarize AggregatedValue = count() összegzése számítógép szerint |A Syslog-Rekordok számlálása számítógép szerint. |
| Syslog &#124; summarize AggregatedValue = count() by létesítmény |Száma a Syslog-rekord létesítmény szerint. |

## <a name="next-steps"></a>További lépések
* Ismerje meg [lekérdezések naplózását](../../azure-monitor/log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.
* Használat [egyéni mezők](../../azure-monitor/platform/custom-fields.md) syslog-rekord származó adatok elemzése az egyes mezőket.
* [Linux-ügynökök konfigurálása](../../azure-monitor/learn/quick-collect-linux-computer.md) más típusú adatok gyűjtéséhez.
