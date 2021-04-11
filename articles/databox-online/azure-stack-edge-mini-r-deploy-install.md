---
title: Oktatóanyag a Azure Stack Edge mini R fizikai eszköz telepítéséhez | Microsoft Docs
description: A Azure Stack Edge mini R-eszköz telepítésével kapcsolatos második oktatóanyag azt ismerteti, hogyan kell a fizikai eszközt az áramellátáshoz és a hálózathoz csatlakozni.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 8b154dabd6f672c6fdaf77c5f8d48f80fb40d5d8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060108"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>Oktatóanyag: Azure Stack Edge mini R telepítése

Ez az oktatóanyag leírja, hogyan telepíthet egy Azure Stack Edge mini R fizikai eszközt. A telepítési eljárás magában foglalja az eszköz kábelezését.

A telepítés elvégzése körülbelül 30 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz vizsgálata
> * Az eszköz bekábelezése

## <a name="prerequisites"></a>Előfeltételek

A fizikai eszközök telepítésének előfeltételei a következők:

### <a name="for-the-azure-stack-edge-resource"></a>Az Azure Stack Edge-erőforráshoz

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Elvégezte az [Azure stack Edge mini R üzembe helyezésének előkészítése](azure-stack-edge-mini-r-deploy-prep.md)című témakör összes lépését.
    * Létrehozott egy Azure Stack Edge-erőforrást az eszköz üzembe helyezéséhez.
    * Létrehozta az aktiválási kulcsot az eszköz Azure Stack Edge-erőforrással való aktiválásához.

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>A Azure Stack Edge mini R fizikai eszközhöz

Az eszköz üzembe helyezése előtt:

- Győződjön meg róla, hogy az eszköz biztonságosan működik-e egy sima, stabil és szintű munkafelületen.
- Győződjön meg arról, hogy az eszköz beállításához kijelölt helyen van:
    - Standard AC-áramellátás független forrástól vagy-
    - Egy rack Power Distribution Unit (PDU). 
    

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén

Előkészületek:

- Tekintse át az Azure Stack Edge mini R üzembe helyezésének hálózati követelményeit, és konfigurálja az adatközponti hálózatot a követelmények szerint. További információ: [Azure stack Edge hálózati követelmények](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements).

- Győződjön meg arról, hogy az eszköz optimális működéséhez a minimális sávszélesség 20 Mbps. <!-- engg TBC -->


## <a name="inspect-the-device"></a>Az eszköz vizsgálata

Ezt az eszközt egyetlen egységként szállítjuk. Az eszköz kibontásához hajtsa végre a következő lépéseket.

1. Helyezze a csomagot egy sima, vízszintes felületre.
2. Ellenőrizze az eszköz meghibásodását. Nyissa meg az esetet, és ellenőrizze az eszközt. Ha az eset vagy az eszköz sérültnek tűnik, forduljon a Microsoft ügyfélszolgálatahoz, és segítsen megállapítani, hogy az eszköz megfelelően működik-e.
3. Az eset megnyitása után ellenőrizze, hogy rendelkezik-e az alábbiakkal:
    - Egy hordozható Azure Stack Edge mini R-eszköz csatlakoztatott oldalsó lökhárítókkal
    - Egy akkumulátor és az eszközhöz csatlakoztatott hátsó fedél. 
    - Egy tápkábel az akkumulátor és az áramforrás csatlakoztatásához 

Ha nem kapta meg az itt felsorolt összes elemet, forduljon Azure Stack Edge ügyfélszolgálatához. A következő lépés az eszköz csatlakoztatása.


## <a name="cable-the-device"></a>Az eszköz bekábelezése

Az alábbi eljárások azt ismertetik, hogyan lehet az Azure Stack Edge-eszközt az áramellátáshoz és a hálózathoz.

Az eszköz kábelezésének megkezdése előtt a következőkre lesz szüksége:

- A Azure Stack Edge mini R fizikai eszköz a telepítési helyen.
- Egy tápkábel.
- Legalább egy 1-GbE RJ-45 hálózati kábel a felügyeleti felülethez való csatlakozáshoz. Az eszközön két 1-GbE hálózati adapter (egy felügyeleti és egy adathálózati) található.
- 1 10 – GbE SFP + réz kábel minden konfigurálandó adathálózati adapterhez. Legalább egy, a 3. és a 4. port közötti adathálózati adaptert csatlakoztatni kell az internethez (az Azure-hoz való csatlakozással).  
- Hozzáférés egy adott energiagazdálkodási egységhez (ajánlott).

> [!NOTE]
> - Ha csak egy adathálózati adaptert csatlakoztat, javasoljuk, hogy használjon egy 10 GbE hálózati adaptert, például a 3-as PORTOT vagy a 4-es PORTOT az Azure-ba való adatküldéshez. 
> - A legjobb teljesítmény érdekében és nagy mennyiségű adat kezeléséhez fontolja meg az összes adatport csatlakoztatását.
> - Az Azure Stack Edge-eszköznek csatlakoznia kell az adatközpont-hálózathoz, hogy az képes legyen az adatok adatforrás-kiszolgálókról történő betöltésére. <!-- engg TBC -->

Az Azure Stack Edge-eszközön:

- Az előlapon egy SSD-szolgáltató van. 

    - Az eszközön 1 SSD-lemez található a tárolóhelyen. 
    - Az eszközhöz egy CFx kártya is tartozik, amely az operációs rendszer lemezének tárolására szolgál.
    
- Az előlapon hálózati adapterek és Wi-Fi-hozzáférés érhető el.

    - 2 X 1 GbE RJ 45 hálózati adapterek. Ezek az 1. és a 2. port az eszköz helyi felhasználói felületén.
    - 2 X 10 GbE SFP + hálózati adapterek. Ezek a 3. és 4. port az eszköz helyi felhasználói felületén. 
    - Egy Wi-Fi port, amelyhez Wi-Fi adóvevő tartozik.

- Az előlapon egy főkapcsoló gomb is látható. 

- A hátlapon egy akkumulátor és egy, az eszközre telepített fedél is található. 


A következő lépésekkel csatlakoztassa az eszközt az áramellátáshoz és a hálózathoz.

1. Azonosítsa az eszköz elülső gépe különböző hálózati és tárolási összetevőit.

    ![Hálózati és tárolási felületek az eszközön](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. Keresse meg a főkapcsolót az eszköz elülső sarkának bal alsó sarkában. 

    ![Eszköz elülső síkja az eszköz főkapcsoló gombjával](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. Az akkumulátor az eszköz hátsó síkjával csatlakozik. Azonosítsa az akkumulátoron található második főkapcsolót. 

    ![Eszköz elülső síkja az akkumulátor főkapcsoló gombjával](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    Kösse össze a tápkábel egyik végét az akkumulátorral, a másikat pedig a konnektorral. 

    ![Tápkábel-kapcsolatok](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    Ha csak az akkumulátoron fut (az akkumulátor nincs csatlakoztatva a Power forráshoz), akkor mind a Power kapcsolót, mind az akkumulátor kapcsolóját be kell kapcsolni a pozícióra. Ha az akkumulátor áramforráshoz csatlakozik, csak az eszköz elülső részén lévő főkapcsoló kapcsolót kell bekapcsolni a pozícióra. 

4. Az eszköz bekapcsolásához nyomja meg az első síkban a főkapcsoló gombot. 
    
    > [!NOTE]
    > Ha be-vagy kikapcsolja a tápellátást az eszközön, le kell szorítania a főkapcsoló gomb tetején található fekete gombot, majd be-vagy kikapcsolhatja a bekapcsolás gombot. 

5. Ha a Wi-Fi ezen az eszközön konfigurálja, használja a következő kábelezési diagramot:

    ![Wi-Fi kábelezése](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - Kapcsolódjon az 1 – GbE hálózati adapter 1-es PORTJÁhoz a fizikai eszköz konfigurálásához használt számítógéphez. A PORT 1 a dedikált felügyeleti felület.


    Ha vezetékes konfigurációt használ ehhez az eszközhöz, használja a következő diagramot:
     
    ![Vezetékes kábelezés](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - Kapcsolódjon az 1 – GbE hálózati adapter 1-es PORTJÁhoz a fizikai eszköz konfigurálásához használt számítógéphez. A PORT 1 a dedikált felügyeleti felület.
    - Csatlakoztasson egy vagy több 2-es PORTOT, a 3. PORTOT vagy a 4-es PORTOT az adatközpont hálózatához/internethez.
    
        - A PORT 2 csatlakoztatása esetén használja az RJ-45 hálózati kábelt.
        - A 10 GbE hálózati adapterek esetében használja az SFP + Copper kábeleket.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte az Azure Stack Edge-témaköröket, például a következőket:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz bekábelezése

A következő oktatóanyag az eszköz csatlakoztatását, beállítását és aktiválását mutatja be.

> [!div class="nextstepaction"]
> [Azure Stack Edge összekapcsolásának és beállításának beállítása](./azure-stack-edge-mini-r-deploy-connect.md)
