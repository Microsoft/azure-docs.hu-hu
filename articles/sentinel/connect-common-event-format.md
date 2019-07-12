---
title: Az Azure-on Előzetesben Sentinel-CEF-adatok csatlakoztatása |} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a CEF-adatok az Azure-Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: rkarlin
ms.openlocfilehash: f9435c4b7649e9b97c209fb554f62228cde95034
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612391"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>A Common Event formattal külső megoldás csatlakoztatása

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure-Sentinel egy külső megoldás, amely lehetővé teszi, hogy mentse a naplófájlokat a Syslog-kapcsolódhat. Ha a készülék lehetővé teszi, hogy mentse naplókat, rendszernapló Common Event Format (CEF), Azure Sentinel-integráció lehetővé teszi elemzések és lekérdezések könnyedén futtathat az adatok között.

> [!NOTE] 
> Adatok tárolódnak a munkaterületet, amely futtatja az Azure-Sentinel földrajzi helye.

## <a name="how-it-works"></a>Működés

Az Azure Sentinel- és a CEF-berendezés közötti kapcsolatot három lépésben történik:

1. A készüléket kell beállítani ezeket az értékeket úgy, hogy a berendezés küld a szükséges naplók az Azure-Sentinel-Syslog-ügynök a szükséges formátumban. Ezek a paraméterek módosíthatja a berendezés mindaddig, amíg is módosítható az a Syslog-démont, az Azure Sentinel-ügynökön.
    - Protokoll = UDP
    - Port = 514-es
    - Létesítmény = helyi – 4
    - Formátum = CEF
2. A Syslog-ügynök gyűjti az adatokat, és biztonságosan küld a Log Analyticsbe, ahol, elemzi és bővített.
3. Az ügynök a az adatokat a Log Analytics-munkaterületen tárolja, így szükség esetén analytics, a korrelációs szabályok és az irányítópultok használatával lekérdezhetők legyenek.

> [!NOTE]
> Az ügynök képes gyűjteni a több forrásból, de proxy dedikált gépre kell telepíteni.

## <a name="step-1-connect-to-your-cef-appliance-via-dedicated-azure-vm"></a>1\. lépés: A CEF-berendezés dedikált Azure-beli Virtuálisgép-n keresztül kapcsolódni

Üzembe kell helyeznie egy dedikált gépen Linux ügynök (virtuális gép vagy a helyszínen) a készüléket és a Sentinel-Azure közötti kommunikáció támogatásához. Az ügynök automatikusan vagy manuálisan telepítheti. Automatikus központi telepítési Resource Manager-sablonok alapján, és csak akkor, ha a dedikált Linux-gép egy új virtuális Gépet hoz létre az Azure-ban nem használható.

 ![Az Azure-ban CEF](./media/connect-cef/cef-syslog-azure.png)

Azt is megteheti telepítheti az ügynököt manuálisan a meglévő Azure virtuális gép, egy virtuális gépen egy másik felhőben vagy a helyszíni gépen. 

 ![A helyszíni CEF](./media/connect-cef/cef-syslog-onprem.png)

### <a name="deploy-the-agent-in-azure"></a>Telepítse az ügynököt az Azure-ban


1. Az Azure-Sentinel-portálon kattintson a **adatösszekötők** válassza ki a készüléket. 

1. A **Linux Syslog-ügynök konfigurációjának**:
   - Válasszon **automatikus központi telepítési** szeretné-e a fent leírtak szerint, hozzon létre egy új gépet, amely előre telepítve van az Azure Sentinel-ügynökkel, és tartalmazza a konfiguráció szükséges. Válassza ki **automatikus központi telepítési** kattintson **automatikus ügynöktelepítés**. Ekkor megjelenik a Vásárlás lapján dedikált Linux virtuális gép, amely automatikusan csatlakozik a munkaterülethez. A virtuális gép egy **D2s v3-as standard (2 vcpu-k, 8 GB memória)** és a egy nyilvános IP-címmel rendelkezik.
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
            
            1. A syslog démon újraindításához `sudo service rsyslog restart`<br> További információ: a [rsyslog dokumentációja](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
           
          - Ha a syslog-ng választotta:

              1. Mondja el a Syslog démon a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök használatával a 25226-os- és a létesítmény local_4 figyelni. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ahol {0} le kell cserélni a munkaterület GUID azonosítója.

              3. A syslog démon újraindításához `sudo service syslog-ng restart` <br>További információkért lásd: a [syslog-ng dokumentációja](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)
      2. Indítsa újra a Syslog-ügynök a következő paranccsal: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Győződjön meg arról, hogy nincsenek hibák az ügynöknaplóban Ez a parancs futtatásával: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

 A megfelelő sémát használ a Log Analytics a CEF-események, keresse meg `CommonSecurityLog`.


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
  
 A megfelelő sémát használ a Log Analytics a CEF-események, keresse meg `CommonSecurityLog`.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>2\. lépés: Common Event Format (CEF) naplókat továbbítani a Syslog-ügynök

Állítsa be a Syslog-üzeneteket küldjön a CEF formátumot a Syslog-ügynök biztonsági megoldását. Ellenőrizze, hogy az ügynök konfigurációs használja ugyanazokat a paramétereket, amelyek jelennek meg. Általában az alábbiak:

- 514-es port
- Létesítmény local4

## <a name="step-3-validate-connectivity"></a>3\. lépés: Kapcsolat ellenőrzése

Upwards of mindaddig, amíg megjelennek a Log Analytics indítása a naplók 20 percig is eltarthat. 

1. Ellenőrizze, hogy a megfelelő funkcióval. A létesítmény meg kell egyeznie a berendezés és az Azure-Sentinel. Az Azure-Sentinel használja, és módosítsa a fájl melyik létesítmény fájlban `security-config-omsagent.conf`. 

2. Győződjön meg arról, hogy a naplók a Syslog-ügynök a megfelelő porthoz van első. Futtassa ezt a parancsot a Syslog-ügynök gépen: `tcpdump -A -ni any  port 514 -vv` Ez a parancs megjeleníti a naplókat, amely az eszközről a Syslog-géphez streameli. Győződjön meg arról, hogy a forrás-berendezés a megfelelő port és a megfelelő létesítmény naplók érkeznek.

3. Győződjön meg arról, hogy megfelelnek-e a naplókat küld a [RFC 5424](https://tools.ietf.org/html/rfc542).

4. A Syslog-ügynököt futtató számítógépen ellenőrizze, hogy ezeket a portokat 514-es, a 25226 nyílt forráskódú és figyel-e, a parancs `netstat -a -n:`. Ez a parancs használatával kapcsolatos további információk: [netstat(8) – Linux man lap](https://linux.die.net/man/8/netstat). Ha megfelelően figyel, láthatja a:

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
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni a CEF-berendezések Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

