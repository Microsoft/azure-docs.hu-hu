---
title: StorSimple virtuális tömb webes felhasználói felületének felügyelete | Microsoft Docs
description: Ismerteti, hogyan lehet alapszintű eszközöket felügyeleti feladatokat végrehajtani a StorSimple virtuális tömb webes FELÜLETén.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 6bea9c9b7807ffb32b6071c968d3186965477009
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000929"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>A webes felhasználói felület használata a StorSimple virtuális tömb felügyeletéhez
![folyamat beállítása folyamatban](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Áttekintés
A cikkben szereplő oktatóanyagok a Microsoft Azure StorSimple Virtual array (más néven StorSimple-alapú virtuális eszköz) használatára vonatkoznak, amely március 2016-os kiadást futtat. Ez a cikk néhány olyan összetett munkafolyamatot és felügyeleti feladatot ismertet, amelyek a StorSimple virtuális tömbön végezhetők el. A StorSimple virtuális tömböt a StorSimple Manager szolgáltatás felhasználói felületének (más néven a portál felhasználói felületének) és az eszköz helyi webes felhasználói felületének használatával kezelheti. Ez a cikk a webes felhasználói felület használatával végrehajtható feladatokra összpontosít.

A cikk az alábbi oktatóanyagokat tartalmazza:

* A szolgáltatásban tárolt adattitkosítási kulcs beszerzése
* A webes felhasználói felület telepítési hibáinak elhárítása
* Naplófájl létrehozása
* Az eszköz leállítása és újraindítása

## <a name="get-the-service-data-encryption-key"></a>A szolgáltatásban tárolt adattitkosítási kulcs beszerzése
A szolgáltatás adattitkosítási kulcsa akkor jön létre, amikor regisztrálja az első eszközt a StorSimple Manager szolgáltatással. Ezt a kulcsot ezután a szolgáltatás regisztrációs kulcsával kell regisztrálni a StorSimple Manager szolgáltatásban található további eszközök regisztrálásához.

Ha elvégezte a szolgáltatási adatok titkosítási kulcsának lekérését, és le kell kérnie azt, hajtsa végre a következő lépéseket a szolgáltatásban regisztrált eszköz helyi webes FELÜLETén.

#### <a name="to-get-the-service-data-encryption-key"></a>A szolgáltatásban tárolt adattitkosítási kulcs beszerzése
1. Kapcsolódjon a helyi webes felhasználói felülethez. Nyissa meg a **konfigurációs**  >  **felhő beállításait**.
2. A lap alján kattintson a **szolgáltatás adattitkosítási kulcsának beolvasása** elemre. Ekkor megjelenik egy kulcs. Másolja ki és mentse a kulcsot.
   
    ![szolgáltatás adattitkosítási kulcsának beolvasása 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>A webes felhasználói felület telepítési hibáinak elhárítása
Bizonyos esetekben, amikor az eszközt a helyi webes felületen konfigurálja, hibákba ütközhet. Az ilyen hibák diagnosztizálásához és elhárításához futtathatja a diagnosztikai teszteket.

#### <a name="to-run-the-diagnostic-tests"></a>A diagnosztikai tesztek futtatása
1. A helyi webes kezelőfelületen lépjen a **Hibaelhárítás**  >  **diagnosztikai tesztek** elemre.
   
    ![diagnosztika futtatása 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. A lap alján kattintson a **diagnosztikai tesztek futtatása** elemre. Ez teszteket kezdeményez a hálózat, az eszköz, a webproxy, az idő vagy a felhő beállításaival kapcsolatos esetleges problémák diagnosztizálásához. A rendszer értesítést küld arról, hogy az eszköz teszteket futtat.
3. A tesztek befejezését követően megjelennek az eredmények. A következő példában a diagnosztikai tesztek eredményei láthatók. Vegye figyelembe, hogy a webproxy beállításai nincsenek konfigurálva ezen az eszközön, ezért a webproxy-teszt nem volt futtatva. A hálózati beállításokkal, a DNS-kiszolgálóval és az időbeállításokkal kapcsolatos egyéb tesztek sikeresek voltak.
   
    ![diagnosztika futtatása 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Naplófájl létrehozása
A naplófájlok a kapcsolódó naplókból állnak, amelyek segítséget nyújthatnak Microsoft ügyfélszolgálatanek az eszközkel kapcsolatos problémák elhárításában. Ebben a kiadásban a helyi webes felületen keresztül lehet létrehozni egy naplófájlt.

#### <a name="to-generate-the-log-package"></a>A naplófájl előállítása
1. A helyi webes kezelőfelületen lépjen a   >  **rendszernaplók** hibaelhárítása című témakörre.
   
    ![1. log csomag előállítása](./media/storsimple-ova-web-ui-admin/image31.png)
2. A lap alján kattintson a **naplófájl létrehozása** lehetőségre. Létrejön a rendszernaplók csomagja. Ez eltarthat néhány percig.
   
    ![2. log csomag előállítása](./media/storsimple-ova-web-ui-admin/image32.png)
   
    A csomag sikeres létrehozása után értesítést kap, és a rendszer frissíti a lapot, hogy jelezze a csomag létrehozásának időpontját és dátumát.
   
    ![3. log-csomag előállítása](./media/storsimple-ova-web-ui-admin/image33.png)
3. Kattintson a **naplófájl letöltése** elemre. A rendszer a tömörített csomagot fogja letölteni.
   
    ![4. log csomag előállítása](./media/storsimple-ova-web-ui-admin/image34.png)
4. Kibonthatja a letöltött naplófájlt, és megtekintheti a rendszernapló fájljait.

## <a name="shut-down-and-restart-your-device"></a>Az eszköz leállítása és újraindítása
A virtuális eszköz a helyi webes felhasználói felületen állítható le vagy indítható újra. Javasoljuk, hogy az újraindítás előtt állítsa offline állapotba a köteteket vagy a megosztásokat a gazdagépen, majd az eszközön. Ez a szolgáltatás a lehető legkisebbre csökkentheti az adatsérülés lehetőségét. 

#### <a name="to-shut-down-your-virtual-device"></a>A virtuális eszköz leállítása
1. A helyi webes kezelőfelületen lépjen a **karbantartási**  >  **energiagazdálkodási beállítások** elemre.
2. Kattintson a lap alján található **Leállítás** elemre.
   
    ![eszköz leállítása 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Megjelenik egy figyelmeztetés, amely azt jelzi, hogy az eszköz leállítása megszakítja az összes folyamatban lévő IO-t, ami állásidőt eredményezett. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![eszköz leállítására vonatkozó figyelmeztetés](./media/storsimple-ova-web-ui-admin/image37.png)
   
    A rendszer értesítést küld arról, hogy a Leállítás megkezdődött.
   
    ![az eszköz leállítása megkezdődött](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Az eszköz ekkor leáll. Ha el szeretné indítani az eszközt, ezt a Hyper-V kezelőjén keresztül kell végrehajtania.

#### <a name="to-restart-your-virtual-device"></a>A virtuális eszköz újraindítása
1. A helyi webes kezelőfelületen lépjen a **karbantartási**  >  **energiagazdálkodási beállítások** elemre.
2. Az oldal alján kattintson az **Újraindítás** elemre.
   
    ![eszköz újraindítása](./media/storsimple-ova-web-ui-admin/image36.png)
3. Megjelenik egy figyelmeztetés, amely szerint az eszköz újraindítása megszakítja az összes folyamatban lévő IOs-t, ami állásidőt eredményezett. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Figyelmeztetés újraindítása](./media/storsimple-ova-web-ui-admin/image37.png)
   
    A rendszer értesítést küld arról, hogy az újraindítás megkezdődött.
   
    ![újraindítás kezdeményezve](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Amíg az újraindítás folyamatban van, elveszíti a kapcsolódást a felhasználói felülethez. A felhasználói felület rendszeres frissítésével nyomon követheti az újraindítást. Azt is megteheti, hogy az eszköz újraindítási állapotát a Hyper-V kezelőjével figyelheti.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [kezelheti az eszközt a StorSimple Manager szolgáltatással](storsimple-virtual-array-manager-service-administration.md).

