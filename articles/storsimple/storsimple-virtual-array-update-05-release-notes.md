---
title: StorSimple Virtual Array Update 0,5 kibocsátási megjegyzések | Microsoft Docs
description: Az 0,5-es frissítést futtató StorSimple virtuális tömb kritikus megnyitási problémáit és megoldásait ismerteti.
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
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: f70cf96baed274eaf260832bd83ab0e2c91f3683
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967040"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>StorSimple Virtual Array Update 0,5 kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtuális tömb frissítéseinek megoldott problémáit.

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. A StorSimple virtuális tömb üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Az 0,5-es frissítés megfelel a szoftver verziójának **10.0.10290.0**.

> [!NOTE]
> A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz inkurzív állásidőt okoz. A frissítés alkalmazásával kapcsolatos részletes információkat a 0,5-es [frissítés telepítése](storsimple-virtual-array-install-update-05.md)című cikkben talál.


## <a name="whats-new-in-the-update-05"></a>Az 0,5-es frissítés újdonságai
Az 0,5-es frissítés elsősorban hibajavítási Build. A legfontosabb fejlesztések és hibajavítások a következők:

- A **biztonsági mentés rugalmasságának továbbfejlesztése** – ez a kiadás olyan javításokat tartalmaz, amelyek javítják a biztonsági mentési rugalmasságot. A korábbi kiadásokban a biztonsági mentések csak bizonyos kivételek esetén lettek újrapróbálkozva. Ez a kiadás újrapróbálkozik az összes biztonsági mentési kivétellel, és a biztonsági mentések rugalmasabbak.

- A **Storage-használat figyelésének frissítései** – a 2017-es, a StorSimple-alapú virtuális eszközök adatsorozatának tárolására vonatkozó figyelési szolgáltatás kivonásra kerül. Ez az 0,4-es vagy annál kisebb frissítést futtató összes virtuális tömb figyelési diagramjaira vonatkozik. Ez a frissítés tartalmazza azokat a módosításokat, amelyek szükségesek ahhoz, hogy továbbra is használhassa a tárterület-használat figyelését a Azure Portal. Telepítse ezt a kritikus frissítést a 2017. június 30. előtt a figyelés funkció használatának folytatásához.


## <a name="issues-fixed-in-the-update-05"></a>Az 0,5-es frissítésben rögzített problémák

A következő táblázat az ebben a kiadásban kijavított problémák összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonsági mentés rugalmassága| A korábbi kiadásokban a biztonsági mentések csak bizonyos kivételek esetén lettek újrapróbálkozva. Ez a kiadás egy javítást tartalmaz, amellyel a biztonsági mentések rugalmasabbak, ha újrapróbálkozik az összes biztonsági mentési kivétellel.|
| 2 |Figyelés| A StorSimple virtuális eszköz adatsorozatának tárterület-használati figyelése 2017. június 30-ig lesz elavult. Ez a művelet hatással van a figyelési diagramokra a StorSimple Eszközkezelő StorSimple virtuális tömbökön futó szolgáltatásán (1200 modell). Ez a kiadás olyan frissítéseket tartalmaz, amelyek lehetővé teszik a felhasználó számára, hogy továbbra is használhassa a tárterület-használat figyelését a virtuális tömbökön a 2017. június 30. után.|
| 3 |Fájlkiszolgáló| A korábbi kiadásokban a felhasználók véletlenül nem másolhatnak titkosított fájlokat a virtuális tömbbe. Ez a kiadás egy javítást tartalmaz, amely nem teszi lehetővé a titkosított fájlok másolását a virtuális tömbbe. Ha az eszköz a frissítés előtt már rendelkezik titkosított fájlokkal, a biztonsági mentések továbbra is sikertelenek lesznek, amíg a rendszer nem törli az összes titkosított fájlt. |


## <a name="known-issues-in-the-update-05"></a>Az 0,5-es frissítés ismert problémái

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
| **8.** |A megosztások felhasznált kapacitása |Előfordulhat, hogy megtekintheti a megosztás felhasználását, ha nincs a megosztáson tárolt információ. Ez a felhasználás azért van, mert a megosztások felhasznált kapacitása metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Egy fájlkiszolgáló vész-helyreállítását csak a forrásoldali eszközével megegyező tartományba hajthatja végre. Ez a kiadás nem támogatja a más tartományokban lévő céleszköz vész-helyreállítását. |Ez egy későbbi kiadásban valósul meg. További információ: a [feladatátvétel és a vész-helyreállítás a StorSimple virtuális tömbben](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |A StorSimple virtuális eszközei nem kezelhetők a jelen kiadás Azure PowerShell. |A virtuális eszközök összes felügyeletét a Azure Portal és a helyi webes felületen keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömb eszköz konzolja csak az en-US billentyűzet formátumában fogadja el a bemenetet. | |
| **12.** |CHAP |A létrehozott CHAP-hitelesítő adatok nem távolíthatók el. Emellett, ha módosítja a CHAP hitelesítő adatait, a köteteket offline állapotba kell állítania, majd a módosítás érvénybe léptetéséhez online állapotba kell helyeznie azokat. |Ezt a problémát egy későbbi kiadás tárgyalja. |
| **13.** |iSCSI-kiszolgáló |Előfordulhat, hogy az iSCSI-kötethez tartozó "használt tároló" nem egyezik meg a StorSimple Eszközkezelő szolgáltatásban és az iSCSI-gazdagépen. |Az iSCSI-gazdagép rendelkezik a fájlrendszer nézettel.<br></br>Az eszköz látja a kiosztott blokkokat, amikor a kötet mérete elérte a maximális méretet. |
| **14.** |Fájlkiszolgáló |Ha egy mappában található fájlhoz egy másodlagos adatfolyam (ADS) tartozik, a HIRDETÉSEKET a rendszer nem készíti el, és nem állítja vissza a vész-helyreállítási, a klónozási és az elemszintű helyreállítás használatával. | |
| **15.** |Fájlkiszolgáló |A szimbolikus hivatkozások nem támogatottak. | |
| **16.** |Fájlkiszolgáló |A Windows titkosított fájlrendszer (EFS) által védett fájlok, amelyeket a rendszer a StorSimple Virtual Array fájlkiszolgálón másol vagy tárol, nem támogatott konfigurációt eredményez.  | |

## <a name="next-step"></a>Következő lépés
[Telepítse a 0,5](storsimple-virtual-array-install-update-05.md) -es frissítést a StorSimple virtuális tömbbe.

## <a name="references"></a>Hivatkozások
Régebbi kiadási megjegyzést keres? Ugrás:

* [StorSimple Virtual Array Update 0,4 kibocsátási megjegyzések](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 kibocsátási megjegyzések](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 és 0,2 kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános elérhetőségi kibocsátási megjegyzései](./storsimple-virtual-array-update-06-release-notes.md)