---
title: Áttelepítési útmutató Azure Stack Edge Pro-FPGA a GPU fizikai eszközhöz
description: Ez az útmutató utasításokat tartalmaz a munkaterhelések áttelepítéséhez egy Azure Stack Edge Pro FPGA eszközről egy Azure Stack Edge Pro GPU-eszközre.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/11/2021
ms.author: alkohli
ms.openlocfilehash: 430e34a1ca631be00ef46170affd4b56c79894a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566404"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Számítási feladatok migrálása Azure Stack Edge Pro-FPGA egy Azure Stack Edge Pro GPU-ra

Ez a cikk azt ismerteti, hogyan telepíthetők át munkaterhelések és adatok egy Azure Stack Edge Pro FPGA-eszközről egy Azure Stack Edge Pro GPU-eszközre. Az áttelepítési folyamat a két eszköz összehasonlításával kezdődik, egy áttelepítési terv és az áttelepítési megfontolások áttekintése. Az áttelepítési eljárás részletesen ismerteti az ellenőrzés és az eszköz tisztításának lépéseit.

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

## <a name="about-migration"></a>A migrálás ismertetése

Az áttelepítés a számítási feladatok és az alkalmazásadatok egyik tárolóhelyről a másikra történő áthelyezésének folyamata. Ez azt eredményezi, hogy a szervezet aktuális adatait egy tárolóeszközről egy másik tárolóeszközre kell másolni – lehetőleg az aktív alkalmazások megszakítása vagy letiltása nélkül –, majd az összes bemeneti/kimeneti (I/O) tevékenység átirányítása az új eszközre. 

Ez az áttelepítési útmutató részletesen ismerteti az adatok Azure Stack Edge Pro FPGA-eszközről egy Azure Stack Edge Pro GPU-eszközre történő áttelepítéséhez szükséges lépéseket. Ez a dokumentum olyan informatikai (IT) szakemberek és szakemberek számára készült, akik az adatközpontban Azure Stack Edge-eszközök üzemeltetéséhez, üzembe helyezéséhez és kezeléséhez felelősek.

Ebben a cikkben a Azure Stack Edge Pro FPGA eszközt a *forrásként* szolgáló eszköznek nevezzük, és az Azure stack Edge Pro GPU-eszköz a *céleszköz* . 

## <a name="comparison-summary"></a>Összehasonlító összefoglalás

Ez a szakasz az Azure Stack Edge Pro GPU és a Azure Stack Edge Pro FPGA-eszközök közötti képességek összehasonlító összegzését tartalmazza. A forrás-és a célként megadott eszköz hardvere nagyjából azonos; csak a hardveres gyorsítási kártya és a tárolókapacitás eltérő lehet.<!--Please verify: These components MAY, but need not necessarily, differ?-->

|    Képesség  | Azure Stack Edge Pro GPU (megcélzott eszköz)  | Azure Stack Edge Pro-FPGA (forrásoldali eszköz)|
|----------------|-----------------------|------------------------|
| Hardver       | Hardveres gyorsítás: 1 vagy 2 NVIDIA T4 GPU <br> A számítás, a memória, a hálózati adapter, az áramellátási egység és a tápkábelek specifikációi azonosak az eszköz FPGA.  | Hardveres gyorsítás: Intel Arria 10 FPGA <br> A számítás, a memória, a hálózati adapter, az áramellátási egység és a tápkábelek specifikációi azonosak az eszköz GPU-val.          |
| Használható tároló | 4,19 TB <br> A paritásos rugalmasság és a belső használat területének megőrzése után | 12,5 TB <br> Miután belső használatra megérdemelte a területet |
| Biztonság       | Tanúsítványok |                                                     |
| Számítási feladatok      | Munkaterhelések IoT Edge <br> Virtuális gépek számítási feladatai <br> A Kubernetes számítási feladatai| Munkaterhelések IoT Edge |
| Díjszabás        | [Díjszabás](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [Díjszabás](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>Áttelepítési terv

Az áttelepítési terv létrehozásához vegye figyelembe a következő információkat:

- Dolgozzon ki egy ütemtervet az áttelepítéshez. 
- Az adatáttelepítés során állásidőt tapasztalhat. Azt javasoljuk, hogy az áttelepítést az állásidő-karbantartási időszak alatt ütemezze, mivel a folyamat zavaró. Az ebben a dokumentumban később ismertetett állásidőben a konfigurációk beállítása és visszaállítása is megtörténik.
- Ismerje meg az állásidő teljes hosszát, és továbbítsa azt az összes érintett fél számára.
- Azonosítsa azokat a helyi adatok, amelyeket át kell telepíteni a forrásoldali eszközről. Elővigyázatosságból ügyeljen arra, hogy a meglévő tárolóban lévő összes adattal egy legutóbbi biztonsági másolat legyen. 


## <a name="migration-considerations"></a>A migrálás szempontjai 

Az áttelepítés folytatása előtt vegye figyelembe a következő információkat: 

- Azure Stack Edge Pro GPU-eszköz nem aktiválható Azure Stack Edge Pro FPGA-erőforrással. Hozzon létre egy új erőforrást az Azure Stack Edge Pro GPU-eszközhöz a következő témakörben leírtak szerint: [Azure stack Edge Pro GPU-sorrend létrehozása](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).
- A FPGA használó forrásoldali eszközön üzembe helyezett Machine Learning modelleket a GPU-val módosítani kell a céleszköz számára. A modellekkel kapcsolatos segítségért forduljon Microsoft ügyfélszolgálatahoz. A forrásoldali eszközön központilag telepített egyéni modellek, amelyek nem használták a FPGA (csak a használt CPU-t), a cél eszközön kell működniük (CPU használatával).
- A forrásoldali eszközön üzembe helyezett IoT Edge-modulok módosítása szükséges lehet, mielőtt a modulok sikeresen üzembe helyezhetők a célszámítógépen. 
- A forrásoldali eszköz támogatja az NFS 3,0 és 4,1 protokollokat. A célként megadott eszköz csak az NFS 3,0 protokollt támogatja.
- A forrásoldali eszköz támogatja az SMB-és NFS-protokollokat. A cél eszköz a REST protokollon keresztül támogatja a Storage-fiókokat a megosztásokhoz tartozó SMB-és NFS-protokollok mellett.
- A forrásoldali eszközön lévő megosztási hozzáférés az IP-címen keresztül történik, míg a megcélzott eszközön a megosztás hozzáférése az eszköz nevén keresztül történik.

## <a name="migration-steps-at-a-glance"></a>Áttelepítési lépések – áttekintés

Ez a táblázat összefoglalja a Migrálás teljes folyamatát, leírja az áttelepítéshez szükséges lépéseket, valamint azt a helyet, ahol a lépéseket végre kell hajtani.

| Ebben a fázisban | Hajtsa végre ezt a lépést| Ezen az eszközön |
|---------------|-------------|----------------|
| Forrás eszköz előkészítése *       | 1. a konfigurációs adatok rögzítése <br>2. az adatmegosztások biztonsági mentése <br>3. IoT Edge munkaterhelések előkészítése| Forrásoldali eszköz  |
| Cél eszköz előkészítése *       |1. új megrendelés létrehozása <br>2. konfigurálás és aktiválás| Céleszköz  |
| Adatok áttelepítése       | 1. az adatok áttelepíthetők a megosztásokból <br>2. IoT Edge munkaterhelések újbóli üzembe helyezése| Céleszköz  |
| Az adatok ellenőrzése            |Áttelepített adatértékek ellenőrzése |Céleszköz  |
| Karbantartás, visszatérés              |Adat-és visszatérési adattörlés| Forrásoldali eszköz  |

**A forrás-és a cél eszközöket párhuzamosan lehet előkészíteni.*

## <a name="prepare-source-device"></a>Forrásoldali eszköz előkészítése

Az előkészítés magában foglalja az Edge Cloud shares, a Edge helyi megosztások és az eszközön telepített IoT Edge modulok azonosítását. 

### <a name="1-record-configuration-data"></a>1. a konfigurációs adatok rögzítése

Hajtsa végre ezeket a lépéseket a forrás eszközön a helyi felhasználói felületen.

Rögzítse a *forrás* eszköz konfigurációs adatait. A [központi telepítési ellenőrzőlista](azure-stack-edge-gpu-deploy-checklist.md) segítségével rögzítheti az eszköz konfigurációját. Az áttelepítés során ezt a konfigurációs információt fogja használni az új céleszköz konfigurálásához. 

### <a name="2-back-up-share-data"></a>2. az adatmegosztások biztonsági mentése

Az eszköz adattípusa a következő típusok egyike lehet:

- Az Edge Felhőbeli megosztások
- Helyi megosztásokban lévő adatforgalom

#### <a name="data-in-edge-cloud-shares"></a>Az Edge Felhőbeli megosztások

Az Edge Cloud Share-adatok az eszközről az Azure-ba kerülnek. Hajtsa végre ezeket a lépéseket a *forrásoldali* eszközön a Azure Portalon keresztül. 

- Készítse el a forrás-eszközön található összes Felhőbeli megosztást és felhasználót tartalmazó listát.
- Készítse el az összes rendelkezésre álló sávszélesség-ütemterv listáját. Ezeket a sávszélesség-ütemterveket újra létre fogja hozni a megcélzott eszközön.
- A rendelkezésre álló hálózati sávszélességtől függően konfigurálja a sávszélesség-ütemtervet az eszközön, hogy maximalizálja a felhőbe irányuló adatszinteket. Ezzel a beállítással csökkenthető az eszköz helyi adatvédelme.
- Győződjön meg arról, hogy a megosztások teljes mértékben a felhőbe vannak bontva. A rétegek megerősíthető a megosztás állapotának ellenőrzésével a Azure Portalban.  

#### <a name="data-in-edge-local-shares"></a>A peremhálózati helyi megosztásokban lévő adatforgalom

Az Edge helyi megosztásokban tárolt adatmennyiség az eszközön marad. Hajtsa végre ezeket a lépéseket a *forrásoldali* eszközön a Azure Portalon keresztül. 

- Az eszközön lévő helyi megosztások listájának létrehozása.
- Mivel az adatgyűjtés egyszeri áttelepítést hajt végre, hozzon létre egy másolatot a helyi megosztási adatbázisról egy másik helyszíni kiszolgálóra. Az adatmásoláshoz használhatja a másolási eszközöket, például az `robocopy` (SMB) vagy az `rsync` (NFS) fájlokat. Előfordulhat, hogy már telepített egy harmadik féltől származó adatvédelmi megoldást a helyi megosztásokban lévő adatbiztonsági mentéshez. A következő harmadik féltől származó megoldások támogatottak Azure Stack Edge Pro FPGA-eszközökkel való használatra:

    | Külső gyártótól származó szoftverek           | Hivatkozás a megoldásra                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Részletekért vegye fel a kapcsolatot az adatkapcsolattal.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> A részletekért forduljon a CommVault.          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Részletekért forduljon a Veritashez.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> A részletekért forduljon a Veeam. |


### <a name="3-prepare-iot-edge-workloads"></a>3. IoT Edge munkaterhelések előkészítése

- Ha telepített IoT Edge modulokat, és FPGA gyorsítást használ, előfordulhat, hogy módosítania kell a modulokat, mielőtt ezek a GPU-eszközön futnak. Kövesse az IoT Edge- [modulok módosítása](azure-stack-edge-gpu-modify-fpga-modules-gpu.md)című témakör utasításait. 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>Cél eszköz előkészítése

### <a name="1-create-new-order"></a>1. új megrendelés létrehozása

Létre kell hoznia egy új rendelést (és egy új erőforrást) a *megcélzott* eszközhöz. A célként megadott eszköznek aktiválva kell lennie a GPU-erőforráson, és nem a FPGA-erőforráshoz.

Rendelés elhelyezéséhez [hozzon létre egy új Azure stack Edge-erőforrást](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) a Azure Portalban.


### <a name="2-set-up-activate"></a>2. beállítás, aktiválás

A *megcélzott* eszközt a korábban létrehozott új erőforrásra kell beállítania és aktiválni. 

A következő lépésekkel konfigurálhatja a *céleszköz* -t a Azure Portal használatával:

1. Gyűjtse össze a [központi telepítési ellenőrzőlistában](azure-stack-edge-gpu-deploy-checklist.md)szükséges adatokat. Használhatja a forrás eszköz konfigurációjától mentett adatokat. 
1. Az eszköz [kicsomagolása](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [rack csatlakoztatása](azure-stack-edge-gpu-deploy-install.md#rack-the-device) és [kábele](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Kapcsolódjon az eszköz helyi felhasználói felületéhez](azure-stack-edge-gpu-deploy-connect.md).
1. Konfigurálja a hálózatot más IP-címekkel (ha statikus IP-címeket használ), mint amelyeket a régi eszközhöz használt. Lásd: a [hálózati beállítások konfigurálása](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Rendelje hozzá a régi eszköz nevét, és adjon meg egy DNS-tartományt. Lásd: az [eszköz beállításának konfigurálása](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Konfigurálja a tanúsítványokat az új eszközön. Lásd: [tanúsítványok konfigurálása](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Szerezze be az aktiválási kulcsot a Azure Portal és aktiválja az új eszközt. Lásd: [az eszköz aktiválása](azure-stack-edge-gpu-deploy-activate.md).

Most már készen áll a megosztási adatok visszaállítására és a régi eszközön futó munkaterhelések üzembe helyezésére.

## <a name="migrate-data"></a>Adatok áttelepítése

Ekkor a forrás eszközről másolja át az adatait a Felhőbeli megosztásokra és az Edge helyi megosztásokra a *céleszköz* .

### <a name="1-from-edge-cloud-shares"></a>1. a peremhálózat Felhőbeli megosztásai

Az alábbi lépéseket követve szinkronizálhatja a felhőben lévő felhőalapú megosztások adatait a céleszköz:

1. [Adja hozzá](azure-stack-edge-j-series-manage-shares.md#add-a-share) a forrás eszközön létrehozott megosztási neveknek megfelelő megosztásokat. A megosztások létrehozásakor győződjön meg arról, hogy a **blob-tároló** beállítása **meglévő** értékre van állítva, majd válassza ki az előző eszközzel használt tárolót.
1. [Adja hozzá](azure-stack-edge-j-series-manage-users.md#add-a-user) az előző eszközhöz hozzáféréssel rendelkező felhasználókat.
1. [A megosztási adatok frissítése](azure-stack-edge-j-series-manage-shares.md#refresh-shares) az Azure-ból. A megosztás frissítése lekéri a meglévő tároló összes Felhőbeli adatait a megosztásokra.
1. Hozza létre újra a megosztásokhoz társítandó sávszélesség-ütemterveket. Lásd: [sávszélesség-ütemterv hozzáadása](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule) a részletes lépésekhez.


### <a name="2-from-edge-local-shares"></a>2. a peremhálózat helyi megosztásai

Előfordulhat, hogy egy harmadik féltől származó biztonsági mentési megoldást helyezett üzembe a IoT számítási feladataihoz tartozó helyi megosztások adatainak védelme érdekében. Ekkor vissza kell állítania ezeket az adatfájlokat.

Miután a helyettesítő eszköz teljesen konfigurálva van, engedélyezze az eszközt a helyi tároláshoz. 

A helyi megosztásokból származó adatok helyreállításához kövesse az alábbi lépéseket:

1. [Állítsa be a számítást az eszközön](azure-stack-edge-gpu-deploy-configure-compute.md).
1. Adja hozzá az összes helyi megosztást a céleszköz. Tekintse meg a [helyi megosztás hozzáadása](azure-stack-edge-gpu-manage-shares.md#add-a-local-share)című témakör részletes lépéseit.
1. Ha hozzáfér az SMB-megosztásokhoz a forrásoldali eszközön, akkor az IP-címeket fogja használni, a céleszköz pedig az eszköz nevét fogja használni. Lásd: [Kapcsolódás SMB-megosztáshoz Azure stack Edge Pro GPU](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-smb-share)-val. A céleszköz NFS-megosztásokhoz való kapcsolódáshoz az eszközhöz társított új IP-címeket kell használnia. Lásd: [Kapcsolódás NFS-megosztáshoz Azure stack Edge Pro GPU](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-nfs-share)-val. 

    Ha a megosztási adatait egy közbenső kiszolgálóra másolta az SMB vagy az NFS protokollon keresztül, a köztes kiszolgáló adatait átmásolhatja a célként megadott eszközön található megosztásokra. Ha a forrás és a cél eszköz is *online állapotban* van, az adatok közvetlenül a forrás eszközről is másolhatók.

    Ha harmadik féltől származó szoftvert használ a helyi megosztásokban lévő információk biztonsági mentésére, akkor futtatnia kell a választott adatvédelmi megoldás által biztosított helyreállítási eljárást. Tekintse meg a következő táblázatban található hivatkozásokat.

    | Külső gyártótól származó szoftverek           | Hivatkozás a megoldásra                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Részletekért vegye fel a kapcsolatot az adatkapcsolattal.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> A részletekért forduljon a CommVault. |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Részletekért forduljon a Veritashez.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> A részletekért forduljon a Veeam. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. IoT Edge munkaterhelések újbóli üzembe helyezése

A IoT Edge-modulok előkészítése után telepítenie kell IoT Edge számítási feladatokat a céleszköz. Ha IoT Edge modulok üzembe helyezésével kapcsolatos hibákat tapasztal, tekintse meg a következőt:

- [A Azure IoT Edge kapcsolatos gyakori problémák és megoldások](../iot-edge/troubleshoot-common-errors.md). 
- [Futásidejű hibák IoT Edge](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors).

## <a name="verify-data"></a>Az adatok ellenőrzése

Az áttelepítés után ellenőrizze, hogy az összes adat áttelepítése megtörtént-e, és hogy a munkaterhelések telepítve vannak-e a céleszköz.

## <a name="erase-data-return"></a>Adat törlése, visszatérés

Az adatáttelepítés befejezése után törölje a helyi adatforrást, és adja vissza a forrásoldali eszközt. Kövesse az [Azure stack Edge Pro-eszköz visszaküldése](azure-stack-edge-return-device.md)című témakör lépéseit.


## <a name="next-steps"></a>Következő lépések

[Megtudhatja, hogyan helyezhet üzembe IoT Edge számítási feladatokat Azure Stack Edge Pro GPU-eszközön](azure-stack-edge-gpu-deploy-compute-module-simple.md)