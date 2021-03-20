---
title: Oktatóanyag a Azure Data Box exportálási sorrendben történő szállításához | Microsoft Docs
description: Megtudhatja, hogyan szállíthatja a Azure Data Box a Microsoftnak az exportálási sorrend befejezése után
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: alkohli
ms.openlocfilehash: 6c2eed9aac614b0765582d5cf373caf50323ef7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99537357"
---
# <a name="tutorial-return-azure-data-box"></a>Oktatóanyag: visszatérési Azure Data Box

Ez az oktatóanyag azt ismerteti, hogyan lehet visszaadni Azure Data Box és az adattörlést, amint az eszköz beérkeznek az Azure-beli adatba.

Ebben az oktatóanyagban az alábbi témaköröket ismerheti meg:

> [!div class="checklist"]
>
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box elküldése a Microsoftnak
> * Adatok törlése a Data Boxról

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Elvégezte az [oktatóanyagot: adatok másolása Azure Data Boxról](data-box-deploy-export-copy-data.md).
* A másolási feladatok befejeződtek. A szállítás előkészítése nem futtatható, ha másolási feladatok vannak folyamatban.

## <a name="prepare-to-ship"></a>A szállítás előkészítése

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

A következő lépések attól függenek, hogy hol küldi vissza az eszközt.

## <a name="ship-data-box-back"></a>Data Box visszaküldése

Győződjön meg arról, hogy az eszközről származó adatok másolása befejeződött, és a **szállításra való előkészítés** futtatása sikeres volt. Az eljárások attól függnek, hogy hol küldi vissza az eszközt.

## <a name="us-canada-europe"></a>[Egyesült Államok, Kanada és Európa](#tab/in-us-canada-europe)

Az alábbi lépéseket akkor végezze el, ha az eszközt az Egyesült Államokban, Kanadában vagy Európában küldi vissza.

1. Győződjön meg arról, hogy az eszköz ki van kapcsolva, és a kábelek el lettek távolítva. 
2. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
3. Győződjön meg arról, hogy a szállítási címke megjelenik az E-Ink megjelenítésnél, és ütemezzen egy pickupot a szállítóval. Ha a címke sérült vagy elveszett, vagy nem jelenik meg az E-Ink megjelenítésnél, forduljon a Microsoft ügyfélszolgálatahoz. Ha a támogatás azt sugallja, az Áttekintés > a Azure Portal a **szállítási címke letöltése** lehetőségre. Töltse le a szállítási címkét, és helyezze el az eszközön. 
4. Az eszköz visszaküldésekor egyeztessen egy csomagfelvételi időpontot a UPS-szel. Csomagfelvétel ütemezéséhez:

   - Hívja fel a helyi UPS-t (ország-/régióspecifikus ingyenes szám).
   - A hívás során adja meg az E-ink kijelzőn vagy a nyomtatott címkén látható visszaszállítási fuvarlevélcímke nyomkövetési számát. Ha nem adja meg a nyomkövetési számot, felárat kell fizetnie a UPS-nek a csomagfelvételkor.
   - Ha bármilyen probléma merül fel a felvétel ütemezésekor, vagy ha a rendszer kéri, hogy fizessen további díjakat, forduljon az Azure Data Box üzemeltetési csapatához. Küldjön egy e-mailt a következő címre: [adbops@microsoft.com](mailto:adbops@microsoft.com).

    A csomagfelvétel ütemezése helyett a legközelebbi átadóhelyen is leadhatja a Data Box Disket.
4. Miután a futár felvette és beolvasta a Data Boxot, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

## <a name="australia"></a>[Ausztrália](#tab/in-australia)

Az Ausztráliában található Azure-adatközpontok egy további biztonsági értesítéssel rendelkeznek. Az összes bejövő csomagnak előzetes értesítéssel kell rendelkeznie. Az Ausztráliában történő szállításhoz az alábbi lépéseket végezze el.

1. Őrizze meg az eszköz szállításához használt eredeti dobozt a visszaküldéshez.
2. Győződjön meg arról, hogy az adatok eszközre történő másolása befejeződött, és a **Szállításra való előkészítés** futtatása sikeres volt.
3. Kapcsolja ki az eszközt, és távolítsa el a kábeleket.
4. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
5. A [DHL-hivatkozásra](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference) kattintva foglaljon egy csomagfelvételt az interneten.

## <a name="japan"></a>[Japán](#tab/in-japan)

1. Őrizze meg az eszköz szállításához használt eredeti dobozt a visszaküldéshez.
2. Kapcsolja ki az eszközt, és távolítsa el a kábeleket.
3. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
4. A küldő információiként írja a vállalat nevét és címét a fuvarlevélre.
5. Küldjön e-mailt a Quantium Solutionsnek az alábbi e-mail-sablonnal.

    * Ha nem kapott japán postai Chakubarai-fuvarlevelet, vagy hiányzik, ezt jelezze az e-mailben. A Quantium Solutions Japan a csomagfelvételkor elküldi a fuvarlevelet a japán postával.
    * Ha több megrendelése van, küldjön e-mailt a külön felvételek érdekében.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

6. A csomagfelvétel lefoglalása után megerősítő e-mailt kap a Quantium Solutionstől. A megerősítő e-mail a Chakubarai-fuvarlevéllel kapcsolatos információkat is tartalmazza.

Szükség esetén a következő módokon léphet kapcsolatba a Quantium Solutions támogatásával (japán nyelven): 

* E-mail: Customerservice.JP@quantiumsolutions.com
* Telefon: 03-5755-0150

## <a name="singapore"></a>[Szingapúr](#tab/in-singapore)

1. Őrizze meg az eszköz szállításához használt eredeti dobozt a visszaküldéshez.
2. Jegyezze fel a nyomkövetési számot (ez a Data Box helyi webes felhasználói felületének „Prepare to Ship” (Szállítás előkészítése) lapján hivatkozási számként (reference number) jelenik meg). Ez akkor érhető el, amikor a szállítás-előkészítési lépés sikeresen befejeződik. Töltse le a fuvarlevélcímkét erről az oldalról, és rögzítse a dobozra.
3. Kapcsolja ki az eszközt, és távolítsa el a kábeleket.
4. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában. 
5. Írjon e-mailt a SingPost ügyfélszolgálatának a következő sablonnal, és adja meg a nyomkövetési számot.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > A munkanapokon fogadott foglalási kérelmek esetében:
   >
   > * 15 óra előtt a csomagfelvétel a következő munkanapon 9 és 13 óra között lesz.
   > * 15 óra előtt a csomagfelvétel a következő munkanapon 14 és 18 óra között lesz.  

## <a name="south-africa"></a>[Dél-afrikai Köztársaság](#tab/in-sa)

1. Tartsa meg az eszköz a visszaszállításhoz való csomagolásához használt eredeti mezőt.
2. Jegyezze fel az eszköz helyi webes FELÜLETén megjelenő hivatkozási számot (fuvarlevél száma). Ez a szám a **szállításra való előkészítés futtatásának sikeres végrehajtása** után jelenik meg.
3. Töltse le és nyomtassa ki az eszköz helyi webes FELÜLETén elérhető szállítási címkét, és helyezze azt a szállítási csomagba.
4. Ha a DHL-mel szeretne bejelentkezni, válasszon a következő lehetőségek közül:

    * Hívja meg az ügyfélszolgálat kapcsolattartási központját, mielőtt 2:00 PM-t **+ 27 (0) 11 9213600**, válassza az 1. lehetőséget, majd adja meg a fuvarlevél számát.
    * Küldjön egy e-mailt a [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) címre az alábbi sablonnal:

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * Ehelyett letilthatja a csomagot a legközelebbi DHL szolgáltatási ponton.

5. Ha bármilyen probléma merül fel, az e-mailben [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) adja meg az észlelt probléma (ok) adatait, és helyezze a fuvarlevél számát a Tárgy: sorba. Felhívhatja a +27(0)119213902 telefonszámot is.

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

1. Az eszközt az eredeti dobozban küldje vissza.
2. Jegyezze fel az eszköz helyi webes FELÜLETén látható hivatkozási számot (a fordított szállítólevél nyomon követési számát). Ez a szám a **szállításra való előkészítés futtatásának sikeres végrehajtása** után jelenik meg.
3. Töltse le és nyomtassa ki az eszköz helyi webes FELÜLETén elérhető szállítási címkét, és helyezze azt a szállítási csomagba.
4. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
5. Hívja a **Quantum Solutionst** a **(852) 2318 1213** telefonszámon munkaidőben (hétfőtől péntekig 9 és 18 óra között).  
6. Hivatkozzon a Microsoft Azure-csomagfelvételre, és adja meg a hivatkozási számot és a visszaküldési címkén (a vonalkód fölött) lévő nyomkövetési számot az átvétel egyeztetéséhez.
7. Szóbeli megerősítést kap a csomagátvételi ütemtervről. Ha a futár nem érkezik a gyűjteményhez, hívja meg az Quantium Solutions forródrótot alternatív megoldásokhoz.
8. A Quantiummal folytatott csomagfelvétel-egyeztetéskor továbbítsa a megerősítést a [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) felé az alábbi sablonnal:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Ha bármilyen probléma merül fel, az e-mail-Data Box Operations Asia [adbo@microsoft.com](mailto:adbo@microsoft.com) a tulajdonos fejlécében adja meg a feladatot, és a probléma merült fel.

## <a name="united-arab-emirates"></a>[Egyesült Arab Emírségek](#tab/in-uae)

1. Őrizze meg az eszköz szállításához használt eredeti dobozt a visszaküldéshez.
2. Győződjön meg arról, hogy az eszközön lévő Adatmásolás befejeződött, és a **szállításra való előkészítés** lépés sikeresen befejeződött.
3. Jegyezze fel a hivatkozási számot az eszköz helyi webes felhasználói felületének **szállításra való előkészítés** oldalán.
4. Kapcsolja ki az eszközt, és távolítsa el a kábeleket. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
6. Az eszközt az eredeti dobozban küldje vissza.
7. E-mail- [Azure Data Box művelet](mailto:adbops@microsoft.com) , amely a csomag azonosítására szolgál majd, amikor az adatközpontban érkezik vissza.
8. Jegyezze fel ezt az azonosítót a nyomtatott szállítási címkén a visszatérési cím mellett, hogy az egyértelműen látható legyen.  
9. Az online felvételt a [DHL Express EAE](https://mydhl.express.dhl/ae/en/home.html#/schedulePickupTab)-be való  >  **beosztással** is elfoglalhatja.
   - Adja meg a hivatkozási számot az eszköz helyi webes FELÜLETének **szállításra való előkészítés** lapján a fuvarlevél száma mezőben.
   - A foglalásokat hetente 6 nappal (kivéve a p és a nyilvános ünnepnapokat) fogadjuk el 9:00 – 2:00 órától.
   - A felvételi kérelmeket legalább 90 percre kell helyezni az ügyfél bezárási ideje előtt.
10. Ha bármilyen probléma merül fel a DHL foglalási eszközével kapcsolatban, a következő módszerek bármelyikével kapcsolatba léphet a DHL-szel:
    - Call 04-2924545.
    - A [ecom.ae@dhl.com](mailto:ecom.ae@dhl.com) probléma (ok) részleteit tartalmazó e-mailek, valamint a fuvarlevél számának elhelyezése a Tárgy: sorban.
    - Hívja meg a DHL ügyfélszolgálatát a 600 567567 címen.

## <a name="self-managed"></a>[Önállóan felügyelt](#tab/in-selfmanaged)

Ha Data Boxt használ Japánban, Szingapúrban, Koreában, Indiában, Dél-Afrikában, az Egyesült Királyságban, Nyugat-Európában vagy Ausztráliában, és a rendelés létrehozása során kiválasztotta az önfelügyelt szállítási lehetőséget, kövesse az alábbi utasításokat.

1. Jegyezze fel a hitelesítési kódot (Authorization code), amely a Data Box helyi webes felhasználói felületének „Prepare to Ship” (Szállítás előkészítése) lapján jelenik meg a lépés sikeres befejeződése után.
2. Kapcsolja ki az eszközt, és távolítsa el a kábeleket. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljánál.
3. Küldjön e-mailt az Azure Data Box operatív csapatnak az alábbi sablonnal, amikor készen áll az eszköz visszaküldésére.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról

Miután az eszköz eléri az Azure-adatközpontot, a Data Box a lemezén lévő adatmennyiséget a [NIST SP 800-88 változat 1 irányelveinek](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)megfelelően törli.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következő témaköröket:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box elküldése a Microsoftnak
> * Adatok törlése a Data Boxról

A következő cikkből megtudhatja, hogyan kezelheti Data Boxeit.

> [!div class="nextstepaction"]
> [Data Box kezelése Azure Portal használatával](./data-box-portal-admin.md)
