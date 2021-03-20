---
title: StorSimple 8000 Series Update 3 – kibocsátási megjegyzések
description: A StorSimple 8000 Series Update 3 új funkcióit, problémáit és megkerülő megoldásait ismerteti.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e081acc357c1b8872dd3a25c552d4cbe9785e212
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96007069"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>3. frissítés a StorSimple 8000 Series-eszközre vonatkozó kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés
A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 Series 3. frissítésével kapcsolatos kritikus nyitott problémákat. Emellett tartalmazzák az ebben a kiadásban található StorSimple szoftverfrissítések listáját is. 

A 3. frissítés a 2,2-es frissítéssel bármely StorSimple-eszközön, illetve a 0,1-es frissítésen is alkalmazható. A 3. frissítéshez társított 6.3.9600.17759.

Mielőtt telepítené a frissítést a StorSimple-megoldásban, tekintse át a kibocsátási megjegyzésekben található információkat.

> [!IMPORTANT]
> * A 3. frissítés az eszköz szoftverét, az LSI-illesztőprogramot és a belső vezérlőprogramot, valamint a Storport és a ûrkikötõt frissítéseket is tartalmaz. A frissítés telepítése körülbelül 1,5 – 2 órát vesz igénybe. 
> * Az új kiadások esetében előfordulhat, hogy a frissítések nem jelennek meg azonnal, mert a frissítések fokozatos bevezetését hajtjuk végre. Várjon néhány napot, majd próbálkozzon újra a frissítések keresésével, mivel ezek hamarosan elérhetővé válnak.
> 
> 

## <a name="whats-new-in-update-3"></a>A 3. frissítés újdonságai
A 3. frissítés során a következő főbb javításokat és hibajavításokat készítettük.

* **Automatikus lemezterület-visszanyerési változások** – a 3. frissítés megkezdése után a lemezterület-visszanyerési algoritmusok a rendszer készenléti vezérlőjén futnak, ami gyorsabb végrehajtást eredményez. A lemezterület-visszanyeréshez szükséges portokkal kapcsolatos további információkért tekintse meg a [StorSimple hálózatkezelési követelményeit](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Teljesítmény-fejlesztések** – a 3. frissítés továbbfejlesztett írási és olvasási teljesítményt nyújt a felhőben.
* **Áttelepítéssel kapcsolatos javítások** – ebben a kiadásban számos hibajavítást és javítást végeztek az 5000/7000 sorozatú eszközökről a 8000 sorozatú eszközökre való áttelepítési funkcióhoz. Az áttelepítési funkció használatáról további információt az [5000/7000 sorozatú eszközről az 8000 sorozatú](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b)eszközre való Migrálás című témakörben olvashat. 
* **Kapcsolódó javítások figyelése** – ebben a kiadásban a figyelési diagramokkal, a szolgáltatás-irányítópulttal és az eszköz irányítópulttal kapcsolatos hibákat javítottuk.

## <a name="issues-fixed-in-update-3"></a>A 3. frissítésben rögzített problémák
Az alábbi táblázatok összefoglalják a 3. frissítésben kijavított problémákat.    

| Nem | Funkció | Probléma | A fizikai eszközre vonatkozik | A virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Gazdagép-oldali adatáttelepítés |A korábbi kiadásban a StorSimple Cloud Appliance a gazdagép-oldali adatáttelepítés során offline állapotba került. Ez a probléma ebben a kiadásban van kijavítva. |Nem |Igen |
| 2 |Helyileg rögzített kötetek |Az előző kiadásban az I/O-hibákkal, a kötet-átalakítási hibákkal és a helyileg rögzített kötetek DataPath hibáival kapcsolatos problémák léptek fel. Ezeket a problémákat a jelen kiadásban a gyökér okozta és javítottuk. |Igen |Nem |
| 3 |Figyelés |Több probléma merült fel a jelentési egységekkel és a figyeléssel, valamint az eszközök irányítópult-diagramjaival kapcsolatban, ahol a helyileg rögzített kötetek esetében helytelen információk jelentek meg. Ezek a problémák ebben a kiadásban vannak kijavítva. |Igen |Nem |
| 4 |Nagy mennyiségű írási I/O |Ha StorSimple használ a nagy mennyiségű írást érintő számítási feladatokhoz, a felhasználó egy ritka hibába fog futni, ahol a munkakészletet a felhőbe sorolták be. Ez a hiba ebben a kiadásban van kijavítva. |Igen |Yes |
| 5 |Backup |Bizonyos ritka esetekben a szoftver korábbi verzióiban, amikor a felhasználó biztonsági másolatot készített egy távoli klónról, a Felhőbeli hibákba ütköznek, és a művelet kikerül a hiba miatt. Ebben a kiadásban a probléma kijavítva lett, és a művelet sikeresen befejeződik. |Igen |Yes |
| 6 |Biztonsági mentési szabályzat |Bizonyos ritka példányokban a szoftver korábbi kiadásaiban a biztonsági mentési szabályzat törlésével kapcsolatos hiba történt. Ez a probléma ebben a kiadásban van kijavítva. |Igen |Yes |

## <a name="known-issues-in-update-3"></a>Ismert problémák a 3. frissítésben
A következő táblázat az ebben a kiadásban található ismert problémák összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma | Megjegyzések/Áthidaló megoldás | A fizikai eszközre vonatkozik | A virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvóruma |Ritka esetekben, ha egy 8600-es eszköz EBOD-borításában lévő lemezek többsége le van választva, ami nem eredményez lemezes kvórumot, a rendszer offline állapotba helyezi a tárolót. Offline állapotban marad, még akkor is, ha a lemezek újra vannak csatlakoztatva. |Az eszközt újra kell indítani. Ha a probléma továbbra is fennáll, forduljon Microsoft ügyfélszolgálata a következő lépésekhez. |Igen |Nem |
| 2 |Helytelen vezérlő-azonosító |A vezérlő cseréjének végrehajtásakor a vezérlő 0 vezérlőként jelenhet meg. A vezérlő cseréjekor, amikor a rendszerkép betöltődik a társ csomópontból, a vezérlő azonosítója kezdetben megjelenhet a társ-vezérlő AZONOSÍTÓJAként. Ritka esetekben ez a viselkedés a rendszer újraindítása után is megjelenhet. |Nincs szükség felhasználói beavatkozásra. Ez a helyzet a vezérlő cseréjének befejeződése után maga is megoldódik. |Igen |Nem |
| 3 |Tárfiókok |Ha a Storage szolgáltatással törli a Storage-fiókot, a rendszer nem támogatott forgatókönyvet használ. Ez olyan helyzetet eredményez, amelyben a felhasználói adatszolgáltatások nem kérhetők le. | |Igen |Yes |
| 4 |Eszköz feladatátvétele |Egy adott forrásoldali eszközről a különböző cél eszközökre irányuló mennyiségi tárolók több feladatátvétele nem támogatott. Az egyetlen elhalt eszközről több eszközre történő feladatátvétel esetén az első meghiúsult eszközön lévő mennyiségi tárolók elvesztik az adatok tulajdonjogát. Ilyen feladatátvétel után ezek a mennyiségi tárolók a klasszikus Azure-portálon megtekintve eltérően jelennek meg vagy viselkednek. | |Igen |Nem |
| 5 |Telepítés |A SharePoint rendszerhez készült StorSimple-adapter során meg kell adnia egy eszköz IP-címét ahhoz, hogy a telepítés sikeresen befejeződik. | |Igen |Nem |
| 6 |Webproxy |Ha a webproxy konfigurációja HTTPS protokollt használ a megadott protokollként, az eszközről a szolgáltatásra irányuló kommunikáció hatással lesz, és az eszköz offline állapotba kerül. A támogatási csomagok a folyamat során is létrejönnek, és jelentős erőforrásokat fogyasztanak az eszközön. |Győződjön meg arról, hogy a webproxy URL-címe HTTP protokollt használ a megadott protokollként. További információ: [Configure web proxy for your device](storsimple-8000-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
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
| 22 |Frissítések |Ha a 3. frissítést alkalmazza, a klasszikus Azure portál **karbantartási** lapja a 2. frissítéssel kapcsolatos következő üzenetet jeleníti meg: "StorSimple 8000 Series Update 2, amely lehetővé teszi a Microsoft számára, hogy proaktív módon gyűjtsön adatokat az eszközről a lehetséges problémák észlelése esetén". Ez félrevezető, mert azt jelzi, hogy az eszköz frissítése folyamatban van a 2. frissítéshez. Miután az eszköz sikeresen frissítve lett a 3. frissítésre, ez az üzenet eltűnik. |Ezt a viselkedést egy későbbi kiadásban rögzíti a rendszer. |Igen |Nem |

## <a name="controller-and-firmware-updates-in-update-3"></a>A vezérlő és a belső vezérlőprogram frissítései a 3. frissítésben
Ez a kiadás LSI-illesztőprogramokat és belső vezérlőprogram-frissítéseket tartalmaz. További információ az LSI-illesztőprogram és a belső vezérlőprogram frissítéseinek telepítéséről: a [3. frissítés telepítése](./index.yml) a StorSimple-eszközön.

## <a name="virtual-device-updates-in-update-3"></a>Virtuális eszközök frissítései a 3. frissítésben
Ez a frissítés nem alkalmazható a StorSimple Cloud Appliancera (más néven virtuális eszközre). Új virtuális eszközöket kell létrehozni. 

## <a name="next-step"></a>Következő lépés
Megtudhatja, hogyan [telepítheti a 3. frissítést](./index.yml) a StorSimple-eszközön.