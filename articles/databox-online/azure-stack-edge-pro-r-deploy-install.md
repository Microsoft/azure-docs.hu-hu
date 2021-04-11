---
title: Oktatóanyag a Azure Stack Edge Pro R fizikai eszköz telepítéséhez | Microsoft Docs
description: A Azure Stack Edge Pro R telepítésének második oktatóanyaga azt ismerteti, hogyan lehet a fizikai eszközt az áramellátáshoz és a hálózathoz csatlakozni.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
ms.openlocfilehash: c751c1d9caa06973171d35d5e6bd1f945f9d7a77
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059904"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>Oktatóanyag: Azure Stack Edge Pro R telepítése

Ez az oktatóanyag leírja, hogyan telepítheti Azure Stack Edge Pro R fizikai eszközét. A telepítési eljárás magában foglalja az eszköz kábelezését.

A telepítés elvégzése körülbelül 30 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz vizsgálata
> * Az eszköz bekábelezése

## <a name="prerequisites"></a>Előfeltételek

A fizikai eszközök telepítésének előfeltételei a következők:

### <a name="for-the-azure-stack-edge-resource"></a>Az Azure Stack Edge-erőforráshoz

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Elvégezte az [Azure stack Edge Pro R üzembe helyezésének előkészítése](azure-stack-edge-pro-r-deploy-prep.md)című témakör összes lépését.
    * Létrehozott egy Azure Stack Edge-erőforrást az eszköz üzembe helyezéséhez.
    * Létrehozta az aktiválási kulcsot az eszköz Azure Stack Edge-erőforrással való aktiválásához.

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>Az Azure Stack Edge Pro R fizikai eszközhöz

Az eszköz üzembe helyezése előtt:

- Győződjön meg róla, hogy az eszköz biztonságosan működik-e egy sima, stabil és szintű munkafelületen.
- Győződjön meg arról, hogy az eszköz beállításához kijelölt helyen van:
    - Standard AC-áramellátás független forrásból

        -VAGY-
    - Egy rack Power Distribution Unit (PDU). Az eszköz szünetmentes áramforrással (UPS) van elküldve
    

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén

Előkészületek:

- Tekintse át a Azure Stack Edge Pro R üzembe helyezésének hálózati követelményeit, és konfigurálja az adatközponti hálózatot a követelmények szerint. További információ: [Azure stack Edge Pro R hálózatkezelési követelményei](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements).

- Győződjön meg arról, hogy az eszköz optimális működéséhez 20 Mbps sebesség szükséges.


## <a name="inspect-the-device"></a>Az eszköz vizsgálata

Ezt az eszközt egyetlen egységként szállítjuk. Az eszköz kibontásához hajtsa végre a következő lépéseket.

1. Helyezze a csomagot egy sima, vízszintes felületre.
2. Ellenőrizze az eszköz meghibásodását. Nyissa meg az esetet, és ellenőrizze az eszközt. Ha az eset vagy az eszköz sérültnek tűnik, forduljon a Microsoft ügyfélszolgálatahoz, és segítsen megállapítani, hogy az eszköz megfelelően működik-e.
3. Az eset megnyitása után ellenőrizze, hogy rendelkezik-e az alábbiakkal:
    - Egyetlen ház Azure Stack Edge Pro R-eszköz
    - Egy szünetmentes tápegység (UPS)
    - 2 rövid tápkábel az eszköz csatlakoztatásához az UPS-hez
    - 1 tápkábel a szünetmentes áramforrás csatlakoztatásához

Ha nem kapta meg az itt felsorolt összes elemet, lépjen kapcsolatba Azure Stack Edge Pro R-támogatással. A következő lépés az eszköz csatlakoztatása.


## <a name="cable-the-device"></a>Az eszköz bekábelezése

Az alábbi eljárások azt ismertetik, hogyan lehet a Azure Stack Edge Pro R-eszközt a Power és a Network számára összekapcsolni.

Az eszköz kábelezésének megkezdése előtt a következőkre lesz szüksége:

- Az Azure Stack Edge Pro R fizikai eszköz a telepítési helyen.
- Egy tápkábel.
- Legalább egy 1-GbE RJ-45 hálózati kábel a felügyeleti felülethez való csatlakozáshoz. Az eszközön két 1-GbE hálózati adapter (egy felügyeleti és egy adathálózati) található.
- Egy 10/25 – GbE SFP + réz kábel minden konfigurálandó adathálózati adapterhez. Legalább egy, a 3. és a 4. port közötti adathálózati adaptert csatlakoztatni kell az internethez (az Azure-hoz való csatlakozással).  
- Hozzáférés egy adott energiagazdálkodási egységhez (ajánlott).

> [!NOTE]
> - Ha csak egy adathálózati adaptert csatlakoztat, javasoljuk, hogy használjon 25/10 GbE hálózati adaptert, például a 3-as PORTOT vagy a 4-es portot az Azure-ba való adatküldéshez. 
> - A legjobb teljesítmény érdekében és nagy mennyiségű adat kezeléséhez fontolja meg az összes adatport csatlakoztatását.
> - Az Azure Stack Edge Pro R-eszköznek csatlakoznia kell az adatközpont-hálózathoz, hogy az adatforrás-kiszolgálókról is képes legyen az adatok betöltésére.

Azure Stack Edge Pro R-eszközön:

- Az előlapon a lemezmeghajtók és a főkapcsoló gomb található.

    - Az eszköz elején 8 lemezes tárolóhely található.
    - Az eszközön 2 X M. 2 SATA-lemez található, amely operációs rendszer lemezként szolgál. 

- A háttérrendszer redundáns tápegységeket (PSUs) tartalmaz.
- A hátsó síkon négy hálózati adapter van:

    - 2 1 – Gbps felületek.
    - 2 25 – Gbps felületek, amelyek 10 GB/s illesztőfelületként is használhatók.
    - Egy alaplapi felügyeleti vezérlő (BMC).

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
A következő lépésekkel csatlakoztassa az eszközt az áramellátáshoz és a hálózathoz.

1. Azonosítsa az eszköz hátsó síkja különböző portokat.

    ![Kábeles eszköz hátsó síkja](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. Keresse meg a lemezes tárolóhelyeket és a főkapcsoló gombot az eszköz elején.

    ![Az eszköz elülső síkja](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. Kösse össze a tápkábel egyik végét a UPS-ben. Csatlakoztassa a tápkábel másik végét a rack energiagazdálkodási egységéhez (PDU). 
5. Nyomja meg a főkapcsolót az eszköz bekapcsolásához.
6. Kapcsolódjon az 1 – GbE hálózati adapter 1-es PORTJÁhoz a fizikai eszköz konfigurálásához használt számítógéphez. A PORT 1 a dedikált felügyeleti felület.
7. Csatlakoztasson egy vagy több 2-es PORTOT, a 3. PORTOT vagy a 4-es PORTOT az adatközpont hálózatához/internethez.

    - A PORT 2 csatlakoztatása esetén használja az RJ-45 hálózati kábelt.
    - A 10/25 GbE hálózati adapterek esetében használja az SFP + Copper kábeleket.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta Azure Stack Edge Pro R-témákat, például a következőket:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz bekábelezése

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan csatlakozhat az eszközhöz.

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Stack Edge Pro R-hez](./azure-stack-edge-pro-r-deploy-connect.md)
