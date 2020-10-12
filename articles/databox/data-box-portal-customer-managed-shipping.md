---
title: Microsoft Azure Data Box önfelügyelt szállítás | AdatMicrosoft Docs
description: A Azure Data Box eszközök önfelügyelt szállítási munkafolyamatát ismerteti
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: cfa2dee41f07e273d933da356f74063453cb1775
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142536"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>A Azure Portal Azure Data Box önfelügyelt szállítás használata

Ez a cikk az önfelügyelt szállítási feladatokat ismerteti Azure Data Box eszközök megrendeléséhez, felvételéhez és letiltásához. Az Data Box eszközt a Azure Portal használatával kezelheti.

## <a name="prerequisites"></a>Előfeltételek

Az önfelügyelt szállítás a [Azure Data Box megrendelése](data-box-deploy-ordered.md)esetén választható. Az önállóan felügyelt szállítás csak a következő régiókban érhető el:

* Egyesült Államok – Államigazgatás
* Nyugat-Európa
* Japán
* Szingapúr
* Dél-Korea
* India
* Dél-afrikai Köztársaság

## <a name="use-self-managed-shipping"></a>Önállóan felügyelt szállítás használata

Data Box rendelés elhelyezésekor kiválaszthatja az önállóan felügyelt szállítási lehetőséget.

1. A Azure Data Boxi sorrendben a **kapcsolattartási adatok**területen válassza a **+ szállítási címek hozzáadása**elemet.
   ![Önállóan felügyelt szállítás](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. A szállítási típus kiválasztásakor válassza az **önfelügyelt szállítási** lehetőséget. Ez a beállítás csak akkor érhető el, ha az előfeltételek szakaszban leírtak szerint támogatott régióban van.

3. Miután megadta a szállítási címet, ellenőriznie kell, és el kell végeznie a rendelést.
   ![Önállóan felügyelt szállítás](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Ha az eszköz elkészült, és e-mailben értesítést kap, beütemezhet egy felvételt.

   A Azure Data Boxi sorrendben válassza az **Áttekintés** , majd a **felvételi ütemterv**lehetőséget.

   ![Data Box-eszköz megrendelése a felvételi szolgáltatáshoz](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Kövesse az Azure-ba való **felvétel menetrendje**című témakör utasításait.

   Az engedélyezési kód beszerzése előtt e-mailben kell [adbops@microsoft.com](mailto:adbops@microsoft.com) megadnia az eszköz felvételét a régió adatközpontjában.

   ![Csomagfelvétel ütemezése](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Miután ütemezte az eszköz felvételét, megtekintheti az eszköz engedélyezési kódját az Azure-beli **Ütemezés ütemezése** lapon.

   ![Az engedélyezési kód megtekintése](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Jegyezze fel ezt az **engedélyezési kódot**. A biztonsági követelményeknek megfelelően a picking ütemezésének időpontjában be kell mutatni annak a személynek a nevét, aki megérkezik a felvételre.

   Azt is meg kell adnia, hogy ki fog szolgálni az adatközpontba. Önnek vagy a kapcsolattartási pontnak a kormány által jóváhagyott, az adatközpontban érvényesített fénykép-AZONOSÍTÓval kell rendelkeznie.

   Emellett az eszköz beszedését végző személynek is rendelkeznie kell az **engedélyezési kóddal**. Az engedélyezési kódot a rendszer az adatközpontok időpontjában érvényesíti.

7. A megrendelés automatikusan a **kiválasztott** állapotba kerül, amint az eszközt az adatközpontból felvette.

    ![Szállítás alatt](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Az eszköz beléptetése után másolja az adatait a webhelyére Data Box. Az Adatmásolás befejezése után előkészítheti a Data Box szállítását. További információ: [szállításra való előkészítés](data-box-deploy-picked-up.md#prepare-to-ship).

   A **szállításra való előkészítés** lépésnek kritikus hibák nélkül kell megfelelnie, ellenkező esetben a szükséges javítások elvégzése után újra futtatnia kell ezt a lépést. A felkészülés a szállításra való sikeres befejezését követően az eszköz helyi felhasználói felületén megtekintheti a legördülő listából az engedélyezési kódot.

   > [!NOTE]
   > Ne ossza meg az engedélyezési kódot e-mailben. Ezt csak az adatközpontban lehet ellenőrizni a legördülő listából.

9. Ha a legördülő listáról kapott egy időpontot, a rendelésnek **készen** kell állnia az Azure-adatközpont állapotának fogadására a Azure Portal. Az eszköz visszaadásához kövesse az **ütemezett legördülő lista** utasításait.

   ![Az engedélyezési kód megtekintése](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Az azonosító és az engedélyezési kód ellenőrzése után a rendszer eldobta az eszközt az adatközpontban, a megrendelés állapotát meg kell **kapnia**.

    ![A fogadás befejeződött](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Az eszköz fogadása után az Adatmásolás folytatódni fog. A másolás befejezésekor a rendelés elkészült.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés az Azure Data Boxszal](data-box-quickstart-portal.md)
