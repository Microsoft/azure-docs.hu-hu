---
title: Csatlakozás Azure-on Előzetesben Sentinel-Palo Alto Networks-adatok |} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a Palo Alto Networks-adatokat az Azure-Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 9b899bcae473edfccbf587baece27089fc001ff4
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921832"
---
# <a name="connect-your-palo-alto-networks-appliance"></a>Csatlakozás a Palo Alto Networks-berendezés

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Csatlakozhat az Azure-Sentinel bármely Palo Alto Networks-berendezés Syslog CEF, a naplófájlokat menti. Az Azure Sentinel-integráció segítségével könnyedén futtathat elemzések és lekérdezések a naplófájl adatai között a Palo Alto Networks. Hogyan Azure Sentinel-fogadnak CEF-adatok a további információkért lásd: [csatlakoztatása CEF berendezések](connect-common-event-format.md).

> [!NOTE]
> Adatokat a munkaterület, amely futtatja az Azure-Sentinel a földrajzi helyen kell tárolni.

## <a name="step-1-connect-your-palo-alto-networks-appliance-using-an-agent"></a>1. lépés: Csatlakozás a Palo Alto Networks-berendezés-ügynök használatával

A Palo Alto Networks-berendezés csatlakozni az Azure-Sentinel, dedikált gépre az ügynökök telepítéséhez szüksége (virtuális gép vagy a helyszínen) a készüléket és a Sentinel-Azure közötti kommunikáció támogatásához. Az ügynök automatikusan vagy manuálisan telepítheti. Automatikus központi telepítési csak akkor használható, ha dedikált számítógépe egy új virtuális Gépet hoz létre az Azure-ban. 

Azt is megteheti telepítheti az ügynököt manuálisan a meglévő Azure virtuális gép, egy virtuális gépen egy másik felhőben vagy a helyszíni gépen.

A hálózati diagram mindkét lehetőség előnyeivel, olvassa el [adatforrások csatlakoztatása](connect-data-sources.md#agent-options).


### <a name="deploy-the-agent-in-azure"></a>Telepítse az ügynököt az Azure-ban

1. Az Azure-Sentinel-portálon kattintson a **adatösszekötők** válassza ki a készüléket. 

1. A **Linux Syslog-ügynök konfigurációjának**:
   - Válasszon **automatikus központi telepítési** szeretné-e a fent leírtak szerint, hozzon létre egy új gépet, amely előre telepítve van az Azure Sentinel-ügynökkel, és tartalmazza a konfiguráció szükséges. Válassza ki **automatikus központi telepítési** kattintson **automatikus ügynöktelepítés**. Ekkor megjelenik a vásárlás lap, amely automatikusan csatlakozik a munkaterülethez, a dedikált virtuális gépek. A virtuális gép egy **D2s v3-as standard (2 vcpu-k, 8 GB memória)** és a egy nyilvános IP-címmel rendelkezik.
      1. Az a **egyéni üzembe helyezés** lapon, adja meg az adatokat, és válassza ki a felhasználónevet és jelszót és elfogadja a feltételeket és kikötéseket, a virtuális gép vásárolhat.
      1. Konfigurálja a berendezést, a lapon szereplő beállításokkal, mint a naplókat. Az általános Common Event Format-összekötőhöz használja ezeket a beállításokat:
         - Protokoll = UDP
         - Port = 514-es
         - Létesítmény = helyi – 4
         - Formátum = CEF
   - Válasszon **manuális üzembe helyezési** Ha egy meglévő virtuális gép használja a dedikált Linux-gép, amelyre az Azure Sentinel-ügynököt kell telepíteni szeretné. 
      1. A **a Syslog-ügynök letöltése és telepítése**válassza **Azure-beli Linuxos virtuális gép**. 
      1. Az a **virtuális gépek** képernyő, amely megnyílik, válassza ki a gépet, használja, és kattintson a kívánt **Connect**.
      1. Az összekötő képernyőn alatt **és előre Syslog konfigurálása**állítsa be a Syslog démon e **rsyslog.d** vagy **syslog-ng**. 
      1. Másolja ezeket a parancsokat, és futtassa őket a berendezés:
          - Ha a kiválasztott rsyslog.d:
              
            1. Mondja el a Syslog démon a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök használatával a 25226-os- és a létesítmény local_4 figyelni. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ahol {0} le kell cserélni a munkaterület GUID azonosítója.
            
            1. A syslog démon újraindításához `sudo service rsyslog restart`
             
          - Ha a syslog-ng választotta:

              1. Mondja el a Syslog démon a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök használatával a 25226-os- és a létesítmény local_4 figyelni. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ahol {0} le kell cserélni a munkaterület GUID azonosítója.

              3. A syslog démon újraindításához `sudo service syslog-ng restart`
      2. Indítsa újra a Syslog-ügynök a következő paranccsal: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Győződjön meg arról, hogy nincsenek hibák az ügynöknaplóban Ez a parancs futtatásával: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>A helyi Linux-kiszolgálón az ügynök telepítése

Ha nem használja az Azure, ügynököt manuálisan telepíti az Azure-Sentinel futtatása egy dedikált Linux-kiszolgálón.


1. Az Azure-Sentinel-portálon kattintson a **adatösszekötők** válassza ki a készüléket.
1. Egy dedikált Linux rendszerű virtuális gép létrehozása alatt **Linux Syslog-ügynök konfigurációjának** válasszon **manuális üzembe helyezési**.
   1. A **a Syslog-ügynök letöltése és telepítése**válassza **nem Azure-beli Linux rendszerű gép**. 
   1. Az a **közvetlen ügynök** képernyő, amely megnyílik, válassza ki **Linux-ügynök** töltse le az ügynököt, vagy letöltheti a Linux rendszerű számítógépen az alábbi paranccsal:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. Az összekötő képernyőn alatt **és előre Syslog konfigurálása**állítsa be a Syslog démon e **rsyslog.d** vagy **syslog-ng**. 
      1. Másolja ezeket a parancsokat, és futtassa őket a berendezés:
         - Ha az rsyslog választotta:
           1. Mondja el a Syslog démon a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök használatával a 25226-os- és a létesítmény local_4 figyelni. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ahol {0} le kell cserélni a munkaterület GUID azonosítója.
           3. A syslog démon újraindításához `sudo service rsyslog restart`
         - Ha a syslog-ng választotta:
            1. Mondja el a Syslog démon a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök használatával a 25226-os- és a létesítmény local_4 figyelni. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ahol {0} le kell cserélni a munkaterület GUID azonosítója.
            3. A syslog démon újraindításához `sudo service syslog-ng restart`
      1. Indítsa újra a Syslog-ügynök a következő paranccsal: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Győződjön meg arról, hogy nincsenek hibák az ügynöknaplóban Ez a parancs futtatásával: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>2. lépés: Továbbítják a Palo Alto Networks-naplókat a Syslog-ügynök

Syslog-üzeneteket az Azure-munkaterülethez a Syslog-ügynökön keresztül a CEF-formátumban továbbítani a Palo Alto Networks konfigurálása:
1.  Lépjen a [Common Event Format (CEF) konfigurációs útmutatók](https://docs.paloaltonetworks.com/resources/cef) , és töltse le a pdf-berendezés típusához. Kövesse az összes az útmutató a CEF-események gyűjtésére a Palo Alto Networks-berendezés beállításához. 

1.  Lépjen a [Syslog konfigurálása figyelési](https://aka.ms/asi-syslog-paloalto-forwarding) , és hajtsa végre a 2. és 3 CEF eseménytovábbítás a Palo Alto Networks-berendezés az Azure Sentinel való konfigurálására.

    1. Állítsa be a **Syslog server formátum** való **BSD**.
    1. Mindenképpen állítsa be a **létesítmény szám** ugyanarra az értékre állítsa be a Syslog-ügynök.

> [!NOTE]
> A PDF-fájlból a másolási és beillesztési műveletek előfordulhat, hogy módosítsa a szöveget, és véletlen karakterek beszúrása. Ennek elkerülése érdekében másolja be a szöveget egy szövegszerkesztőben, és távolítsa el a karaktereket meghiúsulhat a napló formátuma előtt illessze be azt, ahogy ebben a példában látható.
 
  ![CEF szöveg másolása a probléma](./media/connect-cef/paloalto-text-prob1.png)

6. A Palo Alto Networks-események Log Analytics használja a megfelelő sémát, keresse meg **CommonSecurityLog**.

## <a name="step-3-validate-connectivity"></a>3. lépés: Kapcsolat ellenőrzése

Upwards of mindaddig, amíg megjelennek a Log Analytics indítása a naplók 20 percig is eltarthat. 

1. Ellenőrizze, hogy a megfelelő funkcióval. A létesítmény meg kell egyeznie a berendezés és az Azure-Sentinel. Az Azure-Sentinel használja, és módosítsa a fájl melyik létesítmény fájlban `security-config-omsagent.conf`. 

2. Győződjön meg arról, hogy a naplók a Syslog-ügynök a megfelelő porthoz van első. Futtassa ezt a parancsot a Syslog-ügynök gépen: `tcpdump -A -ni any  port 514 -vv` Ez a parancs megjeleníti a naplókat, amely az eszközről a Syslog-géphez streameli. Győződjön meg arról, hogy a forrás-berendezés a megfelelő port és a megfelelő létesítmény naplók érkeznek.

3. Győződjön meg arról, hogy megfelelnek-e a naplókat küld a [RFC 5424](https://tools.ietf.org/html/rfc542).

4. A Syslog-ügynököt futtató számítógépen ellenőrizze, hogy ezeket a portokat 514-es, a 25226 nyílt forráskódú és figyel-e, a parancs `netstat -a -n:`. Ez a parancs használatával kapcsolatos további információk: [netstat(8) – Linux man lap](https://linux.die.netman/8/netstat). Ha megfelelően figyel, láthatja a:

   ![Az Azure Sentinel-portok](./media/connect-cef/ports.png) 

5. Ellenőrizze a démont értéke 514-es, porton figyelnek, amelyen a naplók küldése folyamatban van.
    - Rsyslog:<br>Győződjön meg arról, hogy a fájl `/etc/rsyslog.conf` ezt a konfigurációt tartalmazza:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      További információkért lásd: [imudp: UDP Syslog bemeneti modul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) és [imtcp: TCP, Syslog bemeneti modul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - A syslog-ng esetén:<br>Győződjön meg arról, hogy a fájl `/etc/syslog-ng/syslog-ng.conf` ezt a konfigurációt tartalmazza:

           # source s_network {
            network( transport(UDP) port(514));
             };
     További információkért lásd: [imudp: UDP Syslog bemeneti modul] (további információkért lásd: a [syslog-ng az adatforrás-Edition 3.16 nyílt - felügyeleti útmutató](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Ellenőrizze, hogy nincs-e a Syslog-démont és az ügynök közötti kommunikációhoz. Futtassa ezt a parancsot a Syslog-ügynök gépen: `tcpdump -A -ni any  port 25226 -vv` Ez a parancs megjeleníti a naplókat, amely az eszközről a Syslog-géphez streameli. Győződjön meg arról, hogy a naplók is fogadja az ügynökön.

6. Ha sikeres eredményt adott mindkét azokat a parancsokat, ellenőrizze a Log Analyticsben, hogy tekintse meg, ha a naplók érkezési. Ezek a készülékek a streamelt minden eseményt a Log Analytics a nyers formában jelennek meg `CommonSecurityLog` típusa.

7. Ellenőrizze, hogy vannak-e hibák, vagy ha a naplók nem érkező, nézze meg `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Allocated a napló formátuma eltérés hibák, Ugrás `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` , és keresse meg a fájlban `security_events.conf`, és győződjön meg arról, hogy a naplók a regex formátumban jelenik meg ez a fájl megfelel-e.

8. Győződjön meg arról, hogy a Syslog-üzenet alapértelmezett mérete legfeljebb 2048 bájtok száma (2KB). Ha naplók túl hosszú, frissítse a security_events.conf ezzel a paranccsal: `message_length_limit 4096`








## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni a Palo Alto Networks-berendezések Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

