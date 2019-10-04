---
title: Oktatóanyag az Azure Data Box sorrend |} A Microsoft Docs
description: Megismerheti az Azure Data Box üzembe helyezési követelményeit és a megrendelésének folyamatát
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: b0204673c0706403c8c5a7367be19e590d9cb134
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604092"
---
# <a name="tutorial-order-azure-data-box"></a>Oktatóanyag: Az Azure Data Box sorrend

Az Azure Data Box egy hibrid megoldás, amellyel gyorsan, könnyen és megbízhatóan importálhat helyszíni adatokat az Azure-ba. Az adatokat egy, a Microsoft által biztosított 80 TB (hasznos) kapacitású tárolóeszközre helyezheti át, amelyet aztán visszapostáz a Microsoftnak. Az adatok ezt követően fel lesznek töltve az Azure-ba.

Ez az oktatóanyag leírja, hogyan rendelheti meg az Azure Data Box szolgáltatást. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Data Box üzembe helyezésének előfeltételei
> * A Data Box megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

## <a name="prerequisites"></a>Előfeltételek

Az eszköz üzembe helyezése előtt teljesítse a Data Box szolgáltatás és az eszköz alábbi konfigurációs követelményeit.

### <a name="for-service"></a>A szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
- Győződjön meg róla, hogy a Data Box szolgáltatáshoz használt előfizetés a következő típusok valamelyikébe tartozik:
    - Microsoft nagyvállalati szerződés (EA). További információk az [EA-előfizetésekről](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Felhőszolgáltató (CSP). További információk az [Azure CSP programjáról](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure szponzorálás. További információk az [Azure szponzorálási programjáról](https://azure.microsoft.com/offers/ms-azr-0036p/).

- A Data Box-rendelések létrehozásához tulajdonosi vagy közreműködői jogosultsággal kell rendelkeznie az előfizetésen.

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Rendelkezik egy, az adatközponti hálózatra csatlakoztatott gazdagéppel. A Data Box erről a gépről másolja majd az adatokat. A gazdagépnek egy támogatott operációs rendszert kell futtatnia az [Azure Data Box rendszerkövetelményeinél](data-box-system-requirements.md) leírtaknak megfelelően.
- Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha 10 GbE sebességű kapcsolat nem áll rendelkezésre, 1 GbE sebességű adatkapcsolat is használható, azonban ez rontja a másolási sebességet.


## <a name="order-data-box"></a>A Data Box megrendelése

Az eszköz megrendeléséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. Microsoft Azure hitelesítő adatai használatával jelentkezzen be ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com).
2. Kattintson a **+ Erőforrás létrehozása** gombra, és keressen rá az *Azure Data Box* kifejezésre. Kattintson az **Azure Data Box** lehetőségre.
    
   [![Az Azure Data Box 1 keresése](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Kattintson a **Create** (Létrehozás) gombra.

4. Ellenőrizze, hogy a Data Box szolgáltatás elérhető-e a régióban. Adja meg, vagy jelölje ki a következő információkat, majd kattintson az **Alkalmaz** elemre. 

    |Beállítás  |Value  |
    |---------|---------|
    |Előfizetés     | Válassza ki az EA, CSP vagy az Azure sponsorship-előfizetést a Data Box szolgáltatás. <br> Az előfizetés az Ön számlázási fiókjához lesz társítva.       |
    |Átvitel típusa     | Válassza az **Importálás az Azure-ba** lehetőséget.        |
    |Forrásország     |   Válassza ki az országot vagy régiót, ahol az adatokat jelenleg található.         |
    |Azure-beli célrégió     |     Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat.        |

5. Válassza a Data **Box** lehetőséget. A megrendelésenkénti maximális felhasználható kapacitás 80 TB Hellyel. Nagyobb mennyiségű adat esetén több rendelést is létrehozhat.

      [![Az 1-es Data Box lehetőség kiválasztása](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. A **Megrendelés** területen adja meg a **Megrendelés részleteit**. Adja meg, vagy válassza ki a következő információkat, majd kattintson a **Tovább** gombra.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Name (Név)     |  Adjon meg egy rövid nevet a megrendelés nyomon követéséhez. <br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.      |
    |Erőforráscsoport     |   Használjon egy már létezőt, vagy hozzon létre újat. <br> Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója.         |
    |Azure-beli célrégió     | Válasszon ki egy régiót a tárfiókhoz. <br> További információt a [regionális elérhetőséget](data-box-overview.md#region-availability) tárgyaló témakörben talál.        |
    |A rendeltetési tárolási hely     | Válassza ki a tárfiókot, a felügyelt lemezek vagy mindkettőhöz. <br> A megadott Azure-régió alapján válasszon ki egy vagy több tárfiókot a meglévő tárfiókok szűrt listájából. A Data Box legfeljebb 10 tárfiókkal köthető össze. <br> Ezenkívül létrehozhat egy új **általános célú v1**, **általános célú v2**, vagy **Blob storage-fiók**. <br>Storage-fiókok a virtuális hálózatok támogatottak. Ahhoz, hogy a Data Box szolgáltatás-biztonságos tárfiókokkal működik, a megbízható szolgáltatások belül a tárfiók hálózati tűzfal beállításainak engedélyezése. További információkért lásd: hogyan [hozzáadása az Azure Data Box megbízható szolgáltatásként](../storage/common/storage-network-security.md#exceptions).|

    Ha a storage-fiókot használ a célhelyet, az alábbi képernyőképen láthatja:

    ![Data Box-rendelés storage-fiók](media/data-box-deploy-ordered/order-storage-account.png)

    Data Box segítségével felügyelt lemez gyors létrehozásához a helyi VHD-ből, ha akkor is kell a következő információkat biztosítja:

    |Beállítás  |Value  |
    |---------|---------|
    |Erőforráscsoportok     | Hozzon létre új erőforrás-csoportokat, ha szeretne felügyelt lemez gyors létrehozásához a helyi VHD-ből. Csak akkor, ha az erőforráscsoport korábban lett létrehozva a Data Box-rendelés felügyelt lemez létrehozásakor Data Box szolgáltatás által használható egy meglévő erőforráscsoportot. <br> Adja meg pontosvesszővel elválasztva több erőforráscsoporthoz. Legfeljebb 10 az erőforrás-csoportok támogatottak.|

    ![Data Box-rendelés felügyelt lemez](media/data-box-deploy-ordered/order-managed-disks.png)

    Felügyelt lemezek megadott tárfiók egy átmeneti tárfiókban lesz. A Data Box szolgáltatás feltöltések a VHD-k lapként blobok, az előkészítési tárfiókból felügyelt lemezekké alakítja, és áthelyezi az erőforráscsoportok előtt. További információkért lásd: [adatok ellenőrzése a feltöltése az Azure-bA](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. A **Szállítási cím** területen adja meg a vezeték- és utónevét, a vállalata nevét és postai címét, valamint egy érvényes telefonszámot. Kattintson a **Cím ellenőrzése** elemre. A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről. Kattintson a **tovább**.

8. Az **Értesítési adatok** területen adja meg az e-mail-címeket. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre.

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

9. Tekintse át a megrendeléshez kapcsolódó **Összefoglalót** a kapcsolattartási adatokról, az értesítésekről és az adatvédelmi feltételekről. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

10. Kattintson a **Megrendelés** elemre. A megrendelés létrehozása néhány percet vesz igénybe.


## <a name="track-the-order"></a>A megrendelés nyomon követése

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Nyissa meg a Data Box-rendelés, és folytassa a **áttekintése** állapotának megtekintéséhez. A portálon a megrendelés **Megrendelve** állapotban látható.

Ha nem áll rendelkezésre eszköz, értesítést fog kapni. Ha van elérhető eszköz, a Microsoft kiválaszt egyet a szállításhoz, és előkészíti a csomagot. Az eszköz előkészítése során a következő műveletek lesznek végrehajtva:

- SMB-megosztások létrehozása az eszközzel társított mindegyik tárfiókhoz.
- Hozzáférési hitelesítő adatok (felhasználónév és jelszó) kiosztása mindegyik megosztáshoz.
- Az eszköz zárolásának feloldásához szükséges jelszó létrehozása.
- A Data Box zárolásának célja, hogy megakadályozza az eszközhöz való illetéktelen hozzáférést a teljes eljárás során.

Az eszköz előkészítésének befejeztével a portálon a megrendelés **Feldolgozott** állapotban jelenik meg.

![Data Box rendelés feldolgozva](media/data-box-overview/data-box-order-status-processed.png)

A Microsoft ezután előkészíti, majd feladja a csomagot egy regionális fuvarozónál. Az eszköz feladását követően Ön megkapja a fuvarlevélszámot. A portál a **Feladva** állapotot mutatja.

![Data Box rendelés feladva](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>A rendelés lemondása

A rendelés lemondásához az Azure Portalon lépjen az **Áttekintés** területre, és kattintson a **Megszakítás** gombra a parancssávon.

A megrendelést annak feladását követően bármikor megszakíthatja, mielőtt az Feldolgozott állapotba lép.
 
A lemondott rendelés törléséhez nyissa meg az **Áttekintés** területet, és kattintson a parancssávon a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * A Data Box üzembe helyezésének előfeltételei
> * A Data Box megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

A következő oktatóanyag a Data Box beállítását mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box beállítása](./data-box-deploy-set-up.md)


