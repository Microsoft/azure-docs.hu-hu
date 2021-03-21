---
title: StorSimple Virtual Array Update 0,4 kibocsátási megjegyzések | Microsoft Docs
description: Az 0,4-es frissítést futtató StorSimple virtuális tömb kritikus megnyitási problémáit és megoldásait ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: 392e930dcb69392b6f9425abba0efa516d5c82b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967057"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple Virtual Array Update 0,4 kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtuális tömb frissítéseinek megoldott problémáit.

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. A StorSimple virtuális tömb üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Az 0,4-es frissítés megfelel a szoftver verziójának **10.0.10289.0**.

> [!NOTE]
> A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz inkurzív állásidőt okoz.


## <a name="whats-new-in-the-update-04"></a>Az 0,4-es frissítés újdonságai
Az 0,4-es frissítés elsősorban egy hibajavítási Build, amely néhány fejlesztéssel párosul. Ebben a verzióban számos hiba történt a biztonsági mentési hibák miatt az előző verzióban. A legfontosabb fejlesztések és hibajavítások a következők:

- A **biztonsági mentés teljesítményének továbbfejlesztése** – ez a kiadás a biztonsági mentési teljesítmény javítása érdekében több kulcsfontosságú fejlesztést hajtott végre. Ennek eredményeképpen a nagy mennyiségű fájlt érintő biztonsági másolatok a teljes és növekményes biztonsági mentések során jelentős mértékű csökkentést jelentenek.

- **Továbbfejlesztett visszaállítási teljesítmény** – ez a kiadás olyan fejlesztéseket tartalmaz, amelyek jelentős mértékben javítják a visszaállítási teljesítményt nagy számú fájl használata esetén. Ha 2-4 millió fájlt használ, javasoljuk, hogy hozzon létre egy virtuális tömböt 16 GB RAM-mal a tökéletesítések megtekintéséhez. Ha kevesebb mint 2 000 000 fájlt használ, a virtuális gép minimális követelménye továbbra is 8 GB RAM.

- A **támogatási csomag fejlesztései** – a fejlesztések közé tartozik a lemez, a processzor, a memória, a hálózat és a felhő statisztikáinak beléptetése a támogatási csomagba, ezzel javítva az eszközök problémáinak diagnosztizálására/hibakeresésére szolgáló folyamatot.

- **Helyileg rögzített iSCSI-kötetek korlátozása 200 GB** -ra – a helyileg rögzített kötetek esetében javasoljuk, hogy a StorSimple virtuális tömbben korlátozza a 200 GB-os iSCSI-kötetet. A többplatformos kötetek helyi foglalása továbbra is a kiépített kötet méretének 10%-a, de 200 GB-nál többre van korlátozva. 

- **Biztonsági mentéssel kapcsolatos hibajavítások** – a szoftverek korábbi verzióiban a biztonsági mentések miatt problémák léptek fel. Ezek a hibák a jelen kiadásban jelentek meg.


## <a name="issues-fixed-in-the-update-04"></a>Az 0,4-es frissítésben rögzített problémák

A következő táblázat az ebben a kiadásban kijavított problémák összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonsági mentési teljesítmény|A korábbi kiadásokban a nagy mennyiségű fájlt érintő biztonsági másolatok hosszú időt vesznek igénybe a befejezésig (napokban). Ebben a kiadásban a teljes és növekményes biztonsági mentések a befejezéshez szükséges idő jelentős csökkenését látják. |
| 2 |Támogatási csomag|A lemez, a processzor, a memória, a hálózat és a Felhőbeli statisztika mostantól a támogatási naplókba van bejelentkezve, így a támogatási csomagok rendkívül hatékonyak az eszköz hibáinak elhárítása során.|
| 3 |Backup |A korábbi kiadásokban a hosszú ideig futó biztonsági mentések lemezterületet okozhatnak az eszközön, ami biztonsági mentési hibákat eredményezhet. Ennek a hibának a címzettjei a jelen kiadásban legfeljebb 5 biztonsági mentést végezhetnek egyszerre.|
| 4 |iSCSI | A korábbi kiadásokban a többszintes vagy helyileg rögzített kötetek helyi foglalása a kiosztott kötet méretének 10%-a volt. Ebben a kiadásban az összes iSCSI-kötet (helyileg rögzített vagy lépcsőzetes) helyi foglalása 10%-ra van korlátozva, legfeljebb 200 GB-ig (a 2 TB-nál nagyobb rétegű kötetek esetében), így több helyet szabadíthat fel a helyi lemezen. Javasoljuk, hogy ebben a kiadásban a helyileg rögzített kötetek 200 GB-ra legyenek korlátozva.|


## <a name="known-issues-in-the-update-04"></a>Az 0,4-es frissítés ismert problémái

Az alábbi táblázat összefoglalja a StorSimple virtuális tömb ismert problémáit, és tartalmazza az előző kiadásokból megjelent problémákat. 

| Nem. | Szolgáltatás | Probléma | Áthidaló megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |Az előzetes verzióban létrehozott virtuális eszközök nem frissíthetők támogatott általánosan elérhető verzióra. |Ezeket a virtuális eszközöket egy vész-helyreállítási (DR) munkafolyamat használatával kell átadni az általános elérhetőségi kiadáshoz. |
| **2.** |Kiépített adatlemez |Miután kiépített egy adott méretű adatlemezt, és létrehozta a megfelelő StorSimple virtuális eszközt, az adatlemezt nem szabad kibontani vagy csökkentenie. Az eredmény az eszköz helyi szintjein lévő összes érték elvesztését eredményezi. | |
| **3.** |Csoportházirend |Ha egy eszköz tartományhoz csatlakozik, a csoportházirend alkalmazása hátrányosan befolyásolhatja az eszköz működését. |Győződjön meg arról, hogy a virtuális tömb a Active Directory saját szervezeti egységében van (OU), és nincs alkalmazva csoportházirend-objektum (GPO). |
| **4.** |Helyi webes felhasználói felület |Ha a fokozott biztonsági funkciók engedélyezve vannak az Internet Explorerben (IE ESC), előfordulhat, hogy bizonyos helyi webes felhasználói felületi lapok, például a hibaelhárítás vagy a karbantartás nem működnek megfelelően. Előfordulhat, hogy a lapok gombjai nem működnek. |Kikapcsolhatja a fokozott biztonsági funkciókat az Internet Explorerben. |
| **5.** |Helyi webes felhasználói felület |A Hyper-V virtuális gépen a webes felületen lévő hálózati adapterek 10 GB/s illesztőfelületként jelennek meg. |Ez a viselkedés a Hyper-V reflexiója. A Hyper-V mindig 10 GB/s-ot jelenít meg a virtuális hálózati adapterekhez. |
| **6.** |Lépcsőzetes kötetek vagy megosztások |A StorSimple rétegű kötetekkel dolgozó alkalmazások esetében nem támogatott a bájt-tartomány zárolása. Ha a bájtos tartomány zárolása engedélyezve van, a StorSimple-rétegek nem működnek. |Az ajánlott intézkedések a következők: <br></br>Kapcsolja ki az alkalmazás logikájában a kikapcsolási tartomány zárolását.<br></br>Válassza ezt az alkalmazást helyileg rögzített köteteken, a lépcsőzetes kötetek helyett.<br></br>*Figyelmeztetés*: Ha a helyileg rögzített kötetek és a bájtok tartományának zárolása engedélyezve van, a helyileg rögzített kötet a visszaállítás befejeződése előtt is online lehet. Ilyen esetekben, ha a visszaállítás folyamatban van, meg kell várnia, hogy a visszaállítás befejeződjön. |
| **7.** |Lépcsőzetes megosztások |A nagyméretű fájlok használata lassú kiugró szintet eredményezhet. |Nagyméretű fájlok használata esetén javasoljuk, hogy a legnagyobb fájl mérete kisebb legyen, mint a megosztások méretének 3%-a. |
| **8.** |A megosztások felhasznált kapacitása |Előfordulhat, hogy megtekintheti a megosztás felhasználását, ha nincs a megosztáson tárolt információ. Ennek az az oka, hogy a megosztások felhasznált kapacitása metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Egy fájlkiszolgáló vész-helyreállítását csak a forrásoldali eszközével megegyező tartományba hajthatja végre. Ez a kiadás nem támogatja a más tartományokban lévő céleszköz vész-helyreállítását. |Ez egy későbbi kiadásban valósul meg. |
| **10.** |Azure PowerShell |A StorSimple virtuális eszközei nem kezelhetők a jelen kiadás Azure PowerShell. |A virtuális eszközök összes felügyeletét a klasszikus Azure portálon és a helyi webes felületen keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömb eszköz konzolja csak az en-US billentyűzet formátumában fogadja el a bemenetet. | |
| **12.** |CHAP |A létrehozott CHAP-hitelesítő adatok nem távolíthatók el. Emellett, ha módosítja a CHAP hitelesítő adatait, a köteteket offline állapotba kell állítania, majd a módosítás érvénybe léptetéséhez online állapotba kell helyeznie azokat. |Ezt a problémát egy későbbi kiadás tárgyalja. |
| **13.** |iSCSI-kiszolgáló |Előfordulhat, hogy az iSCSI-kötet "használt tárolója" nem egyezik meg a StorSimple Manager szolgáltatásban és az iSCSI-gazdagépen. |Az iSCSI-gazdagép rendelkezik a fájlrendszer nézettel.<br></br>Az eszköz látja a kiosztott blokkokat, amikor a kötet mérete elérte a maximális méretet. |
| **14.** |Fájlkiszolgáló |Ha egy mappában található fájlhoz egy másodlagos adatfolyam (ADS) tartozik, a HIRDETÉSEKET a rendszer nem készíti el, és nem állítja vissza a vész-helyreállítási, a klónozási és az elemszintű helyreállítás használatával. | |
| **15.** |Fájlkiszolgáló |A szimbolikus hivatkozások nem támogatottak. | |
| **16.** |Fájlkiszolgáló |A Windows titkosított fájlrendszer (EFS) által védett fájlok, amelyeket a rendszer a StorSimple Virtual Array fájlkiszolgálón másol vagy tárol, nem támogatott konfigurációt eredményez.  | |

## <a name="next-step"></a>Következő lépés
[Telepítse a 0,4](storsimple-virtual-array-install-update-04.md) -es frissítést a StorSimple virtuális tömbbe.

## <a name="references"></a>Hivatkozások
Régebbi kiadási megjegyzést keres? Ugrás: 

* [StorSimple Virtual Array Update 0,3 kibocsátási megjegyzések](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 és 0,2 kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános elérhetőségi kibocsátási megjegyzései](./storsimple-virtual-array-update-06-release-notes.md)