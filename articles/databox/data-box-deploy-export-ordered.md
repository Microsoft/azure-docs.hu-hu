---
title: Oktatóanyag az adatok Azure Data Boxból való exportálásához | Microsoft Docs
description: Ismerje meg az üzembe helyezés előfeltételeit és az adatok exportálását Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 42476e2689cc503edc19e8e299a01ce922f1bf42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789195"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>Oktatóanyag: az Azure Data Box exportálási sorrendjének létrehozása

A Azure Data Box egy hibrid megoldás, amely lehetővé teszi az Azure-ba való adatáthelyezést a helyükre. Ez az oktatóanyag leírja, hogyan hozhat létre Azure Data Box exportálási sorrendjét. Az exportálási sorrend létrehozásának fő oka a vész-helyreállítás, abban az esetben, ha a helyszíni tárolás biztonságban van, és vissza kell állítani a biztonsági mentést.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Az Exportálás előfeltételei
> * Data Box megrendelése exportáláshoz
> * Az exportálási sorrend nyomon követése
> * Az exportálási sorrend megszakítása

## <a name="prerequisites"></a>Előfeltételek

Az eszköz megrendelése előtt végezze el az alábbi konfigurációs előfeltételeket a Data Box szolgáltatáshoz és eszközhöz.

### <a name="for-service"></a>A szolgáltatás esetén

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Győződjön meg arról, hogy rendelkezik egy meglévő erőforráscsoporthoz, amelyet a Azure Data Box használhat.

* Győződjön meg arról, hogy az Azure Storage-fiókja, amelyről exportálni kívánja az adatait, az egyik támogatott Storage-fióktípus a [Data Box támogatott Storage](data-box-system-requirements.md#supported-storage-accounts)-fiókokkal.

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik egy, az adatközponti hálózatra csatlakoztatott gazdagéppel. A Azure Data Box adatok másolása erre a számítógépre történik. A gazdagépnek egy támogatott operációs rendszert kell futtatnia az [Azure Data Box rendszerkövetelményeinél](data-box-system-requirements.md) leírtaknak megfelelően.

* Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha egy 10 GbE-kapcsolat nem érhető el, akkor a rendszer egy GbE adatkapcsolatot használ, de a másolási sebesség hatással van.

## <a name="order-data-box-for-export"></a>Data Box rendelése exportáláshoz

Az eszköz megrendeléséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. Microsoft Azure hitelesítő adatai használatával jelentkezzen be ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com).

2. Válassza az **+ Erőforrás létrehozása** lehetőséget, és keressen rá az *Azure Data Box* kifejezésre. Válassza az **Azure Data Box** lehetőséget.

   ![Erőforrás létrehozása](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. Válassza a **Létrehozás** lehetőséget.

   ![Azure Data Box erőforrás létrehozása](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. Ellenőrizze, hogy a Azure Data Box szolgáltatás elérhető-e a régiójában. Adja meg vagy válassza ki a következő információkat, majd válassza az **Alkalmaz** lehetőséget.

    |Beállítás  |Érték  |
    |---------|---------|
    |Átvitel típusa     | Válassza **az Exportálás az Azure-ba** lehetőséget.        |
    |Előfizetés     | Válasszon egy EA-, CSP- vagy Azure Sponsorship-előfizetést a Data Box szolgáltatáshoz. <br> Az előfizetés az Ön számlázási fiókjához lesz társítva.       |
    |Erőforráscsoport     |    Válasszon ki egy meglévő erőforráscsoportot. <br> Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója.         |
    |Forrás Azure-régió    |    Válassza ki azt az Azure-régiót, ahol az adatai jelenleg vannak.         |
    |Rendeltetési ország     |     Válassza ki azt az országot, ahová az eszközt el szeretné szállítani.        |

   ![Válassza ki a Data Box beállításait](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. Válassza a **Data Box** lehetőséget. Egy megrendelés maximális felhasználható kapacitása 80 TB. Nagyobb mennyiségű adat esetén több rendelést is létrehozhat.

   ![Data Box kapacitás kiválasztása](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. A **sorrend** mezőben határozza meg az **alapszintű** rendelés részleteit. Adja meg vagy válassza ki a következő információkat.

    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés     | Az előfizetés automatikusan kitöltődik a korábbi kiválasztás alapján.|
    |Erőforráscsoport | A korábban kiválasztott erőforráscsoport. |
    |Exportálási rendelés neve     |  Adjon meg egy rövid nevet a megrendelés nyomon követéséhez. <br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.      |

    ![Exportálási sorrend alapjai](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    Kattintson a Tovább gombra: a folytatáshoz válassza ki az **adatkijelölést** .

7. Az **adatkijelölés** területen válassza a **Storage-fiók hozzáadása és az Exportálás típusa** lehetőséget.

    ![Storage-fiók és exportálási típus hozzáadása](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. Az **Exportálás kiválasztása beállításnál** adja meg az exportálási beállítás részleteit. Adja meg vagy válassza ki a következő adatokat, majd válassza a **Hozzáadás** lehetőséget.

    |Beállítás  |Érték  |
    |---------|---------|
    |Tárfiók     | Az Azure Storage-fiók, ahonnan exportálni kívánja az adatait. |
    |Exportálás típusa     | Meghatározza az **összes objektumból** exportálandó adatok típusát és az **XML-fájl használatát**.<ul><li> **Minden objektum** – azt adja meg, hogy a feladatsor az **átvitel beállításaitól** függően az összes értéket exportálja.</li><li> **XML-fájl használata** – olyan XML-fájlt ad meg, amely a Storage-fiókból exportálandó blobok és/vagy fájlok elérési útját és előtagokat tartalmazza. Az XML-fájlnak a kiválasztott Storage-fiók tárolójában kell lennie, és a fájlmegosztás lehetőség választása jelenleg nem támogatott. A fájlnak nem üres. XML formátumú fájlnak kell lennie.</li></ul>        |
    |Átvitel beállításai     |  Meghatározza az adatátviteli beállításokat az **összes kijelölése**, **az összes blob** és az **összes fájl** közül. <ul><li> **Az összes kijelölése** – megadja, hogy az összes blob és Azure Files exportálva van-e. Ha olyan Storage-fiókot használ, amely csak a blobokat támogatja (Blob Storage fiók), a **minden fájl** lehetőség nem lesz kiválasztható.</li><li> **Minden blob** – azt adja meg, hogy csak a blokk-és a Blobok legyenek exportálva.</li><li> **Minden fájl** – azt adja meg, hogy minden fájl exportálva legyen, kivéve a blobokat. Az Ön által létrehozott Storage-fiók típusa (GPv1 és GPv2, Premium Storage vagy blob Storage) meghatározza az exportálható adattípusokat. További információ: [támogatott Storage-fiókok exportáláshoz](../import-export/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Részletes napló belefoglalása     | Azt jelzi, hogy szeretne-e részletes naplófájlt használni, amely tartalmazza az összes sikeresen exportált fájl listáját.        |

    > [!NOTE]
    >
    > Ha a **XML-fájl használata** lehetőséget választja az **Exportálás típusa** beállításhoz, meg kell győződnie arról, hogy az XML érvényes elérési utakat és/vagy előtagokat tartalmaz. Hozza létre és adja meg az XML-fájlt.  Ha a fájl érvénytelen, vagy a megadott elérési utak nem felelnek meg, a megrendelés a részleges vagy a nem exportált adatértékekkel végződik.

    Ha szeretné megtudni, hogyan adhat hozzá XML-fájlt egy tárolóhoz, olvassa el az [Exportálás az XML-fájllal](data-box-deploy-export-ordered.md#export-order-using-xml-file)című témakört.

   ![Exportálási beállítás kiválasztása](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   Az XML-bemenet példájának megtekintéséhez lásd: [minta XML-bemenet](data-box-deploy-export-ordered.md#sample-xml-file)

9. Az **Adatválasztás** területen tekintse át a beállításokat, majd kattintson a Tovább gombra **: biztonsági>** a folytatáshoz.

   ![Exportálási sorrend, az adatkijelölés](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    A **biztonsági** képernyőn használhatja a saját titkosítási kulcsát, és választhatja a kettős titkosítás használatát.

    A **biztonsági** képernyőn megjelenő összes beállítás nem kötelező. Ha nem módosítja a beállításokat, az alapértelmezett beállítások lesznek érvényesek.

    ![A Data Box importálási sorrend varázsló biztonsági képernyője](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. Ha a saját ügyfél által felügyelt kulcsát szeretné használni az új erőforrás feloldási hitelesítő adatainak megvédéséhez, bontsa ki a **titkosítási típust**.

    Az ügyfél által felügyelt kulcs konfigurálása a Azure Data Box esetében nem kötelező. Alapértelmezés szerint a Data Box a Microsoft által felügyelt kulcs használatával védi a zárolási kulcsot.

    Az ügyfél által felügyelt kulcs nem befolyásolja, hogy az eszközön lévő adatforgalom hogyan legyen titkosítva. A kulcs csak az eszköz feloldási hitelesítő kulcsának titkosítására szolgál.

    Ha nem szeretne ügyfél által felügyelt kulcsot használni, ugorjon a 16. lépésre.

    ![A titkosítási típus beállításait megjelenítő biztonsági képernyő](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. A kulcs típusaként válassza az **ügyfél által felügyelt kulcs** lehetőséget. Ezután válassza ki **a Key Vault és a kulcs kiválasztása** lehetőséget.
   
    ![Biztonsági képernyő, ügyfél által felügyelt kulcs beállításai](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. A **kiválasztási kulcs Azure Key Vault** képernyőn az előfizetés automatikusan ki lesz töltve.

    - A **Key Vault** esetében választhat egy meglévő kulcstartót a legördülő listából.

      ![Kulcs kiválasztása Azure Key Vault képernyőn](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - Új kulcstartó létrehozásához az **új létrehozása** lehetőséget is választhatja. A **Key Vault létrehozása** képernyőn adja meg az erőforráscsoportot és a kulcstároló nevét. Győződjön meg arról, hogy a **Soft delete** és a **Purge Protection** engedélyezve van. Fogadja el az összes többi alapértelmezett értéket, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.

      ![Új Azure Key Vault beállítások létrehozása](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      Tekintse át a kulcstartó adatait, majd válassza a **Létrehozás** lehetőséget. Várjon pár percet, amíg a Key Vault létrehozása befejeződik.

      ![Új Azure Key Vault felülvizsgálati képernyő](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. A kulcs **kiválasztása Azure Key Vault** képernyőn kiválaszthat egy meglévő kulcsot a kulcstartóban.

    ![Meglévő kulcs kiválasztása Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    Ha új kulcsot szeretne létrehozni, válassza az **új létrehozása** lehetőséget. RSA-kulcsot kell használnia. A méret 2048 vagy nagyobb lehet. Adja meg az új kulcs nevét, fogadja el a többi alapértelmezett értéket, majd válassza a **Létrehozás** lehetőséget.

      ![Új kulcs létrehozása lehetőség](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      Értesítést kap, ha a kulcs létrejött a kulcstartóban.

14. Válassza ki a használni kívánt kulcs **verzióját** , majd válassza a **kiválasztás** lehetőséget.

      ![A Key vaultban létrehozott új kulcs](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    Ha új kulcs-verziót szeretne létrehozni, válassza az **új létrehozása** lehetőséget.

    ![Nyisson meg egy párbeszédpanelt új kulcs verziójának létrehozásához](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    Az **új kulcs létrehozása** képernyőn válassza ki az új kulcs verziójának beállításait, és válassza a **Létrehozás** lehetőséget.

    ![Új kulcs verziójának létrehozása](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    A **Biztonság** képernyőn látható **titkosítási típus** beállításai megmutatják a kulcstartót és a kulcsot.

    ![Ügyfél által felügyelt kulcs és kulcstartó](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. Válassza ki azt a felhasználói identitást, amelyet az erőforráshoz való hozzáférés kezeléséhez fog használni. Válassza **a felhasználói azonosító kiválasztása** lehetőséget. A jobb oldali panelen válassza ki az előfizetést és a használni kívánt felügyelt identitást. Ezután kattintson a **Kiválasztás** gombra.

    A felhasználó által hozzárendelt felügyelt identitás egy önálló Azure-erőforrás, amely több erőforrás kezelésére is használható. További információ: [felügyelt identitások típusai](../active-directory/managed-identities-azure-resources/overview.md).  

    Ha új felügyelt identitást kell létrehoznia, kövesse a [szerepkör létrehozása, listázása, törlése vagy hozzárendelése egy felhasználóhoz rendelt felügyelt identitáshoz a Azure Portal használatával című témakör](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)útmutatását.
    
    ![Felhasználói azonosító kiválasztása](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    A felhasználói identitás a **titkosítási típus** beállításainál jelenik meg.

    Most összecsukhatja a **titkosítási típus** beállításait.

    ![A titkosítási típus beállításaiban megjelenő kiválasztott felhasználói azonosító](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. Ha engedélyezni szeretné a szoftveres kettős titkosítást, bontsa ki a **dupla titkosítás (magas biztonsági környezetekben)** lehetőséget, és jelölje be **a dupla titkosítás engedélyezése a rendeléshez** beállítást. 

    A szoftveres titkosítás a Data Box lévő adatok AES-256 bites titkosításán felül történik.

    > [!NOTE]
    > Ha engedélyezi ezt a beállítást, megteheti, hogy a rendelés feldolgozása és az Adatmásolás tovább tart. Ez a beállítás a megrendelés létrehozása után nem módosítható.

    ![Adatmező importálásának biztonsági képernyője, dupla titkosítás](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    A folytatáshoz válassza a **tovább lehetőséget: kapcsolattartási adatok** .

11. A **kapcsolattartási** adatok területen válassza a **+ szállítási címek hozzáadása** lehetőséget a szállítási információk megadásához.

    ![Szállítási címek hozzáadása](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. A **szállítási cím hozzáadása** területen adja meg a vállalat vezetéknevét, nevét és postacímét, valamint egy érvényes telefonszámot. Válassza az **Érvényesítés** lehetőséget. A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről.

    ![Szállítási címek ellenőrzése](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    Ha olyan régiót rendel, amelyben az önfelügyelt szállítás elérhető, ezt a lehetőséget választhatja. Az önfelügyelt szállítással kapcsolatos további információkért lásd: [saját üzemeltetésű szállítás használata](data-box-portal-customer-managed-shipping.md).

13. Válassza a **szállítási címek hozzáadása** lehetőséget a szállítási adatok sikeres ellenőrzése után.

14. A **kapcsolattartási adatok** területen tekintse át a szállítási címet és az e-mail-címét. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre.

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

    ![Kapcsolattartási adatok](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. Kattintson a **Next (tovább) gombra: Review + Order>**. El kell fogadnia a feltételeket és a kikötéseket a rendelés létrehozásával folytatva.

16. Válassza a **Megrendelés** lehetőséget. A megrendelés létrehozása néhány percet vesz igénybe.

    ![Véglegesítő sorrend](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>Exportálási sorrend XML-fájl használatával

Ha **az XML-fájl használata** lehetőséget választja, megadhatja az exportálni kívánt tárolókat és blobokat (oldalt és blokkokat). Az XML formázásához a [minta XML-fájl táblázatának](#sample-xml-file) specifikációit kell követnie. Az alábbi lépések bemutatják, hogyan lehet XML-fájlt használni az adatai exportálásához:

1. Az **Exportálás típusa** beállításnál válassza az **XML-fájl használata** lehetőséget. Ezt az XML-fájlt adja meg az exportálni kívánt blobok és Azure-fájlok megadásához. Az XML-fájl hozzáadásához jelölje be az **XML-fájl kiválasztásához kattintson ide**.

     ![Exportálási lehetőség kiválasztása, XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. A tároló létrehozásához kattintson a **+ tároló** elemre.

    ![Exportálási beállítás, tárolók](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. Az **új tároló** lapon, amely a Azure Portal jobb oldalán jelenik meg, adja meg a tároló nevét. A névnek kisebbnek kell lennie, és tartalmazhat számokat és kötőjeleket (-). Ezután válassza ki a **nyilvános hozzáférési szintet** a legördülő listából. Javasoljuk, hogy a **magánjellegű (névtelen hozzáférés)** beállítással megakadályozza, hogy mások hozzáférjenek az adatokhoz. A tárolók hozzáférési szintjeivel kapcsolatos további információkért lásd: [tárolók hozzáférési engedélyei](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

   ![Válassza az Exportálás lehetőséget, majd az új tároló beállításai elemet.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. Válassza a **Létrehozás** lehetőséget.

   ![Válassza az Exportálás lehetőséget, majd hozzon létre új tárolót.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   Ha a tároló létrehozása sikeresen megtörtént, a következő üzenet jelenik meg:

   ![A tároló sikeresen létrehozva](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. Válassza ki a létrehozott tárolót, és kattintson rá duplán.

   ![Tároló részleteinek megtekintése](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. Ha duplán kattint a tárolóra, megjelenik a tároló tulajdonságai nézet. Most csatolnia kell (vagy meg kell keresnie) az XML-fájlt, amely tartalmazza az exportálni kívánt blobok és/vagy Azure Files listáját. Válassza a **Feltöltés** lehetőséget.

   ![BLOB feltöltése a tárolóba](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. Sikeresen felvette az XML-fájlt a tárolóba. A rendszer csak az ebben az XML-ben megadott blobokat és Azure Files exportálja.

   ![A tárolóhoz hozzáadott XML-fájl](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="track-the-order"></a>A megrendelés nyomon követése

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Lépjen a Data Box sorrendbe, majd az állapot megtekintéséhez lépjen az **Áttekintés** gombra. A portálon a megrendelés **Megrendelve** állapotban látható.

Az eszköz előkészítésének befejezésekor a rendszer az adatok másolását a kiválasztott Storage-fiókoktól kezdi. A portálon látható, hogy az **Adatmásolás állapota folyamatban** állapotú.

![Data Box exportálási sorrend, az Adatmásolás folyamatban](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box adatok másolása a forrás Storage-fiók (ok) ból. Az Adatmásolás befejezése után Data Box zárolva van, és a portálon megjelenik a sorrend a **Másolás befejeződött** állapotban.

![Data Box exportálási sorrend, az Adatmásolás befejeződött](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

Ha az eszköz nem érhető el, értesítést fog kapni. Ha van elérhető eszköz, a Microsoft kiválaszt egyet a szállításhoz, és előkészíti a csomagot. Az eszköz előkészítése során a következő műveletek végezhetők el:

* SMB-megosztások létrehozása az eszközzel társított mindegyik tárfiókhoz.
* Hozzáférési hitelesítő adatok (felhasználónév és jelszó) kiosztása mindegyik megosztáshoz.
* Az eszköz zárolva van, és csak az eszköz feloldási jelszava használatával érhető el. A jelszó lekéréséhez be kell jelentkeznie a Azure Portal-fiókjába, és ki kell választania az **eszköz adatait**.

A Microsoft ezután előkészíti és elküldi az eszközt egy regionális szolgáltatón keresztül. Az eszköz feladását követően Ön megkapja a fuvarlevélszámot. A portál a **Feladva** állapotot mutatja.

![Data Box exportálási sorrend elküldve](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

Ha az önfelügyelt szállítás be van jelölve, e-mailben értesítést fog kapni a következő lépésekről, amikor az eszköz készen áll az adatközpontból való kiválasztásra. Az önfelügyelt szállítással kapcsolatos további információkért lásd: [saját üzemeltetésű szállítás](data-box-portal-customer-managed-shipping.md).

![Az önfelügyelt szállítás készen áll a felvételre](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>A rendelés lemondása

A megrendelés megszakításához a Azure Portal lépjen az **Áttekintés** elemre, és válassza a **Mégse** lehetőséget a parancssáv alatt.

Egy megrendelés elhelyezése után bármikor lemondhatja azt, mielőtt a rendelés megkezdi a feldolgozást.

Egy megszakított megrendelés törléséhez lépjen az **Áttekintés** elemre, és válassza a **Törlés** elemet a parancssorból.

## <a name="sample-xml-file"></a>Minta XML-fájl

Az alábbi XML-ben egy példa látható a Blobok nevére, a blob előtagjaira és a Azure Files az exportálási sorrendet az **XML-fájl használata** lehetőség kiválasztásakor használt XML-formátumban.

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Néhány fontos pont az XML-fájlokra vonatkozóan:

* Az XML-címkék megkülönböztetik a kis-és nagybetűket, és pontosan a fenti mintában megadott módon kell megegyezniük.
* A címkék megnyitásához és bezárásához egyeznie kell.
* Az XML-címkék helytelenek, vagy a formázás adatexportálási hibát eredményezhet.
* A rendszer nem exportálja az adatexportálást, ha a blob és/vagy a fájl előtagja érvénytelen.

### <a name="examples-of-valid-blob-paths"></a>Példák érvényes blob-elérési utakra

A következő táblázat példákat mutat be a Blobok érvényes elérési útjaira:

   | Szelektor | BLOB elérési útja | Description |
   | --- | --- | --- |
   | Ezzel kezdődik |/ |A Storage-fiókban lévő összes blob exportálása |
   | Ezzel kezdődik |/$root/ |A gyökér tárolóban lévő összes blob exportálása |
   | Ezzel kezdődik |/containers |Minden blob exportálása minden olyan tárolóban, amely előtag- **tárolókkal** kezdődik |
   | Ezzel kezdődik |/container-name/ |Az összes blob exportálása a tároló **-tárolóban – név** |
   | Ezzel kezdődik |/container-name/prefix |Az összes blob exportálása a tároló **-tárolóban –** az előtag- **előtaggal** kezdődő név |
   | Egyenlő |$root/logo.bmp |A blob **logo.bmp** exportálása a gyökér tárolóba |
   | Egyenlő |8tbpageblob/mydata.txt |BLOB **mydata.txt** exportálása a tároló **8tbpageblob** |

## <a name="sample-log-files"></a>Minta naplófájlok

Ez a szakasz az exportálás során létrehozott minta naplófájlokat tartalmazza. A rendszer automatikusan létrehozza a hibanapló-naplókat. A részletes naplófájl létrehozásához ki kell választania a **részletes napló Belefoglalása** Azure Portal az exportálási sorrend konfigurálásakor.
A másolással és a részletes naplókkal kapcsolatos további információkért lásd: [naplók másolása](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
>
> * Az Exportálás előfeltételei
> * Data Box megrendelése exportáláshoz
> * Az exportálási sorrend nyomon követése
> * Az exportálási sorrend megszakítása

A következő oktatóanyag a Data Box beállítását mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box beállítása](./data-box-deploy-set-up.md)