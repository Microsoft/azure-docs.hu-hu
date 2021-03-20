---
title: StorSimple 8000 sorozatú eszközillesztő cseréje | Microsoft Docs
description: Elmagyarázza, hogyan távolíthat el és cserélhet le egy vagy mindkét vezérlő modult a StorSimple 8000 Series eszközön.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 9d8b75c48da2bb13d843258ead378d3e849da951
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514085"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Vezérlő modul cseréje a StorSimple-eszközön
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan lehet egy vagy mindkét vezérlő modult eltávolítani és cserélni egy StorSimple-eszközön. Emellett az egyetlen és kettős vezérlő helyettesítési forgatókönyvek alapjául szolgáló logikát is tárgyalja.

> [!NOTE]
> A vezérlő cseréjének végrehajtása előtt azt javasoljuk, hogy mindig frissítse a vezérlő belső vezérlőprogramot a legújabb verzióra.
> 
> Ha meg szeretné akadályozni, hogy a StorSimple-eszköz kárt okozzon, ne távolítsa el a vezérlőt addig, amíg a LED-ek a következők egyike nem látható:
> 
> * Az összes jelzőfény ki van kapcsolva.
> * A LED 3, ![ zöld pipa ikon ](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png) és ![ piros kereszt ikon ](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) villog, és a LED 0 és a LED 7 is **be van kapcsolva**.


A következő táblázat a támogatott vezérlő-helyettesítési forgatókönyveket mutatja be.

| Case (Eset) | Helyettesítési forgatókönyv | Alkalmazható eljárás |
|:--- |:--- |:--- |
| 1 |Egy vezérlő meghibásodott állapotban van, a másik vezérlő állapota Kifogástalan és aktív. |[Egyetlen vezérlő cseréje](#replace-a-single-controller), amely leírja az [egyetlen vezérlő cseréje mögötti logikát](#single-controller-replacement-logic), valamint a [pótlás lépéseit](#single-controller-replacement-steps). |
| 2 |Mindkét vezérlő meghibásodott, és cserére van szükség. Az alváz, a lemezek és a lemezes ház kifogástalan állapotú. |[Kettős vezérlő cseréje](#replace-both-controllers), amely a [kettős vezérlő cseréje mögötti logikát](#dual-controller-replacement-logic), valamint a [helyettesítő lépéseket](#dual-controller-replacement-steps)ismerteti. |
| 3 |Az azonos eszközről vagy különböző eszközökről származó vezérlőket cseréli a rendszer. Az alváz, a lemezek és a lemezes ház kifogástalan állapotú. |A tárolóhelyek eltérésére figyelmeztető üzenet jelenik meg. |
| 4 |Hiányzik egy vezérlő, és a másik vezérlő meghibásodik. |[Kettős vezérlő cseréje](#replace-both-controllers), amely a [kettős vezérlő cseréje mögötti logikát](#dual-controller-replacement-logic), valamint a [helyettesítő lépéseket](#dual-controller-replacement-steps)ismerteti. |
| 5 |Egy vagy mindkét vezérlő meghiúsult. Az eszköz nem érhető el a soros konzolon vagy a Windows PowerShell távelérési szolgáltatásán keresztül. |A manuális vezérlő helyettesítési eljárásához [forduljon Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) . |
| 6 |A vezérlők eltérő Build-verzióval rendelkeznek, amelynek oka a következő lehet:<ul><li>A vezérlők eltérő verziójú szoftverrel rendelkeznek.</li><li>A vezérlők eltérő belső vezérlőprogram-verzióval rendelkeznek.</li></ul> |Ha a vezérlő szoftver verziója eltérő, a helyettesítő logika észleli a szoftvert, és frissíti a szoftver verzióját a helyettesítő vezérlőn.<br><br>Ha a vezérlő belső vezérlőprogram-verziói eltérnek, és a régi belső vezérlőprogram verziója **nem** frissíthető automatikusan, a Azure Portal riasztási üzenet jelenik meg. Frissítéseket kell keresnie, és telepítenie kell a belső vezérlőprogram frissítéseit.</br></br>Ha a vezérlő belső vezérlőprogram-verziója eltér, és a régi belső vezérlőprogram verziója automatikusan frissíthető, a vezérlő helyettesítési logikája ezt fogja érzékelni, és a vezérlő elindítása után a rendszer automatikusan frissíti a belső vezérlőprogram frissítését. |

Ha nem sikerült, el kell távolítania egy vezérlő modult. Az egyik vagy mindkét vezérlő modul meghibásodhat, ami egyetlen vezérlő helyettesítését vagy kettős vezérlő cseréjét eredményezheti. A helyettesítési eljárások és a mögöttes logika a következőket ismerteti:

* [Egyetlen vezérlő cseréje](#replace-a-single-controller)
* [Mindkét vezérlő cseréje](#replace-both-controllers)
* [Vezérlő eltávolítása](#remove-a-controller)
* [Vezérlő beszúrása](#insert-a-controller)
* [Az aktív vezérlő azonosítása az eszközön](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> A vezérlő eltávolítása és cseréje előtt tekintse át a [StorSimple hardver-összetevő cseréje](storsimple-8000-hardware-component-replacement.md)biztonsági információit.
> 
> 

## <a name="replace-a-single-controller"></a>Egyetlen vezérlő cseréje
Ha a Microsoft Azure StorSimple eszköz egyik két vezérlője meghiúsult, hibás vagy hiányzik, akkor egyetlen vezérlőt kell cserélnie.

### <a name="single-controller-replacement-logic"></a>Egyetlen vezérlő helyettesítési logikája
Egyetlen vezérlő cseréjekor először távolítsa el a sikertelen vezérlőt. (Az eszköz többi vezérlője az aktív vezérlő.) A helyettesítő vezérlő beszúrásakor a következő műveletek végezhetők el:

1. A helyettesítő vezérlő azonnal elindítja a StorSimple-eszközzel folytatott kommunikációt.
2. A rendszer átmásolja az aktív vezérlő virtuális merevlemezének (VHD) pillanatképét a helyettesítő vezérlőre.
3. A pillanatkép úgy módosul, hogy amikor a helyettesítő vezérlő ettől a VHD-től indul, a rendszer készenléti vezérlőként fogja felismerni.
4. Ha a módosítások befejeződik, a rendszer a helyettesítő vezérlőt készenléti vezérlőként indítja el.
5. Ha mindkét vezérlő fut, a fürt online állapotba kerül.

### <a name="single-controller-replacement-steps"></a>Egyetlen vezérlő helyettesítésének lépései
Ha a Microsoft Azure StorSimple-eszköz egyik vezérlője meghibásodik, hajtsa végre a következő lépéseket. (A másik vezérlőnek aktívnak kell lennie és futnia kell. Ha mindkét vezérlő meghibásodik vagy meghibásodik, nyissa meg a [kettős vezérlő helyettesítésének lépéseit](#dual-controller-replacement-steps).)

> [!NOTE]
> 30 – 45 percet is igénybe vehet, hogy a vezérlő újrainduljon, és teljesen helyre tudja állítani az egyetlen vezérlőt helyettesítő eljárásból. A teljes eljárás teljes ideje, beleértve a kábelek csatolását, körülbelül 2 óra.


#### <a name="to-remove-a-single-failed-controller-module"></a>Egyetlen sikertelen vezérlő modul eltávolítása
1. A Azure Portal nyissa meg a StorSimple Eszközkezelő szolgáltatást, kattintson az **eszközök** elemre, majd kattintson a figyelni kívánt eszköz nevére.
2. Válassza a **figyelés > hardver állapota** lehetőséget. A vezérlő 0 vagy a vezérlő 1 állapotának pirosnak kell lennie, ami hibát jelez.
   
   > [!NOTE]
   > A meghibásodott vezérlő egyetlen vezérlőn való cseréje mindig készenléti vezérlő.
   
3. Az 1. ábra és a következő táblázat segítségével keresse meg a sikertelen vezérlő modult.
   
    ![Az eszköz elsődleges bekerítési moduljainak hátlapja](./media/storsimple-controller-replacement/IC740994.png)
   
    **1. ábra** StorSimple-eszköz visszaállítása
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Vezérlő 0 |
   | 4 |Vezérlő 1 |
4. A sikertelen vezérlőn távolítsa el az összes csatlakoztatott hálózati kábelt az adatportokból. Ha 8600 modellt használ, távolítsa el a vezérlőt a EBOD vezérlőhöz csatlakoztató SAS-kábeleket is.
5. Kövesse a [vezérlő eltávolítása](#remove-a-controller) a sikertelen vezérlő eltávolításához című témakör lépéseit.
6. Telepítse a gyári cserét ugyanabba a tárolóhelyre, ahonnan a meghibásodott vezérlő el lett távolítva. Ez elindítja az egyetlen vezérlő helyettesítési logikáját. További információ: [Single Controller helyettesítési logikája](#single-controller-replacement-logic).
7. Míg az egyetlen vezérlőt helyettesítő logika a háttérben halad, csatlakoztassuk újra a kábeleket. Ügyeljen arra, hogy az összes kábelt pontosan ugyanúgy csatlakoztatja, mint a csere előtt.
8. A vezérlő újraindítása után ellenőrizze a **vezérlő állapotát** és a **fürt állapotát** a Azure Portalban annak ellenőrzéséhez, hogy a vezérlő vissza van-e állítva Kifogástalan állapotba, és készenléti állapotban van-e.

> [!NOTE]
> Ha az eszközt a soros konzolon keresztül figyeli, több újraindítást is láthat, amíg a vezérlőt helyreállítják a csere eljárásból. Ha megjelenik a soros konzol menüje, akkor tudja, hogy a csere befejeződött. Ha a menü nem jelenik meg két órán belül a vezérlő cseréjének megkezdése után, vegye [fel a kapcsolatot Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md).
>
> A 4-es frissítés megkezdése után az `Get-HCSControllerReplacementStatus` eszköz Windows PowerShell-felületén található parancsmagot is használhatja a vezérlő lecserélési folyamatának állapotának figyelésére.
> 

## <a name="replace-both-controllers"></a>Mindkét vezérlő cseréje
Ha a Microsoft Azure StorSimple eszközön mindkét vezérlő meghibásodik, hibásak vagy hiányoznak, akkor mindkét vezérlőt le kell cserélni. 

### <a name="dual-controller-replacement-logic"></a>Kettős vezérlő helyettesítési logikája
A kettős vezérlő cseréjekor először távolítsa el a sikertelen vezérlőket, majd szúrja be a helyettesítőket. A két helyettesítő vezérlő beillesztése után a következő műveletek történnek:

1. A 0. tárolóhelyen található helyettesítő vezérlő a következőket ellenőrzi:
   
   1. A belső vezérlőprogram és a szoftver jelenlegi verzióját használja?
   2. A fürt része?
   3. Fut a társ-vezérlő, és fürtözött?
      
      Ha ezek a feltételek egyike sem teljesül, a vezérlő a napi biztonsági mentést keresi (amely a meghajtó **nonDOMstorage** található). A vezérlő átmásolja a virtuális merevlemez legújabb pillanatképét a biztonsági másolatból.
2. A 0. bővítőhely vezérlője maga a pillanatképet használja a rendszerképhez.
3. Eközben az 1. bővítőhely vezérlője megvárja a 0. vezérlőt a lemezkép befejezéséhez és a kezdéshez.
4. A vezérlő 0 elindítása után az 1. vezérlő észleli a 0. vezérlő által létrehozott fürtöt, amely kiváltja az egyetlen vezérlő helyettesítési logikáját. További információ: [Single Controller helyettesítési logikája](#single-controller-replacement-logic).
5. Ezt követően mindkét vezérlő futni fog, és a fürt online állapotba kerül.

> [!IMPORTANT]
> A kettős vezérlő cseréjét követően a StorSimple-eszköz konfigurálása után elengedhetetlen, hogy manuális biztonsági mentést készítsen az eszközről. A napi eszköz konfigurációjának biztonsági mentése nem indul el, amíg a 24 óra el nem telik. A [Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) használata az eszköz manuális biztonsági mentésének elvégzéséhez.


### <a name="dual-controller-replacement-steps"></a>Kettős vezérlő helyettesítésének lépései
Ez a munkafolyamat akkor szükséges, ha a Microsoft Azure StorSimple eszköz mindkét vezérlője meghiúsult. Ez olyan adatközpontokban fordulhat elő, amelyekben a hűtési rendszer leáll, és ennek eredményeképpen a vezérlők rövid időn belül meghiúsulnak. Attól függően, hogy a StorSimple-eszköz ki van-e kapcsolva, vagy egy 8600-es vagy 8100-es modellt használ, különböző lépések szükségesek.

> [!IMPORTANT]
> Akár 45 percet is igénybe vehet, amíg a vezérlő újraindul, és teljesen helyre tudja állítani a kettős vezérlő helyettesítési eljárását. A teljes eljárás teljes ideje, beleértve a kábelek csatolását, körülbelül 2,5 óra.

#### <a name="to-replace-both-controller-modules"></a>Mindkét vezérlő modul cseréje
1. Ha az eszköz ki van kapcsolva, hagyja ki ezt a lépést, és folytassa a következő lépéssel. Ha az eszköz be van kapcsolva, kapcsolja ki az eszközt.
   
   1. Ha 8600 modellt használ, először kapcsolja ki az elsődleges burkolatot, majd kapcsolja ki a EBOD bekerítését.
   2. Várjon, amíg az eszköz teljesen leállt. Az eszköz hátulján lévő összes LED ki lesz kapcsolva.
2. Távolítsa el az adatportokhoz csatlakozó összes hálózati kábelt. Ha 8600 modellt használ, távolítsa el azokat az SAS-kábeleket is, amelyek az elsődleges bekerítést csatlakoztatják az EBOD-ház számára.
3. Távolítsa el mindkét vezérlőt a StorSimple-eszközről. További információ: [vezérlő eltávolítása](#remove-a-controller).
4. Illessze be először a 0. vezérlőhöz tartozó gyári cserét, majd szúrja be az 1. vezérlőt. További információ: [vezérlő beszúrása](#insert-a-controller). Ez elindítja a kettős vezérlő helyettesítési logikáját. További információ: [kettős vezérlő helyettesítési logikája](#dual-controller-replacement-logic).
5. Míg a vezérlő cseréjének logikája a háttérben halad, csatlakoztassuk újra a kábeleket. Ügyeljen arra, hogy az összes kábelt pontosan ugyanúgy csatlakoztatja, mint a csere előtt. Tekintse meg a modellre vonatkozó részletes utasításokat a [StorSimple 8100-eszköz telepítéséhez](storsimple-8100-hardware-installation.md) vagy a [StorSimple 8600](storsimple-8600-hardware-installation.md)-eszköz telepítéséhez.
6. Kapcsolja be a StorSimple eszközt. Ha 8600 modellt használ:
   
   1. Győződjön meg arról, hogy a EBOD bekerítése először be van kapcsolva.
   2. Várjon, amíg a EBOD ház fut.
   3. Kapcsolja be az elsődleges bekerítést.
   4. Miután az első vezérlő újraindult, és kifogástalan állapotban van, a rendszer futni fog.
      
      > [!NOTE]
      > Ha az eszközt a soros konzolon keresztül figyeli, több újraindítást is láthat, amíg a vezérlőt helyreállítják a csere eljárásból. Ha megjelenik a soros konzol menü, akkor tudja, hogy a csere befejeződött. Ha a menü nem jelenik meg a vezérlő cseréje után 2,5 órán belül, forduljon a [Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Vezérlő eltávolítása
Az alábbi eljárással távolíthat el egy hibás vezérlő modult a StorSimple-eszközről.

> [!NOTE]
> A következő ábrák a 0. vezérlőre vonatkoznak. Az 1. vezérlő esetében ezek fordítottak.


#### <a name="to-remove-a-controller-module"></a>Vezérlő modul eltávolítása
1. Fogja meg a modul zárolását a hüvelykujj és a mutatóujj között.
2. A vezérlő zárolásának felszabadításához finoman nyomja össze a hüvelykujját és a mutatóujját.
   
    ![Vezérlő zárolásának felszabadítása](./media/storsimple-controller-replacement/IC741047.png)
   
    **2. ábra** Vezérlő zárolásának felszabadítása
3. A kilincset leíróként használva csúsztassa ki a vezérlőt az alvázról.
   
    ![Kicsúsztatható vezérlő az alvázon kívül](./media/storsimple-controller-replacement/IC741048.png)
   
    **3. ábra** A vezérlő kicsúsztatása az alvázról

## <a name="insert-a-controller"></a>Vezérlő beszúrása
Az alábbi eljárással telepítheti a gyári vezérlő modult, miután eltávolította a hibás modult a StorSimple-eszközről.

#### <a name="to-install-a-controller-module"></a>Vezérlő modul telepítése
1. Ellenőrizze, hogy van-e sérülés a csatoló-összekötők számára. Ne telepítse a modult, ha bármelyik összekötő PIN-kód sérült vagy hajlított.
2. Csúsztassa a vezérlő modult az alvázra, miközben a zárolás teljesen ki van szabadítva.
   
    ![A csúszó vezérlőt a váz](./media/storsimple-controller-replacement/IC741053.png)
   
    **4. ábra** Az alvázon lévő csúszó vezérlő
3. Ha beszúrta a vezérlő modult, kezdje el bezárni a zárolást, miközben továbbra is leküldi a vezérlő modult az alvázra. A zárolás a vezérlő üzembe helyezéséhez vezet.
   
    ![Vezérlő zárolásának bezárása](./media/storsimple-controller-replacement/IC741054.png)
   
    **5. ábra** A vezérlő retesz bezárása
4. Elkészült, amikor a zárolás a helyére kerül. Az **OK** LED-nek most be kell jelentkeznie.
   
   > [!NOTE]
   > Akár 5 percet is igénybe vehet a vezérlő és a LED aktiválása.
  
5. Annak ellenőrzéséhez, hogy a csere sikeres-e, a Azure Portal lépjen az eszközre, majd keresse meg a  >  **hardver állapotának** figyelése lehetőséget, és győződjön meg arról, hogy a vezérlő 0 és a vezérlő 1 állapota Kifogástalan (az állapot zöld).

## <a name="identify-the-active-controller-on-your-device"></a>Az aktív vezérlő azonosítása az eszközön
Számos szituáció létezik, például az eszköz első vagy a vezérlő cseréje, amely megköveteli, hogy megkeresse az aktív vezérlőt egy StorSimple-eszközön. Az aktív vezérlő feldolgozza az összes lemez belső vezérlőprogram és hálózati műveletét. Az aktív vezérlő azonosításához a következő módszerek bármelyikét használhatja:

* [A Azure Portal használata az aktív vezérlő azonosításához](#use-the-azure-portal-to-identify-the-active-controller)
* [A Windows PowerShell StorSimple-bővítménye használata az aktív vezérlő azonosításához](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Az aktív vezérlő azonosításához keresse meg a fizikai eszközt](#check-the-physical-device-to-identify-the-active-controller)

A következő eljárások mindegyikét ismertetjük.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>A Azure Portal használata az aktív vezérlő azonosításához
A Azure Portal navigáljon az eszközhöz, majd a   >  **hardver állapotának** figyeléséhez, és görgessen a **vezérlők** szakaszhoz. Itt ellenőrizheti, hogy melyik vezérlő aktív.

![Az aktív vezérlő azonosítása Azure Portal](./media/storsimple-controller-replacement/IC752072.png)

**6. ábra** Az aktív vezérlőt megjelenítő Azure Portal

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>A Windows PowerShell StorSimple-bővítménye használata az aktív vezérlő azonosításához
Amikor az eszközt a soros konzolon keresztül éri el, megjelenik egy szalagcím üzenet. A szalagcím üzenet olyan alapvető eszköz-információkat tartalmaz, mint például a modell, a név, a telepített szoftver verziója és az elérni kívánt vezérlő állapota. Az alábbi képen egy szalagcímre mutató üzenet látható:

![Soros szalagcím üzenet](./media/storsimple-controller-replacement/IC741098.png)

**7. ábra** A Controller 0 aktívként megjelenített szalagcíme

A szalagcím üzenettel meghatározhatja, hogy a csatlakoztatott vezérlő aktív vagy passzív.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Az aktív vezérlő azonosításához keresse meg a fizikai eszközt
Az eszközön található aktív vezérlő azonosításához keresse meg az elsődleges ház hátoldalán található 5. adatport feletti kék LED-t.

Ha a LED villog, a vezérlő aktív, és a másik vezérlő készenléti állapotban van. A következő diagramot és táblázatot használja támogatásként.

![Az eszköz elsődleges burkolatának hátlapja a dataports](./media/storsimple-controller-replacement/IC741055.png)

**8. ábra** Az elsődleges ház hátoldala adatportokkal és figyelő LED-ekkel

| Címke | Description |
|:--- |:--- |
| 1-6 |0 – 5 hálózati port |
| 7 |Kék LED |

## <a name="next-steps"></a>Következő lépések
További információ a [StorSimple hardveres összetevők cseréjéről](storsimple-8000-hardware-component-replacement.md).

