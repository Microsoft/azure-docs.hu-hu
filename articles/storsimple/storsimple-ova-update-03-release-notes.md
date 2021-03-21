---
title: StorSimple Virtual Array Update 0,3 kibocsátási megjegyzések
description: Az 0,3-es frissítést futtató StorSimple virtuális tömb kritikus megnyitási problémáit és megoldásait ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: 8301b45fe778bd3df7fc665db2662ba81d3e644f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966105"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple Virtual Array Update 0,3 kibocsátási megjegyzések
## <a name="overview"></a>Áttekintés
A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtuális tömb frissítéseinek megoldott problémáit.

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. A StorSimple virtuális tömb üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Az 0,3-es frissítés megfelel a szoftver verziójának **10.0.10288.0**.

> [!NOTE]
> A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz inkurzív állásidőt okoz.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Az 0,3-es frissítés újdonságai
Az 0,3-es frissítés elsősorban hibajavítási Build. Ebben a verzióban számos hiba történt a biztonsági mentési hibák miatt az előző verzióban.

## <a name="issues-fixed-in-the-update-03"></a>Az 0,3-es frissítésben rögzített problémák
A következő táblázat az ebben a kiadásban kijavított problémák összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonsági másolatok |Probléma merült fel a korábbi kiadásban, ahol a biztonsági mentések sikertelenek lesznek a fájlmegosztás esetében. Ha ez a probléma merült fel, a biztonsági mentési feladat meghiúsul, és kritikus riasztás történt a StorSimple Manager szolgáltatásban, hogy értesítse a felhasználót. Ez a probléma nem befolyásolta a megosztásokon vagy az adathozzáférésen alapuló adatvédelmet. A probléma okát azonosította és rögzítették ebben a kiadásban. <br></br> A javítás nem vonatkozik visszamenőlegesen azokra a megosztásokra, amelyek már látják ezt a problémát. A problémát tapasztaló ügyfeleknek először a 0,3-es frissítést kell alkalmazniuk, majd a probléma megoldásához a teljes rendszer biztonsági mentéséhez a Microsoft ügyfélszolgálatat kell felvennie. A Microsoft ügyfélszolgálata való kapcsolatfelvétel helyett az ügyfelek az érintett megosztások kifogástalan biztonsági mentésével is visszaállíthatják az új megosztást. |
| 2 |iSCSI |Hiba történt a korábbi kiadásban, ahol a kötetek eltűnnek az adatoknak a StorSimple virtuális tömbben lévő kötetre másolásakor. Ez a probléma ebben a kiadásban lett kijavítva. <br></br> A javítások csak az újonnan létrehozott köteteken lépnek életbe. A javítások nem vonatkoznak visszamenőlegesen a problémát már megtekintő kötetekre. Javasoljuk, hogy az érintett köteteket online állapotba hozza a klasszikus Azure portálon keresztül, végezze el a kötetek biztonsági mentését, majd állítsa vissza ezeket a köteteket az új kötetekre. |

## <a name="known-issues-in-the-update-03"></a>Az 0,3-es frissítés ismert problémái
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

## <a name="next-step"></a>Következő lépés
[Telepítse a 0,3](./storsimple-virtual-array-install-update-06.md) -es frissítést a StorSimple virtuális tömbbe.

## <a name="references"></a>Hivatkozások
Régebbi kiadási megjegyzést keres? Ugrás: 

* [StorSimple Virtual Array Update 0,1 és 0,2 kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános elérhetőségi kibocsátási megjegyzései](./storsimple-virtual-array-update-06-release-notes.md)