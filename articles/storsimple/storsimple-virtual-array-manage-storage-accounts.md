---
title: StorSimple virtuális Array Storage-fiók hitelesítő adatainak kezelése | Microsoft Docs
description: A cikk azt ismerteti, hogyan használhatja a StorSimple Eszközkezelő configure (Konfigurálás) lapot a StorSimple virtuális tömbhöz társított Storage-fiók hitelesítő adatainak hozzáadásához, szerkesztéséhez, törléséhez vagy elforgatásához.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 70b6231829173059ffee675105bacbe1bd1532f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023891"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>StorSimple-Eszközkezelő használata a StorSimple virtuális tömbhöz tartozó Storage-fiók hitelesítő adatainak kezeléséhez

## <a name="overview"></a>Áttekintés
A StorSimple virtuális tömb StorSimple Eszközkezelő szolgáltatás paneljének **konfigurációs** szakasza a StorSimple Manager szolgáltatásban létrehozható globális szolgáltatási paramétereket mutatja be. Ezek a paraméterek a szolgáltatáshoz kapcsolódó összes eszközre alkalmazhatók, és a következők:

* Tárfiók hitelesítő adatai
* Hozzáférés-vezérlési rekordok
  
  ![Eszközkezelő szolgáltatás irányítópultja](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Ez az oktatóanyag azt ismerteti, hogyan adhat hozzá, szerkeszthet vagy törölhet Storage-fiók hitelesítő adatait a StorSimple virtuális tömbhöz. Az oktatóanyagban szereplő információk csak a StorSimple virtuális tömbre vonatkoznak. További információ a Storage-fiókok 8000 sorozatban való kezeléséről: [a StorSimple Manager szolgáltatás használata a Storage-fiók kezeléséhez](./storsimple-8000-manage-storage-accounts.md).

A Storage-fiók hitelesítő adatai tartalmazzák azokat a hitelesítő adatokat, amelyeket az eszköz használ a Storage-fiókhoz a felhőalapú szolgáltatóhoz való hozzáféréshez. Microsoft Azure Storage-fiókok esetében ezek a hitelesítő adatok, például a fiók neve és az elsődleges elérési kulcs.

A **Storage-fiók hitelesítő adatai** panelen a számlázási előfizetéshez létrehozott összes Storage-fiók hitelesítő adatai táblázatos formátumban jelennek meg, amely a következő információkat tartalmazza:

* **Name (név** ) – a fiókhoz a létrehozáskor hozzárendelt egyedi név.
* **SSL engedélyezve** – azt jelzi, hogy a TLS engedélyezve van-e, és az eszközről a felhőbe irányuló kommunikáció a biztonságos csatornán keresztül történik-e.
  
  ![Konfigurációs szakasz](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

A Storage-fiók **hitelesítő** adatai panelen végrehajtható hitelesítő adatokkal kapcsolatos leggyakoribb feladatok a következők:

* Tárfiók hitelesítő adatainak hozzáadása
* Storage-fiók hitelesítő adatainak szerkesztése
* Storage-fiók hitelesítő adatainak törlése

## <a name="types-of-storage-account-credentials"></a>A Storage-fiók hitelesítő adatainak típusai
A StorSimple-eszközzel három típusú Storage-fiók hitelesítő adatai használhatók.

* Automatikusan **generált Storage-fiók hitelesítő adatai** – ahogy a neve is sugallja, az ilyen típusú Storage-fiók hitelesítő adatai automatikusan létrejönnek a szolgáltatás első létrehozásakor. A Storage-fiók hitelesítő adatainak létrehozásával kapcsolatos további tudnivalókért tekintse meg az [új szolgáltatás létrehozása](storsimple-virtual-array-manage-service.md#create-a-service)című témakört.
* **a Storage-fiók hitelesítő adatai a szolgáltatás előfizetésében** – ezek az Azure Storage-fiók hitelesítő adatai, amelyek ugyanahhoz az előfizetéshez vannak társítva, mint a szolgáltatáshoz. További információ a Storage-fiók hitelesítő adatainak létrehozásáról: [Tudnivalók az Azure Storage-fiókokról](../storage/common/storage-account-create.md).
* **a Storage-fiók hitelesítő adatai a szolgáltatás előfizetésén kívül** – ezek az Azure Storage-fiók hitelesítő adatai, amelyek nincsenek társítva a szolgáltatáshoz, és valószínűleg léteztek a szolgáltatás létrehozása előtt.

## <a name="add-a-storage-account-credential"></a>Tárfiók hitelesítő adatainak hozzáadása
A StorSimple Eszközkezelő szolgáltatás konfigurálásához hozzáadhat egy Storage-fiók hitelesítő adatait úgy, hogy megadja a Storage-fiókhoz társított egyedi rövid nevet és hozzáférési hitelesítő adatokat. Lehetősége van arra is, hogy engedélyezze a Transport Layer Security (TLS) üzemmódot (korábbi nevén SSL (SSL)), hogy biztonságos csatornát hozzon létre az eszköz és a felhő közötti hálózati kommunikációhoz.

Egy adott felhőalapú szolgáltatóhoz több fiókot is létrehozhat. A Storage-fiók hitelesítő adatainak mentésekor a szolgáltatás megpróbál kommunikálni a felhőalapú szolgáltatóval. A hitelesítő adatok és a megadott hozzáférési anyagok jelenleg hitelesítés alatt állnak. A rendszer csak akkor hozza létre a Storage-fiók hitelesítő adatait, ha a hitelesítés sikeres. Ha a hitelesítés sikertelen, a rendszer a megfelelő hibaüzenetet jeleníti meg.

Az Azure Storage-fiók hitelesítő adatainak hozzáadásához kövesse az alábbi eljárásokat:

* A Eszközkezelő szolgáltatással azonos Azure-előfizetéssel rendelkező Storage-fiók hitelesítő adatainak hozzáadása
* Azure Storage-fiók hitelesítő adatainak hozzáadása a Eszközkezelő szolgáltatás-előfizetésen kívül

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>A Eszközkezelő szolgáltatással azonos Azure-előfizetéssel rendelkező Storage-fiók hitelesítő adatainak hozzáadása

1. Navigáljon a Eszközkezelő szolgáltatáshoz, válassza ki, majd kattintson rá duplán. Ekkor megnyílik az **Áttekintés** panel.
2. A **konfigurációs** szakaszban válassza ki a **Storage-fiók hitelesítő adatait** .
3. Kattintson a **Hozzáadás** parancsra.
4. A **Storage-fiók hozzáadása** panelen tegye a következőket:
   
    1. Az **előfizetés** mezőben válassza az **aktuális** lehetőséget.
    2. Adja meg az Azure Storage-fiók nevét.
    3. Az **Engedélyezés** gombra kattintva biztonságos csatornát hozhat létre a StorSimple-eszköz és a felhő közötti hálózati kommunikációhoz. Csak akkor válassza a **Letiltás** lehetőséget, ha privát felhőben működik.
    4. Kattintson a **Hozzáadás** parancsra. A rendszer értesítést küld arról, hogy a Storage-fiók létrehozása sikeres volt.<br></br>
   
        ![Meglévő Storage-fiók hitelesítő adatainak hozzáadása](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Azure Storage-fiók hitelesítő adatainak hozzáadása a Eszközkezelő szolgáltatás-előfizetésen kívül

1. Navigáljon a Eszközkezelő szolgáltatáshoz, válassza ki, majd kattintson rá duplán. Ekkor megnyílik az **Áttekintés** panel.
2. A **konfigurációs** szakaszban válassza ki a **Storage-fiók hitelesítő adatait** . Ez felsorolja a StorSimple Eszközkezelő szolgáltatáshoz társított összes meglévő Storage-fiók hitelesítő adatait.
3. Kattintson a **Hozzáadás** parancsra.
4. A **Storage-fiók hozzáadása** panelen tegye a következőket:
   
    1. Az **előfizetés** mezőben válassza az **egyéb** lehetőséget.
   
    2. Adja meg az Azure Storage-fiók hitelesítő adatainak nevét.
   
    3. A **Storage-fiók hozzáférési kulcsa** szövegmezőben adja meg az Azure Storage-fiók hitelesítő adatainak elsődleges elérési kulcsát. A kulcs beszerzéséhez nyissa meg az Azure Storage szolgáltatást, válassza ki a Storage-fiók hitelesítő adatait, majd kattintson a **fiók kulcsainak kezelése** lehetőségre. Most már az elsődleges hozzáférési kulcsot is másolhatja.
   
    4. A TLS engedélyezéséhez kattintson az **Engedélyezés** gombra egy biztonságos csatorna létrehozásához a StorSimple Eszközkezelő szolgáltatás és a felhő közötti hálózati kommunikációhoz. Kattintson a **Letiltás** gombra, ha privát felhőben működik.
   
    5. Kattintson a **Hozzáadás** parancsra. A rendszer értesítést küld a Storage-fiók hitelesítő adatainak sikeres létrehozása után.

5. Az újonnan létrehozott Storage-fiók hitelesítő adatai a StorSimple konfigurálása Eszközkezelő szolgáltatás paneljén a **Storage-fiók hitelesítő adatai** területen jelennek meg.
   
    ![Adja hozzá a Storage-fiók hitelesítő adatait a Eszközkezelő szolgáltatás-előfizetésen kívül](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Storage-fiók hitelesítő adatainak szerkesztése
Módosíthatja az eszköz által használt Storage-fiók hitelesítő adatait. Ha olyan Storage-fiók hitelesítő adatait szerkeszti, amely jelenleg használatban van, a módosításhoz elérhető mezők a hozzáférési kulcs és a TLS-mód a Storage-fiók hitelesítő adataihoz. Megadhatja az új tárterület-hozzáférési kulcsot, vagy módosíthatja az **SSL-mód engedélyezése** lehetőséget, és mentheti a frissített beállításokat.

#### <a name="to-edit-a-storage-account-credential"></a>A Storage-fiók hitelesítő adatainak szerkesztése
1. Navigáljon a Eszközkezelő szolgáltatáshoz, válassza ki, majd kattintson rá duplán. Ekkor megnyílik az **Áttekintés** panel.
2. A **konfigurációs** szakaszban válassza ki a **Storage-fiók hitelesítő adatait** . Ez felsorolja a StorSimple Eszközkezelő szolgáltatáshoz társított összes meglévő Storage-fiók hitelesítő adatait.
3. A Storage-fiók hitelesítő adatainak táblázatos listájában válassza ki, majd kattintson duplán arra a fiókra, amelyet módosítani szeretne.
4. A Storage-fiók hitelesítő adatainak **tulajdonságai** panelen tegye a következőket:
   
   1. Ha szükséges, módosíthatja az SSL-mód **engedélyezése** lehetőséget.
   2. Dönthet úgy, hogy újragenerálja a Storage-fiók hitelesítőadat-hozzáférési kulcsait. További információ: a [Storage-fiók elérési kulcsainak kezelése](../storage/common/storage-account-keys-manage.md). Adja meg az új Storage-fiók hitelesítő kulcsát. Azure Storage-fiók esetén ez az elsődleges hozzáférési kulcs.
   3. A beállítások mentéséhez kattintson a **Tulajdonságok panel** tetején található **Mentés** gombra. A beállítások frissülnek a **Storage-fiók hitelesítő adatai** panelen.
      
      ![Storage-fiók hitelesítő adatainak szerkesztése](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Storage-fiók hitelesítő adatainak törlése
> [!IMPORTANT]
> A Storage-fiók hitelesítő adatait csak akkor lehet törölni, ha az nincs használatban. Ha a Storage-fiók hitelesítő adatai használatban vannak, értesítést kap.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>A Storage-fiók hitelesítő adatainak törlése
1. Navigáljon a Eszközkezelő szolgáltatáshoz, válassza ki, majd kattintson rá duplán. Ekkor megnyílik az **Áttekintés** panel.
2. A **konfigurációs** szakaszban válassza ki a **Storage-fiók hitelesítő adatait** . Ez felsorolja a StorSimple Eszközkezelő szolgáltatáshoz társított összes meglévő Storage-fiók hitelesítő adatait.
3. A Storage-fiók hitelesítő adatainak táblázatos listájában válassza ki, majd kattintson duplán a törölni kívánt fiókra.
4. A Storage-fiók hitelesítő adatainak **tulajdonságai** panelen tegye a következőket:
   
   1. A hitelesítő adatok törléséhez kattintson a **Törlés** gombra.
   2. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a törlés folytatásához. A táblázatos lista frissül, hogy tükrözze a módosításokat.
      
      ![Storage-fiók hitelesítő adatainak törlése](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>A Storage-fiók hitelesítő kulcsainak szinkronizálása
Biztonsági okokból a kulcsfontosságú rotációs szolgáltatás gyakran követelmény az adatközpontokban. Egy Microsoft Azure rendszergazda újra létrehozhatja vagy módosíthatja az elsődleges vagy másodlagos kulcsot úgy, hogy közvetlenül hozzáfér a Storage-fiók hitelesítő adataihoz (a Microsoft Azure Storage szolgáltatáson keresztül). A StorSimple Eszközkezelő szolgáltatás nem látja automatikusan ezt a változást.

Ahhoz, hogy tájékoztassa a StorSimple Eszközkezelő szolgáltatását a változásról, el kell érnie a StorSimple Eszközkezelő szolgáltatást, el kell érnie a Storage-fiók hitelesítő adatait, majd szinkronizálnia kell az elsődleges vagy másodlagos kulcsot (attól függően, hogy melyik lett módosítva). A szolgáltatás ezután lekéri a legújabb kulcsot, titkosítja a kulcsokat, és elküldi a titkosított kulcsot az eszköznek.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>A Storage-fiók hitelesítő adatainak kulcsainak szinkronizálása a szolgáltatással megegyező előfizetésben (csak az Azure-ban)
1. A szolgáltatás kirakodási paneljén válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **konfiguráció** szakaszban kattintson a **Storage-fiók hitelesítő adatai** elemre.
2. A **Storage-fiók hitelesítő adatai** panelen, a Storage-fiók hitelesítő adatainak listájában válassza ki azt a Storage-fiók hitelesítő adatait, amelynek kulcsait szinkronizálni szeretné.
3. A kiválasztott Storage-fiók hitelesítő adatainak **Tulajdonságok** paneljén tegye a következőket:
   
    1. Kattintson a **továbbiak**, majd a **hozzáférési kulcs szinkronizálása** elemre.
   
    2. Ha a rendszer megerősítést kér, kattintson a szinkronizálás **kulcs** elemre a szinkronizálás befejezéséhez.
    
4. A StorSimple Eszközkezelő szolgáltatásban frissítenie kell azt a kulcsot, amely korábban megváltozott a Microsoft Azure Storage szolgáltatásban. Ha az elsődleges hozzáférési kulcs módosult (újragenerált), akkor a **Storage-fiók kulcsának szinkronizálása** panelen kattintson az elsődleges elemre, majd a **kulcs szinkronizálása** elemre. Ha a másodlagos kulcs módosult, kattintson a **másodlagos**, majd a **kulcs szinkronizálása** elemre.
   
    ![Hozzáférési kulcs szinkronizálása](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [kezelheti a StorSimple virtuális tömböt](storsimple-ova-web-ui-admin.md).