---
title: StorSimple Virtual Array Update 1,0 kibocsátási megjegyzések
description: Az 1,0-es frissítést futtató StorSimple virtuális tömb kritikus megnyitási problémáit és megoldásait ismerteti.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 2ec88e4e97d45b27c0226198491b3adec6448496
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000895"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>StorSimple Virtual Array Update 1,0 kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtuális tömb frissítéseinek megoldott problémáit.

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. A StorSimple virtuális tömb üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Az 1,0-es frissítés megfelel a szoftver verziójának **10.0.10296.0**.

> [!IMPORTANT]
> - A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz inkurzív állásidőt okoz. A frissítés alkalmazásával kapcsolatos részletes információkat a 1,0-es [frissítés telepítése](storsimple-virtual-array-install-update-1.md)című cikkben talál.
>
> - Az 1. frissítés csak akkor érhető el a Azure Portalon keresztül, ha az eszközön az 0,6-es frissítés fut.

## <a name="whats-new-in-update-10"></a>Az 1,0-es frissítés újdonságai

**Az 1,0-es frissítés a StorSimple Eszközkezelő szolgáltatás hitelesítésével kapcsolatos változásokat tartalmaz, és a lehető leghamarabb üzembe kell helyezni.** Ez a frissítés a következő fejlesztéseket és hibajavításokat tartalmazza:

 - **Azure Active Directory (HRE) használata a StorSimple Eszközkezelő Service** -ben való hitelesítéshez – a 1,0-as frissítéstől kezdve Azure Active Directory a rendszer a StorSimple Eszközkezelő szolgáltatással való hitelesítésre használja. A régi hitelesítési mechanizmus elavulttá válik a 2017 decemberében. Az összes felhasználónak tartalmaznia kell az új hitelesítési URL-címeket a tűzfalszabályok szabályaiban. További információ: a [StorSimple virtuális tömb hálózati követelményeiben](storsimple-ova-system-requirements.md)felsorolt hitelesítési URL-címek.
 
    Ha a tűzfalszabályok nem tartalmazzák a hitelesítési URL-címet, a felhasználók kritikus riasztást kapnak arról, hogy a StorSimple-eszköz nem tudta hitelesíteni a szolgáltatást. Ha a felhasználók ezt a riasztást látják, fel kell venniük az új hitelesítési URL-címet. További információkért keresse fel a [StorSimple hálózati riasztások](storsimple-virtual-array-manage-alerts.md)című témakört.

 - **Teljesítmény-javítás** – számos hibajavítás történt a Felhőbeli olvasások, a csomagok és a rétegek sebességének javítása érdekében. Ennek eredményeképpen a biztonsági mentési és a visszaállítási teljesítmény is javult az iSCSI-és fájlkiszolgáló-eszközök esetében.

 - A **szemét-gyűjtemény továbbfejlesztése** – ez a kiadás olyan hibajavításokat tartalmaz, amelyek javítják a begyűjtési ciklusok teljesítményét, amikor az eszköz-és a Storage-fiók két távoli régióban található.

 - **Naplózás fejlesztése** – ez a kiadás a Garbage Collection és az I/O útvonalhoz kapcsolódó naplózási fejlesztéseket tartalmazza.


## <a name="issues-fixed-in-update-10"></a>Az 1,0-es frissítésben rögzített problémák

A következő táblázat az ebben a kiadásban kijavított problémák összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |HRE-alapú hitelesítés| Ez a kiadás olyan módosításokat tartalmaz, amelyek lehetővé teszik a HRE számára a StorSimple Eszközkezelő való hitelesítést.|
| 2 |Garbage-gyűjtemény| Ezt a problémát egy olyan ügyfél-telephelyen jelentették be, amelyben az eszköz-és a Storage-fiók különböző régiókban található, és az ügyfél időszakos hálózati hibát jelzett, ami hatással van a számlázásra. Ebben a kiadásban a probléma megoldódott. |
| 3 |Teljesítmény| Ez a kiadás olyan változásokat tartalmaz, amelyek a helyreállítás/a Felhőbeli olvasások/szintek teljesítményének növelését eredményezik.|
| 4 |Frissítés| Hiba történt a korábbi kiadásban, amely biztonsági mentési hibákat eredményezett egy ügyfél webhelyén. Ez a probléma ebben a kiadásban van kijavítva.|

## <a name="known-issues-in-update-10"></a>Az 1,0-es frissítés ismert problémái

Az alábbi táblázat összefoglalja a StorSimple virtuális tömb ismert problémáit, és tartalmazza az előző kiadásokból megjelent problémákat.

| Nem. | Szolgáltatás | Probléma | Áthidaló megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |Az előzetes verzióban létrehozott virtuális tömbök nem frissíthetők támogatott általánosan elérhető verzióra. |Ezeket a virtuális tömböket az általános rendelkezésre állási kiadáshoz a vész-helyreállítási (DR) munkafolyamat használatával kell végrehajtani. |
| **2.** |Kiépített adatlemez |Miután kiépített egy megadott méretű adatlemezt, és létrehozta a megfelelő StorSimple virtuális tömböt, nem szabad kibontania vagy csökkentenie az adatlemezt. Az eredmény az eszköz helyi szintjein lévő összes érték elvesztését eredményezi. | |
| **3.** |Csoportházirend |Ha egy eszköz tartományhoz csatlakozik, a csoportházirend alkalmazása hátrányosan befolyásolhatja az eszköz működését. |Győződjön meg arról, hogy a virtuális tömb a Active Directory saját szervezeti egységében van (OU), és nincs alkalmazva csoportházirend-objektum (GPO). |
| **4.** |Helyi webes felhasználói felület |Ha a fokozott biztonsági funkciók engedélyezve vannak az Internet Explorerben (IE ESC), előfordulhat, hogy bizonyos helyi webes felhasználói felületi lapok, például a hibaelhárítás vagy a karbantartás nem működnek megfelelően. Előfordulhat, hogy a lapok gombjai nem működnek. |Kikapcsolhatja a fokozott biztonsági funkciókat az Internet Explorerben. |
| **5.** |Helyi webes felhasználói felület |A Hyper-V virtuális gépen a webes felületen lévő hálózati adapterek 10 GB/s illesztőfelületként jelennek meg. |Ez a viselkedés a Hyper-V reflexiója. A Hyper-V mindig 10 GB/s-ot jelenít meg a virtuális hálózati adapterekhez. |
| **6.** |Lépcsőzetes kötetek vagy megosztások |A StorSimple rétegű kötetekkel dolgozó alkalmazások esetében nem támogatott a bájt-tartomány zárolása. Ha a bájtos tartomány zárolása engedélyezve van, a StorSimple-rétegek nem működnek. |Az ajánlott intézkedések a következők: <br></br>Kapcsolja ki az alkalmazás logikájában a kikapcsolási tartomány zárolását.<br></br>Válassza ezt az alkalmazást helyileg rögzített köteteken, a lépcsőzetes kötetek helyett.<br></br>*Figyelmeztetés*: Ha a helyileg rögzített kötetek és a bájtok tartományának zárolása engedélyezve van, a helyileg rögzített kötet a visszaállítás befejeződése előtt is online lehet. Ilyen esetekben, ha a visszaállítás folyamatban van, meg kell várnia, hogy a visszaállítás befejeződjön. |
| **7.** |Lépcsőzetes megosztások |A nagyméretű fájlok használata lassú kiugró szintet eredményezhet. |Nagyméretű fájlok használata esetén javasoljuk, hogy a legnagyobb fájl mérete kisebb legyen, mint a megosztások méretének 3%-a. |
| **8.** |A megosztások felhasznált kapacitása |Előfordulhat, hogy megtekintheti a megosztás felhasználását, ha nincs a megosztáson tárolt információ. Ez a felhasználás azért van, mert a megosztások felhasznált kapacitása metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Egy fájlkiszolgáló vész-helyreállítását csak a forrásoldali eszközével megegyező tartományba hajthatja végre. Ez a kiadás nem támogatja a más tartományokban lévő céleszköz vész-helyreállítását. |Ez egy későbbi kiadásban valósul meg. További információ: a [feladatátvétel és a vész-helyreállítás a StorSimple virtuális tömbben](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |A StorSimple virtuális tömbök nem kezelhetők a jelen kiadás Azure PowerShell. |A virtuális eszközök összes felügyeletét a Azure Portal és a helyi webes felületen keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömb eszköz konzolja csak az en-US billentyűzet formátumában fogadja el a bemenetet. | |
| **12.** |CHAP |A létrehozott CHAP-hitelesítő adatok nem távolíthatók el. Emellett, ha módosítja a CHAP hitelesítő adatait, a köteteket offline állapotba kell állítania, majd a módosítás érvénybe léptetéséhez online állapotba kell helyeznie azokat. |Ezt a problémát egy későbbi kiadás tárgyalja. |
| **13.** |iSCSI-kiszolgáló |Előfordulhat, hogy az iSCSI-kötethez tartozó "használt tároló" nem egyezik meg a StorSimple Eszközkezelő szolgáltatásban és az iSCSI-gazdagépen. |Az iSCSI-gazdagép rendelkezik a fájlrendszer nézettel.<br></br>Az eszköz látja a kiosztott blokkokat, amikor a kötet mérete elérte a maximális méretet. |
| **14.** |Fájlkiszolgáló |Ha egy mappában található fájlhoz egy másodlagos adatfolyam (ADS) tartozik, a HIRDETÉSEKET a rendszer nem készíti el, és nem állítja vissza a vész-helyreállítási, a klónozási és az elemszintű helyreállítás használatával. | |
| **15.** |Fájlkiszolgáló |A szimbolikus hivatkozások nem támogatottak. | |
| **16.** |Fájlkiszolgáló |A Windows titkosított fájlrendszer (EFS) által védett fájlok, amelyeket a rendszer a StorSimple Virtual Array fájlkiszolgálón másol vagy tárol, nem támogatott konfigurációt eredményez.  | |
| **17.** |Frissítések |Ha a gyorsjavítást a helyi felhasználói felületen keresztül kísérli meg, a következő hibaüzenet jelenik meg: 2359302 (hex 0x240006), amely azt jelzi, hogy a gyorsjavítás már telepítve van az eszközön.   | |
| **18.** |Frissítések |Ha a helyi webes KEZELŐFELÜLETtel telepíti az 1. frissítést a virtuális tömbön, meg kell győződnie arról, hogy az 0,6-es frissítést futtatja. Ha a 0,6-es frissítésnél kisebb verziót futtat, először telepítenie kell a 0,6-es frissítést, majd az 1. frissítést kell alkalmaznia. Ha közvetlenül telepíti a 1,0-es frissítést egy előzetes verziójú 0,6-es verzióról, néhány frissítést nem fog tudni kihagyni, és a figyelési diagramok nem fognak működni.   | |


## <a name="next-steps"></a>Következő lépések
[Telepítse a 1,0](storsimple-virtual-array-install-update-1.md) -es frissítést a StorSimple virtuális tömbbe.

## <a name="references"></a>Hivatkozások
Régebbi kiadási megjegyzést keres? Ugrás:
*  [StorSimple Virtual Array Update 0,6 kibocsátási megjegyzések](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 kibocsátási megjegyzések](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 kibocsátási megjegyzések](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 kibocsátási megjegyzések](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 és 0,2 kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános elérhetőségi kibocsátási megjegyzései](./storsimple-virtual-array-update-06-release-notes.md)