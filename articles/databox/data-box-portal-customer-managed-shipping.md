---
title: Microsoft Azure Data Box önfelügyelt szállítás | AdatMicrosoft Docs
description: A Azure Data Box eszközök önfelügyelt szállítási munkafolyamatát ismerteti
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 07529b18191c71776a9a36edbfa4cfd8ded5af4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524549"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>A Azure Portal Azure Data Box önfelügyelt szállítás használata

Ez a cikk az önfelügyelt szállítási feladatokat ismerteti Azure Data Box eszközök megrendeléséhez, felvételéhez és letiltásához. Az Data Box eszközt a Azure Portal használatával kezelheti.

## <a name="prerequisites"></a>Előfeltételek

Az önfelügyelt szállítás a [Azure Data Box megrendelése](data-box-deploy-ordered.md)esetén választható. Az önállóan felügyelt szállítás csak a következő régiókban érhető el:

* Egyesült Államok – Államigazgatás
* Egyesült Királyság
* Nyugat-Európa
* Japán
* Szingapúr
* Dél-Korea
* India
* Dél-afrikai Köztársaság
* Ausztrália

## <a name="use-self-managed-shipping"></a>Önállóan felügyelt szállítás használata

Data Box rendelés elhelyezésekor kiválaszthatja az önállóan felügyelt szállítási lehetőséget.

1. A Azure Data Boxi sorrendben a **kapcsolattartási adatok** területen válassza a **+ szállítási címek hozzáadása** elemet.
 
   ![Saját üzemeltetésű szállítás, szállítási címek hozzáadása](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. A szállítási típus kiválasztásakor válassza az **önfelügyelt szállítási** lehetőséget. Ez a beállítás csak akkor érhető el, ha az előfeltételek szakaszban leírtak szerint támogatott régióban van.

3. Miután megadta a szállítási címet, ellenőriznie kell, és teljesítenie kell a rendelését.

   ![Önfelügyelt szállítás, ellenőrzés és hozzáadási címek](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Ha az eszköz elkészült, és e-mailben értesítést kap, beütemezhet egy felvételt.

   A Azure Data Boxi sorrendben válassza az **Áttekintés** , majd a **felvételi ütemterv** lehetőséget.

   ![Data Box sorrend, felvételi lehetőség beosztása](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Kövesse az Azure-ba való **felvétel menetrendje** című témakör utasításait.

   Az engedélyezési kód beszerzése előtt e-mailben kell [adbops@microsoft.com](mailto:adbops@microsoft.com) megadnia az eszköz felvételét a régió adatközpontjában.

   ![Az Azure-utasítások felvételének ütemterve](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Az eszköz felvételének megkezdése után megtekintheti az eszköz engedélyezési kódját az Azure-ban való **felvétel ütemterve** ablaktáblán.

   ![Az eszköz engedélyezési kódjának megtekintése](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Jegyezze fel ezt az **engedélyezési kódot**. A biztonsági követelményeknek megfelelően a picking szolgáltatás ütemezésének időpontjában be kell mutatni annak a személynek a nevét, aki megérkezik a felvételre.

   Azt is meg kell adnia, hogy ki fog szolgálni az adatközpontba. Önnek vagy a kapcsolattartási pontnak a kormány által jóváhagyott, az adatközpontban érvényesített fénykép-AZONOSÍTÓval kell rendelkeznie.

   Emellett az eszköz beszedését végző személynek is rendelkeznie kell az **engedélyezési kóddal**. Az engedélyezési kódot a rendszer az adatközpontok időpontjában érvényesíti.

7. A megrendelés automatikusan a **kiválasztott** állapotba kerül, amint az eszközt az adatközpontból felvette.

    ![Megrendelés a kiválasztott állapotban](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Az eszköz beléptetése után másolja az adatait a webhelyére Data Box. Az Adatmásolás befejezése után előkészítheti a Data Box szállítását. További információ: [szállításra való előkészítés](data-box-deploy-picked-up.md#prepare-to-ship).

   A **szállításra való előkészítés** lépést kritikus hibák nélkül kell befejezni. Ellenkező esetben a szükséges javítások elvégzése után újra futtatnia kell ezt a lépést. A **szállításra való előkészítés** lépés sikeres befejezése után megtekintheti az eszköz helyi felhasználói felületén található legördülő lista engedélyezési kódját.

   > [!NOTE]
   > Ne ossza meg az engedélyezési kódot e-mailben. Ezt csak az adatközpontban lehet ellenőrizni a legördülő listából.

9. Ha a legördülő listáról kapott egy időpontot, a rendelésnek **készen** kell állnia az Azure-adatközpont állapotának fogadására a Azure Portal. Az eszköz visszaadásához kövesse az **ütemezett legördülő lista** utasításait.

   ![Az eszköz kidobására vonatkozó utasítások](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Az azonosító és az engedélyezési kód ellenőrzése után a rendszer eldobta az eszközt az adatközpontban, a megrendelés állapotát meg kell **kapnia**.

    ![Egy megkapott állapotú rendelés](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Az eszköz fogadása után az Adatmásolás folytatódni fog. A másolás befejezésekor a rendelés elkészült.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés az Azure Data Boxszal](data-box-quickstart-portal.md)
