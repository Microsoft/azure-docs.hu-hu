---
title: StorSimple 8000 sorozat Update 2,2 kibocsátási megjegyzések | Microsoft Docs
description: A StorSimple 8000 Series Update 2,2 új funkcióit, problémáit és megkerülő megoldásait ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 73b9ecd03875b60ed2d9b9d4c8e8a3a0c8de3cfa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956602"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 sorozat Update 2,2 kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés
A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 Series Update 2,2-es verziójának kritikus megnyitási problémáit. Emellett tartalmazzák az ebben a kiadásban található StorSimple szoftverfrissítések listáját is.

Az 2,2-es frissítés bármely StorSimple-eszközön (GA) vagy a 0,1-es frissítésen keresztül is alkalmazható a 2,1-as frissítéssel. Az 2,2-es frissítéshez társított 6.3.9600.17708.

Mielőtt telepítené a frissítést a StorSimple-megoldásban, tekintse át a kibocsátási megjegyzésekben található információkat.

> [!IMPORTANT]
> * Az 2,2-es frissítés csak a szoftveres frissítéseket tartalmaz. A frissítés telepítése körülbelül 1,5 – 2 órát vesz igénybe. 
> * Ha a 2,1-es frissítést futtatja, javasoljuk, hogy a lehető leghamarabb alkalmazza a 2,2-es frissítést.
> * Az új kiadások esetében előfordulhat, hogy a frissítések nem jelennek meg azonnal, mert a frissítések fokozatos bevezetését hajtjuk végre. Várjon néhány napot, majd próbálkozzon újra a frissítések keresésével, mivel ezek hamarosan elérhetővé válnak.
> 
> 

## <a name="whats-new-in-update-22"></a>Az 2,2-es frissítés újdonságai
A 2,2-es frissítésben az alábbi főbb fejlemények történtek.

* **Automatizált lemezterület-visszanyerési optimalizálás** – ha a rendszer törli az adatmennyiséget a dinamikusan kiosztott köteteken, a fel nem használt tárolási blokkokat vissza kell állítani. Ez a kiadás javította a terület visszanyerésének folyamatát a felhőből, ami azt eredményezi, hogy a fel nem használt terület gyorsabban elérhetővé válik az előző verziókhoz képest.
* **Snapshot Performance Enhancements** – a 2,2-es frissítés javította a Felhőbeli Pillanatképek feldolgozásának idejét bizonyos helyzetekben, ahol nagy mennyiségű kötet van használatban, és minimális az adatváltozás. A fejlesztés előnyeit kihasználó forgatókönyv az archív kötetek.
* A **támogatási csomag összegyűjtésének megerősítése** – javult a támogatási csomag összegyűjtése és feltöltése ebben a kiadásban. 
* **Megbízhatósági változások frissítése** – ez a kiadás olyan hibajavításokat tartalmaz, amelyek javítják a frissítés megbízhatóságát.

## <a name="issues-fixed-in-update-22"></a>Az 2,2-es frissítésben rögzített problémák
Az alábbi táblázat a 2,2-es és a 2,1-es frissítésekben rögzített problémák összegzését tartalmazza.    

| Nem | Funkció | Probléma | A fizikai eszközre vonatkozik | A virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Gazdagép teljesítménye |A korábbi kiadásokban a gazdagép-oldali teljesítménnyel kapcsolatos problémák megfigyelhetők egy helyileg rögzített kötet létrehozásakor és egy többrétegű kötetnek egy helyileg rögzített kötetre való átalakítása során. Ezek a problémák ebben a kiadásban vannak kijavítva, ami a gazda teljesítményének javulását eredményezi a kötetek létrehozása és a konverziós eljárások során. |Igen |Nem |
| 2 |Helyileg rögzített kötetek |Ritka esetekben a rendszer összeomlik, amikor helyileg rögzített kötetet hoz létre. Ez a hiba a jelen kiadásban lett javítva. |Igen |Nem |
| 3 |Rétegezés |Szórványos összeomlások fordultak elő, amikor a StorSimple Cloud Appliances (8010 és 8020) metaadatok a felhőbe lettek bontva. Ez a probléma ebben a kiadásban van kijavítva. |Nem |Igen |
| 4 |Pillanatkép létrehozása |A növekményes Pillanatképek létrehozásával kapcsolatos problémák merültek fel a nagy mennyiségű és minimális adatváltozás nélküli forgatókönyvekben. Ezek a problémák ebben a kiadásban vannak kijavítva. |Igen |Yes |
| 5 |Openstack-hitelesítés |Ha a Openstack-t a felhőalapú szolgáltatóként használja, akkor a felhasználó a hitelesítéshez kapcsolódóan nem gyakori hibába ütközött, ahol a JSON-elemző összeomlást eredményezett. Ez a hiba ebben a kiadásban van kijavítva. |Igen |Nem |
| 6 |Gazdagép-oldali másolat |A szoftver korábbi verzióiban a ODX-időzítéssel kapcsolatos ritka hibákat észlelt a rendszer az adatok egyik kötetről egy másik kötetre való másolása során. Ez egy vezérlő feladatátvételét eredményezheti, és a rendszer valószínűleg helyreállítási módba lép. Ez a hiba ebben a kiadásban van kijavítva. |Igen |Nem |
| 7 |Windows Management Instrumentation (WMI) |A szoftver korábbi verzióiban a webproxy meghibásodásának számos példánya volt a " \<ManagementException> szolgáltató betöltési hibája" kivétellel. Ez a hiba a WMI-memóriavesztés számára lett kiváltva, és már kijavítva van. |Igen |Nem |
| 8 |Frissítés |Bizonyos ritka esetekben a szoftver korábbi verzióiban a felhasználó "CisPowershellHcsscripterror" kapott a frissítések vizsgálatának vagy telepítésének megkísérlésekor. Ez a probléma ebben a kiadásban van kijavítva. |Igen |Yes |
| 9 |Támogatási csomag |Ebben a kiadásban javult a támogatási csomag összegyűjtésének és feltöltésének módja. |Igen |Yes |

## <a name="known-issues-in-update-22"></a>Az 2,2-es frissítés ismert problémái
A következő táblázat az ebben a kiadásban található ismert problémák összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma | Megjegyzések/Áthidaló megoldás | A fizikai eszközre vonatkozik | A virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvóruma |Ritka esetekben, ha egy 8600-es eszköz EBOD-borításában lévő lemezek többsége le van választva, ami nem eredményez lemezes kvórumot, a rendszer offline állapotba helyezi a tárolót. Offline állapotban marad, még akkor is, ha a lemezek újra vannak csatlakoztatva. |Az eszközt újra kell indítani. Ha a probléma továbbra is fennáll, forduljon Microsoft ügyfélszolgálata a következő lépésekhez. |Igen |Nem |
| 2 |Helytelen vezérlő-azonosító |A vezérlő cseréjének végrehajtásakor a vezérlő 0 vezérlőként jelenhet meg. A vezérlő cseréjekor, amikor a rendszerkép betöltődik a társ csomópontból, a vezérlő azonosítója kezdetben megjelenhet a társ-vezérlő AZONOSÍTÓJAként. Ritka esetekben ez a viselkedés a rendszer újraindítása után is megjelenhet. |Nincs szükség felhasználói beavatkozásra. Ez a helyzet a vezérlő cseréjének befejeződése után maga is megoldódik. |Igen |Nem |
| 3 |Tárfiókok |Ha a Storage szolgáltatással törli a Storage-fiókot, a rendszer nem támogatott forgatókönyvet használ. Ez olyan helyzetet eredményez, amelyben a felhasználói adatszolgáltatások nem kérhetők le. | |Igen |Yes |
| 4 |Eszköz feladatátvétele |Egy adott forrásoldali eszközről a különböző cél eszközökre irányuló mennyiségi tárolók több feladatátvétele nem támogatott. Az egyetlen elhalt eszközről több eszközre történő feladatátvétel esetén az első meghiúsult eszközön lévő mennyiségi tárolók elvesztik az adatok tulajdonjogát. Ilyen feladatátvétel után ezek a mennyiségi tárolók a klasszikus Azure-portálon megtekintve eltérően jelennek meg vagy viselkednek. | |Igen |Nem |
| 5 |Telepítés |A SharePoint rendszerhez készült StorSimple-adapter során meg kell adnia egy eszköz IP-címét ahhoz, hogy a telepítés sikeresen befejeződik. | |Igen |Nem |
| 6 |Webproxy |Ha a webproxy konfigurációja HTTPS protokollt használ a megadott protokollként, az eszközről a szolgáltatásra irányuló kommunikáció hatással lesz, és az eszköz offline állapotba kerül. A támogatási csomagok a folyamat során is létrejönnek, és jelentős erőforrásokat fogyasztanak az eszközön. |Győződjön meg arról, hogy a webproxy URL-címe HTTP protokollt használ a megadott protokollként. További információ: [Configure web proxy for your device](./storsimple-8000-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
| 7 |Webproxy |Ha a webproxyt egy regisztrált eszközön konfigurálja és engedélyezi, akkor újra kell indítania az aktív vezérlőt az eszközön. | |Igen |Nem |
| 8 |Magas Felhőbeli késés és magas I/O-munkaterhelés |Ha a StorSimple-eszköz nagyon magas Felhőbeli késések (másodpercek sorrendje) és magas I/O-munkaterhelések kombinációját tapasztalja, az eszközök mennyisége csökkentett teljesítményű állapotba kerül, és az I/O-művelet meghiúsulhat az "eszköz nem üzemkész" hiba miatt. |Ennek a helyzetnek a helyreállításához manuálisan kell újraindítani az eszközöket, vagy feladatátvételt kell végrehajtani az eszközön. |Igen |Nem |
| 9 |Azure PowerShell |Ha a **Get-&#124; AzureStorSimpleStorageAccountCredential StorSimple parancsmagot használja Select-Object – első 1 – várja** meg az első objektum kiválasztását, hogy új **volumecontainer tárhoz való** objektumot hozzon létre, a parancsmag az összes objektumot visszaadja. |Zárja be a parancsmagot zárójelben a következő módon: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object – első 1 – várakozás** |Igen |Yes |
| 10 |Áttelepítés |Ha több mennyiségi tárolót továbbítanak az áttelepítéshez, a legújabb biztonsági mentéshez használt ETA csak az első kötet-tároló esetében pontos. Emellett a párhuzamos áttelepítés az első kötet-tároló első 4 biztonsági mentése után is elindul. |Javasoljuk, hogy egyszerre egy mennyiségi tárolót telepítsen át. |Igen |Nem |
| 11 |Áttelepítés |A visszaállítást követően a kötetek nincsenek hozzáadva a biztonsági mentési házirendhez vagy a virtuális lemez csoportjához. |A biztonsági másolatok létrehozásához hozzá kell adnia ezeket a köteteket egy biztonsági mentési szabályzathoz. |Igen |Yes |
| 12 |Áttelepítés |Az áttelepítés befejezése után az 5000/7000 sorozatú eszköz nem fér hozzá az áttelepített adattárolóhoz. |Azt javasoljuk, hogy az áttelepítés befejezése és véglegesítése után törölje az áttelepített adattárolókat. |Igen |Nem |
| 13 |Klón és DR |Az 1. frissítést futtató StorSimple-eszközök nem tudják az 1. frissítés előtti szoftvert futtató eszközön a klónozást vagy a vész-helyreállítást végrehajtani. |A művelet végrehajtásához frissítenie kell a megcélzott eszközt az 1. frissítéshez |Igen |Yes |
| 14 |Áttelepítés |Az áttelepítésre vonatkozó konfigurációs biztonsági mentés meghiúsulhat egy 5000-7000 sorozatú eszközön, ha nincsenek társított kötetek nélküli kötetek. |Törölje az összes olyan üres kötetet, amely nem rendelkezik társított kötetekkel, majd próbálja megismételni a konfiguráció biztonsági mentését. |Igen |Nem |
| 15 |Azure PowerShell parancsmagok és helyileg rögzített kötetek |Helyileg rögzített kötet nem hozható létre Azure PowerShell parancsmagokon keresztül. (Minden Azure PowerShell-n keresztül létrehozott kötetet a rendszer lépcsőzetesen fogja kialakítani.) |A helyileg rögzített kötetek konfigurálásához mindig a StorSimple Manager szolgáltatást használja. |Igen |Nem |
| 16 |A helyileg rögzített kötetek számára elérhető terület |Ha töröl egy helyileg rögzített kötetet, előfordulhat, hogy az új kötetek számára elérhető terület nem frissül azonnal. A StorSimple Manager szolgáltatás körülbelül óránként frissíti a helyi helyet. |Várjon egy órát, mielőtt megpróbálja létrehozni az új kötetet. |Igen |Nem |
| 17 |Helyileg rögzített kötetek |A visszaállítási feladatokkal elérhetővé teszi az ideiglenes pillanatkép biztonsági mentését a biztonsági mentési katalógusban, de csak a visszaállítási feladatokhoz. Emellett a **biztonsági mentési házirendek** lapon a **tmpCollection** előtaggal rendelkező virtuális lemezeket is elérhetővé teszi, de csak a visszaállítási feladatokhoz. |Ez akkor fordulhat elő, ha a visszaállítási feladatokhoz csak a helyileg rögzített kötetek vagy a helyileg rögzített és a lépcsőzetes kötetek kombinációja tartozik. Ha a visszaállítási feladatokban csak a lépcsőzetes kötetek szerepelnek, akkor ez a viselkedés nem fog történni. Felhasználói beavatkozásra nincs szükség. |Igen |Nem |
| 18 |Helyileg rögzített kötetek |Ha a visszaállítási feladatot visszavonja, és a vezérlő feladatátvétele azonnal megtörténik, a visszaállítási feladat **nem** a **megszakított** állapotot fogja megjeleníteni. Ha a visszaállítási feladat meghiúsul, és a vezérlő feladatátvétele azonnal megtörténik, a visszaállítási feladat **sikertelenül** fog **megjelenni** . |Ez akkor fordulhat elő, ha a visszaállítási feladatokhoz csak a helyileg rögzített kötetek vagy a helyileg rögzített és a lépcsőzetes kötetek kombinációja tartozik. Ha a visszaállítási feladatokban csak a lépcsőzetes kötetek szerepelnek, akkor ez a viselkedés nem fog történni. Felhasználói beavatkozásra nincs szükség. |Igen |Nem |
| 19 |Helyileg rögzített kötetek |Ha visszavon egy visszaállítási feladatot, vagy ha egy visszaállítás meghiúsul, majd egy vezérlő feladatátvétele történik, a **feladatok** lapon egy további visszaállítási feladat jelenik meg. |Ez akkor fordulhat elő, ha a visszaállítási feladatokhoz csak a helyileg rögzített kötetek vagy a helyileg rögzített és a lépcsőzetes kötetek kombinációja tartozik. Ha a visszaállítási feladatokban csak a lépcsőzetes kötetek szerepelnek, akkor ez a viselkedés nem fog történni. Felhasználói beavatkozásra nincs szükség. |Igen |Nem |
| 20 |Helyileg rögzített kötetek |Ha egy, a 1,2-as vagy korábbi frissítéssel létrehozott, többplatformos kötetet próbál alakítani egy helyileg rögzített kötetre, és az eszközön nincs elég hely, vagy ha a felhő meghibásodik, akkor a klón (ok) sérült lehet. |Ez a probléma csak olyan köteteken fordul elő, amelyek a 2,1-es frissítés előtti szoftverrel lettek létrehozva és klónozottak. Ez nem ritka forgatókönyv. | | |
| 21 |Kötet konvertálása |Ne frissítse a kötethez csatolt ACR-EK, amíg a kötet-átalakítás folyamatban van (a többhelyes rögzítés a helyileg rögzített vagy fordítva). A ACR-EK frissítése adatsérülést eredményezhet. |Ha szükséges, frissítse a ACR-EK a kötet konvertálása előtt, és ne végezzen további ACR-frissítéseket, amíg az átalakítás folyamatban van. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Vezérlő és belső vezérlőprogram frissítései a 2,2-es frissítésben
Ez a kiadás csak szoftveres frissítéseket tartalmaz. Ha azonban a 2. frissítés előtti verzióról frissít, akkor telepítenie kell az eszközön az illesztőprogramot, a Storport-t, a ûrkikötõt-t és (bizonyos esetekben) a lemez belső vezérlőprogram-frissítéseit.

Az illesztőprogram, a Storport, a ûrkikötõt és a lemezes belső vezérlőprogram frissítéseinek telepítésével kapcsolatos további információkért lásd: a 2,2-es [frissítés telepítése](./storsimple-8000-install-update-5.md) a StorSimple-eszközön.

## <a name="virtual-device-updates-in-update-22"></a>Virtuális eszközök frissítései a 2,2-es frissítésben
Ez a frissítés nem alkalmazható a virtuális eszközre. Új virtuális eszközöket kell létrehozni. 

## <a name="next-step"></a>Következő lépés
Megtudhatja, hogyan [telepítheti a 2,2](./storsimple-8000-install-update-5.md) -es frissítést a StorSimple-eszközön.