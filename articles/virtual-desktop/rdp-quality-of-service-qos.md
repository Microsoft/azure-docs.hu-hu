---
title: Szolgáltatásminőség (QoS) implementálása a Windows rendszerű virtuális asztali gépeken (előzetes verzió)
titleSuffix: Azure
description: A QoS (előzetes verzió) beállítása a Windows rendszerű virtuális asztali gépekhez.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94639374"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>Szolgáltatásminőség (QoS) implementálása a Windows rendszerű virtuális asztali gépeken (előzetes verzió)

> [!IMPORTANT]
> A szolgáltatásminőség (QoS) házirend-támogatása a Windows Virtual Desktop szolgáltatáshoz jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az [RDP-Shortpath](./shortpath.md) közvetlen UDP-alapú átvitelt biztosít távoli asztal ügyfél és a munkamenet-állomás között. Az RDP-Shortpath lehetővé teszi a szolgáltatásminőség-(QoS-) házirendek konfigurálását az RDP-értékekhez.
A Windows virtuális asztal QoS szolgáltatása lehetővé teszi az olyan valós idejű RDP-forgalom használatát, amely érzékeny a hálózati késésekre a kevésbé érzékeny forgalom előtt. Ilyen kevésbé érzékeny forgalom például egy új alkalmazás letöltése, ahol a letöltéshez szükséges extra másodpercek nem nagy mennyiségű. A QoS Windows Csoportházirend objektumokat használ a valós idejű adatfolyamokban lévő összes csomag azonosításához és megjelöléséhez, és segít a hálózatnak a sávszélesség dedikált részének biztosítását az RDP-forgalom számára.

Ha az ebben a cikkben ismertetett problémák valamelyikével rendelkező felhasználók nagy csoportjait támogatja, valószínűleg a QoS megvalósítására van szükség. Előfordulhat, hogy néhány felhasználóval rendelkező kisvállalkozás nem igényel QoS-t, de hasznos lehet még ott is.

A QoS valamilyen formája nélkül a következő problémákkal találkozhat:

* Jitter – a különböző díjszabásba érkező RDP-csomagok, amelyek vizuális és hanghibákhoz vezethetnek
* Csomagok elvesztése – eldobott csomagok, amelyek további időt igénylő újraküldést eredményeznek
* Késleltetett átállási idő (RTT) – az RDP-csomagok hosszú időt vesznek igénybe a célhelyek eléréséhez, ami észrevehető késést eredményezhet a távoli alkalmazás bemenete és reakciója között.

Ezeknek a problémáknak a legkevésbé bonyolult módja az adatkapcsolatok méretének, valamint belsőleg és az internetre való kiterjesztésének megnövekedése. Mivel ez gyakran megtiltható, a QoS lehetővé teszi, hogy kezelje a sávszélesség hatékonyabb hozzáadása helyett használt erőforrásokat. A minőségi problémák megoldásához javasoljuk, hogy először a QoS-t használja, majd adja hozzá a sávszélességet, ha szükséges.

Ahhoz, hogy a QoS érvényes legyen, konzisztens QoS-beállításokat kell alkalmaznia a szervezeten belül. Az elérési út azon része, amely nem támogatja a QoS-prioritásokat, csökkentheti a minőségi RDP-munkamenetet.

## <a name="introduction-to-qos-queues"></a>A QoS-várólisták bemutatása

A QoS biztosításához a hálózati eszközöknek képesnek kell lenniük a forgalom besorolására, és meg kell tudniuk különböztetni az RDP-t más hálózati forgalomtól.

Amikor a hálózati forgalom belép egy útválasztóba, a rendszer a forgalmat egy várólistába helyezi. Ha nincs konfigurálva QoS-házirend, akkor csak egy üzenetsor létezik, és az összes adat a kezdeti, az elsővel azonos prioritással lesz kezelve. Ez azt jelenti, hogy az RDP-forgalom elakadhat a forgalom mögött, ahol néhány további ezredmásodperces késleltetés nem lenne probléma.

A QoS implementálása során több várólistát is definiálhat a különböző torlódás-kezelési funkciók egyikével, például a Cisco prioritási sorba állításával és az [osztály-alapú súlyozott tisztességes Üzenetsor-kezeléssel (CBWFQ)](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) és a torlódások elkerülésével kapcsolatos funkciókkal, például a [súlyozott véletlenszerű korai észleléssel (WRED)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html).

Egy egyszerű analógia az, hogy a QoS virtuális "telekocsi sávokat" hoz létre az adathálózatban. Így bizonyos típusú adattípusok soha nem vagy ritkán fordulnak elő késéssel. Miután létrehozta ezeket a sávokat, módosíthatja a relatív méretet, és sokkal hatékonyabban kezelheti a kapcsolati sávszélességet, miközben továbbra is üzleti szintű tapasztalatokat helyez el a szervezet felhasználói számára.

## <a name="qos-implementation-checklist"></a>QoS-megvalósítási ellenőrzőlista

Magas szinten végezze el a következőket a QoS megvalósításához:

1. [Győződjön meg arról, hogy a hálózat készen áll](#make-sure-your-network-is-ready)
2. [Győződjön meg arról, hogy az RDP-Shortpath engedélyezve van](./shortpath.md) – a QoS-házirendek nem támogatottak a fordított csatlakozási átvitelhez
3. [DSCP-jelölők behelyezésének megvalósítása](#insert-dscp-markers) a munkamenet-gazdagépeken

A QoS megvalósítására való felkészülés során tartsa szem előtt a következő irányelveket:

* A munkamenet-gazdagép legrövidebb elérési útja a legjobb
* A (többek között a proxyk vagy a csomagok ellenőrzési eszközei) közötti akadályok nem ajánlottak

## <a name="make-sure-your-network-is-ready"></a>Győződjön meg arról, hogy a hálózat készen áll

Ha a QoS megvalósítását tervezi, akkor már meg kell határoznia a sávszélességre vonatkozó követelményeket és az egyéb [hálózati követelményeket](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context).
  
A hálózati forgalom a hálózaton keresztül jelentősen befolyásolhatja a média minőségét. A sávszélesség hiánya a teljesítmény romlását és a gyenge felhasználói élményt eredményezi. A Windows rendszerű virtuális asztalok bevezetésének és használatának növekedésével a [log Analytics](./diagnostics-log-analytics.md) segítségével azonosíthatja a problémákat, majd a QoS és a szelektív sávszélesség hozzáadásával végezheti el a módosításokat.

### <a name="vpn-considerations"></a>VPN-megfontolások

A QoS csak az elvárt módon működik, ha az ügyfelek és a munkamenet-gazdagépek közötti összes kapcsolaton implementálva van. Ha a QoS-t egy belső hálózaton használja, és a felhasználó távoli helyről jelentkezik be, akkor csak a belső, felügyelt hálózaton belül rangsorolhatja. Bár a távoli telephelyek egy virtuális magánhálózat (VPN) megvalósításával tudnak felügyelt kapcsolatokat fogadni, a VPN a csomagok túlterheltségét és a valós idejű forgalom késleltetését eredményezi.

A kontinenseket átölelő, felügyelt hivatkozásokkal rendelkező globális szervezetben erősen ajánlott a QoS, mert a kapcsolatok sávszélessége korlátozott a LAN-hoz képest.

## <a name="insert-dscp-markers"></a>DSCP-jelölők beszúrása

A QoS-t egy Csoportházirend objektummal (GPO) implementálhatja a munkamenet-gazdagépek számára, hogy olyan DSCP-jelölőt helyezzen be az IP-csomagok fejlécében, amely az adott típusú forgalom azonosítására szolgál. Az útválasztók és más hálózati eszközök úgy konfigurálhatók, hogy felismerjék ezeket a jelöléseket, és egy külön, magasabb prioritású várólistán helyezze el a forgalmat.

A DSCP-jelöléseket olyan postai bélyegekhez hasonlíthatja össze, amelyek jelzik, hogy a postai feldolgozók milyen sürgősen teszik közzé a kézbesítést, és hogyan érdemes a legjobban rendezni a gyors kézbesítést. Ha úgy konfigurálta a hálózatot, hogy elsőbbséget biztosítson az RDP-adatfolyamoknak, az elveszett csomagok és a késői csomagok jelentősen csökkennek.

Ha az összes hálózati eszköz ugyanazt a besorolást, jelölést és prioritást használja, lehetséges a késések, az eldobott csomagok és a vibrálás csökkentése vagy megszüntetése. Az RDP szempontjából az alapvető konfigurációs lépés a csomagok besorolása és jelölése. Ahhoz azonban, hogy a végpontok közötti QoS sikeres legyen, az RDP-konfigurációt is körültekintően kell összehangolni a mögöttes hálózati konfigurációval.
A DSCP érték azt jelzi, hogy egy megfelelően konfigurált hálózat milyen prioritást biztosít a csomagok vagy a streamek számára.

Javasoljuk, hogy a 46-as DSCP-értéket használja, amely leképezi a **gyorsított továbbítási (EF) DSCP-** osztályt.

### <a name="implement-qos-on-session-host-using-group-policy"></a>QoS implementálása a munkamenet-gazdagépen a Csoportházirend használatával

A házirend alapú szolgáltatásminőség (QoS) a Csoportházirendon belül az előre definiált DSCP érték beállításához használható.

Ha QoS-házirendet szeretne létrehozni a tartományhoz csatlakoztatott munkamenet-gazdagépekhez, először jelentkezzen be egy olyan számítógépre, amelyen Csoportházirend a felügyelet telepítve van. Nyissa meg Csoportházirend-felügyeletet (válassza a Start, a felügyeleti eszközök, majd a Csoportházirend felügyelet lehetőséget), majd hajtsa végre a következő lépéseket:

1. A Csoportházirend-kezelés területen keresse meg azt a tárolót, amelyben létre szeretné hozni az új házirendet. Ha például az összes munkamenetben lévő számítógép egy **"munkamenet-gazdagépek"** nevű szervezeti egységben található, az új szabályzatot a munkamenet-GAZDAGÉPEK szervezeti egységében kell létrehozni.

2. Kattintson a jobb gombbal a megfelelő tárolóra, majd válassza a **csoportházirend-objektum létrehozása ebben a tartományban, és hivatkozás itt** lehetőséget.

3. Az **új csoportházirend-objektum** párbeszédpanelen írja be az új csoportházirend objektum nevét a **név** mezőbe, majd kattintson az **OK gombra**.

4. Kattintson a jobb gombbal az újonnan létrehozott házirendre, majd válassza a **Szerkesztés** lehetőséget.

5. A Csoportházirend-felügyeleti szerkesztő bontsa ki a **Számítógép konfigurációja**, majd a **Windows beállításai** csomópontot, kattintson a jobb gombbal a **házirend alapú QoS** elemre, majd válassza az **új házirend létrehozása** parancsot.

6. A **házirend alapú QoS** párbeszédpanel Megnyitás lapján adja meg az új szabályzat nevét a **név** mezőben. Válassza a **DSCP érték megadása** lehetőséget, és állítsa az értéket **46**-re. Hagyja **meg a kimenő sávszélesség-szabályozási arány** kijelölését, majd válassza a **tovább** lehetőséget.

7. A következő lapon jelölje ki a **csak az ilyen végrehajtható névvel rendelkező alkalmazásokat** , és adja meg a **svchost.exe** nevet, majd kattintson a **tovább** gombra. Ez a beállítás arra utasítja a szabályzatot, hogy csak a Távoli asztal szolgáltatásból származó egyező forgalmat rangsorolja.

8. A harmadik lapon győződjön meg arról, hogy a **forrás IP-címe** és **a cél IP-címe** is ki van választva, majd kattintson a **tovább** gombra. Ez a két beállítás biztosítja a csomagok felügyeletét, függetlenül attól, hogy melyik számítógépről (IP-címről) küldték a csomagokat, és hogy melyik számítógép (IP-cím) kapja meg a csomagokat.

9. A négy oldalon válassza az **UDP** lehetőséget a **válassza ki** azt a protokollt, amelyre a QoS-házirend vonatkozik a legördülő listára.

10. A címsor alatt **adja meg a forrásport számát**, válasszon **a forrás port vagy tartomány közül**. A kísérő szövegmezőbe írja be a **3390** karakterláncot. Válassza a **Befejezés** gombot.

Az új létrehozott házirendek nem lépnek érvénybe, amíg a Csoportházirend frissült a munkamenet-gazdagép számítógépein. Bár a Csoportházirend rendszeresen frissíti a saját példányát, a következő lépésekkel kényszerítheti az azonnali frissítést:

1. Minden olyan munkamenet-gazdagépen, amelyre frissíteni kívánja Csoportházirend, nyisson meg egy parancssort rendszergazdaként (*Futtatás rendszergazdaként*).

1. A parancssorba írja be a következőt:

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>QoS implementálása a munkamenet-gazdagépen a PowerShell használatával

Az RDP-Shortpath az alábbi PowerShell-parancsmag használatával állíthatja be:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Szolgáltatásminőség (QoS) házirend](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>Következő lépések

* A Windows rendszerű virtuális asztali sávszélességre vonatkozó követelmények megismeréséhez tekintse meg [a Windows rendszerű virtuális asztalok RDP protokoll (RDP) sávszélesség-követelményeit](rdp-bandwidth.md)ismertető témakört.
* A Windows rendszerű virtuális asztali hálózati kapcsolat megismeréséhez tekintse meg a [Windows rendszerű virtuális asztali hálózati kapcsolat ismertetése](network-connectivity.md)című témakört.
