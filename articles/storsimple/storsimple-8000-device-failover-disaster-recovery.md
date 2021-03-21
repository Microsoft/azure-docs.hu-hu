---
title: Feladatátvétel és vész-helyreállítás a StorSimple 8000 Series eszközön
description: Ismerje meg, hogyan végezhet feladatátvételt a StorSimple-eszközön, egy másik fizikai eszközön vagy egy felhőalapú berendezésen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: dffa059b18e159d04b5e3bb8555dabf801ede692
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017117"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Feladatátvétel és vészhelyreállítás StorSimple 8000 sorozatú eszközök esetében

## <a name="overview"></a>Áttekintés

Ez a cikk az StorSimple 8000 sorozatú eszközök eszköz-feladatátvételi funkcióját ismerteti, valamint azt, hogy ez a funkció hogyan használható a StorSimple-eszközök helyreállítására vészhelyzet esetén. A StorSimple az eszköz feladatátvételét használja az adatközpontban lévő forrás-eszközről egy másik eszközre való Migrálás során. A jelen cikkben található útmutatás a StorSimple 8000 sorozatú fizikai eszközökre és a 3. frissítést és újabb verziókat futtató felhőalapú készülékekre vonatkozik.

A StorSimple az **eszközök** panel használatával indítja el az eszköz feladatátvételi funkcióját vészhelyzet esetén. Ez a panel a StorSimple Eszközkezelő szolgáltatáshoz csatlakoztatott összes StorSimple-eszközt listázza.

![Eszközök panel](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Vész-helyreállítás (DR) és eszköz feladatátvétele

Vész-helyreállítási (DR) forgatókönyv esetén az elsődleges eszköz működése leáll. A StorSimple az elsődleges eszközt használja _forrásként_ , és áthelyezi a kapcsolódó Felhőbeli adategységeket egy másik _céleszköz_ . Ezt a folyamatot *feladatátvételnek* nevezzük. A következő ábra a feladatátvétel folyamatát szemlélteti.

![Mi történik az eszköz feladatátvételével?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

A feladatátvétel céljának eszköze lehet fizikai eszköz, vagy akár egy felhőalapú készülék is. Előfordulhat, hogy a célként megadott eszköz azonos vagy egy másik földrajzi helyen található, mint a forrásoldali eszköz.

A feladatátvétel során kiválaszthatja a kötetek tárolóit az áttelepítéshez. A StorSimple ezután módosítja a kötet-tárolók tulajdonosát a forrásoldali eszközről a céleszköz. Miután a mennyiségi tárolók megváltoztatják a tulajdonjogot, a StorSimple törli ezeket a tárolókat a forrás eszközről. A törlés befejezése után visszatérhet a céleszköz. A feladat- _visszavételi_ feladat visszaküldi a tulajdonjogot az eredeti forrásoldali eszközre.

### <a name="cloud-snapshot-used-during-device-failover"></a>Az eszköz feladatátvétele során használt Felhőbeli pillanatkép

A DR-t követően a legutóbbi Felhőbeli biztonsági mentés használatával visszaállíthatja az adattároló eszközét. A Felhőbeli pillanatképekkel kapcsolatos további információkért lásd: [a StorSimple Eszközkezelő szolgáltatás használata manuális biztonsági mentéshez](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

A StorSimple 8000 sorozaton a biztonsági mentési szabályzatok a biztonsági másolatokhoz vannak társítva. Ha ugyanahhoz a kötethez több biztonsági mentési szabályzat is van, akkor a StorSimple kiválasztja a legtöbb kötetet tartalmazó biztonsági mentési házirendet. A StorSimple ezután a kiválasztott biztonsági mentési szabályzat legutóbbi biztonsági mentését használja a céleszköz adatainak visszaállításához.

Tegyük fel, hogy van két biztonsági mentési szabályzat, a *defaultPol* és a *customPol*:

* *defaultPol*: egy kötet, *Vol1*, naponta fut, 10:30 órakor.
* *customPol*: négy kötet, *Vol1*, *vol2*, *vol3*, *vol4*, naponta fut, 10:00 órakor.

Ebben az esetben a StorSimple rangsorolja az összeomlás-konzisztencia szolgáltatást, és a *customPol* használja, mivel több kötetet tartalmaz. A szabályzat legutóbbi biztonsági másolata az adatok visszaállítására szolgál. A biztonsági mentési szabályzatok létrehozásával és kezelésével kapcsolatos további információkért látogasson el [a StorSimple Eszközkezelő szolgáltatás használatára a biztonsági mentési házirendek kezeléséhez](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Gyakori megfontolandó szempontok az eszköz-feladatátvétellel kapcsolatban

Az eszköz feladatátvétele előtt tekintse át a következő információkat:

* Az eszköz feladatátvételének elindítása előtt a mennyiségi tárolókban lévő összes kötetnek offline állapotban kell lennie. Nem tervezett feladatátvétel esetén a StotSimple-kötetek automatikusan offline állapotba kerülnek. Ha azonban tervezett feladatátvételt hajt végre (a DR-teszt teszteléséhez), akkor az összes kötetet offline állapotba kell helyeznie.
* A DR-hez csak a társított Felhőbeli pillanatképtel rendelkező kötetek vannak felsorolva. Az adatok helyreállításához legalább egy olyan kötet-tárolónak kell tartoznia, amelyhez hozzá van rendelve egy Felhőbeli pillanatkép.
* Ha vannak olyan Felhőbeli Pillanatképek, amelyek több mennyiségi tárolóra kiterjednek, a StorSimple készletként adja át ezeket a mennyiségi tárolókat. Ritka példányban, ha vannak olyan helyi Pillanatképek, amelyek több mennyiségi tárolóra terjednek ki, de a kapcsolódó Felhőbeli Pillanatképek nem, a StorSimple a helyi Pillanatképek feladatátvételét hajtja végre, és a helyi adatvesztést a DR. után elvész.
* A DR számára elérhető céleszköz olyan eszközök, amelyeken elegendő hely van a kiválasztott mennyiségi tárolók elhelyezésére. Minden olyan eszköz, amely nem rendelkezik elegendő hellyel, nem jelenik meg célként megadott eszközként.
* A DR (korlátozott időtartamra szóló) után az adatelérési teljesítmény jelentős hatással lehet, mivel az eszköznek a felhőből kell hozzáférnie az adatokhoz, és helyileg kell tárolnia azokat.

#### <a name="device-failover-across-software-versions"></a>Eszköz feladatátvétele a szoftverek verziói között

Az üzemelő példányok StorSimple Eszközkezelő szolgáltatása több, fizikai és Felhőbeli eszközzel is rendelkezhet, amelyek mindegyike különböző szoftveres verziót futtat.

A következő táblázat segítségével meghatározhatja, hogy feladatátvételt hajthat végre, vagy visszatérhet egy másik, eltérő verziót futtató eszközre, és hogy a kötetek típusai hogyan viselkednek a DR-ben.

#### <a name="failover-and-failback-across-software-versions"></a>Feladatátvétel és feladat-visszavétel a szoftver verziói között

| Feladatátvétel/feladat-visszavétel | Fizikai eszköz | Felhőalapú készülék |
| --- | --- | --- |
| 3. frissítés a 4. frissítéshez |A többszintes kötetek átadása lépcsőzetesen történik. <br></br>A helyileg rögzített kötetek feladatátvétele helyileg rögzítettként történik. <br></br> A feladatátvételt követően, amikor pillanatképet készít a 4. frissítés eszközről, a [hő-alapú nyomkövetési](storsimple-update4-release-notes.md#whats-new-in-update-4) rúgások szerepelnek a alkalmazásban. |A helyileg rögzített kötetek átadása lépcsőzetesen történik. |
| 4. frissítés a 3. frissítéshez |A többszintes kötetek átadása lépcsőzetesen történik. <br></br>A helyileg rögzített kötetek feladatátvétele helyileg rögzítettként történik. <br></br> A hő-metaadatok visszaállításához használt biztonsági másolatok. <br></br>A hő-alapú nyomon követés nem érhető el a 3. frissítésben a feladat-visszavételt követően. |A helyileg rögzített kötetek átadása lépcsőzetesen történik. |


## <a name="device-failover-scenarios"></a>Eszközök feladatátvételi forgatókönyvei

Ha katasztrófa van, dönthet úgy, hogy feladatátvételt hajt végre a StorSimple-eszközön:

* [Egy fizikai eszközre](storsimple-8000-device-failover-physical-device.md).
* [Önmagához](storsimple-8000-device-failover-same-device.md).
* [Egy felhőalapú készülékre](storsimple-8000-device-failover-cloud-appliance.md).

Az előző cikkek részletesen ismertetik a fenti feladatátvételi esetekre vonatkozó lépéseket.


## <a name="failback"></a>Feladat-visszavétel

A 3. és újabb verziók esetében a StorSimple a feladat-visszavételt is támogatja. A feladat-visszavételi feladat csak a feladatátvétel fordítottja, a cél lesz a forrás és az eredeti forrásoldali eszköz a feladatátvétel során. 

A feladat-visszavétel során a StorSimple újraszinkronizálja az adatokat az elsődleges helyre, megállítja az I/O-és az alkalmazási tevékenységet, és visszavált az eredeti helyre.

A feladatátvétel befejezése után a StorSimple a következő műveleteket hajtja végre:

* A StorSimple megtisztítja a forrás eszközön átadott mennyiségű tárolót.
* A StorSimple a forrásoldali eszközön mennyiségi tárolóként (feladatátvétel után) kezdeményezi a háttérben futó feladatot. Ha a feladat végrehajtása közben megkísérli a feladatátvételt, a rendszer értesítést küld erről a hatásról. Várjon, amíg a feladat be nem fejeződik a feladat-visszavétel elindításához.
* A mennyiségi tárolók törlésének befejezéséhez szükséges idő számos tényezőtől függ, például az adatok mennyiségétől, a biztonsági másolatok számától és a művelethez rendelkezésre álló hálózati sávszélességtől.

Ha feladatátvételi tesztet vagy failbacks tesztet tervez, javasoljuk, hogy tesztelje a kötetek tárolóit kevesebb adattal (GB). Általában a feladatátvétel befejezése után 24 órával a feladat-visszavételt is elindíthatja.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

K. **Mi történik, ha a DR sikertelen vagy részleges sikeres?**

A. Ha a DR sikertelen, javasoljuk, hogy próbálkozzon újra. A második eszköz feladatátvételi feladata tisztában van az első feladatok előrehaladásával, és ettől kezdve ettől kezdve kezdődik.

K. **Törölhetek egy eszközt, miközben az eszköz feladatátvétele folyamatban van?**

A. Egy eszköz nem törölhető, amíg a DR folyamatban van. Az eszközt csak a DR befejezése után törölheti. Az eszköz feladatátvételi feladatának előrehaladását a **feladatok** panelen figyelheti.

K. **Mikor kezdődik a szemetet tartalmazó gyűjtemény a forrásoldali eszközön, hogy a forrás eszközön lévő helyi adategység törölve legyen?**

A. A szemét-gyűjtemény csak az eszköz teljes tisztítása után engedélyezhető a forrásoldali eszközön. A tisztítás magában foglalja az olyan objektumok tisztítását, amelyek feladatátvételét a forrás eszközről, például a kötetekről, a biztonsági mentési objektumokról (nem adatokról), a mennyiségi tárolóról és a házirendekről.

K. **Mi történik, ha a forrásoldali eszközön a kötethez tartozó tárolók törlési feladata sikertelen?**

A.  Ha a törlési feladatot nem sikerül végrehajtani, manuálisan törölheti a kötetek tárolóit. Az **eszközök** panelen válassza ki a forrás eszközt, majd kattintson a **kötetek tárolók** elemre. Válassza ki a fellépett, majd a panel alján található tárolókat, és kattintson a **Törlés** gombra. Miután törölte az összes feladatátvételi mennyiségű tárolót a forrásoldali eszközön, elindíthatja a feladat-visszavételt. További információért látogasson el [a mennyiségi tároló törlése](storsimple-8000-manage-volume-containers.md#delete-a-volume-container)elemre.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Üzletmenet-folytonossági katasztrófa-helyreállítás (BCDR)

Az üzletmenet folytonossága vész-helyreállítási (BCDR) forgatókönyv akkor fordul elő, ha a teljes Azure-adatközpont működése leáll. Ez a forgatókönyv hatással lehet a StorSimple Eszközkezelő szolgáltatásra és a kapcsolódó StorSimple-eszközökre.

Ha egy StorSimple-eszköz közvetlenül a katasztrófa előtt lett regisztrálva, akkor előfordulhat, hogy az eszköznek a gyári beállítások visszaállítását kell végeznie. A katasztrófa után a StorSimple-eszköz offline állapotban jelenik meg a Azure Portal. Ezt az eszközt törölni kell a portálról. Állítsa vissza az eszközt a gyári beállításokra, és regisztrálja újra a szolgáltatással.

## <a name="next-steps"></a>Következő lépések

Ha készen áll az eszköz feladatátvételének végrehajtására, válasszon a következő forgatókönyvek közül a részletes utasítások közül:

- [Feladatátvétel egy másik fizikai eszközre](storsimple-8000-device-failover-physical-device.md)
- [Feladatátvétel ugyanarra az eszközre](storsimple-8000-device-failover-same-device.md)
- [Feladatátvétel StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Ha az eszköz feladatátvétele megtörtént, válasszon az alábbi lehetőségek közül:

* [Inaktiválja vagy törölje a StorSimple-eszközt](storsimple-8000-deactivate-and-delete-device.md).
* A [StorSimple-eszköz felügyeletéhez használja a StorSimple Eszközkezelő szolgáltatást](storsimple-8000-manager-service-administration.md).

