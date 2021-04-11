---
title: Oktatóanyag a speciális üzembe helyezési adatok szűréséhez, elemzéséhez Azure Stack Edge Pro-beli számításokkal | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat számítási szerepkört Azure Stack Edge Pro-ban, és hogyan alakíthatja át az adatokat a speciális üzembe helyezési folyamathoz az Azure-ba való küldés előtt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: alkohli
ms.openlocfilehash: 81953f573c47d229fcaccd7c11f62155acd2f119
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063644"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro-for-advanced-deployment-flow"></a>Oktatóanyag: az adatátalakítás a Azure Stack Edge Pro-val speciális üzembe helyezési folyamat esetén

Ez az oktatóanyag azt ismerteti, hogyan lehet számítási szerepkört konfigurálni egy speciális telepítési folyamathoz az Azure Stack Edge Pro-eszközön. A számítási szerepkör konfigurálása után Azure Stack Edge Pro átalakíthatja az adatokat, mielőtt elküldené az Azure-ba.

A számítás konfigurálható az eszköz egyszerű vagy speciális üzembe helyezési folyamatához.

| Feltételek | Egyszerű üzembe helyezés                                | Speciális üzembe helyezés                   |
|------------------|--------------------------------------------------|---------------------------------------|
| A következőre szánt     | IT-rendszergazdák                                | Fejlesztők                            |
| Típus             | Azure Stack Edge szolgáltatás használata modulok üzembe helyezéséhez      | A IoT Hub szolgáltatás használata modulok üzembe helyezéséhez |
| Üzembe helyezett modulok | Egyirányú                                           | Láncolt vagy több modul           |


Ez az eljárás körülbelül 20 – 30 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Eseményindító hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt beállít egy számítási szerepkört a Azure Stack Edge Pro-eszközön, ügyeljen rá, hogy:

- Aktiválta a Azure Stack Edge Pro-eszközt a következő témakörben leírtak szerint: a [Azure stack Edge Pro csatlakoztatása, beállítása és aktiválása](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Számítás konfigurálása

A Azure Stack Edge Pro-ban való számítás konfigurálásához létre kell hoznia egy IoT Hub-erőforrást.

1. Az Azure Stack Edge-erőforrás Azure Portal válassza az **Áttekintés** lehetőséget. A jobb oldali ablaktáblában válassza ki a **IoT Edge** csempét.

    ![Ismerkedés a számítási feladatokkal](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. A **IoT Edge szolgáltatás engedélyezése** csempén válassza a **Hozzáadás** lehetőséget. Ez a művelet lehetővé teszi IoT Edge szolgáltatást, amely lehetővé teszi IoT Edge modulok helyi telepítését az eszközön.

    ![Ismerkedés a 2. számítási feladatokkal](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. A **IoT Edge létrehozása szolgáltatásban** adja meg a következőket:

   
    |Mező  |Érték  |
    |---------|---------|
    |Előfizetés     |Válasszon egy előfizetést a IoT Hub erőforráshoz. Kiválaszthatja ugyanazt az előfizetést, mint amelyet az Azure Stack Edge-erőforrás használ.        |
    |Erőforráscsoport     |Adja meg a IoT Hub erőforráshoz tartozó erőforráscsoport nevét. Kiválaszthatja ugyanazt az erőforráscsoportot, mint amelyet az Azure Stack Edge-erőforrás használ.         |
    |IoT Hub     | Válasszon az **új** vagy a **meglévő** listából. <br> Alapértelmezés szerint IoT-erőforrás létrehozásához a rendszer Standard szintet (S1) használ. Ingyenes szintű IoT-erőforrás használatához hozzon létre egyet, majd válassza ki a létrehozott erőforrást.      |
    |Name     |Fogadja el az alapértelmezett értéket, vagy adja meg a IoT Hub erőforrás nevét.         |

    ![Ismerkedés a 3. számítási feladatokkal](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. A IoT Hub erőforrás létrehozása néhány percet vesz igénybe. A IoT Hub erőforrás létrehozása után az **áttekintő** frissítések jelzik, hogy a IoT Edge szolgáltatás fut. 

    Ha a IoT Edge szolgáltatás a peremhálózati eszközön van konfigurálva, két eszközt hoz létre: egy IoT-eszközt és egy IoT Edge-eszközt. Mindkét eszköz megtekinthető a IoT Hub erőforrásban. Ezen a IoT Edge eszközön a IoT Edge futtatókörnyezet is fut. Ezen a ponton csak a Linux platform érhető el a IoT Edge eszközhöz.

    Annak ellenőrzéséhez, hogy a peremhálózati számítási szerepkör konfigurálva van-e, válassza **IoT Edge szolgáltatás > tulajdonságok** lehetőséget, és tekintse meg a IoT eszközt és a IoT Edge eszközt. 

    ![Ismerkedés a 4. számítási feladatokkal](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-4.png)
    

## <a name="add-shares"></a>Megosztások hozzáadása

Az oktatóanyag speciális üzembe helyezéséhez két megosztásra lesz szüksége: egy peremhálózati megosztásra és egy másik peremhálózati helyi megosztásra.

1. Vegyen fel egy peremhálózati megosztást az eszközön a következő lépések végrehajtásával:

    1. Az Azure Stack Edge-erőforrásban válassza a **IoT Edge > megosztások** lehetőséget.
    2. A **megosztások** lapon a parancssáv területen válassza a **+ megosztás hozzáadása** elemet.
    3. A **megosztás hozzáadása** panelen adja meg a megosztás nevét, és válassza ki a megosztás típusát.
    4. Az Edge-megosztás csatlakoztatásához jelölje be a **megosztás Edge-számítással való használatának** jelölőnégyzetét.
    5. Válassza ki a **Storage-fiókot**, a **Storage szolgáltatást**, egy meglévő felhasználót, majd válassza a **Létrehozás** lehetőséget.

        ![Peremhálózati megosztás hozzáadása](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    Az Edge-megosztás létrehozása után a rendszer sikeres létrehozási értesítést fog kapni. A rendszer frissíti a megosztási listát, hogy tükrözze az új megosztást.

2. Vegyen fel egy peremhálózati helyi megosztást a peremhálózati eszközön úgy, hogy megismétli az előző lépés összes lépését, és bejelöli a **beállítás helyi megosztásként** jelölőnégyzetet. A helyi megosztásban lévő adatmennyiség az eszközön marad.

    ![Peremhálózat helyi megosztásának hozzáadása](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. A **megosztások** panelen láthatja a megosztások frissített listáját.

    ![Megosztások frissített listája](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Az újonnan létrehozott helyi megosztás tulajdonságainak megtekintéséhez válassza ki a megosztást a listából. Az **Edge számítási modulok helyi csatlakoztatási pontja** mezőben másolja ki a megosztásnak megfelelő értéket.

    Ezt a helyi csatlakoztatási pontot fogja használni a modul telepítésekor.

    ![A "helyi csatlakoztatási pont az Edge számítási modulok számára" mező](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. A létrehozott peremhálózat-megosztás tulajdonságainak megtekintéséhez válassza ki a megosztást a listából. Az **Edge számítási modulok helyi csatlakoztatási pontja** mezőben másolja ki a megosztásnak megfelelő értéket.

    Ezt a helyi csatlakoztatási pontot fogja használni a modul telepítésekor.

    ![Egyéni modul hozzáadása](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Eseményindító hozzáadása

1. Nyissa meg az Azure Stack Edge-erőforrást, és lépjen **IoT Edge > eseményindítók** oldalra. Válassza az **+ trigger hozzáadása** lehetőséget.

    ![Trigger hozzáadása](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Az **trigger hozzáadása panelen adja** meg a következő értékeket.

    |Mező  |Érték  |
    |---------|---------|
    |Trigger neve     | Az trigger egyedi neve.         |
    |Trigger típusa     | Válassza a **fájl** trigger lehetőséget. Egy fájltrigger aktiválódik, valahányszor bekövetkezik egy fájlesemény, például amikor a rendszer egy fájlt ír a bemeneti megosztásba. Egy másik oldalon ütemezett eseményindító, amely az Ön által meghatározott ütemezés alapján következik be. Ebben a példában egy fájl-triggerre van szükségünk.    |
    |Bemeneti megosztás     | Válasszon egy bemeneti megosztást. Ebben az esetben a peremhálózat helyi megosztása a bemeneti megosztás. Az itt használt modul áthelyezi a fájlokat a peremhálózati helyi megosztásból egy peremhálózati megosztásba, ahol azokat a rendszer feltölti a felhőbe.        |

    ![2. trigger hozzáadása](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. A trigger létrehozása után értesítést kap. Az eseményindítók listája frissült az újonnan létrehozott eseményindító megjelenítéséhez. Válassza ki az imént létrehozott triggert.

    ![3. trigger hozzáadása](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Másolja és mentse a minta útvonalát. Ezt a mintavételezési útvonalat módosítani fogja, majd később a IoT Hubben kell használni.

    `"sampleroute&quot;: &quot;FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![4. trigger hozzáadása](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Modul hozzáadása

Ezen a peremhálózati eszközön nincsenek egyéni modulok. Hozzáadhat egy egyéni vagy egy előre elkészített modult. Ha szeretné megismerni, hogyan hozhat létre egyéni modult, ugorjon a [C# modul fejlesztése a Azure stack Edge Pro-eszközhöz](azure-stack-edge-create-iot-edge-module.md)című témakörre.

Ebben a szakaszban egy egyéni modult ad hozzá a IoT Edge eszközhöz, amelyet a [C# modul fejlesztése a Azure stack Edge Pro](azure-stack-edge-create-iot-edge-module.md)-hoz című témakörben hozott létre. Ez az egyéni modul a peremhálózati eszköz peremhálózati helyi megosztásának fájljait veszi át, és áthelyezi őket egy peremhálózati (Felhőbeli) megosztásra az eszközön. A felhőalapú megosztás ezután leküldi a fájlokat a Felhőbeli megosztáshoz társított Azure Storage-fiókba.

1. Nyissa meg az Azure Stack Edge-erőforrást, és válassza a **IoT Edge > áttekintés** lehetőséget. A **modulok** csempén válassza az **ugrás az Azure IoT hub** lehetőséget.

    ![Speciális központi telepítés kiválasztása](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. A IoT Hub erőforrásban nyissa meg **IoT Edge eszköz** elemet, majd válassza ki IoT Edge eszközét.

    ![Ugrás a IoT Edge eszközre IoT Hub](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Az **eszköz részletei** lapon válassza a **modulok beállítása** lehetőséget.

    ![A modulok beállítása hivatkozás](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-3.png)

4. A **modulok hozzáadása** területen tegye a következőket:

    1. Adja meg az egyéni modulhoz tartozó tároló beállításjegyzék-beállításainak nevét, nevét, felhasználónevét és jelszavát.
    A név, a cím és a felsorolt hitelesítő adatok a modulok megfelelő URL-címmel való lekérésére használatosak. A modul üzembe helyezéséhez az **Üzemelő példány moduljai** területen válassza ki az **IoT Edge-modult**. Ez a IoT Edge-modul egy Docker-tároló, amelyet az Azure Stack Edge Pro-eszközhöz társított IoT Edge eszközön telepíthet.

        ![A modulok beállítása lap](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Adja meg az egyéni IoT Edge-modul beállításait. Adja meg a következő értékeket.
     
        |Mező  |Érték  |
        |---------|---------|
        |Név     | A modul egyedi neve. Ez a modul egy Docker-tároló, amelyet a Azure Stack Edge Pro-hoz társított IoT Edge eszközre telepíthet.        |
        |Rendszerkép URI-ja     | A modulhoz tartozó tároló rendszerképéhez tartozó képuri.        |
        |Szükséges hitelesítő adatok     | Ha be van jelölve, a rendszer a felhasználónevet és a jelszót használja a modulok megfelelő URL-címmel való lekéréséhez.        |
    
        A **tároló létrehozása beállítások** mezőben adja meg a peremhálózati modulok helyi csatlakoztatási pontjait, amelyeket az előző lépésekben másolt a peremhálózat-megosztáshoz és a peremhálózati helyi megosztáshoz.

        > [!IMPORTANT]
        > Az itt használt elérési utak a tárolóba vannak csatlakoztatva, ezért meg kell egyezniük a tárolóban lévő funkciókkal. Ha [egy egyéni modult](azure-stack-edge-create-iot-edge-module.md#update-the-module-with-custom-code)követ, a modulban megadott kód a másolt elérési utakat várja. Ne módosítsa ezeket az elérési utakat.
    
        A **tároló létrehozása beállítások** mezőben a következő mintát szúrhatja be:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Adja meg a modulhoz használt környezeti változókat. A környezeti változók olyan opcionális információkat biztosítanak, amelyek segítenek meghatározni a környezetet, amelyben a modul fut.

        ![A tároló létrehozása beállítások mező](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Ha szükséges, konfigurálja a speciális peremhálózati futtatókörnyezet beállításait, majd kattintson a **tovább** gombra.

        ![2. egyéni modul hozzáadása](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5. Az **útvonalak megadása** területen állítsa be a modulok közötti útvonalakat.  
   
   ![Az útvonalak meghatározása](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-7.png)

    Az *útvonalat* lecserélheti a korábban átmásolt következő útvonal-karakterláncra. Ebben a példában adja meg annak a helyi megosztásnak a nevét, amely a Felhőbeli megosztásba küldi az adattovábbítást. Cserélje le a `modulename` nevet a modul nevére. Kattintson a **Tovább** gombra.
        
    ```
    "route&quot;: &quot;FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Az útvonalak meghatározása szakasz](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-8.png)

6. Az **üzembe helyezés áttekintése** területen tekintse át az összes beállítást, majd kattintson a **Submit (elküldés** ) gombra a modul központi telepítéshez való küldéséhez.

   ![A modulok beállítása 2. oldal](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Ez a művelet elindítja a modul üzembe helyezését. Az üzembe helyezés befejezése után a modul **futtatókörnyezeti állapota** **fut**.

    ![3. egyéni modul hozzáadása](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Adatok átalakításának ellenőrzése, átvitel

Az utolsó lépés az, hogy ellenőrizze, hogy a modul csatlakoztatva van-e, és hogy a várt módon fut-e. A modul futtatási ideje állapotának futnia kell a IoT Edge eszközön a IoT Hub erőforrásban.

A következő lépésekkel ellenőrizheti az adatok átalakítását és az Azure-ba való átvitelt.
 
1. A Fájlkezelőben a korábban létrehozott Edge helyi és peremhálózati megosztásokhoz is csatlakozhat.

   ![Adatok átalakításának ellenőrzése](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1. Adja hozzá az adatokat a helyi megosztáshoz.

   ![Az adatátalakítás 2 ellenőrzése](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![Az adatátalakító 3 ellenőrzése](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Az adatok ezután a Felhőbeli megosztásról a Storage-fiókba kerülnek. Az adatmegjelenítéshez nyissa meg a Storage-fiókját, majd válassza a **Storage Explorer** lehetőséget. A feltöltött adatait megtekintheti a Storage-fiókjában.

    ![Az adatátalakítás 4 ellenőrzése](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Végrehajtotta az érvényesítési folyamatot.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Eseményindító hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

Az Azure Stack Edge Pro-eszköz felügyeletének megismeréséhez lásd:

> [!div class="nextstepaction"]
> [Helyi webes felhasználói felület használata Azure Stack Edge Pro felügyeletéhez](azure-stack-edge-manage-access-power-connectivity-mode.md)
