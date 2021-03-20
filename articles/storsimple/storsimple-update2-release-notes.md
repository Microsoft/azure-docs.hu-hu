---
title: StorSimple 8000 sorozat Update 2 kibocsátási megjegyzések | Microsoft Docs
description: A StorSimple 8000 Series Update 2 új funkcióit, problémáit és megkerülő megoldásait ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: b68e338c2675a3aeefda390d12ab2d82ea1c7a9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956653"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>StorSimple 8000 sorozat Update 2 kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés
A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 Series 2. frissítésével kapcsolatos kritikus nyitott problémákat. Emellett az ebben a kiadásban található StorSimple szoftver, illesztőprogram és lemezes belső vezérlőprogram-frissítések listáját is tartalmazzák. 

A 2. frissítés a 1,2-es frissítéssel bármely StorSimple-eszközön, illetve a 0,1-es frissítésen is alkalmazható. A 2. frissítéshez társított 6.3.9600.17673.

Mielőtt telepítené a frissítést a StorSimple-megoldásban, tekintse át a kibocsátási megjegyzésekben található információkat.

> [!IMPORTANT]
> * A frissítés telepítése körülbelül 4-7 órát vesz igénybe (beleértve a Windows-frissítéseket is). 
> * A 2. frissítés szoftverekkel, LSI illesztőprogrammal és SSD belső vezérlőprogram-frissítésekkel rendelkezik.
> * Az új kiadások esetében előfordulhat, hogy a frissítések nem jelennek meg azonnal, mert a frissítések fokozatos bevezetését hajtjuk végre. Várjon néhány napot, majd próbálkozzon újra a frissítések keresésével, mivel ezek hamarosan elérhetővé válnak.
> 
> 

## <a name="whats-new-in-update-2"></a>A 2. frissítés újdonságai
A 2. frissítés a következő új funkciókat mutatja be.

* **Helyileg rögzített kötetek** – a StorSimple 8000 sorozat korábbi kiadásaiban az adatblokkokat a használat alapján a felhőbe soroltuk. Nem garantálható, hogy a blokkok a helyi gépen maradnak. A 2. frissítésben a kötetek létrehozásakor a kötetet helyileg rögzítettként jelölheti meg, és a kötetből származó elsődleges adatok nem lesznek a felhőbe bontva. A helyileg rögzített kötetek pillanatképeit a rendszer továbbra is a felhőbe másolja a biztonsági mentés céljából, így a felhő felhasználható adatmobilitási és vész-helyreállítási célokra. Emellett módosíthatja a kötet típusát (azaz a többkötetes kötetek konvertálása helyileg rögzített kötetekre, és a helyileg rögzített kötetek a lépcsőzetesre konvertálhatók). 
* **StorSimple** – korábban a StorSimple 8000 sorozat a virtuális eszközt vész-helyreállítási vagy fejlesztési/tesztelési megoldásként helyezi üzembe. A virtuális eszköznek csak egyetlen modellje volt (1100-es modell). A 2. frissítés két virtuális eszköz modelljét mutatja be: 
  
  * 8010 (korábbi nevén 1100) – nincs változás; a kapacitása 30 TB, és az Azure standard Storage-t használja.
  * 8020 – kapacitása 64 TB, és a jobb teljesítmény érdekében az Azure Premium Storage-t használja.
    
    A virtuális eszközök modelljeihez egyetlen VHD tartozik (8010/8020). A virtuális eszköz első indításakor észleli a platform paramétereit, és alkalmazza a modell megfelelő verzióját.
* **Hálózatkezelési változások** – a 2. frissítés a következő hálózatkezelési funkciókat tartalmazza:
  
  * Több hálózati adapter is engedélyezhető a felhőhöz, így a feladatátvétel akkor fordulhat elő, ha a hálózati adapter meghibásodik.
  * Az Útválasztás fejlesztése a felhőben engedélyezve lévő blokkokhoz tartozó rögzített mérőszámokkal.
  * Feladatátvétel előtt online újrapróbálkozás a sikertelen erőforrásokról.
  * Új riasztások a szolgáltatási hibákhoz.
* A frissítések **frissítése** – a 1,2-es és korábbi frissítésekben a StorSimple 8000 sorozat két csatornán keresztül frissült: Windows Update a fürtözés, az iSCSI és így tovább, és Microsoft Update a bináris fájlok és a belső vezérlőprogram számára.
    A 2. frissítés Microsoft Update használ az összes frissítési csomaghoz. Ehhez a javításhoz vagy feladatátvételhez kevesebb idő szükséges. 
* **Belső vezérlőprogram frissítései** – a következő belső vezérlőprogram-frissítéseket tartalmazza:
  
  * LSI: lsi_sas2.sys termék verziója 2.00.72.10
  * Csak SSD (HDD-frissítések nélkül): XMGG, XGEG, KZ50, F6C2 és VR08
* **Proaktív támogatás** – a 2. frissítés lehetővé teszi a Microsoft számára további diagnosztikai adatok lekérését az eszközről. Ha az operatív csapatunk olyan eszközöket azonosít, amelyek problémákba ütköznek, akkor jobb, ha adatokat gyűjtünk az eszközről, és diagnosztizálják a problémákat. A **2. frissítés elfogadásával lehetővé teszi a proaktív támogatás megadását**.    

## <a name="issues-fixed-in-update-2"></a>A 2. frissítésben rögzített problémák
A következő táblázat a 2. frissítésekben rögzített problémák összegzését tartalmazza.    

| Nem. | Szolgáltatás | Probléma | A fizikai eszközre vonatkozik | A virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Hálózati adapterek |Az 1. frissítésre való frissítés után a StorSimple Manager szolgáltatás jelentett arról, hogy a data2 és a data3 portok egyik vezérlőn sem voltak sikertelenek. A probléma kijavítva. |Igen |Nem |
| 2 |Frissítések |Az 1. frissítésre való frissítés után a klasszikus Azure portálon több eszközön hallható riasztási riasztások fordultak elő. A probléma kijavítva. |Igen |Nem |
| 3 |Openstack-hitelesítés |Ha a Openstack-t felhőalapú szolgáltatóként használja, hibaüzenetet kaphat arról, hogy a Felhőbeli hitelesítési karakterlánc túl hosszú. Ezt a problémát megoldottuk. |Igen |Nem |

## <a name="known-issues-in-update-2"></a>A 2. frissítés ismert problémái
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
| 20 |Helyileg rögzített kötetek |Ha egy, a 1,2-as vagy korábbi frissítéssel létrehozott, többplatformos kötetet próbál alakítani egy helyileg rögzített kötetre, és az eszközön nincs elég hely, vagy ha a felhő meghibásodik, akkor a klón (ok) sérült lehet. |Ez a probléma csak a 2. frissítés előtti szoftverrel létrehozott és klónozott kötetek esetében fordul elő. Ez nem ritka forgatókönyv. | | |
| 21 |Kötet konvertálása |Ne frissítse a kötethez csatolt ACR-EK, amíg a kötet-átalakítás folyamatban van (a többhelyes rögzítés a helyileg rögzített vagy fordítva). A ACR-EK frissítése adatsérülést eredményezhet. |Ha szükséges, frissítse a ACR-EK a kötet konvertálása előtt, és ne végezzen további ACR-frissítéseket, amíg az átalakítás folyamatban van. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>A vezérlő és a belső vezérlőprogram frissítései a 2. frissítésben
Ez a kiadás frissíti az illesztőprogramot és a lemez belső vezérlőprogramot az eszközön.

* Az LSI belső vezérlőprogram frissítésével kapcsolatos további információkért lásd a Microsoft Tudásbázis 3121900. cikkét. 
* A lemezes belső vezérlőprogram frissítésével kapcsolatos további információkért lásd a Microsoft Tudásbázis 3121899. cikkét.

## <a name="virtual-device-updates-in-update-2"></a>Virtuális eszközök frissítései a 2. frissítésben
Ez a frissítés nem alkalmazható a virtuális eszközre. Új virtuális eszközöket kell létrehozni. 

## <a name="next-step"></a>Következő lépés
Megtudhatja, hogyan [telepítheti a 2. frissítést](./storsimple-8000-install-update-5.md) a StorSimple-eszközön.