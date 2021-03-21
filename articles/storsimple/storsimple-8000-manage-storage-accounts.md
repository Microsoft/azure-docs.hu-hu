---
title: A Storage-fiók hitelesítő adatainak kezelése, StorSimple 8000 sorozatú eszköz
description: A cikk azt ismerteti, hogyan használható a StorSimple Eszközkezelő configure (Konfigurálás) lap a Storage-fiók biztonsági kulcsainak hozzáadásához, szerkesztéséhez, törléséhez vagy elforgatásához.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 0df0c12d5942179e2492bfdc932fa25d126a2426
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016870"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>A StorSimple Eszközkezelő szolgáltatás használata a Storage-fiók hitelesítő adatainak kezeléséhez

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő Service panel **konfigurációs** szakasza tartalmazza az összes olyan globális szolgáltatási paramétert, amelyek a StorSimple Eszközkezelő szolgáltatásban hozhatók létre. Ezek a paraméterek a szolgáltatáshoz kapcsolódó összes eszközre alkalmazhatók, és a következők:

* Tárfiók hitelesítő adatai
* Sávszélesség-sablonok 
* Hozzáférés-vezérlési rekordok 

Ez az oktatóanyag a Storage-fiók hitelesítő adatainak hozzáadását, szerkesztését és törlését, illetve egy Storage-fiók biztonsági kulcsainak elforgatását ismerteti.

 ![A Storage-fiók hitelesítő adatainak listája 1](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

A Storage-fiókok tartalmazzák azokat a hitelesítő adatokat, amelyeket a StorSimple-eszköz használ a Storage-fiókhoz a felhőalapú szolgáltatóhoz való hozzáféréshez. Microsoft Azure Storage-fiókok esetében ezek a hitelesítő adatok, például a fiók neve és az elsődleges elérési kulcs. 

A **Storage-fiók hitelesítő adatai** panelen a számlázási előfizetéshez létrehozott összes Storage-fiók táblázatos formában jelenik meg, amely a következő információkat tartalmazza:

* **Name (név** ) – a fiókhoz a létrehozáskor hozzárendelt egyedi név.
* **SSL engedélyezve** – azt jelzi, hogy a TLS engedélyezve van-e, és az eszközről a felhőbe irányuló kommunikáció a biztonságos csatornán keresztül történik-e.
* **Használja** – a kötetek száma a Storage-fiók használatával.

Az elvégezhető Storage-fiókokkal kapcsolatos leggyakoribb feladatok a következők:

* Storage-fiók hozzáadása 
* Storage-fiók szerkesztése 
* Tárfiók törlése 
* Storage-fiókok kulcsának rotációja 

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

A StorSimple-eszközzel háromféle Storage-fiók használható.

* Automatikusan **létrehozott Storage-fiókok** – ahogy a neve is sugallja, az ilyen típusú Storage-fiók automatikusan létrejön a szolgáltatás első létrehozásakor. Ha többet szeretne megtudni a Storage-fiók létrehozásáról, tekintse meg az [1. lépés: új szolgáltatás létrehozása](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) a helyszíni [StorSimple-eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md)című témakört. 
* **Storage-fiókok a szolgáltatás előfizetésében** – ezek azok az Azure Storage-fiókok, amelyek ugyanahhoz az előfizetéshez vannak társítva, mint a szolgáltatáshoz. További információ a Storage-fiókok létrehozásáról: [Tudnivalók az Azure Storage-fiókokról](../storage/common/storage-account-create.md). 
* **Storage-fiókok a szolgáltatás-előfizetésen kívül** – ezek azok az Azure Storage-fiókok, amelyek nincsenek társítva a szolgáltatáshoz, és valószínűleg léteztek a szolgáltatás létrehozása előtt.

## <a name="add-a-storage-account"></a>Storage-fiók hozzáadása

A Storage-fiók hozzáadásához adjon meg egy egyedi rövid nevet és a hozzáférési hitelesítő adatokat, amelyek a Storage-fiókhoz vannak társítva (a megadott felhőalapú szolgáltatóval). Lehetősége van arra is, hogy engedélyezze a Transport Layer Security (TLS) üzemmódot (korábbi nevén SSL (SSL)), hogy biztonságos csatornát hozzon létre az eszköz és a felhő közötti hálózati kommunikációhoz.

Egy adott felhőalapú szolgáltatóhoz több fiókot is létrehozhat. Vegye figyelembe azonban, hogy a Storage-fiók létrehozása után nem változtathatja meg a felhőalapú szolgáltatót.

A Storage-fiók mentésekor a szolgáltatás megpróbál kommunikálni a felhőalapú szolgáltatóval. A hitelesítő adatokat és a megadott hozzáférési anyagokat a rendszer most hitelesíti. A rendszer csak akkor hozza létre a Storage-fiókot, ha a hitelesítés sikeres. Ha a hitelesítés sikertelen, a rendszer a megfelelő hibaüzenetet jeleníti meg.

Az Azure Storage-fiók hitelesítő adatainak hozzáadásához kövesse az alábbi eljárásokat:

* A Eszközkezelő szolgáltatással azonos Azure-előfizetéssel rendelkező Storage-fiók hitelesítő adatainak hozzáadása
* Azure Storage-fiók hitelesítő adatainak hozzáadása a Eszközkezelő szolgáltatás-előfizetésen kívül

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Azure Storage-fiók hitelesítő adatainak hozzáadása a StorSimple kívül Eszközkezelő szolgáltatás-előfizetés

1. Navigáljon a StorSimple Eszközkezelő szolgáltatáshoz, válassza ki, majd kattintson rá duplán. Ekkor megnyílik az **Áttekintés** panel.
2. A **konfigurációs** szakaszban válassza ki a **Storage-fiók hitelesítő adatait** . Ez felsorolja a StorSimple Eszközkezelő szolgáltatáshoz társított összes meglévő Storage-fiók hitelesítő adatait.
3. Kattintson a **Hozzáadás** parancsra.
4. A **Storage-fiók hitelesítő adatainak hozzáadása** panelen tegye a következőket:
   
    1. Az **előfizetés** mezőben válassza az **egyéb** lehetőséget.
   
    2. Adja meg az Azure Storage-fiók hitelesítő adatainak nevét.
   
    3. A **Storage-fiók hozzáférési kulcsa** szövegmezőben adja meg az Azure Storage-fiók hitelesítő adatainak elsődleges elérési kulcsát. A kulcs beszerzéséhez nyissa meg az Azure Storage szolgáltatást, válassza ki a Storage-fiók hitelesítő adatait, majd kattintson a **fiók kulcsainak kezelése** lehetőségre. Most már az elsődleges hozzáférési kulcsot is másolhatja.
   
    4. A TLS engedélyezéséhez kattintson az **Engedélyezés** gombra egy biztonságos csatorna létrehozásához a StorSimple Eszközkezelő szolgáltatás és a felhő közötti hálózati kommunikációhoz. Kattintson a **Letiltás** gombra, ha privát felhőben működik.
   
    5. Kattintson a **Hozzáadás** parancsra. A rendszer értesítést küld a Storage-fiók hitelesítő adatainak sikeres létrehozása után.

5. Az újonnan létrehozott Storage-fiók hitelesítő adatai a StorSimple konfigurálása Eszközkezelő szolgáltatás paneljén a **Storage-fiók hitelesítő adatai** területen jelennek meg.
   


## <a name="edit-a-storage-account"></a>Storage-fiók szerkesztése

A mennyiségi tárolók által használt Storage-fiókot szerkesztheti. Ha olyan Storage-fiókot szerkeszt, amely jelenleg használatban van, a módosításra szolgáló egyetlen mező a Storage-fiók elérési kulcsa. Megadhatja az új Storage-hozzáférési kulcsot, és mentheti a frissített beállításokat.

#### <a name="to-edit-a-storage-account"></a>A Storage-fiók szerkesztése

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.

    ![Storage-fiók hitelesítő adatai 2](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. A **Storage-fiók hitelesítő adatai** panelen, a Storage-fiók hitelesítő adatainak listájából válassza ki a szerkeszteni kívánt elemet, és kattintson rá. 

3. Az SSL-kijelölés **engedélyezése** lehetőségre módosíthatja. A Storage-fiók hozzáférési kulcsainak elforgatásához kattintson a **továbbiak...** lehetőségre, majd a **hozzáférési kulcs szinkronizálása** elemre. A kulcs elforgatásának végrehajtásával kapcsolatos további információkért tekintse meg a [Storage-fiókok kulcsának rotációs](#key-rotation-of-storage-accounts) lépéseit. A beállítások módosítása után kattintson a **Mentés** gombra. 

    ![Szerkesztett Storage-fiók hitelesítő adatainak mentése](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. 

    ![Módosítások megerősítése](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

A rendszer frissíti és menti a beállításokat a Storage-fiókjához. 

## <a name="delete-a-storage-account"></a>Tárfiók törlése

> [!IMPORTANT]
> Csak akkor törölheti a Storage-fiókot, ha azt nem használja mennyiségi tároló. Ha a Storage-fiókot egy mennyiségi tároló használja, először törölje a kötet-tárolót, majd törölje a társított Storage-fiókot.

#### <a name="to-delete-a-storage-account"></a>Storage-fiók törlése

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.

2. A Storage-fiókok táblázatos listájában vigye a kurzort a törölni kívánt fiókra. Kattintson a jobb gombbal a helyi menü meghívásához, majd kattintson a **Törlés** parancsra.

    ![A Storage-fiók hitelesítő adatainak törlése](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a törlés folytatásához. A táblázatos lista frissülni fog, hogy tükrözze a módosításokat.

    ![Törlés megerősítése](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Storage-fiókok kulcsának rotációja

Biztonsági okokból a kulcsfontosságú rotációs szolgáltatás gyakran követelmény az adatközpontokban. Minden Microsoft Azure előfizetés egy vagy több társított Storage-fiókkal rendelkezhet. Az ezekhez a fiókokhoz való hozzáférést az egyes Storage-fiókok előfizetési és hozzáférési kulcsai vezérlik. 

A Storage-fiók létrehozásakor a Microsoft Azure 2 512 bites tárterület-hozzáférési kulcsokat hoz létre, amelyeket a rendszer a Storage-fiók elérésekor a hitelesítéshez használ. A két tárterület-hozzáférési kulcs lehetővé teszi a kulcsok újragenerálása megszakítás nélkül a tárolási szolgáltatáshoz vagy a szolgáltatáshoz való hozzáféréshez. A jelenleg használatban lévő kulcs az *elsődleges* kulcs, a biztonsági mentési kulcs pedig *másodlagos* kulcsként hivatkozik rá. A két kulcs egyikét meg kell adni, ha a Microsoft Azure StorSimple eszköz hozzáfér a felhőalapú tárolási szolgáltatóhoz.

## <a name="what-is-key-rotation"></a>Mi az a Key rotáció?

Az alkalmazások általában csak az egyik kulcsot használják az adatai eléréséhez. Bizonyos idő elteltével az alkalmazások átválthatnak a második kulcs használatára. Miután átváltotta az alkalmazásokat a másodlagos kulcsra, kiválaszthatja az első kulcsot, majd létrehozhatja az új kulcsot. A két kulcs ily módon lehetővé teszi az alkalmazások számára, hogy állásidő nélkül hozzáférjenek az adataihoz.

A Storage-fiók kulcsai mindig titkosított formában tárolódnak a szolgáltatásban. Ezek azonban visszaállíthatók a StorSimple Eszközkezelő szolgáltatás használatával. A szolgáltatás lekérheti az azonos előfizetésben lévő összes Storage-fiók elsődleges kulcsát és másodlagos kulcsát, beleértve a tárolási szolgáltatásban létrehozott fiókokat, valamint a StorSimple Eszközkezelő szolgáltatás első létrehozásakor generált alapértelmezett tárolási fiókokat is. A StorSimple Eszközkezelő szolgáltatás mindig ezeket a kulcsokat a klasszikus Azure portálon fogja beszerezni, majd titkosított módon tárolja őket.

## <a name="rotation-workflow"></a>Rotációs munkafolyamat

A Microsoft Azure rendszergazdája az elsődleges vagy másodlagos kulcsot újra létrehozhatja vagy módosíthatja úgy, hogy közvetlenül a Storage-fiókhoz fér hozzá (a Microsoft Azure Storage szolgáltatáson keresztül). A StorSimple Eszközkezelő szolgáltatás nem látja automatikusan ezt a változást.

Ahhoz, hogy tájékoztassa a StorSimple Eszközkezelő szolgáltatását a változásról, hozzá kell férnie a StorSimple Eszközkezelő szolgáltatáshoz, el kell érnie a Storage-fiókot, majd szinkronizálnia kell az elsődleges vagy másodlagos kulcsot (attól függően, hogy melyik módosult). A szolgáltatás ezután lekéri a legújabb kulcsot, titkosítja a kulcsokat, és elküldi a titkosított kulcsot az eszköznek.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>A tárolási fiókok kulcsainak szinkronizálása a szolgáltatással megegyező előfizetésben 
1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.
2. A Storage-fiókok táblázatos listájából válassza ki azt, amelyet módosítani szeretne. 

    ![kulcsok szinkronizálása](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Kattintson a... **Továbbiak** , majd válassza **a szinkronizálás hozzáférési kulcsát a forgatáshoz**.   

    ![2. kulcsok szinkronizálása](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. A StorSimple Eszközkezelő szolgáltatásban frissítenie kell azt a kulcsot, amely korábban megváltozott a Microsoft Azure Storage szolgáltatásban. Ha az elsődleges hozzáférési kulcs módosult (újragenerált), válassza az **elsődleges** kulcs lehetőséget. Ha a másodlagos kulcs módosult, válassza a **másodlagos** kulcs elemet. Kattintson a **szinkronizálási kulcs** lehetőségre.
      
      ![kulcsok szinkronizálása 3](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

A kulcs sikeres szinkronizálása után értesítést kap.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>A szolgáltatás-előfizetésen kívüli tárolási fiókok kulcsainak szinkronizálása
1. A **szolgáltatások** lapon kattintson a configure ( **Konfigurálás** ) fülre.
2. Kattintson a **Storage-fiókok hozzáadása/szerkesztése** elemre.
3. A párbeszédpanelen tegye a következőket:
   
   1. Válassza ki a frissíteni kívánt elérési kulccsal rendelkező Storage-fiókot.
   2. Frissítenie kell a Storage hozzáférési kulcsát a StorSimple Eszközkezelő szolgáltatásban. Ebben az esetben a Storage hozzáférési kulcsát láthatja. Adja meg az új kulcsot a **Storage-fiók elérési kulcsa** mezőben. 
   3. Mentse a módosításokat. Most frissíteni kell a Storage-fiók hozzáférési kulcsát.

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple biztonságáról](storsimple-8000-security.md).
* További információ [a StorSimple Eszközkezelő szolgáltatás a StorSimple-eszköz felügyeletéhez való használatáról](storsimple-8000-manager-service-administration.md).