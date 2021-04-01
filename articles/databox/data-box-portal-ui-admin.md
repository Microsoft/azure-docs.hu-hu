---
title: Azure Data Box Disk – portáladminisztrációs útmutató | Microsoft Docs
description: Megtudhatja, hogyan kezelheti a Data Box Disk a Azure Portal használatával. A megrendelések kezelése, a lemezek kezelése, valamint egy megrendelés állapotának nyomon követése.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 538a650c6063422f89c8ed3d1753981a293693b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94338086"
---
# <a name="use-azure-portal-to-administer-your-data-box-disk"></a>A Data Box Disk felügyelete az Azure Portal használatával

A cikk oktatóanyagai a Microsoft Azure Data Box Disk előzetes verziójára vonatkoznak. A cikk a Data Box Disken végrehajtható komplex munkafolyamatokat és felügyeleti feladatokat ismerteti. 

A Data Box Disk az Azure Portalon keresztül felügyelhető. A cikk az Azure Portalon végrehajtható feladatokra összpontosít. Az Azure Portalon kezelheti a rendeléseket, felügyelheti a meghajtókat, és nyomon követheti a rendelések a befejezési állapotukhoz közeli állapotát.

## <a name="cancel-an-order"></a>Rendelés visszavonása

A rendelések azok feladása utáni visszavonása mellett számos különféle okból döntet. A rendelést a meghajtók előkészítését megelőzően vonhatja vissza. A meghajtók előkészítését és a rendelés feldolgozását követően már nincs lehetőség a visszavonására. 

A rendelés visszavonásához kövesse az alábbi lépéseket.

1.  Lépjen az **Áttekintés > Megszakítás** menüpontra. 

    ![Parancs megszakítása egy rendelés áttekintés lapján](media/data-box-portal-ui-admin/portal-ui-admin-cancel-command.png)

2.  Adja meg a rendelés visszavonásának okát.  

    ![Megrendelés megszakításának oka](media/data-box-portal-ui-admin/portal-ui-admin-cancel-order-reason.png)

3.  Miután visszavonta a rendelést, a portálon a rendelés állapota frissül a **Megszakítva** állapotra.

    ![Megszakított sorrend](media/data-box-portal-ui-admin/portal-ui-admin-canceled-order.png)

A rendelések visszavonásakor nem kap értesítő e-mailt.

## <a name="clone-an-order"></a>Rendelés klónozása

A klónozás bizonyos helyzetekben hasznosnak bizonyulhat. Például a felhasználó egyes adatokat már átvitt a Data Box Disk használatával. Ahogy egyre újabb adatok keletkeznek, újabb meghajtókra van szükség ezek az Azure-ba való átviteléhez. Ebben az esetben egyszerűen klónozhatja az előző rendelést.

A rendelések klónozásához kövesse az alábbi lépéseket.

1.  Lépjen az **Áttekintés > Klónozás** menüpontra. 

    ![Klónozási parancs egy rendelés áttekintés lapján](media/data-box-portal-ui-admin/portal-ui-admin-clone-command.png)

2.  A rendelés részletei változatlanok maradnak. A rendelés neve az eredeti rendelés neve lesz a *-Klón* utótaggal kiegészítve. Jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy átolvasta az adatvédelmi információkat. Kattintson a **Létrehozás** lehetőségre.    

A klón létrehozása néhány percet vesz igénybe, és a portálon megjelenik az új rendelés.

[![Klónozott sorrend](media/data-box-portal-ui-admin/portal-ui-admin-cloned-order.png)](media/data-box-portal-ui-admin/portal-ui-admin-cloned-order.png#lightbox) 

## <a name="delete-order"></a>Rendelés törlése

A rendeléseket azok teljesítését követően esetleg törölni szeretné. A rendelés tartalmazza az Ön személyes adatait, úgymint a nevét, a címét és a kapcsolattartási adatait. A rendelés törlésekor a személyes adatok törölve lesznek.

Csak a teljesített vagy visszavont rendeléseket törölheti. A rendelések törléséhez kövesse az alábbi lépéseket.

1. Lépjen a **Minden erőforrás** menüpontra. Keresse meg a rendelést.

    ![Keresési megrendelések](media/data-box-portal-ui-admin/portal-ui-admin-search-data-box-disk-orders.png)

2. Kattintson a törölni kívánt rendelésre, és lépjen az **Áttekintés** felületre. A parancssoron kattintson a **Törlés** elemre.

    ![Megrendelés törlése](media/data-box-portal-ui-admin/portal-ui-admin-delete-command.png)

3. Ha a rendszer kéri, írja be a rendelés nevét a rendelés törlésének megerősítéséhez. Kattintson a **Törlés** gombra.

     ![Megrendelés törlésének megerősítése](media/data-box-portal-ui-admin/portal-ui-admin-confirm-deletion.png)


## <a name="download-shipping-label"></a>Fuvarlevélcímke letöltése

Ha a meghajtókkal szállított fuvarlevélcímke elveszett vagy nem találja, le kell töltenie a portálról. 

A fuvarlevélcímkék letöltéséhez kövesse az alábbi lépéseket.
1.  Lépjen az **Áttekintés > Levélcímke letöltése** menüpontra. Ez a lehetőség csak a meghajtó kézbesítését követően érhető el. 

    ![Fuvarlevélcímke letöltése](media/data-box-portal-ui-admin/portal-ui-admin-download-shipping-label.png)

2.  Ezzel letölti a következő fuvarlevélcímkét a visszaküldéshez. Mentse a címkét, majd nyomtassa ki, és tüntesse fel a visszaküldött csomagon.

    ![Példa a fuvarlevélcímkére](media/data-box-portal-ui-admin/portal-ui-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Szállítási cím szerkesztése

Előfordulhat, hogy a rendelés feladását követően módosítania kell a szállítási címet. Ezt csak a meghajtók feladásáig teheti meg. A meghajtó feladását követően ez a lehetőség már nem érhető el.

A rendelés szerkesztéséhez kövesse az alábbi lépéseket.

1. Lépjen a **Rendelés részletei > Szállítási cím szerkesztése** menüpontra.

    ![A szállítási címek parancs szerkesztése a Rendelés részletei között](media/data-box-portal-ui-admin/portal-ui-admin-edit-shipping-address-command.png)

2. Itt szerkesztheti a szállítási címet, majd mentheti a változásokat.

    ![Szállítási címek szerkesztése párbeszédpanel](media/data-box-portal-ui-admin/portal-ui-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Értesítési adatok szerkesztése

Lehet, hogy módosítani szeretné, hogy mely felhasználók kapjanak értesítő e-maileket a rendelés állapotáról. Például előfordulhat, hogy valamelyik felhasználót értesíteni kell, ha a meghajtót kiszállítják vagy már átvették. Előfordulhat, hogy egy másik felhasználónak is tájékoztatnia kell, amikor az Adatmásolás befejeződött, így ellenőrizhetik, hogy az adatok az Azure Storage-fiókban találhatók-e, mielőtt törölné a forrásból. Ilyen esetekben szerkesztheti az értesítési adatokat.

Az értesítési adatok szerkesztéséhez kövesse az alábbi lépéseket.

1. Lépjen a **Rendelés részletei > Értesítési adatok szerkesztése** menüpontra.

    ![Értesítés részleteinek szerkesztése parancs a sorrend részletei között](media/data-box-portal-ui-admin/portal-ui-admin-edit-notification-details-command.png)

2. Itt szerkesztheti az értesítési adatokat, majd mentheti a változásokat.
 
    ![Értesítés részleteinek szerkesztése párbeszédpanel](media/data-box-portal-ui-admin/portal-ui-admin-edit-notification-details-dbox.png)

## <a name="view-order-status"></a>Rendelés állapotának megtekintése

|Rendelés állapota |Leírás |
|---------|---------|
|Megrendelve     | A rendelés sikeresen fel lett adva. <br> Ha a lemezek nem érhetők el, értesítést kap. <br>Ha vannak elérhető meghajtók, a Microsoft kiválaszt egyet a szállításhoz, és előkészíti a meghajtócsomagot.        |
|Feldolgozva     | A rendelés feldolgozása befejeződött. <br> A rendelése feldolgozása során a következő műveletek történnek meg:<li>A lemezek titkosítása a AES-128 BitLocker használatával. </li> <li>A Data Box Disk-meghajtók zárolása az illetéktelen hozzáférés megakadályozására.</li><li>A folyamat során létrejön a lemezek zárolását feloldó hozzáférési kulcs.</li>        |
|Feladva     | A rendelés fel lett adva. A rendelést 1–2 napon belül kapja kézhez.        |
|Kézbesítve     | A rendelés ki lett kézbesítve a rendelésben megadott címre.        |
|Szállítás alatt     |A visszaküldött csomagot a futár átvette. <br> A szállítás az Azure-adatközpontban való fogadása után automatikusan feltölti az Azure-ba.         |
|Megérkezett     | A meghajtókat átvették az Azure-adatközpontban. Az adatok másolása hamarosan megkezdődik.        |
|Adatok átmásolva     |Az adatok másolása folyamatban van.<br> Várjon, amíg az adatok másolása befejeződik.         |
|Befejeződött       |A rendelés sikeresen teljesítve lett.<br> Ellenőrizze, hogy az adatok elérhetők-e az Azure-ban, mielőtt törölné a helyszíni adatokat a kiszolgálókról.         |
|Befejeződött, hibákkal| Az adatok másolása befejeződött, azonban hibák jelentkeztek. <br> Tekintse át a feltöltési naplókat az **áttekintésben** megadott elérési út használatával. További információt a [feltöltési hibák naplóinak letöltése](data-box-disk-troubleshoot-upload.md#download-logs)című témakörben olvashat.   |
|Megszakítva            |A rendelés vissza lett vonva. <br> Vagy visszavonta a rendelést, vagy hiba történt, és a szolgáltatás vonta vissza.     |



## <a name="next-steps"></a>Következő lépések

- Ismerje meg [a Data Box Disk-hibák elhárításának](data-box-disk-troubleshoot.md) módját.
