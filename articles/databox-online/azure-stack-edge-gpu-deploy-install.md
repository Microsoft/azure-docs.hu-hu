---
title: Oktatóanyag a telepítéshez – kicsomagolás, rack, Cable Azure Stack Edge Pro GPU fizikai eszköz | Microsoft Docs
description: A Azure Stack Edge Pro GPU telepítésének második oktatóanyaga a fizikai eszköz kicsomagolásával, állványával és csatlakoztatásával kapcsolatos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: a415dba4bb4fd5b95e98ae7c104cae92db27f34c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060159"
---
# <a name="tutorial-install-azure-stack-edge-pro-with-gpu"></a>Oktatóanyag: Azure Stack Edge Pro telepítése GPU-val

Ez az oktatóanyag leírja, hogyan telepíthet egy Azure Stack Edge Pro fizikai eszközt GPU-val. A telepítési eljárás magában foglalja az eszköz kicsomagolását, az állvány csatlakoztatását és a kábelezést. 

A telepítés elvégzése körülbelül két órát is igénybe vehet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz csatlakoztatása
> * Az eszköz bekábelezése

## <a name="prerequisites"></a>Előfeltételek

A fizikai eszközök telepítésének előfeltételei a következők:

### <a name="for-the-azure-stack-edge-resource"></a>Az Azure Stack Edge-erőforráshoz

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Elvégezte a [felkészülés az Azure stack Edge Pro és a GPU használatával történő üzembe helyezése](azure-stack-edge-gpu-deploy-prep.md)című témakör összes lépését.
    * Létrehozott egy Azure Stack Edge-erőforrást az eszköz üzembe helyezéséhez.
    * Létrehozta az aktiválási kulcsot az eszköz Azure Stack Edge-erőforrással való aktiválásához.

 
### <a name="for-the-azure-stack-edge-pro-physical-device"></a>A Azure Stack Edge Pro fizikai eszközhöz

Az eszköz üzembe helyezése előtt:

- Győződjön meg róla, hogy az eszköz biztonságosan működik-e egy sima, stabil és szintű munkafelületen.
- Győződjön meg arról, hogy az eszköz beállításához kijelölt helyen van:
    - Standard AC-áramellátás független forrásból.

        -VAGY-
    - egy kiszolgálószekrényhez való áramelosztó egység (PDU) szünetmentes tápegységgel (UPS).
    - Azon állványon elérhető 1U-tárolóhely, amelyhez csatlakoztatni kívánja az eszközt.

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén

Előkészületek:

- Tekintse át a Azure Stack Edge Pro üzembe helyezésének hálózati követelményeit, és konfigurálja az adatközponti hálózatot a követelmények szerint. További információ: [Azure stack Edge Pro hálózati követelmények](azure-stack-edge-system-requirements.md#networking-port-requirements).

- Győződjön meg arról, hogy az eszköz optimális működéséhez 20 Mbps sebesség szükséges.


## <a name="unpack-the-device"></a>Az eszköz kicsomagolása

Az eszköz egyetlen dobozban érkezik. Az eszköz kibontásához hajtsa végre a következő lépéseket. 

1. Helyezze a csomagot egy sima, vízszintes felületre.
2. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a doboz vagy a csomagolás súlyosan sérült, ne nyissa meg. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e.
3. Bontsa ki a dobozt. A csomag felbontása után ellenőrizze, hogy megvannak-e a következők:
    - Egyetlen ház Azure Stack Edge Pro-eszköz
    - Két tápkábel
    - Egy vasúti készlet szerelvény
    - Biztonsági, környezeti és szabályozási tájékoztató füzet

Ha nem kapta meg az összes itt felsorolt elemet, [forduljon a Microsoft ügyfélszolgálatahoz](azure-stack-edge-contact-microsoft-support.md). A következő lépés az eszköz csatlakoztatása.


## <a name="rack-the-device"></a>Az eszköz állványra szerelése

Az eszközt a standard 19 hüvelykes állványra kell telepíteni. A következő eljárással csatlakoztathatja az eszközt egy standard 19 hüvelykes állványra.

> [!IMPORTANT]
> Azure Stack Edge Pro-eszközöknek rack-csatlakoztatással kell rendelkezniük a megfelelő működéshez.


### <a name="prerequisites"></a>Előfeltételek

- Mielőtt elkezdené, olvassa el a biztonsági, környezeti és szabályozási információs füzet biztonsági utasításait. Ezt a füzetet az eszközzel szállították.
- Kezdje el a sínek telepítését a rendelkezésre álló, a rack ház aljához legközelebb eső helyen.
- A kiépített vasúti csatlakoztatási konfigurációhoz:
    -  Nyolc csavart kell megadnia: #10-32, #12-24, #M5 vagy #M6. A csavarok fő átmérőjének 10 mm-nél (0,4 ") kisebbnek kell lennie.
    -  Egy lapos csavarhúzóra van szüksége.

### <a name="identify-the-rail-kit-contents"></a>A Rail Kit tartalmának azonosítása

Keresse meg a következő összetevőket a Rail Kit szerelvény telepítéséhez:
- Két A7-es Dell ReadyRails II csúszó vasúti szerelvény
- Két Hook-és hurok-pánt

    ![A vasúti készlet tartalmának azonosítása](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Eszköz nélküli sínek (szögletes furat vagy kerek furatos állványok) telepítése és eltávolítása

> [!TIP]
> Ez a lehetőség kevésbé fontos, mert nem szükséges eszközöket telepíteni és eltávolítani a síneket a menet nélküli négyzetbe vagy kerek lyukakba az állványokon.

1. Helyezze a bal és a jobb oldali sín **elülső** részeit, és az összes végpontot a függőleges rack karimák elülső oldalán lévő lyukakba vigye.
2. Illessze be az egyes végpontokat a kívánt U-szóközök alsó és felső részén.
3. Folytassa a sín hátsó végét egészen addig, amíg teljes mértékben nem ül a függőleges állvány peremén, és a retesz a helyére kattan. Ismételje meg ezeket a lépéseket úgy, hogy az előtér-darabot a függőleges rack karimán pozícionálja és helyezze el.
4. A sínek eltávolításához húzza le a zárolás feloldása gombot a végének középpontjában, és törölje az egyes korlátokat.

    ![Eszköz nélküli sínek telepítése és eltávolítása](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Eszközön futó sínek (többszálú furatok) telepítése és eltávolítása

> [!TIP]
> Ez a beállítás azért szükséges, mert egy eszközre (_egy lapos csavarhúzóra_) van szükség a sínek a rackben lévő menetes kör alakú lyukakba való telepítéséhez és eltávolításához.

1. Távolítsa el a PIN-kód első és hátsó szerelvényeit egy lapos csavarhúzó használatával.
2. A sínre reteszelő alszerelvények lekérése és elforgatásával távolítsa el őket a beépítési zárójelből.
3. Csatolja a bal és a jobb oldali szerelvényt az elülső függőleges rack karimához két pár csavart használva.
4. Csúsztassa a bal és a jobb oldali zárójelet a hátsó függőleges rack karimák felé, és csatolja őket két pár csavar használatával.

    ![Eszközre telepített sínek telepítése és eltávolítása 2](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>A rendszer telepítése rackben

1. Húzza ki a belső diát a rackből, amíg a helyükre nem zár.
2. Keresse meg a rendszer mindkét oldalán a hátsó vasúti patthelyzetet, és csökkentse azokat a hátsó, a dia szerelvényeken található nyílásokkal. A rendszer lefelé forgatása, amíg az összes vasúti patthelyzet be nem illeszkedik a J-slotba.
3. A befelé irányuló bekapcsolás után a zárolási kar kattintson a helyére.
4. Nyomja le mindkét síneken a slide-Release Lock gombokat, és csúsztassa a rendszerét a rackbe.

    ![Rendszer telepítése rackben](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>A rendszer eltávolítása a rackből

1. Keresse meg a rögzítési kart a belső sínek oldalain.
2. Oldja fel az egyes kart úgy, hogy a kiadási helyükre elforgatja.
3. A rendszer szilárdan fogja megfogni a rendszer oldalait, és addig húzza azt addig, amíg a sínre nem állnak a J-slotok. Emelje fel a rendszer felszínét a rackből, és helyezze el egy szint felületén.

    ![Rendszer eltávolítása a rackből](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>A Slam-zár bevonása és felszabadítása

> [!NOTE]
> A Slam-zárakkal nem rendelkező rendszerek esetén a rendszer a csavarok segítségével gondoskodik a rendszerről, az eljárás 3. lépésében leírtak szerint.

1. Az előtérben keresse meg a Slam-zárat a rendszer egyik oldalán.
2. A zárolások automatikusan felvesznek, mivel a rendszer leküldi az állványra, és a zárolásokra való ráhúzással felszabadítja azokat.
3. A rendszer a rackben vagy más instabil környezetekben történő szállításának biztosításához keresse meg a rögzített csavart az egyes zárolások alatt, és minden csavart egy #2 Phillips csavarhúzóval kell megszigorítani.

    ![A Slam-zárak bevonása és kiadása](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Az eszköz bekábelezése

Irányítsa a kábeleket, majd csatlakoztassa az eszközt. Az alábbi eljárások azt ismertetik, hogyan lehet a Azure Stack Edge Pro-eszközt az áramellátáshoz és a hálózathoz.

Az eszköz kábelezésének megkezdése előtt a következőkre lesz szüksége:

- A Azure Stack Edge Pro fizikai eszköz, a kicsomagolt és a rack csatlakoztatása.
- Két tápkábel.
- Legalább egy 1-GbE RJ-45 hálózati kábel a felügyeleti felülethez való csatlakozáshoz. Az eszközön két 1-GbE hálózati adapter (egy felügyeleti és egy adathálózati) található.
- Egy 25-GbE SFP+ rézkábel minden konfigurálni kívánt adathálózati adapterhez. Legalább egy adathálózati adaptert a 2. port, a 3. port, az 5. port vagy a 6-os port közül kell csatlakoztatni az internethez (az Azure-hoz való csatlakozással).  
- Hozzáférés két energiaellátási egységhez (ajánlott).
- Legalább 1 1-GbE hálózati kapcsolóval egy 1 GbE hálózati adapter csatlakozik az internethez az adatkapcsolathoz. A helyi webes felhasználói felület nem lesz elérhető, ha a csatlakoztatott kapcsoló nem legalább 1 GbE. Ha 25/10 – GbE felületet használ az adataihoz, akkor 25 GbE vagy 10 GbE kapcsolóra van szüksége.

> [!NOTE]
> - Ha csak egy adathálózati adaptert csatlakoztat, javasoljuk, hogy használjon 25/10 GbE hálózati adaptert, például a 3. portot, a 4-es portot, az 5. portot vagy a 6-os PORTOT az Azure-ba való adatküldéshez. 
> - A legjobb teljesítmény érdekében és nagy mennyiségű adat kezeléséhez fontolja meg az összes adatport csatlakoztatását.
> - Az Azure Stack Edge Pro-eszköznek csatlakoznia kell az adatközpont-hálózathoz, hogy az adatforrás-kiszolgálókról is képes legyen az adatok betöltésére.

Azure Stack Edge Pro-eszközön:

- Az előlapon a lemezmeghajtók és a főkapcsoló gomb található.

    - Az eszköz elején 10 lemezes tárolóhely található.
    - A 0. tárolóhely 240 GB-os SATA-meghajtót használ operációsrendszer-lemezként. Az 1. bővítőhely üres, a 2 – 6. bővítőhely pedig adatlemezként használt SSD-NVMe. A 7 és 9 közötti tárolóhelyek szintén üresek.
- A háttérrendszer redundáns tápegységeket (PSUs) tartalmaz.
- A hátsó síkon hat hálózati adapter van:

    - 2 1 – Gbps felületek.
    - 4 25 – Gbps felületek, amelyek 10 GB/s illesztőfelületként is használhatók.
    - Egy alaplapi felügyeleti vezérlő (BMC).

- A hátsó síkon két hálózati kártya felel meg a hat portnak:

    - **Egyéni Microsoft `Qlogic` Cavium 25G NDC-adapter** – 1. port a 4. porton keresztül.
    - **Mellanox Dual port 25G ConnectX-4 csatornás hálózati adapter** -5. és 6. port.

A hálózati kártyák által támogatott kábelek, kapcsolók és adóvevők teljes listájáért keresse fel a következőt:

- [ `Qlogic` CAVIUM 25G NDC-adapter együttműködési mátrixa](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
- [Mellanox Dual port 25G ConnectX-4 csatornás hálózati adapter kompatibilis termékek](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).  

 
A következő lépésekkel csatlakoztassa az eszközt az áramellátáshoz és a hálózathoz.

1. Azonosítsa az eszköz hátsó síkja különböző portokat. Előfordulhat, hogy az eszközön a GPU-k számától függően az alábbi eszközök egyike érkezett a gyárból.

    - Eszköz két perifériás Component Interconnect-(PCI-) tárolóhelytel és egy GPU-val

        ![Kábeles eszköz hátsó síkja](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    - Eszköz három PCI bővítőhelytel és egy GPU-val

        ![Kábeles eszköz hátsó síkja 2](./media/azure-stack-edge-gpu-deploy-install/ase-three-pci-slots-one-gpu.png)

    - Eszköz három PCI bővítőhelytel és két GPU-val

        ![Kábeles eszköz hátsó síkja 3](./media/azure-stack-edge-gpu-deploy-install/ase-three-pci-slots-two-gpu.png)

2. Keresse meg a lemezes tárolóhelyeket és a főkapcsoló gombot az eszköz elején.

    ![Az eszköz elülső síkja](./media/azure-stack-edge-gpu-deploy-install/ase-gpu-device-front-plane-labeled.png)

3. Csatlakoztassa a tápkábeleket a burkolat egy-egy tápegységéhez. A magas rendelkezésre állás biztosításához az egyes tápegységeket különböző áramforrásokhoz csatlakoztassa.
4. Csatlakoztassa a tápkábeleket a kiszolgálószekrény áramelosztó egységeihez (PDU-k). Ügyeljen arra, hogy a két tápegység külön áramforrást használjon.
5. Nyomja meg a főkapcsolót az eszköz bekapcsolásához.
6. Kapcsolódjon az 1 – GbE hálózati adapter 1-es PORTJÁhoz a fizikai eszköz konfigurálásához használt számítógéphez. Az 1. PORT felügyeleti felületként szolgál.
    
    > [!NOTE]
    > Ha a számítógépet közvetlenül csatlakoztatja az eszközhöz (anélkül, hogy a kapcsolón keresztül halad át), használjon egy fordított kábelt vagy egy USB Ethernet-adaptert.

7. A PORT 2, PORT 3, PORT 4, PORT 5 vagy PORT 6 közül legalább egyet az adatközponti hálózathoz/internethez kell csatlakoztatni.

    - Ha a 2-es PORTOT csatlakoztatja, használja az 1-GbE RJ-45 hálózati kábelt.
    - A 10/25 GbE hálózati adapterek esetében használja az SFP + réz kábeleket vagy a szálat. Ha Fiber-t használ, használjon optikai és SFP-adaptert.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta Azure Stack Edge Pro-témaköröket, például a következőket:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz állványra szerelése
> * Az eszköz bekábelezése

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan csatlakozhat az eszközhöz.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro összekötése](./azure-stack-edge-gpu-deploy-connect.md)
