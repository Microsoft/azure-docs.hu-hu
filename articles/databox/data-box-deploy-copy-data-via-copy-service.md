---
title: 'Oktatóanyag: másolás az eszközre az adatmásolási szolgáltatás használatával'
titleSuffix: Azure Data Box
description: Ebből az oktatóanyagból megtudhatja, hogyan másolhat Adatmásolást a Azure Data Box eszközre az adatmásolási szolgáltatás segítségével.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: e664055893bbdef0f7090811b8a160a1b8a4a1fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124048"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>Oktatóanyag: az adatmásolási szolgáltatás használata az Adatmásolás Azure Data Boxba (előzetes verzió)

Ez az oktatóanyag azt ismerteti, hogyan lehet adatot befogadni az adatmásolási szolgáltatás köztes gazdagép nélküli használatával. Az adatmásolási szolgáltatás helyileg fut Microsoft Azure Data Box, csatlakozik a hálózathoz csatlakoztatott Storage-eszközhöz (NAS) az SMB-n keresztül, és átmásolja az adatait a Data Boxba.

Az adatmásolási szolgáltatás használata:

- Olyan NAS-környezetekben, ahol előfordulhat, hogy a közbenső gazdagépek nem érhetők el.
- Az adatok betöltéséhez és feltöltéséhez szükséges heteket tartalmazó kisméretű fájlok. Az adatmásolási szolgáltatás jelentősen javítja a kis méretű fájlok betöltését és feltöltési idejét.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Adatok másolása a Data Boxra

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Elvégezte az oktatóanyagot: [Azure Data Box beállítása](data-box-deploy-set-up.md).
2. Megkapta a Data Box eszközét, és a megrendelési állapotot a portálon **kézbesítjük**.
3. Rendelkezik a forrás NAS-eszköz hitelesítő adataival, amelyeket az adatmásoláshoz fog csatlakozni.
4. Nagy sebességű hálózathoz csatlakozik. Javasoljuk, hogy legalább 1 10-Gigabit Ethernet-(GbE-) kapcsolatban legyen. Ha egy 10 GbE-kapcsolat nem érhető el, akkor 1 GbE adatkapcsolatot használhat, de a másolási sebességet is érinti.

## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

Miután csatlakozott a NAS-eszközhöz, a következő lépés az adatai másolása. Az adatok másolásának megkezdése előtt tekintse át a következőket:

* Az adatok másolása közben ellenőrizze, hogy az adatok mérete megfelel-e az [Azure Storage-ban és a Data Box korlátokban](data-box-limits.md)leírt méretkorlát-korlátoknak.

* Ha az Data Box által feltöltött adatok párhuzamosan fel vannak töltve más alkalmazások által Data Boxn kívül, a feltöltési feladatok és az adatok sérülése is eredményezhet.

* Ha az adatmásolási szolgáltatás az Adatmásolás során módosul, az adatvesztés vagy az adatsérülés tapasztalható.

> [!IMPORTANT]
> Gondoskodjon róla, hogy megtartja a forrásadatok egy másolatát addig, amíg ellenőrizheti, hogy a Data Box valóban átvitte-e az adatokat az Azure Storage-ba.

Az adatok adatmásolási szolgáltatással történő másolásához létre kell hoznia egy feladatot:

1. A Data Box eszköz helyi webes felületén nyissa meg az   >  **Adatmásolás** kezelése lapot.
2. Az **Adatmásolás** lapon válassza a **Létrehozás** lehetőséget.

    ![Válassza a létrehozás lehetőséget az "Adatmásolás" oldalon](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. A **feladatok és indítás konfigurálása** párbeszédpanelen adja meg a következő mezőket:
    
    |Mező                          |Érték    |
    |-------------------------------|---------|
    |**Feladat neve**                       |A feladatokhoz 230 karakternél rövidebb egyedi név. A következő karakterek nem engedélyezettek a feladattípusban:,,,,,, \<, \> \| \? \* \\ \: \/ és \\\.         |
    |**Forráshely**                |Adja meg az adatforrás SMB-elérési útját a következő formátumban: `\\<ServerIPAddress>\<ShareName>` vagy `\\<ServerName>\<ShareName>` .        |
    |**Felhasználónév**                       |A Felhasználónév `\\<DomainName><UserName>` formátuma az adatforrás eléréséhez. Ha egy helyi rendszergazda csatlakozik, akkor explicit biztonsági engedélyekre van szükségük. Kattintson a jobb gombbal a mappára, válassza a **Tulajdonságok** lehetőséget, majd válassza a **Biztonság** elemet. Ehhez hozzá kell adnia a helyi rendszergazdát a **Biztonság** lapon.       |
    |**Jelszó**                       |Az adatforrás eléréséhez használt jelszó.           |
    |**Cél tárfiók**    |Válassza ki a cél Storage-fiókot, hogy az adatok a listáról legyenek feltöltve.         |
    |**Cél típusa**       |Válassza ki a cél tárolási típust a listából: **blob letiltása**, **oldal blobja** vagy **Azure Files**.        |
    |**Cél tároló/megosztás**    |Adja meg annak a tárolónak vagy megosztásnak a nevét, amelyhez fel kívánja tölteni az adatait a célhely Storage-fiókjába. A név lehet egy megosztás neve vagy egy tároló neve. Például használhatja a következőket: `myshare` vagy `mycontainer`. A nevet megadhatja a (z) formátumban is `sharename\directory_name` `containername\virtual_directory_name` .        |
    |**Fájlokra vonatkozó megfelelő minta másolása**    | A fájlnév-megfeleltetési mintát a következő két módon adhatja meg:<ul><li>**Helyettesítő kifejezések használata:** Csak `*` `?` a helyettesítő karakteres kifejezésekben támogatott. A kifejezés például megfelel a `*.vhd` kiterjesztéssel rendelkező összes fájlnak `.vhd` . Hasonlóképpen, a `*.dl?` kiterjesztéssel `.dl` vagy a-vel kezdődő összes fájlra megegyezik `.dl` , például: `.dll` . Hasonlóképpen, `*foo` az összes olyan fájlra illeszkedik, amelynek a nevei véget ért `foo` .<br>A mezőbe közvetlenül is beírhatja a helyettesítő karaktert. Alapértelmezés szerint a mezőben megadott értéket helyettesítő kifejezésként kezeli a rendszer.</li><li>**Reguláris kifejezések használata:** A POSIX-alapú reguláris kifejezések támogatottak. A reguláris kifejezés például megfelel a `.*\.vhd` kiterjesztéssel rendelkező összes fájlnak `.vhd` . A reguláris kifejezések esetében adja meg a közvetlenül a következőt: `<pattern>` `regex(<pattern>)` . További információ a reguláris kifejezésekről: [reguláris kifejezés nyelve – gyors hivatkozás](/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Fájl optimalizálása**              |Ha ez a funkció engedélyezve van, az 1 MB-nál kisebb fájlok a betöltés során lesznek csomagolva. Ez a csomagolás felgyorsítja a kis méretű fájlok adatmásolási feladatait. Emellett jelentős időt takaríthat meg, ha a fájlok száma messze meghaladja a címtárak számát.        |
 
4. Válassza az **Indítás** elemet. A rendszer érvényesíti a bemeneteket, és ha az érvényesítés sikeres, akkor elindul a feladatok. Eltarthat néhány percig, amíg a feladatok elindulnak.

    ![Feladatok indítása a "feladatok és indítás konfigurálása" párbeszédpanelről](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. A rendszer létrehozza a megadott beállításokkal rendelkező feladatot. Feladatokhoz szüneteltetheti, folytathatja, megszakíthatja vagy újraindíthatja a feladatokat. Jelölje be a feladatok neve melletti jelölőnégyzetet, majd válassza ki a megfelelő gombot.

    ![Feladatok kezelése az "adatok másolása" oldalon](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Szüneteltetheti a feladatokat, ha az a NAS-eszköz erőforrásait a maximális idő alatt befolyásolja:

        ![Feladat szüneteltetése az "adatok másolása" oldalon](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        A feladatot később is folytathatja a munkaidőn kívüli órákban:

        ![Feladatok folytatása az "adatok másolása" oldalon](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Bármikor megszakíthatja a feladatokat:

        ![Feladat megszakítása az "adatok másolása" oldalon](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Egy feladat megszakításakor megerősítésre van szükség:

        ![A feladatok megszakításának megerősítése](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Ha úgy dönt, hogy megszakítja a feladatot, a már másolt adatok nem törlődnek. Ha törölni szeretné a Data Box eszközre másolt összes adatfájlt, állítsa alaphelyzetbe az eszközt.

        ![Eszköz alaphelyzetbe állítása](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Ha lemond vagy szüneteltet egy feladatot, előfordulhat, hogy a nagyméretű fájlok csak részben másolhatók. Ezeket a részben másolt fájlokat a rendszer az Azure-ba feltöltötte. Egy feladat megszakítása vagy felfüggesztése esetén győződjön meg arról, hogy a fájlok megfelelően lettek másolva. A fájlok ellenőrzéséhez tekintse meg az SMB-megosztásokat, vagy töltse le az AJ-fájlt.

    - Ha egy átmeneti hiba miatt nem sikerült végrehajtani a feladatot, például hálózati hibát észlelt, újraindíthatja a feladatokat. A feladatokat azonban nem lehet újraindítani, ha elérte a terminál állapotát, például **sikeres** vagy **hibákkal fejeződött** be. A feladatok meghibásodását a fájl-elnevezési vagy a fájlméretbeli problémák okozhatják. A rendszer naplózza ezeket a hibákat, de a befejezése után a feladatot nem lehet újraindítani.

        ![Sikertelen feladatok újraindítása](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Ha hibát tapasztal, és nem tudja újraindítani a feladatot, töltse le a naplókat, és keresse meg a hibát a naplófájlokban. A probléma javítása után hozzon létre egy új feladatot a fájlok másolásához. [A fájlokat SMB-kapcsolaton keresztül is másolhatja](data-box-deploy-copy-data.md).
    
    - Ebben a kiadásban nem törölhet feladatot.
    
    - Korlátlan számú feladatot hozhat létre, de egyszerre legfeljebb 10 feladatot futtathat egyszerre.
    - Ha a **fájl optimalizálása** be van kapcsolva, a kis méretű fájlok betöltése a másolási teljesítmény javítása érdekében történik. Ezekben az esetekben egy csomagolt fájl jelenik meg (a fájl neveként GUID azonosítóval fog rendelkezni). Ne törölje ezt a fájlt. A feltöltés során a rendszer kicsomagolja.

6. Amíg a művelet folyamatban van, az **adatok másolása** oldalon:

    - Az **állapot** oszlopban megtekintheti a másolási feladatok állapotát. Az állapot a következőket teheti:
        - **Futó**
        - **Sikertelen**
        - **Sikeres**
        - **Felfüggesztés**
        - **Szüneteltetve**
        - **Megszakítás**
        - **Megszakítva**
        - **Befejeződött, hibákkal**
    - A **Files (fájlok** ) oszlopban láthatja a másolandó fájlok számát és teljes méretét.
    - A **feldolgozott** oszlopban láthatja a feldolgozott fájlok számát és teljes méretét.
    - A feladathoz tartozó **részletek** oszlopban válassza a **nézet** lehetőséget a feladatok részleteinek megtekintéséhez.
    - Ha bármilyen hiba fordul elő a másolási folyamat során, ahogy az a **# errors (hibák** ) oszlopban látható, lépjen a **hibanapló** oszlopra, és töltse le a hibaelhárításhoz szükséges hibákat.

Várja meg, amíg a másolási feladatok befejeződik. Mivel néhány hiba csak a **Kapcsolódás és a másolás** lapon van naplózva, győződjön meg arról, hogy a másolási feladatokhoz nem történt hiba, mielőtt a következő lépésre lép.

![Nincs hiba a "kapcsolat és másolás" oldalon](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Az adatok integritásának biztosítása érdekében egy ellenőrzőösszeget számítunk fel az adatok másolása során. A másolás befejezése után válassza az **irányítópult megtekintése** lehetőséget, hogy ellenőrizze a felhasznált területet és a szabad területet az eszközön.
    
![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

A másolási feladatok befejezése után kiválaszthatja **szállításra való előkészítés**.

>[!NOTE]
> A **szállításra való előkészítés** nem futtatható, amíg a másolási feladatok folyamatban vannak.

## <a name="next-steps"></a>Következő lépések

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan szállíthatja vissza Data Box eszközét a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box eszköz szállítása a Microsoftnak](./data-box-deploy-picked-up.md)