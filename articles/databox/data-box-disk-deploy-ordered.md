---
title: Oktatóanyag a Azure Data Box Disk rendeléséhez | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan regisztrálhat az Azure Data Box Disk szolgáltatásra, és hogyan rendelheti meg azt az adatok Azure-ba történő importálásához.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 04ae2589e8abfab4826f2286d5f00869c24442f5
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057966"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Oktatóanyag: Azure Data Box Disk rendelése

Az Azure Data Box Disk egy hibrid felhőalapú megoldás, amellyel gyorsan, könnyen és megbízhatóan importálhat helyszíni adatokat az Azure-ba. Ön letölti az adatokat a Microsoft által SSD-lemezekre, majd visszaküldi azokat a Microsoftnak. Az adatok ezt követően fel lesznek töltve az Azure-ba.

Ez az oktatóanyag leírja, hogyan rendelheti meg az Azure Data Box Disk szolgáltatást. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A Data Box Disk megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

## <a name="prerequisites"></a>Előfeltételek

A központi telepítése előtt végezze el a következő konfigurálási előfeltételeket a Data Box szolgáltatáshoz és Data Box Diskhoz.

### <a name="for-service"></a>A szolgáltatás esetén

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik egy ügyfélszámítógéppel, amelyről lemásolhatja az adatokat. Az ügyfélszámítógépen:
  * egy [támogatott operációs rendszernek](data-box-disk-system-requirements.md#supported-operating-systems-for-clients) kell futnia;
  * Windows-ügyfél esetén további [szükséges szoftvereknek](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) telepítve kell lennie.  

## <a name="order-data-box-disk"></a>A Data Box Disk megrendelése

Jelentkezzen be ide:

* A Azure Portal ezen az URL-címen: https://portal.azure.com rendelés Data Box Disk.
* Vagy a Azure Government portál ezen az URL-címen: https://portal.azure.us . További részletekért lépjen a [kapcsolódás Azure Government a portál használatával](../azure-government/documentation-government-get-started-connect-with-portal.md).

Data Box Disk megrendeléséhez hajtsa végre az alábbi lépéseket.

1. Az Azure Portal bal felső sarkában kattintson a **+ Erőforrás létrehozása** elemre, és keresse meg az *Azure Data Box* lehetőséget. Kattintson az **Azure Data Box** lehetőségre.

   ![Keresse meg az Azure Data Box lehetőséget 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Kattintson a **Létrehozás** lehetőségre.

3. Ellenőrizze, hogy a Data Box szolgáltatás elérhető-e a régióban. Adja meg, vagy jelölje ki a következő információkat, majd kattintson az **Alkalmaz** elemre.

    ![Data Box Disk lehetőség kiválasztása](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Beállítás|Érték|
    |---|---|
    |Előfizetés|Válassza ki azt az előfizetést, amely számára engedélyezve van a Data Box szolgáltatás.<br> Az előfizetés az Ön számlázási fiókjához lesz társítva. |
    |Átvitel típusa| Importálás az Azure-ba|
    |Forrásország/-régió | Válassza ki azt az országot vagy régiót, ahol az adatok jelenleg találhatók.|
    |Azure-beli célrégió|Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat.|
  
4. Válassza a **Data Box Disk** lehetőséget. A megoldás maximális kapacitása megrendelésenként (5 lemez) 35 TB. Nagyobb adatmennyiséghez több megrendelést is létrehozhat.

     ![Data Box Disk 2. lehetőség kiválasztása](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

5. A **Megrendelés** területen adja meg a **Megrendelés részleteit**. Adja meg vagy válassza ki a következő információkat.

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|Adjon meg egy rövid nevet a megrendelés nyomon követéséhez.<br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie. |
    |Erőforráscsoport| Használjon egy már létezőt, vagy hozzon létre újat. <br> Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. |
    |Azure-beli célrégió| Válasszon ki egy régiót a tárfiókhoz.<br> Jelenleg csak az USA régióiban, Nyugat-és Észak-Európában, Kanadában és Ausztráliában működő tárfiókok támogatottak. |
    |Adatok becsült mérete (TB)| Adjon meg egy becsült mennyiséget TB-ban. <br>Az adatmennyiség alapján a Microsoft kiküldi Önnek a megfelelő számú 8 TB-os SSD-t (7 TB felhasználható kapacitás). <br>5 lemez maximális felhasználható kapacitása 35 TB. |
    |Lemez hozzáférési kulcsa| Ha bejelölte az **Egyéni kulcs használata az Azure által létrehozott hozzáférési kulcs helyett** lehetőséget, adja meg a lemez hozzáférési kulcsát. <br> Adjon meg egy 12 – 32 karakterből álló alfanumerikus kulcsot, amely legalább egy numerikus és egy speciális karakterből áll. Az engedélyezett speciális karakterek a következők: `@?_+`. <br> Úgy is dönthet, hogy kihagyja ezt a lehetőséget, és az Azure által létrehozott hozzáférési kulcsot használja a lemezek feloldásához.|
    |Tárolási célhely     | Válassz a Tárfiók vagy a Felügyelt lemezek lehetőséget, vagy mindkettőt. <br> A megadott Azure-régió alapján válassza ki a Storage-fiókot egy meglévő Storage-fiók szűrt listájából. Data Box Disk csak 1 Storage-fiókkal lehet összekapcsolni. <br> Létrehozhat egy új **Általános célú v1**, **Általános célú v2** fiókot vagy egy **Blob Storage-fiókot** is. <br>A virtuális hálózattal rendelkező tárfiókok támogatottak. Ahhoz, hogy a Data Box szolgáltatás működjön a biztonságos tárfiókok esetében, engedélyezze a megbízható szolgáltatásokat a tárfiók hálózati tűzfalának beállításai között. További információ: [Azure Data Box hozzáadása megbízható szolgáltatásként](../storage/common/storage-network-security.md#exceptions).|

    Ha tárfiókot használ tárolási célhelyként, a következő képernyőképet fogja látni:

    ![A Storage-fiók Data Box Disk sorrendje](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Ha Data Box Disk használatával felügyelt lemezeket hoz létre a helyszíni virtuális merevlemezekről, a következő információkat is meg kell adnia:

    |Beállítás  |Érték  |
    |---------|---------|
    |Erőforráscsoport     | Hozzon létre egy új erőforráscsoportot, ha felügyelt lemezeket kíván létrehozni a helyszíni virtuális merevlemezekről. Csak akkor használjon meglévő erőforráscsoportot, ha Data Box szolgáltatás által Data Box Disk megrendeléshez lett létrehozva. <br> Csak egy erőforráscsoport támogatott.|

    ![Felügyelt lemez Data Box Disk sorrendje](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    A felügyelt lemezekhez megadott tárfiókot előkészítési tárfiókként használja a rendszer. A Data Box szolgáltatás feltölti a virtuális merevlemezeket az átmeneti tárolási fiókba, majd átalakítja azokat a felügyelt lemezekre, és áthelyezi azokat az erőforráscsoportokba. További információ: [Az Azure-ba történő adatfeltöltés ellenőrzése](data-box-disk-deploy-upload-verify.md#verify-data-upload-to-azure).

6. Kattintson a **Tovább** gombra.

    ![A megrendelés részletei](media/data-box-disk-deploy-ordered/data-box-order-details.png)

7. A **Szállítási cím** lapon adja meg az utónevét és a vezetéknevét, a cége nevét és postai címét, valamint egy érvényes telefonszámot. Kattintson a **Cím ellenőrzése** elemre. A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről.

   A megrendelés feldolgozása után e-mailben értesítést fog kapni. Ha az önfelügyelt szállítást választotta, tekintse meg a saját felügyeletű [szállítás használata](data-box-disk-portal-customer-managed-shipping.md)című témakört.

    ![Szállítási cím megadása](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
8. Az **Értesítési adatok** területen adja meg az e-mail-címeket. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre.

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

9. Tekintse át a megrendeléshez kapcsolódó **Összefoglalót** a kapcsolattartási adatokról, az értesítésekről és az adatvédelmi feltételekről. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

10. Kattintson a **Megrendelés** elemre. A megrendelés létrehozása néhány percet vesz igénybe.

## <a name="track-the-order"></a>A megrendelés nyomon követése

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Keresse meg a megrendelést, és nyissa meg az **Áttekintés** területet az állapot megtekintéséhez. A portál a **Megrendelve** állapotot mutatja.

![Data Box Disk állapot megrendelve](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png)

Ha a lemezek nem érhetők el, értesítést kap. Ha a lemezek elérhetők, a Microsoft azonosítja a lemezeket a szállításhoz, és előkészíti a csomagot. A lemezek előkészítése a következőket foglalja magába:

* A lemezek titkosítása a AES-128 BitLocker használatával.  
* A lemezek zárolása az illetéktelen hozzáférés megakadályozására.
* A folyamat során létrejön a lemezek zárolását feloldó hozzáférési kulcs.

A lemezek előkészítésének lezárultával a portál a **Feldolgozott** állapotot mutatja.

A Microsoft ezután előkészíti, majd lebonyolítja a szállítást egy regionális szolgáltatón keresztül. A szállítás megkezdésekor Ön megkapja a nyomkövetési azonosító számot. A portál a **Feladva** állapotot mutatja.

## <a name="cancel-the-order"></a>A rendelés lemondása

A rendelés lemondásához az Azure Portalon lépjen az **Áttekintés** területre, és kattintson a **Megszakítás** gombra a parancssávon.

Csak a lemezek megrendelése után lehet megszakítani, és a rendelés feldolgozása folyamatban van a szállításhoz. Ha a megrendelés feldolgozása már megtörtént, nem mondhatja le a rendelést.

![Rendelés lemondása](media/data-box-disk-deploy-ordered/cancel-order1.png)

A lemondott rendelés törléséhez nyissa meg az **Áttekintés** területet, és kattintson a parancssávon a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
>
> * A Data Box Disk megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

A következő oktatóanyag a Data Box Disk beállítását mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box Disk beállítása](./data-box-disk-deploy-set-up.md)