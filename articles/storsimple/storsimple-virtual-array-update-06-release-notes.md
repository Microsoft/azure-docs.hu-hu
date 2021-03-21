---
title: StorSimple Virtual Array Update 0,6 kibocsátási megjegyzések | Microsoft Docs
description: Az 0,6-es frissítést futtató StorSimple virtuális tömb kritikus megnyitási problémáit és megoldásait ismerteti.
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
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: 24bddb47a32a7b8ad4ea659d52c2d5b2730be9ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000912"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>StorSimple Virtual Array Update 0,6 kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtuális tömb frissítéseinek megoldott problémáit.

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. A StorSimple virtuális tömb üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Az 0,6-es frissítés megfelel a szoftver verziójának **10.0.10293.0**.

> [!IMPORTANT]
> - A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz inkurzív állásidőt okoz. A frissítés alkalmazásával kapcsolatos részletes információkat a 0,6-es [frissítés telepítése](storsimple-virtual-array-install-update-06.md)című cikkben talál.
>
> - Javasoljuk, hogy azonnal telepítse a 0,6-es frissítést, mivel a kritikus biztonsági javításokat tartalmaz.


## <a name="whats-new-in-the-update-06"></a>Az 0,6-es frissítés újdonságai
Az 0,6-es frissítés kritikus fontosságú frissítés, és azonnal üzembe kell helyezni. Ez a frissítés a következő javításokat tartalmazza: 

- **Windows biztonsági javítások** – ez a kiadás a **Windows kritikus biztonsági javításait** tartalmazza. Tekintse át az alábbi biztonsági frissítéseket a biztonsági problémákról és a kapcsolódó javításokról:
    - [December 2016 biztonsági csak a Windows 8,1 és a Windows Server 2012 R2 minőségi frissítése](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Március 2017 biztonsági csak a Windows 8,1 és a Windows Server 2012 R2 minőségi frissítése](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [2017. május 9. – KB4019213 (csak biztonsági frissítés)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- A **javítás visszaállítása** – korábbi kiadásokban hiba történt, ami megakadályozza a visszaállítás befejezését. Ez a hiba a jelen kiadásban lett javítva.


## <a name="issues-fixed-in-the-update-06"></a>Az 0,6-es frissítésben rögzített problémák

A következő táblázat az ebben a kiadásban kijavított problémák összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonság| Ez a kiadás kritikus Windowsos biztonsági frissítéseket tartalmaz. Javasoljuk, hogy azonnal telepítse ezt a frissítést.|
| 2 |Visszaállítás| A visszaállítás során egy olyan versenyhelyzet fordult elő, amely megakadályozza a visszaállítási feladatok befejezését. A hibajavítás megjavítja ezt a versenyhelyzet-feltételt.|


## <a name="known-issues-in-the-update-06"></a>Az 0,6-es frissítés ismert problémái

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
| **17.** |Frissítések |Ha a gyorsjavítást a helyi felhasználói felületen keresztül kísérli meg, a következő hibaüzenet jelenik meg: 2359302 (hex 0x240006), amely azt jelzi, hogy a gyorsjavítás már telepítve van az eszközön.   | |

## <a name="next-step"></a>Következő lépés
[Telepítse a 0,6](storsimple-virtual-array-install-update-06.md) -es frissítést a StorSimple virtuális tömbbe.

## <a name="references"></a>Hivatkozások
Régebbi kiadási megjegyzést keres? Ugrás:

* [StorSimple Virtual Array Update 0,5 kibocsátási megjegyzések](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 kibocsátási megjegyzések](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 kibocsátási megjegyzések](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 és 0,2 kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános elérhetőségi kibocsátási megjegyzései]()