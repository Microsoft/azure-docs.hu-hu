---
title: Syslog-adatbázis összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Csatlakoztasson minden olyan gépet vagy készüléket, amely támogatja a syslog-t az Azure Sentinelhez a készülék és az Azure Sentinel közötti linuxos gépen lévő ügynök használatával.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: f249a95551916311fab51ebef72b55d9a4343c0b
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530518"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Adatok gyűjtése Linux-alapú forrásokból a syslog használatával

A Linux-alapú, syslog-supported Machines vagy készülékekről származó eseményeket az Azure Sentinelbe is továbbíthatja a Linux rendszerhez készült Log Analytics Agent (korábbi nevén OMS-ügynök) használatával. Ezt megteheti bármely olyan gépen, amely lehetővé teszi, hogy közvetlenül a gépre telepítse a Log Analytics-ügynököt. A gép natív syslog démona a megadott típusok helyi eseményeit gyűjti, és helyileg továbbítja azokat az ügynöknek, amely a Log Analytics-munkaterületre továbbítja azokat.

> [!NOTE]
> - Ha a készülék a syslog-en keresztül támogatja a **Common Event Format (CEF) formátumot**, a rendszer egy átfogóbb adatkészletet gyűjt, és az adatokat a gyűjteményben elemzi. Válassza ezt a lehetőséget, és kövesse a [külső megoldás CEF használatával történő összekapcsolásával](connect-common-event-format.md)kapcsolatos utasításokat.
>
> - Log Analytics támogatja a **rsyslog** vagy **syslog-ng** démonok által küldött üzenetek gyűjteményét, ahol a rsyslog az alapértelmezett. Az alapértelmezett syslog démon a Red Hat Enterprise Linux (RHEL), a CentOS és a Oracle Linux Version (**sysklog**) 5. verziójában nem támogatott a syslog-események gyűjtéséhez. A rendszernapló-adatok ezen disztribúciók ezen verziójából való összegyűjtéséhez a rsyslog démont telepíteni és konfigurálni kell a sysklog lecserélése érdekében.

## <a name="how-it-works"></a>Működés

A **syslog** egy olyan eseménynaplózási protokoll, amely közös a Linux rendszerben. Ha a **Linux** rendszerhez készült log Analytics-ügynök telepítve van a virtuális gépre vagy készülékre, a telepítési rutin konfigurálja a helyi syslog démont, hogy továbbítsa az üzeneteket az ügynöknek az 25224-as TCP-porton. Az ügynök ezután a HTTPS-kapcsolaton keresztül elküldi az üzenetet a Log Analytics munkaterületnek, ahol a rendszer az **Azure Sentinel > naplóiban naplózza** a syslog táblában az Eseménynapló-bejegyzésbe.

További információ: [syslog-adatforrások Azure monitorban](../azure-monitor/platform/data-sources-syslog.md).

## <a name="configure-syslog-collection"></a>Syslog-gyűjtemény konfigurálása

### <a name="configure-your-linux-machine-or-appliance"></a>A linuxos gép vagy berendezés konfigurálása

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd válassza ki a **syslog** -összekötőt.

1. A **syslog** panelen válassza az **összekötő lap megnyitása** lehetőséget.

1. Telepítse a Linux-ügynököt. **Az ügynök telepítési helyének kiválasztása:**
    
    **Azure Linux rendszerű virtuális gépek esetén:**
      
    1. Válassza **az ügynök telepítése az Azure Linux rendszerű virtuális gépen** lehetőséget.
    
    1. Kattintson a **letöltés & az Azure-beli linuxos virtuális gépekhez készült telepítési ügynök >** hivatkozásra. 
    
    1. A **virtuális gépek** panelen kattintson arra a virtuális gépre, amelyre telepíteni szeretné az ügynököt, majd kattintson a **Kapcsolódás** elemre. Ismételje meg ezt a lépést minden olyan virtuális gép esetében, amelyhez csatlakozni szeretne.
    
    **Bármely más linuxos gépen:**

    1. Válassza **az ügynök telepítése nem Azure-beli Linux-gépen** lehetőséget.

    1. Kattintson a **letöltés & az ügynök telepítése nem Azure-beli Linux-gépekhez >** hivatkozásra. 

    1. Az **ügynökök kezelése** panelen kattintson a Linux- **kiszolgálók** lapra, majd másolja a **letöltéshez és** a Linux-ügynök bevezetéséhez szükséges parancsot, és futtassa azt a Linux rendszerű gépen. 
    
   > [!NOTE]
   > Győződjön meg arról, hogy a szervezet biztonsági házirendjének megfelelően konfigurálja a számítógépek biztonsági beállításait. Beállíthatja például, hogy a hálózati beállítások összhangban legyenek a szervezete hálózati biztonsági házirendjével, és a démon portjait és protokollait a biztonsági követelményekkel összhangba lehessen állítani.

### <a name="configure-the-log-analytics-agent"></a>A Log Analytics-ügynök konfigurálása

1. A syslog-összekötő paneljének alján kattintson a **munkaterület-ügynökök megnyitása konfigurációs >** hivatkozásra.

1. Az **ügynökök konfigurálása** panelen válassza a **syslog** fület. Ezután adja hozzá az összekötőhöz tartozó létesítményeket a gyűjtéshez. Válassza a **létesítmény hozzáadása** lehetőséget, és válasszon a létesítmények legördülő listájából.
    
    - Adja hozzá azokat a létesítményeket, amelyeket a syslog-berendezés tartalmaz a naplójának fejlécében. 
    
    - Ha rendellenes SSH bejelentkezési észlelést szeretne használni a gyűjtött adatokkal, adja hozzá az **Auth** és a **authpriv**. További részletekért tekintse meg a [következő szakaszt](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) .

1. Ha hozzáadta az összes figyelni kívánt szolgáltatást, ellenőrizze, hogy az összes kívánt súlyossági szint be van-e jelölve.

1. Kattintson az **Alkalmaz** gombra. 

1. Győződjön meg arról, hogy a virtuális gépen vagy berendezésen a megadott létesítményeket küldi el.

1. A syslog-naplófájlok **naplófájljainak** lekérdezéséhez írja be a `Syslog` következőt: a lekérdezési ablak.

1. A következő témakörben ismertetett lekérdezési paramétereket használhatja a [függvények használatával Azure monitor napló lekérdezésekben](../azure-monitor/log-query/functions.md) a syslog-üzenetek elemzéséhez. Ezután mentheti a lekérdezést új Log Analytics függvényként, és új adattípusként használhatja azt.

> [!NOTE]
> **Ugyanazzal a géppel az egyszerű syslog *és* a CEF üzenetek továbbítása**
>
> A meglévő [CEF-naplózási továbbító géppel](connect-cef-agent.md) is felhasználhatja a naplók egyszerű syslog-forrásokból való összegyűjtését és továbbítását. Azonban az alábbi lépéseket kell elvégeznie, hogy elkerülje az események mindkét formátumban való küldését az Azure Sentinel számára, mivel ez az események ismétlődését eredményezi.
>
>    Ha már beállította az [adatgyűjtést a CEF-forrásaiból](connect-common-event-format.md), és a fentiek szerint konfigurálta a log Analytics-ügynököt:
>
> 1. Minden olyan gépen, amely CEF formátumú naplókat küld, szerkesztenie kell a syslog konfigurációs fájlját, hogy eltávolítsa a CEF üzenetek küldésére használt létesítményeket. Így a CEF-ben eljuttatott létesítmények nem lesznek elküldve a syslog-ben. Ennek módjával kapcsolatos részletes útmutatásért lásd: [a syslog konfigurálása Linux-ügynökön](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. A következő parancs futtatásával le kell tiltania az ügynök szinkronizálását az Azure Sentinel syslog-konfigurációjával. Ez biztosítja, hogy az előző lépésben végrehajtott konfigurációs módosítás ne legyen felülírva.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>A syslog-összekötő konfigurálása rendellenes SSH-bejelentkezés észleléséhez

> [!IMPORTANT]
> A rendellenes SSH bejelentkezési észlelés jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Sentinel gépi tanulást (ML) alkalmazhat a syslog-adatokra a rendellenes Secure Shell-(SSH-) bejelentkezési tevékenységek azonosításához. A forgatókönyvek a következők:

- Lehetetlen utazás – ha két sikeres bejelentkezési esemény következik be két olyan helyről, amely nem érhető el a két bejelentkezési esemény időkeretén belül.
- Váratlan hely – a sikeres bejelentkezési esemény előfordulási helye gyanús. Például a hely nem látható a közelmúltban.
 
Az észleléshez a syslog-adatösszekötő adott konfigurációja szükséges: 

1. A fenti [log Analytics ügynök konfigurálása](#configure-the-log-analytics-agent) szakasz 2. lépésében ellenőrizze, hogy az **Auth** és a **authpriv** is ki van-e választva a figyeléshez, és hogy az összes csomópont ki van-e választva. 

2. A syslog-adatok gyűjtéséhez elegendő idő szükséges. Ezután navigáljon az **Azure Sentinel-logs** elemhez, és másolja és illessze be a következő lekérdezést:
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    Szükség esetén módosítsa az **időtartományt** , majd válassza a **Futtatás** lehetőséget.
    
    Ha az eredményül kapott darabszám nulla, erősítse meg az összekötő konfigurációját, és hogy a figyelt számítógépek sikeres bejelentkezési tevékenységet hajtanak végre a lekérdezéshez megadott időszakra vonatkozóan.
    
    Ha az eredményül kapott darabszám nagyobb nullánál, a syslog-adatok alkalmasak a rendellenes SSH-bejelentkezések észlelésére. Ezt az észlelést az **Analytics**-  >   **szabály sablonjai**  >  **(előzetes verzió) rendellenes SSH-bejelentkezések észlelésével** engedélyezheti.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan kapcsolódhat a syslog helyszíni készülékekhez az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

