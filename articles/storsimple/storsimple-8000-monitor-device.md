---
title: A StorSimple 8000 Series eszköz monitorozása
description: Ismerteti, hogyan használható a StorSimple Eszközkezelő szolgáltatás a használat, az I/O-teljesítmény és a kapacitás kihasználtságának figyelésére.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: de4cae90f2cc1223271672d9fc5ba5b42e4409e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94962433"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>A StorSimple-eszköz figyelése a StorSimple Eszközkezelő szolgáltatással

## <a name="overview"></a>Áttekintés
A StorSimple-megoldásban lévő egyes eszközök figyeléséhez használhatja a StorSimple Eszközkezelő szolgáltatást. Létrehozhat egyéni diagramokat az I/O-teljesítmény, a kapacitás kihasználtsága, a hálózati átviteli sebesség és az eszköz teljesítményének mérőszámai alapján, és rögzítheti az irányítópultra. További információért keresse fel a [portál irányítópultjának testreszabása](../azure-portal/azure-portal-dashboards.md)című témakört.

Egy adott eszköz figyelési információinak megtekintéséhez a Azure Portal válassza ki a StorSimple Eszközkezelő szolgáltatást. Az eszközök listájából válassza ki az eszközt, majd lépjen a **figyelés** elemre. Ekkor megtekintheti a kiválasztott eszköz **kapacitását**, **felhasználását** és **teljesítményének** diagramját.

## <a name="capacity"></a>Kapacitás
A **kapacitás** nyomon követi a kiépített területet és az eszközön maradó területet. A fennmaradó kapacitás ezután helyileg rögzítettként vagy lépcsőzetesen jelenik meg.

A kiépített és a fennmaradó kapacitást a lépcsőzetes és a helyileg rögzített kötetek részletezik. Minden kötetnél megjelenik a kiosztott kapacitás, valamint az eszköz hátralévő kapacitása.

![I/o-kapacitás](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Használat
A **használat** a kötetek, a kötetek tárolói vagy az eszköz által használt adattárolóhelyek mennyiségével kapcsolatos metrikákat követi nyomon. A jelentéseket az elsődleges tárterület, a Felhőbeli tárhely vagy az eszköz tárterületének kapacitáskihasználása alapján hozhatja létre. A kapacitás kihasználtsága egy adott köteten, egy adott mennyiségi tárolón vagy az összes köteten található tárolón mérhető.
Alapértelmezés szerint az elmúlt 24 órában használatos. A diagram szerkesztésével módosíthatja azt az időtartamot, ameddig a használatot jelenteni kell, ha kiválasztja a következőt:
* Az elmúlt 24 óra
* Az elmúlt 7 nap
* Az elmúlt 30 nap
* Elmúlt 90 nap
* Az elmúlt év

A használati diagramok esetében két fő mérőszámot, növekedést és tartományt kell jelenteni. A tartomány a kijelölt időtartamon (például az elmúlt 7 napban) jelentett felhasználás minimális értékére és a felhasznált adatok minimális értékeire vonatkozik.

A növekedés arra utal, hogy az első naptól kezdve az elmúlt nap folyamán a használat növekedését a kiválasztott időtartam alatt. 

A növekedést és a tartományt a következő egyenletek is megadhatják:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

A használt elsődleges, Felhőbeli és helyi tárterület a következőképpen olvasható:

### <a name="primary-storage-usage"></a>Elsődleges tárterület-használat
Ezek a diagramok a StorSimple kötetekre írt adatmennyiséget mutatják, mielőtt az adatgyűjtés deduplikálása és tömörítése megtörténik. Megtekintheti a mennyiségi tárolóban lévő összes kötet által használt elsődleges tárterületet vagy egyetlen kötetet. A használt elsődleges tárterület tovább bontható az elsődleges, a felhasznált és az elsődleges helyileg rögzített tárolóban.

A következő diagramok a StorSimple-eszközhöz használt elsődleges tárolót mutatják be a Felhőbeli pillanatképek készítése előtt és után. Mivel ez csak mennyiségi adatok, a Felhőbeli pillanatkép nem változtathatja meg az elsődleges tárterületet. Amint láthatja, a diagramon a Felhőbeli Pillanatképek készítésének eredményeképpen használt elsődleges, vagy helyileg rögzített tárolóban nem látható különbség. A Felhőbeli pillanatkép az eszközön körülbelül 11:50 órakor indult el.

![Elsődleges kapacitás kihasználtsága a Felhőbeli pillanatkép után](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Ha most futtatja az i/o-t a StorSimple-eszközhöz csatlakoztatott gazdagépen, a rendszer az elsődleges rétegű tárolás vagy az elsődleges helyileg rögzített tároló növekedését fogja látni, attól függően, hogy melyik kötetek (rétegű vagy helyileg rögzített) alapján történik az adatírás. Itt láthatók a StorSimple-eszközök elsődleges tárolási használati diagramjai. Ezen az eszközön a StorSimple-gazdagép a (z) 2:30 órakor megkezdte az írásokat az eszköz egy rétegű kötetén. A (z) a gazdagépen futó IO-hoz tartozó, írási bájt/mp-ben látható csúcsot láthatja.

![Teljesítmény, ha a rétegű köteteken fut az IO](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Ha megtekinti a felhasznált elsődleges többhelyes tárolót, amely így lett felmentve, míg az elsődleges helyileg rögzített használat változatlan marad, mivel a helyileg rögzített köteteken az eszközön nem történik írás.

![Az elsődleges kapacitás kihasználtsága, ha az IO a lépcsőzetes köteteknél fut](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Ha a 3. vagy újabb frissítést futtatja, az elsődleges tárterületet az egyes kötetek, az összes kötet, az összes rétegű kötet és az összes helyileg rögzített kötet alapján lebonthatja, ahogy az alábbi ábrán is látható. Az összes helyileg rögzített kötet lebontásával gyorsan megállapíthatja, hogy milyen mértékben használják a helyi szintet.

![Az összes rétegű kötet elsődleges kapacitásának kihasználtsága](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Az összes helyileg rögzített kötet elsődleges kapacitásának kihasználtsága](./media/storsimple-8000-monitor-device/monitor-usage4.png)

További rákattinthat a listában szereplő összes kötetre, és megtekintheti a megfelelő használatot.

![Elsődleges kapacitás kihasználtsága minden helyileg rögzített kötethez 2](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Felhőbeli tárolás használata
Ezek a diagramok a felhasznált Felhőbeli tárhely mennyiségét mutatják. Ezeket az adatfájlokat a rendszer deduplikálja és tömöríti. Ez az összeg olyan Felhőbeli pillanatképeket tartalmaz, amelyek nem tükrözik az elsődleges köteteket, és az örökölt vagy a szükséges megőrzési célokra szolgálnak. Összehasonlíthatja az elsődleges és a Felhőbeli tárterület-használati adatokat, így az adatcsökkenési arányt is megtalálhatja, bár a szám nem lesz pontos.

A következő diagramok egy StorSimple-eszköz Felhőbeli tárolási kihasználtságát mutatják be Felhőbeli pillanatkép készítésekor.

* A Felhőbeli pillanatkép a 11:50-kor megkezdődött az eszközön, és láthatja, hogy a Felhőbeli pillanatkép előtt nem volt használatban Felhőbeli tárterület. 
* A Felhőbeli pillanatkép befejezését követően a Felhőbeli tárterület kihasználtsága 0,89 GB-ot eredményez. 
* A Felhőbeli pillanatkép folyamatban van, és az eszközről a felhőbe irányuló i/o-érték is megfelelő csúcsot mutat.

    ![Felhőbeli tárolás kihasználtsága a Felhőbeli pillanatkép előtt](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Felhőbeli tárolás kihasználtsága a Felhőbeli pillanatkép után](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![IO az eszközről a felhőbe Felhőbeli Pillanatképek során](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Helyi tárterület-használat
Ezek a diagramok az eszköz teljes kihasználtságát mutatják, amely nagyobb, mint az elsődleges tárterület-használat, mivel az SSD lineáris szintet tartalmaz. Ez a csomag olyan adatmennyiséget tartalmaz, amely az eszköz más szintjein is megtalálható. Az SSD lineáris csomag kapacitása úgy van lefoglalva, hogy amikor az új adatértékek bekerülnek, a rendszer áthelyezi a régieket a HDD-szintjére (a deduplikált és a tömörített időt), majd ezt követően a felhőbe.

Idővel a felhasznált elsődleges tárterület és a használt helyi tárterület valószínűleg együtt fog növekedni, amíg az adat a felhőbe nem kerül. Ezen a ponton a használt helyi tárterület valószínűleg a fennsíkon kezdődik, de a felhasznált elsődleges tárterület egyre nagyobb mennyiségű adatírást eredményez.

A következő diagramok a StorSimple-eszközhöz használt elsődleges tárolót mutatják be a Felhőbeli Pillanatképek készítésekor. A Felhőbeli pillanatkép a következő időpontban indult: 11:50 órakor, és a helyi tárterület egyre csökken. A felhasznált helyi tárterület 1,445 GB és 1,09 GB között volt. Ez azt jelzi, hogy a lineáris SSD-csomag tömörítetlen állapotának legnagyobb valószínűsége deduplikált, tömörített és a HDD-szinten lett áthelyezve. Vegye figyelembe, hogy ha az eszközön már van nagy mennyiségű adattal az SSD-és a HDD-szinten, akkor előfordulhat, hogy ez a csökkenés nem jelenik meg. Ebben a példában az eszköz kis mennyiségű adattal rendelkezik.

![Helyi tárterület kihasználtsága a Felhőbeli pillanatkép után](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Teljesítmény
A **teljesítmény** a gazdagépen lévő iSCSI-kezdeményező felületek, valamint az eszköz vagy az eszköz, illetve a felhő közötti olvasási és írási műveletek számával kapcsolatos mérőszámokat követi nyomon. Ez a teljesítmény mérhető egy adott kötetre, egy adott mennyiségi tárolóra vagy az összes mennyiségi tárolóra. A teljesítmény magában foglalja a CPU-kihasználtságot, valamint az eszközön lévő különböző hálózati adapterek hálózati átviteli sebességét is.

### <a name="io-performance-for-initiator-to-device"></a>Kezdeményező által az eszközre irányuló I/O-teljesítmény
Az alábbi diagram a kezdeményező I/O-adatait jeleníti meg az eszközön az éles eszköz összes kötete számára. A rajzolt metrikák olvasási és írási bájtok másodpercenként. Az olvasási, írási és a függőben lévő i/o-műveletek, illetve olvasási és írási késések is megadhatók.

![Az i/o-teljesítmény a kezdeményező számára az eszközre](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Eszközről a felhőbe irányuló I/O-teljesítmény
Ugyanarra az eszközre az I/O-műveletek az eszközről a felhőbe irányuló adatokra lesznek felrajzolva az összes kötet tárolóban. Ezen az eszközön csak a lineáris szinten szerepelnek az adathalmazok, és semmi sincs a felhőben. Az eszközről a felhőbe nem történt írási/olvasási művelet. Ezért a diagramban lévő csúcsok 5 percen belül vannak, és az eszköz és a szolgáltatás között a szívverés ellenőrzésének gyakorisága megfelel.

Ugyanezen eszköz esetében a 11:50-kor kezdődően a kötethez tartozó adatokhoz is Felhőbeli pillanatkép készült. Ez az eszközről a felhőbe irányuló adatforgalmat eredményezett. Az írások ebben az időtartamban a felhőbe voltak kézbesítve. Az i/o-diagram a pillanatkép készítésének időpontjához tartozó írási bájt/mp-csúcsot jeleníti meg.

![IO az eszközről a felhőbe Felhőbeli Pillanatképek során](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Hálózati átviteli sebesség az eszköz hálózati adapterei számára
A **hálózati átviteli sebesség** a gazdagépen és az eszközön, illetve az eszköz és a felhő közötti iSCSI-kezdeményező hálózati adapterekről továbbított adatok mennyiségére vonatkozó metrikákat követi nyomon. Az eszközön lévő összes iSCSI hálózati adapter esetében nyomon követheti ezt a metrikát.

A következő diagramok azt mutatják be, hogy az eszközön a 0, a 1 1 GbE hálózat milyen hálózati átviteli sebességgel rendelkezik, ami mind a Cloud-Enabled (alapértelmezett), mind az iSCSI-engedélyezve volt. Ezen az eszközön június 14-én, körülbelül 9 órakor a felhőbe soroltuk be az adatkereteket (a Felhőbeli Pillanatképek nem voltak elérhetők, ami azt jelenti, hogy az adatforgalom a felhőbe való áthelyezését eredményezte). A hálózati átviteli sebesség gráfjának egy megfelelő csúcsa van, és a legtöbb hálózati forgalom a felhőben is kimenő.

![Hálózati átviteli sebesség (0)](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Ha megnézzük az adatok 1 interfész átviteli sebesség diagramot, egy másik 1 GbE hálózati adaptert, amely csak iSCSI-kompatibilis volt, akkor ebben az időtartamban gyakorlatilag nem történt hálózati forgalom.

![Adatok hálózati átviteli sebessége 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Az eszköz CPU-kihasználtsága
A **CPU-kihasználtság** az eszközön használt CPU-ra vonatkozó metrikákat követi nyomon. A következő diagram az eszköz CPU-kihasználtsági statisztikáit mutatja be éles környezetben.

![Az eszköz CPU-kihasználtsága](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [használhatja a StorSimple Eszközkezelő szolgáltatás-eszköz irányítópultot](./storsimple-8000-device-dashboard.md).
* Ismerje meg, hogyan kezelheti a [StorSimple-eszközt a StorSimple Eszközkezelő szolgáltatással](./storsimple-8000-manager-service-administration.md).