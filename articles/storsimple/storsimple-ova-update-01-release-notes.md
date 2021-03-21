---
title: StorSimple virtuális tömb frissítése 0,2 & 0,1 kibocsátási megjegyzések
description: Az 0,2-es és a 0,1-es frissítést futtató StorSimple virtuális tömb kritikus megnyitási problémáit és megoldásait ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: 67d3fafb9b4b93ae52bdb09d673bf65cad30b816
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966156"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple Virtual Array Update 0,2 és 0,1 kibocsátási megjegyzések
## <a name="overview"></a>Áttekintés
A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtuális tömb frissítéseinek megoldott problémáit. (Microsoft Azure StorSimple virtuális tömböt a StorSimple helyszíni virtuális eszköznek vagy a StorSimple virtuális eszköznek is nevezzük.) 

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. A StorSimple virtuális eszköz üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Az 0,2-es frissítés megfelel a szoftver **10.0.10280.0**; Az 0,1-es frissítés a **10.0.10279.0** verziója. Az alábbi szakaszban az egyes frissítések változásai láthatók. 

> [!NOTE]
> A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz állásidőt von maga után.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Az 0,2-es frissítésben rögzített problémák
Az 0,2-es frissítés a 0,1-es frissítés összes módosítását tartalmazza az alábbi táblázatban ismertetett javítás mellett:

| Szolgáltatás | Probléma |
| --- | --- |
| Frissítések |A legutóbbi kiadásban a frissítések nem észlelhetők automatikusan a klasszikus Azure-portálon, ezért a helyi webes felhasználói felületet kell használnia a frissítések telepítéséhez. Ez a probléma ebben a kiadásban van kijavítva. Az 0,2-es frissítés telepítése után a klasszikus Azure portál használatával telepítheti a jövőbeli frissítéseket. |

## <a name="whats-new-in-the-update-01"></a>Az 0,1-es frissítés újdonságai
Az 0,1-es frissítés a következő hibajavításokat és javításokat tartalmazza. 

* **Továbbfejlesztett rugalmasság a Felhőbeli kimaradások** terén: ebben a kiadásban számos hibajavítás található a vész-helyreállítás, a biztonsági mentés, a visszaállítás és a rétegek megszakadása esetén a Felhőbeli kapcsolat megszakadása esetén. 
* **Továbbfejlesztett visszaállítási teljesítmény**: ebben a kiadásban olyan hibajavítások vannak, amelyek jelentősen csökkentették a visszaállítási feladatok befejezési idejét.
* **Automatizált lemezterület-visszanyerési optimalizálás**: Ha a rendszer törli az adatmennyiséget a dinamikusan kiosztott köteteken, a fel nem használt tárolási blokkokat vissza kell állítani. Ez a kiadás javította a terület visszanyerésének folyamatát a felhőből, ami azt eredményezi, hogy a fel nem használt terület gyorsabban elérhetővé válik az előző verziókhoz képest.
* **Új virtuális lemez lemezképei**: új VHD-, VHDX-és VMDK érhetők el a klasszikus Azure portálon keresztül. Ezeket a lemezképeket letöltheti a 0,1-es frissítés új eszközeinek kiépítéséhez.
* A **feladatok állapotának javítása a portálon**: a szoftver korábbi verziójában a feladat állapota jelentéskészítés a portálon nem volt részletes. Ez a probléma ebben a kiadásban van feloldva.
* **Tartományhoz való kapcsolódási élmény**: az eszköz tartományhoz való csatlakoztatásával és átnevezésével kapcsolatos hibajavítások.

## <a name="issues-fixed-in-the-update-01"></a>Az 0,1-es frissítésben rögzített problémák
A következő táblázat az ebben a kiadásban kijavított problémák összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |VMDK |Egyes VMware-verziók esetében az operációsrendszer-lemez ritka riasztást okoz, és megszakad a normál működés. Ez a kiadásban megoldódott. |
| 2 |iSCSI-kiszolgáló |A legutóbbi kiadásban a felhasználónak meg kell adnia egy átjárót a StorSimple virtuális eszköz minden engedélyezett hálózati adapteréhez. Ez a verzió úgy módosul, hogy a felhasználónak legalább egy átjárót be kell állítania az összes engedélyezett hálózati adapterhez. |
| 3 |Támogatási csomag |A szoftver korábbi verziójában a támogatási csomagok gyűjtése sikertelen volt, ha a csomag mérete nagyobb, mint 1 GB. Ez a probléma ebben a kiadásban van kijavítva. |
| 4 |Felhőbeli hozzáférés |A legutóbbi kiadásban, ha a StorSimple virtuális tömb nem rendelkezett hálózati kapcsolattal, és a szolgáltatás újraindult, a helyi felhasználói felület csatlakozási problémákba ütközik. Ez a probléma ebben a kiadásban van kijavítva. |
| 5 |Figyelési diagramok |Az előző kiadásban az eszköz feladatátvételét követően a Felhőbeli kapacitás kihasználtsági diagramjai helytelen értékeket jeleztek a klasszikus Azure portálon. Ez a jelenlegi kiadásban van rögzítve. |

## <a name="known-issues-in-the-update-01"></a>Az 0,1-es frissítés ismert problémái
Az alábbi táblázat összefoglalja a StorSimple virtuális tömb ismert problémáit, és tartalmazza az előző kiadásokból megjelent problémákat. **Az ebben a kiadásban feljegyzett problémák csillaggal vannak megjelölve. A listában szereplő összes probléma csaknem a StorSimple Virtual Array GA-kiadásával történt.**

| Nem. | Szolgáltatás | Probléma | Áthidaló megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |Az előzetes verzióban létrehozott virtuális eszközök nem frissíthetők támogatott általánosan elérhető verzióra. |Ezeket a virtuális eszközöket egy vész-helyreállítási (DR) munkafolyamat használatával kell átadni az általános elérhetőségi kiadáshoz. |
| **2.** |Kiépített adatlemez |Miután kiépített egy adott méretű adatlemezt, és létrehozta a megfelelő StorSimple virtuális eszközt, az adatlemezt nem szabad kibontani vagy csökkentenie. Ennek eredményeképpen az eszköz helyi szintjein lévő összes érték elvesztését fogja eredményezni. | |
| **3.** |Csoportházirend |Ha egy eszköz tartományhoz csatlakozik, a csoportházirend alkalmazása hátrányosan befolyásolhatja az eszköz működését. |Győződjön meg arról, hogy a virtuális tömb a Active Directory saját szervezeti egységében van (OU), és nincs alkalmazva csoportházirend-objektum (GPO). |
| **4.** |Helyi webes felhasználói felület |Ha a fokozott biztonsági funkciók engedélyezve vannak az Internet Explorerben (IE ESC), előfordulhat, hogy bizonyos helyi webes felhasználói felületi lapok, például a hibaelhárítás vagy a karbantartás nem működnek megfelelően. Előfordulhat, hogy a lapok gombjai nem működnek. |Kikapcsolhatja a fokozott biztonsági funkciókat az Internet Explorerben. |
| **5.** |Helyi webes felhasználói felület |A Hyper-V virtuális gépen a webes felületen lévő hálózati adapterek 10 GB/s illesztőfelületként jelennek meg. |Ez a viselkedés a Hyper-V reflexiója. A Hyper-V mindig 10 GB/s-ot jelenít meg a virtuális hálózati adapterekhez. |
| **6.** |Lépcsőzetes kötetek vagy megosztások |A StorSimple rétegű kötetekkel dolgozó alkalmazások esetében nem támogatott a bájt-tartomány zárolása. Ha a bájtos tartomány zárolása engedélyezve van, a StorSimple-rétegek nem fognak működni. |Az ajánlott intézkedések a következők: <br></br>Kapcsolja ki az alkalmazás logikájában a kikapcsolási tartomány zárolását.<br></br>Válassza ezt az alkalmazást helyileg rögzített köteteken, a lépcsőzetes kötetek helyett.<br></br>*Figyelmeztetés*: Ha a helyileg rögzített kötetek és a bájtos címtartomány zárolása engedélyezve van, vegye figyelembe, hogy a helyileg rögzített kötet a visszaállítás befejeződése előtt is online lehet. Ilyen esetekben, ha a visszaállítás folyamatban van, meg kell várnia, hogy a visszaállítás befejeződjön. |
| **7.** |Lépcsőzetes megosztások |A nagyméretű fájlok használata lassú kiugró szintet eredményezhet. |Nagyméretű fájlok használata esetén javasoljuk, hogy a legnagyobb fájl mérete kisebb legyen, mint a megosztások méretének 3%-a. |
| **8.** |A megosztások felhasznált kapacitása |A megosztáson tárolt adatmennyiség hiányában megtekintheti a megosztás felhasználását. Ennek az az oka, hogy a megosztások felhasznált kapacitása metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Egy fájlkiszolgáló vész-helyreállítását csak a forrásoldali eszközével megegyező tartományba hajthatja végre. Ez a kiadás nem támogatja a más tartományokban lévő céleszköz vész-helyreállítását. |Ez egy későbbi kiadásban lesz implementálva. |
| **10.** |Azure PowerShell |A StorSimple virtuális eszközei nem kezelhetők a jelen kiadás Azure PowerShell. |A virtuális eszközök összes felügyeletét a klasszikus Azure portálon és a helyi webes felületen keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömb eszköz konzolja csak az en-US billentyűzet formátumában fogadja el a bemenetet. | |
| **12.** |CHAP |A létrehozott CHAP-hitelesítő adatok nem távolíthatók el. Emellett, ha módosítja a CHAP hitelesítő adatait, a köteteket offline állapotba kell állítania, majd a módosítás érvénybe léptetéséhez online állapotba kell helyeznie azokat. |Ezeket egy későbbi kiadásban fogjuk kezelni. |
| **13.** |iSCSI-kiszolgáló |Előfordulhat, hogy az iSCSI-kötet "használt tárolója" nem egyezik meg a StorSimple Manager szolgáltatásban és az iSCSI-gazdagépen. |Az iSCSI-gazdagép rendelkezik a fájlrendszer nézettel.<br></br>Az eszköz látja a kiosztott blokkokat, amikor a kötet mérete elérte a maximális méretet. |
| **14.** |Fájlkiszolgáló * |Ha egy mappában található fájlhoz egy másodlagos adatfolyam (ADS) tartozik, a HIRDETÉSEKET a rendszer nem készíti el, és nem állítja vissza a vész-helyreállítási, a klónozási és az elemszintű helyreállítás használatával. | |

## <a name="next-step"></a>Következő lépés
[Telepítse a frissítéseket](./storsimple-virtual-array-install-update-06.md) a StorSimple virtuális tömbbe.