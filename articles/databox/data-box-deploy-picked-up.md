---
title: Microsoft Azure Data Box Disk visszaküldésével kapcsolatos oktatóanyag | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan küldheti vissza az Azure Data Boxot, beleértve a szállítás előkészítését, a Data Box szállítását, az adatfeltöltés ellenőrzését és az adatok törlését a Data Boxról.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 267094ea6a7295a65b93fb7700c97e4280da341d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539111"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Oktatóanyag: Az Azure Data Box visszaküldése, majd az Azure-ba történő adatfeltöltés ellenőrzése

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>A Data Box visszaküldése, majd az Azure-ba történő adatfeltöltés ellenőrzése

::: zone-end

::: zone target="docs"

Az oktatóanyagból megtudhatja, hogyan küldheti vissza az Azure Data Boxot, és ellenőrizheti az adatok az Azure-ba való feltöltését.

Ebben az oktatóanyagban az alábbi témaköröket ismerheti meg:

> [!div class="checklist"]
>
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Boxról

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Elvégezte az [Oktatóanyag: Adatok másolása az Azure Data Boxra, majd ellenőrzése](data-box-deploy-copy-data.md) lépéseit.
* A másolási feladatok befejeződtek, és nem látható hiba a **Csatlakozás és másolás** oldalon. **A szállítás előkészítése** nem futtatható, ha másolási feladatok vannak folyamatban, vagy hibák láthatók a **Csatlakozás és másolás** oldalon.

## <a name="prepare-to-ship"></a>A szállítás előkészítése

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Az adatok másolásának befejeződése után előkészítheti és elküldheti az eszközt. Amikor az eszköz megérkezik az Azure-adatközpontba, az adatok automatikusan fel lesznek töltve az Azure-ba.

## <a name="prepare-to-ship"></a>A szállítás előkészítése

A szállítás előkészítése előtt győződjön meg arról, hogy a másolási feladatok befejeződtek.

1. Nyissa meg a helyi webes kezelőfelület **Prepare to ship** (Szállítás előkészítése) lapját, és kezdje meg az előkészületeket a szállításra.
2. Kapcsolja ki az eszközt a helyi webes kezelőfelületen. Válassza le a kábeleket az eszközről.

A következő lépések attól függenek, hogy hol küldi vissza az eszközt.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Data Box visszaküldése

Győződjön meg arról, hogy az adatok eszközre történő másolása befejeződött, és **A szállítás előkészítése** futtatása sikeres volt. Az eljárások attól függnek, hogy hol küldi vissza az eszközt.

::: zone-end

## <a name="us-canada-europe"></a>[Egyesült Államok, Kanada és Európa](#tab/in-us-canada-europe)

Az alábbi lépéseket akkor végezze el, ha az eszközt az Egyesült Államokban, Kanadában vagy Európában küldi vissza.

1. Győződjön meg arról, hogy az eszköz ki van kapcsolva, és a kábelek el lettek távolítva.
2. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
3. Győződjön meg arról, hogy a fuvarlevélcímke látható az E-ink kijelzőn, és egyeztesse a csomagfelvételt a szállítóval. Ha a címke sérült, elveszett vagy nem jelenik meg az E-ink kijelzőn, vegye fel a kapcsolatot a Microsoft ügyfélszolgálatával. Ha az ügyfélszolgálat azt javasolja, lépjen az Azure Portalon az **Áttekintés > Fuvarlevélcímke letöltése** területre. Töltse le a fuvarlevélcímkét, és rögzítse az eszközre. 
4. Az eszköz visszaküldésekor egyeztessen egy csomagfelvételi időpontot a UPS-szel. Csomagfelvétel ütemezéséhez:

    * Hívja fel a helyi UPS-t (ország-/régióspecifikus ingyenes szám).
    * A hívás során adja meg az E-ink kijelzőn vagy a nyomtatott címkén látható visszaszállítási fuvarlevélcímke nyomkövetési számát. Ha nem adja meg a nyomkövetési számot, felárat kell fizetnie a UPS-nek a csomagfelvételkor.
    * Ha bármilyen probléma merül fel a felvétel ütemezésekor, vagy ha a rendszer kéri, hogy fizessen további díjakat, forduljon az Azure Data Box üzemeltetési csapatához. Küldjön egy e-mailt a következő címre: [adbops@microsoft.com](mailto:adbops@microsoft.com).

    A csomagfelvétel ütemezése helyett a legközelebbi átadóhelyen is leadhatja a Data Box Disket.
4. Miután a futár felvette és beolvasta a Data Boxot, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról

Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

::: zone-end

## <a name="australia"></a>[Ausztrália](#tab/in-australia)

Az Ausztráliában található Azure-adatközpontok egy további biztonsági értesítéssel rendelkeznek. Az összes bejövő csomagnak előzetes értesítéssel kell rendelkeznie. Az Ausztráliában történő szállításhoz az alábbi lépéseket végezze el.

1. Őrizze meg az eszköz szállításához használt eredeti dobozt a visszaküldéshez.
2. Győződjön meg arról, hogy az adatok eszközre történő másolása befejeződött, és a **Szállításra való előkészítés** futtatása sikeres volt.
3. Kapcsolja ki az eszközt, és távolítsa el a kábeleket.
4. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
5. A [DHL-hivatkozásra](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference) kattintva foglaljon egy csomagfelvételt az interneten.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról

Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

::: zone-end

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

::: zone-end

## <a name="singapore"></a>[Szingapúr](#tab/in-singapore)

1. Őrizze meg az eszköz szállításához használt eredeti dobozt a visszaküldéshez.
2. Jegyezze fel a nyomkövetési számot (ez a Data Box helyi webes felhasználói felületének **„Prepare to Ship” (A szállítás előkészítése)** lapján hivatkozási számként (reference number) jelenik meg). A nyomkövetési szám akkor érhető el, amikor a **szállítás-előkészítési** (Prepare to ship) lépés sikeresen befejeződik. Töltse le a fuvarlevélcímkét erről az oldalról, és rögzítse a dobozra.
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
   > * 15 óra előtt a csomagfelvétel a következő munkanapon 9 és 13 óra között lesz.
   > * 15 óra előtt a csomagfelvétel a következő munkanapon 14 és 18 óra között lesz.  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról

Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

::: zone-end

## <a name="south-africa"></a>[Dél-afrikai Köztársaság](#tab/in-sa)

1. Az eszközt az eredeti dobozban küldje vissza.
2. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
3. Jegyezze fel a nyomkövetési számot (ez a Data Box helyi webes felhasználói felületének **Prepare to Ship** (Szállítás előkészítése) lapján hivatkozási számként (reference number) jelenik meg). A nyomkövetési szám akkor érhető el, amikor a szállítás-előkészítési (Prepare to ship) lépés sikeresen befejeződik. Töltse le a fuvarlevélcímkét erről az oldalról, és rögzítse a dobozra.
4. Kérjen egy visszatérési kódot az Azure Data Box üzemeltetési csapatától. A visszatérési kódra azért van szükség, hogy vissza lehessen küldeni a csomagot az adatközpontnak. Küldjön egy e-mailt a következő címre: [adbops@microsoft.com](mailto:adbops@microsoft.com). Jegyezze fel ezt a kódot a fuvarlevélcímkére a visszaküldési cím mellé, ügyelve rá, hogy jól látható legyen.
5. Foglaljon csomagfelvételt a DHL-lel a következő módszerek egyikével:
 
   * A [DHL Express South Africa webhelyén, a **Schedule a Pickup**](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference) (Csomagfelvétel ütemezése) területen foglalhat online csomagfelvételt.
   * Küldjön egy e-mailt a [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) címre az alábbi sablonnal:

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * Másik lehetőségként leadhatja a csomagot a legközelebbi DHL feladási ponton.

6. Ha problémába ütközik, küldjön e-mailt a következő címre: [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com). Az e-mailben írja meg a tapasztalt probléma/problémák részleteit, a tárgysorban pedig adja meg a fuvarlevél számát. Felhívhatja a +27(0)119213902 telefonszámot is.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról

Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

::: zone-end

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

1. Az eszközt az eredeti dobozban küldje vissza.
2. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
3. Hívja a **Quantum Solutionst** a **(852) 2318 1213** telefonszámon munkaidőben (hétfőtől péntekig 9 és 18 óra között).  
4. Hivatkozzon a Microsoft Azure-csomagfelvételre, és adja meg a hivatkozási számot és a visszaküldési címkén (a vonalkód fölött) lévő nyomkövetési számot az átvétel egyeztetéséhez.
5. Szóbeli megerősítést kap a csomagfelvételi ütemtervről. Ha a futár nem érkezik meg a csomagfelvételért, hívja a Quantium Solutions telefonszámát alternatív megoldásokért.
6. A Quantium Solutionsszal folytatott csomagfelvétel-egyeztetéskor továbbítsa a megerősítést a [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) felé az alábbi sablonnal:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium Solutions.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Ha problémába ütközik, küldjön e-mailt a Data Box Operations Asia számára a következő címre: [adbo@microsoft.com](mailto:adbo@microsoft.com). Az e-mailben írja meg a tapasztalt probléma/problémák részleteit, a tárgysorban pedig adja meg a feladat nevét.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 

::: zone-end

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the **Prepare to Ship** page of the Data Box local web UI). The tracking number is available after the **Prepare to ship** step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[Önállóan felügyelt](#tab/in-selfmanaged)

Ha Data Boxt használ az USA kormányában, Japánban, Szingapúrban, Koreában, Indiában, Dél-Afrikában, az Egyesült Királyságban, Nyugat-Európában vagy Ausztráliában, és a rendelés létrehozásakor kiválasztotta az önfelügyelt szállítást, kövesse az alábbi utasításokat.

1. Jegyezze fel a hitelesítési kódot (Authorization code), amely a Data Box helyi webes felhasználói felületének **„Prepare to Ship” (A szállítás előkészítése)** lapján jelenik meg a lépés sikeres befejeződése után.
2. Kapcsolja ki az eszközt, és távolítsa el a kábeleket. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljánál.
3. Az alábbi sablon használatával küldjön e-mailt az Azure Data Box üzemeltetési csapatának, ha készen áll az eszköz visszaküldésére.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

::: zone-end

---

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

