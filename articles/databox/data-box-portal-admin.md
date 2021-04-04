---
title: Azure Data Box kezelése, Azure Data Box Heavy a Azure Portal használatával | Microsoft Docs
description: Ismerteti, hogyan használható a Azure Portal a Azure Data Box és a Azure Data Box Heavy felügyeletéhez.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 46a18cb2b6e1682427d5674be28b240f35b120fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678649"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>A Azure Portal használatával felügyelheti Azure Data Box és Azure Data Box Heavy

Ez a cikk a Azure Data Box és a Azure Data Box Heavy is vonatkozik. Ez a cikk a Azure Data Box eszközön végrehajtható összetett munkafolyamatokat és felügyeleti feladatokat ismerteti. Az Data Box eszközt a Azure Portal vagy a helyi webes felületen keresztül felügyelheti.

A cikk az Azure Portalon végrehajtható feladatokra összpontosít. A Azure Portal segítségével kezelheti a rendeléseket, kezelheti Data Box eszközét, és nyomon követheti a sorrend állapotát a befejezésig.

## <a name="cancel-an-order"></a>Rendelés visszavonása

Előfordulhat, hogy a megrendelés után meg kell szakítania a megrendelést.

Az importálási és exportálási megrendelések esetében a feldolgozás előtt csak a rendelést lehet megszakítani. A megrendelés feldolgozása és a Data Box eszköz előkészítése után nem lehet megszakítani a rendelést.

A rendelés visszavonásához kövesse az alábbi lépéseket.

1.  Lépjen az **Áttekintés > Megszakítás** menüpontra.

    ![Parancs megszakítása egy rendelés áttekintés lapján](media/data-box-portal-admin/portal-admin-cancel-command.png)

2.  Adja meg a rendelés visszavonásának okát.  

    ![Megrendelés megszakítása párbeszédpanel](media/data-box-portal-admin/portal-admin-cancel-order-dbox.png)

3.  Miután visszavonta a rendelést, a portálon a rendelés állapota frissül a **Megszakítva** állapotra.

## <a name="clone-an-order"></a>Rendelés klónozása

A klónozás bizonyos helyzetekben hasznosnak bizonyulhat. Például a Data Box használatával végezheti el az adatok átvitelét. Ahogy egyre több adatok jönnek létre, egy másik Data Box eszközt kell használnia az adatok Azure-ba történő átviteléhez. Ebben az esetben csak a megrendelést klónozással lehet megtekinteni.

Az importálási sorrend klónozásához hajtsa végre az alábbi lépéseket.

1.  Lépjen az **Áttekintés > Klónozás** menüpontra. 

    ![Klónozási parancs egy rendelés áttekintés lapján](media/data-box-portal-admin/portal-admin-clone-command.png)

2.  A rendelés részletei változatlanok maradnak. A rendelés neve az eredeti rendelés neve lesz a *-Klón* utótaggal kiegészítve. Jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy átolvasta az adatvédelmi információkat. Kattintson a **Létrehozás** lehetőségre.

A klón létrehozása néhány percet vesz igénybe, és a portálon megjelenik az új rendelés.


## <a name="delete-order"></a>Rendelés törlése

A rendeléseket azok teljesítését követően esetleg törölni szeretné. A rendelés tartalmazza az Ön személyes adatait, úgymint a nevét, a címét és a kapcsolattartási adatait. A rendelés törlésekor a személyes adatok törölve lesznek.

Csak a teljesített vagy visszavont rendeléseket törölheti. A rendelések törléséhez kövesse az alábbi lépéseket.

1. Lépjen a **Minden erőforrás** menüpontra. Keresse meg a rendelést.

2. Kattintson a törölni kívánt rendelésre, és lépjen az **Áttekintés** felületre. A parancssoron kattintson a **Törlés** elemre.

    ![Parancs törlése az Áttekintés lapon egy rendeléshez](media/data-box-portal-admin/portal-admin-delete-command.png)

3. Ha a rendszer kéri, írja be a rendelés nevét a rendelés törlésének megerősítéséhez. Kattintson a **Törlés** gombra.

## <a name="download-shipping-label"></a>Fuvarlevélcímke letöltése

Előfordulhat, hogy le kell töltenie a szállítási címkét, ha a Data Box E-Ink megjelenítése nem működik, és nem jeleníti meg a visszáru-szállítási címkét. Data Box Heavy nem jelenik meg E-Ink, így ez a munkafolyamat nem vonatkozik a Data Box Heavyra.

A fuvarlevélcímkék letöltéséhez kövesse az alábbi lépéseket.

1.  Lépjen az **Áttekintés > Levélcímke letöltése** menüpontra. Ez a lehetőség csak az eszköz kézbesítését követően érhető el. 

    ![Fuvarlevélcímke letöltése](media/data-box-portal-admin/portal-admin-download-shipping-label.png)

2.  Ezzel letölti a következő fuvarlevélcímkét a visszaküldéshez. Mentse a címkét, és nyomtassa ki. Illessze be a címkét a Clear hüvelybe az eszközön. Győződjön meg arról, hogy a címke jól látható. Távolítsa el a matricákat, amelyek az előző szállítás során kerültek az eszközre.

    ![Példa a fuvarlevélcímkére](media/data-box-portal-admin/portal-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Szállítási cím szerkesztése

Előfordulhat, hogy a rendelés feladását követően módosítania kell a szállítási címet. Ezt csak az eszköz feladásáig teheti meg. Az eszköz feladását követően ez a lehetőség már nem érhető el.

A rendelés szerkesztéséhez kövesse az alábbi lépéseket.

1. Lépjen a **Rendelés részletei > Szállítási cím szerkesztése** menüpontra.

    ![A szállítási címek parancs szerkesztése a Rendelés részletei között](media/data-box-portal-admin/portal-admin-edit-shipping-address-command.png)

2. Szerkesztheti és érvényesítheti a szállítási címet, majd mentheti a változásokat.

    ![Szállítási címek szerkesztése párbeszédpanel](media/data-box-portal-admin/portal-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Értesítési adatok szerkesztése

Előfordulhat, hogy módosítania kell azokat a felhasználókat, akik megkapják a rendelés állapotára vonatkozó e-maileket. Például előfordulhat, hogy valamelyik felhasználót értesíteni kell, ha az eszközt kiszállítják vagy már átvették. Előfordulhat, hogy egy másik felhasználónak is tájékoztatnia kell, amikor az Adatmásolás befejeződött, így ellenőrizhetik, hogy az adatok az Azure Storage-fiókban találhatók-e, mielőtt törölné a forrásból. Ilyen esetekben szerkesztheti az értesítési adatokat.

Az értesítési adatok szerkesztéséhez kövesse az alábbi lépéseket.

1. Lépjen a **Rendelés részletei > Értesítési adatok szerkesztése** menüpontra.

    ![Értesítés részleteinek szerkesztése parancs a sorrend részletei között](media/data-box-portal-admin/portal-admin-edit-notification-details-command.png)

2. Itt szerkesztheti az értesítési adatokat, majd mentheti a változásokat.
 
    ![Értesítés részleteinek szerkesztése párbeszédpanel](media/data-box-portal-admin/portal-admin-edit-notification-details-dbox.png)


## <a name="download-order-history"></a>Rendelés előzményeinek letöltése

A Data Box-rendelés befejezése után az eszközlemezeken található adatok törölve lesznek. Ha az eszközök adatainak törlése befejeződött, a rendelési előzmények letölthetők az Azure Portalon.

A rendelési előzmények letöltéséhez kövesse az alábbi lépéseket.

1. A Data Box-rendelésben lépjen az **Áttekintés** lapra. Ellenőrizze, hogy a rendelés befejeződött-e. Ha a rendelés végrehajtása és az eszközök adatainak törlése befejeződött, lépjen a **Rendelés részletei** területre. Elérhető a **Rendelés előzményeinek letöltése** lehetőség.

    ![Rendelés előzményeinek letöltése](media/data-box-portal-admin/portal-admin-download-order-history.png)

2. Kattintson a **Rendelés előzményeinek letöltése** elemre. A letöltött előzmények tartalmazzák a szállítói nyomkövetési naplók rekordját. Két, Data Box Heavy-eszközön található csomópontnak megfelelő log-készlet lesz. A napló aljára görgetve a következő hivatkozásokat láthatja:
    
   - **Naplók másolása** – a Data Boxról az Azure Storage-fiókba (importálási sorrendbe) vagy a Storage-fiókból a Data boxba (exportálási sorrendbe) másolt fájlok listája.
   - **Naplók** – információt tartalmaz arról, hogyan lehet bekapcsolni a Data Box és hozzáférni a megosztásokhoz, ha az Data Box az Azure-adatközponton kívül esik.
   - **Importálási sorrendben lévő AJ-fájlok** – a fájlok (más néven a fájl jegyzékfájlja) listáját is letöltheti a **szállításra való előkészítés** során, valamint fájlneveket, fájlméreteket és a fájl ellenőrzőösszegét.
   - **Részletes naplók az exportálási sorrendben** – megtalálhatók a fájlnevekkel, fájlméretekkel és ellenőrzőösszeg-számítással rendelkező fájlok listája, amikor az adatok az Azure Storage-fiókokból a Data boxba másolódnak.

   Íme egy példa az importálási sorrendből származó megrendelési előzményekre.

    ```output
    -------------------------------
    Microsoft Data Box Order Report
    -------------------------------
    Name                                               : DataBoxTestOrder                              
    StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
    DeviceType                                         : DataBox                                           
    -------------------
    Data Box Activities
    -------------------
    Time(UTC)                 | Activity                       | Status          | Description  
    
    10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
    11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
    11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
    11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
    11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
    11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
    11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
    11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
    11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
    11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
    11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
    11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
    11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
    11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
    11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
    11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
    1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
    1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
    1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
    1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
    1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
    1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
    1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
    1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
    1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
    1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
    1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
    1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
    1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
    1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
    1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
    1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
    1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
    1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
    2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
    2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
    2/4/2019 8:01:10 PM       | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

    ------------------
    Data Box Log Links
    ------------------

    Account Name         : Gus                                                       
    Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
    Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
    BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
    ```

    Ezután a tárfiókba lépve megtekintheti a másolási naplókat.

   ![A Storage-fiók másolási naplói](media/data-box-portal-admin/portal-admin-storage-account-copy-logs.png)

   Megtekintheti a felügyeleti naplók láncát is, amely tartalmazza a naplókat és az ANYAGJEGYZÉK-fájlokat.

   ![Tárolási fiókhoz tartozó felügyeleti naplók lánca](media/data-box-portal-admin/portal-admin-storage-account-chain-of-custody-logs.png)

## <a name="view-order-status"></a>Rendelés állapotának megtekintése

Amikor az eszköz állapota megváltozik a portálon, e-mailben értesítjük.

### <a name="statuses-for-import-order"></a>Importálási sorrendi állapotok

Az importálási rendelés állapotait itt találja.

|Rendelés állapota |Leírás |
|---------|---------|
|Megrendelve     | A rendelés sikeresen fel lett adva. <br>Ha az eszköz elérhető, a Microsoft kiválaszt egy eszközt a szállításhoz, és előkészíti. <br> Ha az eszköz nem érhető el azonnal, a rendszer feldolgozza a rendelést, amikor az eszköz elérhetővé válik. A rendelés feldolgozása néhány napot, de akár hónapokat is igénybe vehet. Ha a rendelés nem teljesíthető 90 napon belül, a rendszer megszakítja a sorrendet, és értesítést kap.         |
|Feldolgozva     | A rendelés feldolgozása befejeződött. Az eszköz szállításra való előkészítése a rendelésnek megfelelően megtörténik az adatközpontban.         |
|Feladva     | A rendelés fel lett adva. A portálon a rendelésnél feltüntetett követési azonosítóval követheti nyomon a szállítást.        |
|Kézbesítve     | A rendelés ki lett kézbesítve a rendelésben megadott címre.        |
|Szállítás alatt     |A visszaküldött csomagot a szállítmányozó átvette és ellenőrizte.         |
|Megérkezett     | Az eszközt átvették és ellenőrizték az Azure-adatközpontban. <br> Miután a szállítmányt megvizsgálták, elkezdődik az eszköz feltöltése.      |
|Adatok másolása     | Az adatok másolása folyamatban van. Kövesse nyomon a rendelés másolásának folyamatát az Azure Portalon. <br> Várjon, amíg az adatok másolása befejeződik. |
|Befejeződött       |A rendelés sikeresen teljesítve lett.<br> Ellenőrizze, hogy az adatok elérhetők-e az Azure-ban, mielőtt törölné a helyszíni adatokat a kiszolgálókról.         |
|Befejeződött, hibákkal| Az adatok másolása befejeződött, azonban hibák jelentkeztek a másolás során. <br> Tekintse át a másolási naplókat az Azure Portalon megadott útvonalon. [A másolási naplókra vonatkozó példákat a feltöltés hibával fejeződött](./data-box-logs.md#upload-completed-with-errors)be.   |
|Figyelmeztetésekkel fejeződött be| Az Adatmásolás befejeződött, de az adatai módosultak. Az adatfájl-vagy blob-nevek módosításával kijavított nem kritikus blob-vagy fájlnév-hibák történtek. <br> Tekintse át a másolási naplókat az Azure Portalon megadott útvonalon. Jegyezze fel az adataiban történt módosításokat. Tekintse át [a naplók másolására vonatkozó példákat, ha a feltöltés figyelmeztetésekkel fejeződött](./data-box-logs.md#upload-completed-with-warnings)be.   |
|Megszakítva            |A rendelés vissza lett vonva. <br> Vagy megszakította a rendelést, vagy a szolgáltatás hiba után megszakította a sorrendet. Ha a rendelés nem teljesíthető 90 napon belül, a rendelést is megszakították, és értesítést kap.     |
|A fölöslegessé vált elemek eltávolítása | Az eszközlemezeken található összes adat törölve lesz. Az eszköz adatainak törlése akkor fejeződik be, amikor a rendelés előzményei letölthetővé válnak az Azure Portalon.|

### <a name="statuses-for-export-order"></a>Az exportálási sorrend állapota

Az exportálási rendelés állapota.

|Rendelés állapota |Leírás |
|---------|---------|
|Megrendelve     | Az exportálási sorrend sikeresen elhelyezve. <br>Ha az eszköz elérhető, a Microsoft kiválaszt egy eszközt a szállításhoz, és előkészíti. <br> Ha az eszköz nem érhető el azonnal, a rendelés akkor lesz feldolgozva, amikor az eszköz elérhetővé válik. A rendelés feldolgozása néhány napot, de akár hónapokat is igénybe vehet. Ha a rendelés nem hajtható végre 90 napon belül, akkor a rendszer visszavonja azt, és e-mailben értesíti Önt.         |
|Megszakítva            |A rendelés vissza lett vonva. <br> Vagy megszakította a rendelést (csak a rendelés feldolgozását megelőzően lehet megszakítani), vagy hibát észlelt, és a szolgáltatás megszakította a rendelést. Ha a rendelés nem hajtható végre 90 napon belül, akkor a rendszer visszavonja azt, és e-mailben értesíti Önt.     |
|Feldolgozva     | A rendelés feldolgozása befejeződött. A megrendelés alapján az eszköz készen áll az adatközpontban tárolt adatmásolásra. Létrejönnek az eszközök megosztása.         |
|Az Adatmásolás folyamatban van     | Folyamatban van a megadott Azure Storage-fiókokból származó adatok másolása az eszközre. Kövesse nyomon a rendelés másolásának folyamatát az Azure Portalon. <br> Várjon, amíg az adatok másolása befejeződik. |
|A másolás befejeződött     | A megadott Azure Storage-fiókoktól az eszközre történő Adatmásolás befejeződött. Részletes naplófájl (ha a beállítás engedélyezve van a sorrendben), és a rendszer egy másolási naplót hoz létre a Storage-fiókban. A részletes napló tartalmazza az eszközre másolt összes fájl (név, elérési út, számítási ellenőrzőösszeg) adatait. A másolási napló tartalmazza a másolási folyamat összegzését, beleértve azoknak a fájloknak a listáját, amelyek hibák miatt nem másolhatók. <br> A Storage-fiók adatmennyisége a következőképpen marad. |
|A másolás hibákkal fejeződött be| Az adatok másolása befejeződött, azonban hibák jelentkeztek a másolás során. <br> Tekintse át az Azure Storage-fiók másolási naplóit a Azure Portal megadott elérési úttal. Tekintse át [a naplók másolásának példáit, ha a letöltés hibákkal fejeződött](./data-box-logs.md#upload-completed-with-errors)be.   |
|A másolás figyelmeztetésekkel fejeződött be| Az Azure Storage-fiókból történő Adatmásolás befejeződött, de az adatok nem kritikus hibákkal rendelkeztek. <br> Tekintse át a másolási naplókat az Azure Portalon megadott útvonalon. Jegyezze fel a nem kritikus hibákat. Tekintse át [a másolási naplók példáit, ha a letöltés figyelmeztetésekkel fejeződött](./data-box-logs.md#upload-completed-with-warnings)be.   |
|A másolás hibákkal meghiúsult| Az Azure Storage-fiókból történő Adatmásolás meghiúsult, és a rendelés leáll. Az eszköz nem lesz elküldve. <br> Tekintse át az Azure Storage-fiók másolási naplóit a Azure Portal megadott elérési úttal. [Ha a letöltés során hiba történt, tekintse meg a naplók másolására vonatkozó példákat](./data-box-logs.md#upload-completed-with-errors).   |
|Feladva     |A rendelés fel lett adva. A portálon a rendelésnél feltüntetett követési azonosítóval követheti nyomon a szállítást.        |
|Kézbesítve     |A rendelés ki lett kézbesítve a rendelésben megadott címre.        |
|Szállítás alatt     |A visszaküldött csomagot a szállítmányozó átvette és ellenőrizte.         |
|Megérkezett     | Az eszközt átvették és ellenőrizték az Azure-adatközpontban. <br> A rendszer ellenőrzi a szállítást.      |
|Befejeződött           |A megrendelés befejeződött.     |
|A fölöslegessé vált elemek eltávolítása | Az eszközlemezeken található összes adat törölve lesz. Az eszköz adatainak törlése akkor fejeződik be, amikor a rendelés előzményei letölthetővé válnak az Azure Portalon.|

> [!NOTE]
> Ha a másolási feladatot az Azure Storage-fiókokból származó adatoknak a Data Boxba való betöltéséhez hibák vagy figyelmeztetések egészítik ki, az eszköz továbbra is hajókon marad. A rendszer csak a másolási hiba esetén leállítja a sorrendet, és az eszköz kiszállítása nem történik meg.


Ha önfelügyelt szállítást használ, a másolás befejeződése után és az eszköz fogadása előtt a következő állapotok jelennek meg (az előző táblázatban említettek helyett):

|Rendelés állapota |Leírás |
|---------|---------|
|Készen áll az Azure-adatközpontban való felvételre      |Az eszköz készen áll az Azure-adatközpontba való felvételre.        |
|Szállítás alatt    |Felvette az eszközt.         |
|Készen áll az Azure-adatközpontban való fogadásra     |Az eszköz készen áll az Azure-adatközpontban való fogadásra.        |
|Megérkezett     |Az eszköz az Azure-adatközpontban érkezett.      |





## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan lehet [elhárítani a Data Box és Data Box Heavy problémákat](data-box-troubleshoot.md).