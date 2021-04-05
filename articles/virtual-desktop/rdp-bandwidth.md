---
title: RDP protokoll sávszélességre vonatkozó követelmények Windows rendszerű virtuális asztali környezet – Azure
titleSuffix: Azure
description: A Windows rendszerű virtuális asztal RDP-sávszélességre vonatkozó követelményeinek ismertetése.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: c9ef72241ac62efd8555de59bb52949321364035
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96325179"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a>RDP protokoll (RDP) sávszélességre vonatkozó követelmények

A RDP protokoll (RDP) egy kifinomult technológia, amely különböző technikákat használ a kiszolgáló távoli grafikájának az ügyfél eszközre történő kézbesítésének tökéletesítéséhez. A használati esettől, a számítási erőforrások és a hálózati sávszélességtől függően az RDP dinamikusan módosítja a különböző paramétereket, hogy a lehető legjobb felhasználói élményt nyújtsa.

RDP protokoll a több dinamikus virtuális csatornát (DVCs) egyetlen adatcsatornába, amely különböző hálózati átviteleken keresztül lett elküldve. Külön DVCs a távoli grafikák, a bevitel, az eszközök átirányítása, a nyomtatás és mások számára. A WVD-partnerek a KONKURENS felületet használó bővítményeket is végrehajthatják.
Az RDP-kapcsolaton keresztül továbbított adatok mennyisége a felhasználói tevékenységtől függ. Előfordulhat például, hogy egy felhasználó az alapszintű szöveges tartalommal dolgozik a legtöbb munkamenetben, és minimális sávszélességet használ, de ezután kinyomtatja a 200-oldal dokumentumot a helyi nyomtatóba. Ez a nyomtatási feladatok jelentős mennyiségű hálózati sávszélességet fognak használni.

Távoli munkamenet használata esetén a hálózat elérhető sávszélessége jelentősen befolyásolja a felhasználói élmény minőségét. A különböző alkalmazások és megjelenítési felbontások eltérő hálózati konfigurációt igényelnek, ezért elengedhetetlen, hogy a hálózati konfiguráció megfeleljen az igényeinek.

## <a name="estimating-bandwidth-utilization"></a>A sávszélesség kihasználtságának becslése

Az RDP különböző tömörítési algoritmusokat használ a különböző típusú adattípusokhoz. Az alábbi táblázat az adatátvitelek becslését mutatja be:

| Adattípus | Irány | A becslés módja |
|---|---|---|
| Távoli grafikák | Munkamenet-gazdagép az ügyfélhez | [Tekintse meg a részletes útmutatást](#estimating-bandwidth-used-by-remote-graphics) |
| Szívdobbanás | Mindkét irányban | ~ 20 bájt 5 másodpercenként  |
| Bevitel | Ügyfél és a munkamenet-állomás között | Az adatok mennyisége a felhasználói tevékenységen alapul, a legtöbb műveletnél kevesebb mint 100 bájt.  |
| Fájlátvitel | Mindkét irányban | A fájlok átvitele tömeges tömörítést használ. A. zip tömörítés használata a közelítéshez |
| Nyomtatás | Munkamenet-gazdagép az ügyfélhez | A nyomtatási feladatok átvitele az illesztőprogramtól függ, és tömeges tömörítést használ. a közelítéshez használja a. zip tömörítést. |

Más forgatókönyvek esetén a sávszélességre vonatkozó követelmények változhatnak attól függően, hogy miként használják őket, például:

* Hang-vagy videokonferencia
* Valós idejű kommunikáció
* Streaming 4K-videó

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>Távoli grafikák által használt sávszélesség becslése

Nehéz megjósolni a távoli asztal sávszélesség-használatát. A felhasználói tevékenységek a távoli asztali forgalom nagy részét generálták. Minden felhasználó egyedi, és a munkamintákban mutatkozó különbségek jelentősen megváltoztathatják a hálózati használatot.

A sávszélesség-követelmények megismerésének legjobb módja a valós felhasználói kapcsolatok figyelése. A figyelés a beépített teljesítményszámlálók vagy a hálózati berendezések által végezhető el.

Sok esetben azonban megbecsülheti a hálózati kihasználtságot a RDP protokoll működésének és a felhasználók munkahelyi mintázatának elemzésével.

A távoli protokoll biztosítja a távoli kiszolgáló által létrehozott grafikus elemek helyi figyelőn való megjelenítését. Pontosabban biztosítja az asztali bitképet, amely teljes egészében a-kiszolgálón található.
Az asztali bitképek elküldésekor az első módszerhez hasonlóan egyszerű feladatnak tűnik, jelentős mennyiségű erőforrást igényel. A tömörítetlen formában található 1080p asztali rendszerképek például körülbelül 8 MB méretűek. Ha ezt a rendszerképet egy 30 Hz-es, szerény képernyő-frissítési sebességgel rendelkező, helyileg csatlakoztatott monitoron jeleníti meg, a sávszélesség körülbelül 237 MB/s.

A hálózaton keresztül továbbított adatmennyiség csökkentése érdekében az RDP több módszer kombinációját használja, beleértve a

* Frame rate-optimalizálások
* Képernyő tartalmának besorolása
* Content-specifikus kodekek
* Progresszív képkódolás
* Ügyféloldali gyorsítótárazás

A távoli grafikák alaposabb megismeréséhez vegye figyelembe a következőket:

* Minél gazdagabb a grafika, annál nagyobb sávszélességet fog igénybe
  * A szöveg, az ablak felhasználói felületének elemei és a folytonos színterületek kevesebb sávszélességet vesznek igénybe, mint bármi más.
  * A természetes képek jelentik a legjelentősebb közreműködőket a sávszélesség-használathoz. Az ügyféloldali gyorsítótárazás azonban segít a csökkentésében.
* A képernyő csak a megváltozott részeit továbbítja. Ha a képernyőn nincsenek látható frissítések, a rendszer nem továbbítja a frissítéseket.
* A videolejátszás és más, magas keretre kiterjedő tartalom lényegében egy képdiavetítés. Az RDP dinamikusan használja a megfelelő video codec-ket, hogy azok az eredeti képkockákhoz közelednek. Azonban még mindig grafika, és még mindig a legjelentősebb közreműködő a sávszélesség kihasználtsága.
* A távoli asztal üresjárati ideje nem vagy minimális képernyő-frissítést jelent; így a hálózati használat minimális üresjárati időpontokban.
* Ha a távoli asztali ügyfél ablaka kisméretű, a rendszer nem továbbít grafikus frissítéseket a munkamenet-gazdagépről.

Ne feledje, hogy a hálózatra helyezett terhelés az alkalmazás számítási feladatának és a megjelenítési felbontásának megfelelően függ. Ha a frame rate vagy a Display felbontása növekszik, a sávszélességre vonatkozó követelmény is emelkedni fog. Ha például egy nagy felbontású kijelzővel rendelkező kis méretű számítási feladat több elérhető sávszélességet igényel, mint a normál vagy alacsony felbontású, könnyű számítási feladatok. A különböző megjelenítési felbontások különböző elérhető sávszélességet igényelnek.

Az alábbi táblázat a különböző grafikai forgatókönyvekben használt adatok becslését ismerteti. Ezek a számok egyetlen figyelő konfigurációra vonatkoznak, amely 1920 × 1080-feloldással, valamint alapértelmezett grafikus móddal és H. 264/AVC 444 grafikus móddal is rendelkezik.

| Eset | Alapértelmezett mód | H. 264/AVC 444 üzemmód | Miniatűr | A forgatókönyv leírása |
|:---|---:|---:|---|---|
| Tétlen | 0,3 kbps | 0,3 kbps |:::image type="content" source="media/idle.png" alt-text="Képernyőkép az üresjárati kapcsolatban":::| A felhasználó szünetelteti a munkáját, és nincs aktív képernyős frissítés |
| Microsoft Word | 100-150 kbps | 200-300 Kbps |:::image type="content" source="media/word.gif" alt-text="A Microsoft Word animációja":::| A felhasználó aktívan dolgozik a Microsoft Wordben, begépeli, beilleszti a grafikát és váltani a dokumentumok között |
| Microsoft Excel | 150-200 kbps | 400-500 kbps |:::image type="content" source="media/excel.gif" alt-text="Microsoft Excel-animáció":::| A felhasználó aktívan együttműködik a Microsoft Excelben, a képleteket és diagramokat tartalmazó több cella egyszerre frissül. |
| Microsoft PowerPoint | 4 – 4.5 Mbps | 1,6 – 1,8 Mbps |:::image type="content" source="media/powerpoint.gif" alt-text="A Microsoft PowerPoint animációja":::| A felhasználó aktívan dolgozik a Microsoft PowerPointban, begépelve, beillesztve. A felhasználó a formázott grafikát is módosítja, és a dia áttűnési effektusait is használhatja |
| Webböngészés | 6 – 6,5 Mbps | 0,9 – 1 MB/s |:::image type="content" source="media/web.gif" alt-text="Webes böngészés animációja":::| A felhasználó aktívan dolgozik egy olyan grafikusan gazdag webhellyel, amely több statikus és animált képet tartalmaz. A felhasználó vízszintesen és függőlegesen görgeti az oldalakat |
| Képtár | 3.3 – 3,6 Mbps | 0.7 – 0,8 Mbps |:::image type="content" source="media/image-gallery.gif" alt-text="A Képtár animációja":::| A felhasználó aktívan dolgozik a képkatalógus alkalmazásban. képek tallózása, nagyítása, átméretezése és elforgatása |
| Videó lejátszása | 8.5 – 9,5 Mbps | 2,5 – 2,8 Mbps |:::image type="content" source="media/video.gif" alt-text="Videolejátszás animációja":::| A felhasználó egy 30 FPS-videót figyel, amely az 1/2-es képernyőt használja |
| Teljes képernyős videolejátszás | 7.5 – 8,5 Mbps | 2,5 – 3,1 Mbps |:::image type="content" source="media/fullscreen-video.gif" alt-text="Teljes képernyős videolejátszás animációja":::| A felhasználó egy 30 képkocka méretű videót néz, amely teljes képernyős |

## <a name="dynamic-bandwidth-allocation"></a>Dinamikus sávszélesség kiosztása

A RDP protokoll egy modern protokoll, amely úgy van kialakítva, hogy dinamikusan módosítsa a hálózati feltételeket.
A sávszélesség-használat szigorú korlátainak használata helyett az RDP folyamatos hálózati észlelést használ, amely aktívan figyeli a rendelkezésre álló hálózati sávszélességet és a csomagok időkorlátját. Az eredmények alapján az RDP dinamikusan kiválasztja a grafikus kódolási beállításokat, és lefoglalja a sávszélességet az eszközök átirányításához és más virtuális csatornákhoz.  
Ez a technológia lehetővé teszi az RDP számára a teljes hálózati cső használatát, ha az elérhető és gyorsan visszakerül a hálózatra, ha valami másra van szükség.
Az RDP észleli a képminőség, a Képkockasebesség vagy a tömörítési algoritmusok módosítását, ha más alkalmazások is kérik a hálózatot.

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>A hálózati sávszélesség használatának korlátozása szabályozási sebességgel

A legtöbb esetben nincs szükség a sávszélesség-használat korlátozására, mivel a korlátozás érintheti a felhasználói élményt. A korlátozott hálózatokban azonban érdemes korlátozni a hálózat kihasználtságát. Egy másik példa a bérelt hálózatokra, amelyek a felhasznált forgalom mennyiségét terhelik.

Ilyen esetekben korlátozhatja az RDP kimenő hálózati forgalmat a QoS-házirendben megadott szabályozási sebesség megadásával.

  >[!NOTE]
  > Győződjön meg [arról, hogy az RDP-Shortpath engedélyezve van](./shortpath.md) – a sávszélesség korlátozásának korlátozása nem támogatott a fordított kapcsolat átvitele esetén.

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>A munkamenet-gazdagépre vonatkozó szabályozási sebesség korlátozásának megvalósítása Csoportházirend használatával

A házirend alapú szolgáltatásminőség (QoS) a Csoportházirendon belül az előre definiált szabályozási sebesség beállítására használható.

Ha QoS-házirendet szeretne létrehozni a tartományhoz csatlakoztatott munkamenet-gazdagépekhez, először jelentkezzen be egy olyan számítógépre, amelyen Csoportházirend a felügyelet telepítve van. Nyissa meg Csoportházirend-felügyeletet (válassza a Start, a felügyeleti eszközök, majd a Csoportházirend felügyelet lehetőséget), majd hajtsa végre a következő lépéseket:

1. A Csoportházirend-kezelés területen keresse meg azt a tárolót, amelyben létre szeretné hozni az új házirendet. Ha például az összes munkamenetben lévő számítógép a **munkamenet-gazdagépek** nevű szervezeti egységben található, az új szabályzatot a munkamenet-GAZDAGÉPEK szervezeti egységében kell létrehozni.

2. Kattintson a jobb gombbal a megfelelő tárolóra, majd válassza a **csoportházirend-objektum létrehozása ebben a tartományban, és hivatkozás itt** lehetőséget.

3. Az **új csoportházirend-objektum** párbeszédpanelen írja be az új csoportházirend objektum nevét a **név** mezőbe, majd kattintson az **OK gombra**.

4. Kattintson a jobb gombbal az újonnan létrehozott házirendre, majd válassza a **Szerkesztés** lehetőséget.

5. A Csoportházirend-felügyeleti szerkesztő bontsa ki a **Számítógép konfigurációja**, majd a **Windows beállításai** csomópontot, kattintson a jobb gombbal a **házirend alapú QoS** elemre, majd válassza az **új házirend létrehozása** parancsot.

6. A **házirend alapú QoS** párbeszédpanel Megnyitás lapján adja meg az új szabályzat nevét a **név** mezőben. Válassza a **kimenő sávszélesség megadása** lehetőséget, és állítsa be a szükséges értéket, majd kattintson a **tovább** gombra.

7. A következő lapon jelölje ki a **csak az ilyen végrehajtható névvel rendelkező alkalmazásokat** , és adja meg a **svchost.exe** nevet, majd kattintson a **tovább** gombra. Ez a beállítás arra utasítja a szabályzatot, hogy csak a Távoli asztal szolgáltatásból származó egyező forgalmat rangsorolja.

8. A harmadik lapon győződjön meg arról, hogy a **forrás IP-címe** és **a cél IP-címe** is ki van választva. Kattintson a **Tovább** gombra. Ez a két beállítás biztosítja a csomagok felügyeletét, függetlenül attól, hogy melyik számítógépről (IP-címről) küldték a csomagokat, és hogy melyik számítógép (IP-cím) kapja meg a csomagokat.

9. A négy oldalon válassza az **UDP** lehetőséget a **válassza ki** azt a protokollt, amelyre a QoS-házirend vonatkozik a legördülő listára.

10. A címsor alatt **adja meg a forrásport számát**, válasszon **a forrás port vagy tartomány közül**. A kísérő szövegmezőbe írja be a **3390** karakterláncot. Válassza a **Befejezés** gombot.

Az új létrehozott házirendek nem lépnek érvénybe, amíg a Csoportházirend frissült a munkamenet-gazdagép számítógépein. Bár a Csoportházirend rendszeresen frissíti a saját példányát, a következő lépésekkel kényszerítheti az azonnali frissítést:

1. Minden olyan munkamenet-gazdagépen, amelyre frissíteni kívánja Csoportházirend, nyisson meg egy parancssort rendszergazdaként (*Futtatás rendszergazdaként*).

2. A parancssorba írja be a következőt:

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>A munkamenet-gazdagépre vonatkozó szabályozási sebesség korlátozása a PowerShell használatával

Az RDP-Shortpath vonatkozó szabályozási sebességet az alábbi PowerShell-parancsmag használatával állíthatja be:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>Következő lépések

* A Windows rendszerű virtuális asztali sávszélességre vonatkozó követelmények megismeréséhez tekintse meg [a Windows rendszerű virtuális asztalok RDP protokoll (RDP) sávszélesség-követelményeit](rdp-bandwidth.md)ismertető témakört.
* A Windows rendszerű virtuális asztali hálózati kapcsolat megismeréséhez tekintse meg a [Windows rendszerű virtuális asztali hálózati kapcsolat ismertetése](network-connectivity.md)című témakört.
* A Windows rendszerű virtuális asztali szolgáltatások szolgáltatásminőség (QoS) szolgáltatásának megkezdéséhez lásd: [a Windows rendszerű virtuális asztali szolgáltatás szolgáltatásminőség (QoS) implementálása](rdp-quality-of-service-qos.md).
