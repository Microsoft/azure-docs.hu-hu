---
title: StorSimple Virtual Array Update 1,1 kibocsátási megjegyzések | Microsoft Docs
description: Az 1,1-es frissítést futtató StorSimple virtuális tömb kritikus megnyitási problémáit és megoldásait ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: alkohli
ms.openlocfilehash: df4722a4232686b0d981c190bdc154f0d1bec62e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960204"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>StorSimple Virtual Array Update 1,1 kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtuális tömb frissítéseinek megoldott problémáit.

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. A StorSimple virtuális tömb üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Az 1,1-es frissítés megfelel a szoftver verziójának **10.0.10307.0**.

> [!IMPORTANT]
> - A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz inkurzív állásidőt okoz. A frissítés alkalmazásával kapcsolatos részletes információkat a 1,1-es [frissítés telepítése](storsimple-virtual-array-install-update-11.md)című cikkben talál.
>
> - Az 1,1-es frissítés csak akkor érhető el a Azure Portalon keresztül, ha az eszközön a 1,0-es frissítés fut.

## <a name="whats-new-in-update-11"></a>Az 1,1-es frissítés újdonságai

Ez a frissítés a következő javításokat és hibajavításokat tartalmazza:

 - **Biztonsági mentési hibák** – továbbfejlesztettük a rugalmasságot a Felhőbeli hibák és a magas CPU-felhasználás terén.
 - **Naplózás** – a részletes mód naplózása módosult, amikor az eszköz támogatja a munkamenetet.


## <a name="issues-fixed-in-update-11"></a>Az 1,1-es frissítésben rögzített problémák

A következő táblázat az ebben a kiadásban kijavított problémák összegzését tartalmazza.

| Nem. | Funkció | Probléma |
| --- | --- | --- |
| 1 |Biztonsági másolatok| Ez a kiadás a Felhőbeli hibák és a magas CPU-használat rugalmasságának növelésével javítja a biztonsági mentési hibákat.|
| 2 |Naplózás| Ez a kiadás a naplózási módosításokat tartalmazza, miközben az eszköz támogatja a-munkamenetet a részletes módban.|


## <a name="known-issues-in-update-11"></a>Az 1,1-es frissítés ismert problémái

Az alábbi táblázat összefoglalja a StorSimple virtuális tömb ismert problémáit, és tartalmazza az előző kiadásokból megjelent problémákat.

| Nem. | Funkció | Probléma | Áthidaló megoldás/megjegyzések |
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
[Telepítse a 1,1](storsimple-virtual-array-install-update-11.md) -es frissítést a StorSimple virtuális tömbbe.

## <a name="references"></a>Referencia
Régebbi kiadási megjegyzést keres? Ugrás:
* [StorSimple Virtual Array Update 1,0 kibocsátási megjegyzések](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0,6 kibocsátási megjegyzések](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 kibocsátási megjegyzések](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 kibocsátási megjegyzések](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 kibocsátási megjegyzések](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 és 0,2 kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános elérhetőségi kibocsátási megjegyzései](./storsimple-virtual-array-update-06-release-notes.md)