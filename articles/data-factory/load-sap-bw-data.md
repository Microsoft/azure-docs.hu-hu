---
title: Adatok betöltése az SAP Business Warehouse-ból
description: Adatok másolása Azure Data Factory használatával az SAP Business Warehouse-ból (BW)
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 3dabb6d5df0a74cc7ae2fb8b381ad9e0dfe04e63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370699"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Adatok másolása az SAP Business Warehouse-ból Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt mutatja be, hogyan használhatók a Azure Data Factory az adatok az SAP Business Warehouse-ból (BW) az Open hub-on keresztül történő másolásához Azure Data Lake Storage Gen2. Az adatmásoláshoz hasonló eljárás használható más [támogatott fogadó adattárakba](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Az adatok SAP BWból történő másolásával kapcsolatos általános információkért, SAP BW beleértve a nyitott hub-integrációt és a különbözeti folyamatokat, lásd: [adatok másolása az SAP Business Warehouse-ból az Open hub használatával a Azure Data Factory segítségével](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Előfeltételek

- **Azure Data Factory**: Ha még nem rendelkezik ilyennel, kövesse az [adatelőállító létrehozásához](quickstart-create-data-factory-portal.md#create-a-data-factory)szükséges lépéseket.

- **SAP BW nyitott hub-célhely (OHD) az "adatbázis-tábla" típussal**: hozzon létre egy OHD, vagy annak ellenőrzéséhez, hogy a OHD megfelelően van-e konfigurálva Data Factory-integrációhoz, tekintse meg a jelen cikk [SAP BW Open hub Destination](#sap-bw-open-hub-destination-configurations) configurations című szakaszát.

- **A SAP BW felhasználónak a következő engedélyekkel kell rendelkeznie**:

  - A Remote Function calls (RFC) és a SAP BW engedélyezése.
  - A **S_SDSAUTH** engedélyezési objektum "végrehajtás" tevékenységének engedélyei.

- **Egy [saját üzemeltetésű integrációs modul (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) a 3,0-es SAP .net-összekötővel**. Kövesse ezeket a telepítési lépéseket:

  1. Telepítse és regisztrálja a saját üzemeltetésű Integration Runtime 3,13-es vagy újabb verzióját. (Ezt a folyamatot a cikk későbbi részében ismertetjük.)

  2. Töltse le a [64 bites SAP-összekötőt Microsoft .NET 3,0-](https://support.sap.com/en/product/connectors/msnet.html) es verzióra az SAP webhelyéről, és telepítse ugyanarra a számítógépre, mint a saját üzemeltetésű IR-t. A telepítés során győződjön meg arról, hogy a telepítési **lépések választhatók** párbeszédpanelen a **szerelvények telepítése a GAC** -ba lehetőséget választotta, ahogy az alábbi képen látható:

     ![Az SAP .NET-összekötő beállítása párbeszédpanel](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Teljes másolat készítése SAP BW Open hub-ból

Az Azure Portalon lépjen az adat-előállítóra. Válassza a **létrehozás & a figyelő** lehetőséget a Data Factory felhasználói felület külön lapon való megnyitásához.

1. Az **első lépések** lapon válassza a **Adatok másolása** lehetőséget a adatok másolása eszköz megnyitásához.

2. A **Tulajdonságok** lapon adja meg a **feladat nevét**, majd kattintson a **tovább** gombra.

3. A **forrás adattár** lapon válassza az **+ új kapcsolatok létrehozása** lehetőséget. Válassza ki **SAP BW az Open hub** elemet az összekötő-katalógusból, majd válassza a **Folytatás** lehetőséget. Az összekötők szűréséhez beírhatja az **SAP** kifejezést a keresőmezőbe.

4. Az **SAP BW megnyitása a hub-kapcsolatban** lapon kövesse az alábbi lépéseket az új kapcsolatok létrehozásához.

   ![SAP BW Open hub-társított szolgáltatás oldalának létrehozása](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. A **Csatlakozás integrációs modul használatával** listában válasszon ki egy meglévő, saját üzemeltetésű IR-t. Vagy ha még nem rendelkezik ilyennel, hozzon létre egyet.

      Új, saját üzemeltetésű IR létrehozásához válassza az **+ új** lehetőséget, majd válassza a **saját** üzemeltetésű lehetőséget. Adjon meg egy **nevet**, majd kattintson a **tovább** gombra. Válassza az **expressz telepítés** lehetőséget az aktuális számítógépen való telepítéshez, vagy kövesse a megadott **manuális telepítési** lépéseket.

      Az [Előfeltételek](#prerequisites)között említettek szerint győződjön meg arról, hogy a Microsoft .net 3,0-hez készült SAP-összekötő ugyanarra a számítógépre van telepítve, amelyen a saját üzemeltetésű IR fut.

   2. Töltse ki a SAP BW **kiszolgáló nevét**, **a rendszer számát**, az **ügyfél-azonosítót,** a **nyelvet** (ha nem az **en**), a **felhasználónevet** és a **jelszót**.

   3. Válassza a **Kapcsolódás tesztelése** lehetőséget a beállítások ellenőrzéséhez, majd válassza a **Befejezés** lehetőséget.

   4. Létrejön egy új kapcsolatok. Kattintson a **Tovább** gombra.

5. A **hub-célhelyek kiválasztása** lapon tallózzon a SAP BWban elérhető nyitott hub-célhelyek között. Válassza ki a OHD az adatok másolásához, majd kattintson a **tovább** gombra.

   ![Válassza ki SAP BW hub-céltábla megnyitása](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Ha szüksége van rá, válasszon szűrőt. Ha a OHD csak egyetlen adatátviteli folyamatból (DTP) származó, egyetlen kérelem-AZONOSÍTÓval rendelkező adatokból áll, vagy biztos abban, hogy a DTP elkészült, és az adatok másolását szeretné végrehajtani, törölje a jelet a **legutóbbi kérelem kizárása** jelölőnégyzetből.

   További információ ezekről a beállításokról a jelen cikk [SAP BW Open hub-cél konfigurációk](#sap-bw-open-hub-destination-configurations) című szakaszában olvasható. Válassza a **validate (ellenőrzés** ) lehetőséget, hogy duplán ellenőrizze, milyen adatforrások lesznek visszaadva. Ezután kattintson a **Tovább** gombra.

   ![SAP BW nyitott hub-szűrő konfigurálása](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. A **cél adattár** lapon válassza az **+ új kapcsolatok létrehozása**  >  **Azure Data Lake Storage Gen2**  >  **Folytatás** lehetőséget.

8. Az **Azure Data Lake Storage kapcsolódásának meghatározása** lapon kövesse az alábbi lépéseket a kapcsolatok létrehozásához.

   ![ADLS Gen2 társított szolgáltatás oldalának létrehozása](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Válassza ki a Data Lake Storage Gen2-kompatibilis fiókot a **név** legördülő listából.
   2. Kattintson a **Befejezés** gombra a kapcsolat létrehozásához. Ezután kattintson a **Tovább** gombra.

9. A **kimeneti fájl vagy mappa kiválasztása** lapon adja meg a **copyfromopenhub** a kimeneti mappa neveként. Ezután kattintson a **Tovább** gombra.

   ![Kimeneti mappa kiválasztása lap](media/load-sap-bw-data/choose-output-folder.png)

10. A **fájlformátum beállítása** lapon kattintson a **tovább** gombra az alapértelmezett beállítások használatához.

    ![Fogadó formátum oldalának meghatározása](media/load-sap-bw-data/specify-sink-format.png)

11. A **Beállítások** lapon bontsa ki a **teljesítmény beállításai** elemet. Adjon meg egy értéket a **másolási párhuzamosság mértékének** (például 5) a párhuzamos SAP BW betöltéséhez. Ezután kattintson a **Tovább** gombra.

    ![Másolási beállítások konfigurálása](media/load-sap-bw-data/configure-copy-settings.png)

12. Az **Összefoglalás** lapon tekintse át a beállításokat. Ezután kattintson a **Tovább** gombra.

13. A folyamat figyeléséhez az **üzembe helyezés** lapon válassza a **figyelő** lehetőséget.

    ![Üzembe helyezés lap](media/load-sap-bw-data/deployment.png)

14. Figyelje meg, hogy a lap bal oldalán található **figyelés** lap automatikusan ki van választva. A **műveletek** oszlop hivatkozásokat tartalmaz a tevékenység-futtatási részletek megtekintéséhez és a folyamat újrafuttatásához.

    ![Folyamat figyelési nézete](media/load-sap-bw-data/pipeline-monitoring.png)

15. A folyamat futtatásához társított tevékenység-futtatások megtekintéséhez válassza a **műveletek** oszlop **Megtekintés tevékenység** futtatása elemét. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Ha vissza szeretne váltani a folyamat – Futtatás nézetre, válassza a felül található **folyamatok** hivatkozást. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Tevékenység-figyelési képernyő](media/load-sap-bw-data/activity-monitoring.png)

16. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a **részletek** hivatkozást, amely a tevékenység-figyelés nézetben a **műveletek** alá tartozó szemüveg ikon. A rendelkezésre álló részletek közé tartozik a forrásról a fogadóba másolt adatmennyiség, az adatok átviteli sebessége, a végrehajtási lépések és időtartam és a felhasznált konfigurációk.

    ![Tevékenységek figyelésének részletei](media/load-sap-bw-data/activity-monitoring-details.png)

17. A **kérelmek maximális azonosítójának** megtekintéséhez lépjen vissza a tevékenység-figyelés nézetre, és válassza a **kimenet** lehetőséget a **műveletek** területen.

    ![Tevékenység kimeneti képernyője](media/load-sap-bw-data/activity-output.png)

    ![Tevékenység kimenetének részletei nézet](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Növekményes másolás SAP BW Open hub-ból

> [!TIP]
> Tekintse meg a [SAP BW nyitott hub-összekötő különbözeti folyamatát](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) , amelyből megtudhatja, hogyan másolja a SAP BW Open hub-összekötőt a Data Factory a növekményes adatok SAP BW Ez a cikk az alapszintű összekötő konfigurációjának megismeréséhez is segítséget nyújt.

Most folytassa a növekményes másolás konfigurálását SAP BW Open hub-ból.

A növekményes másolás a **kérelem azonosítója** alapján "nagy vízjel" mechanizmust használ. Ez az azonosító automatikusan jön létre SAP BW Open hub-célhelyen a DTP használatával. A következő ábra a munkafolyamatot mutatja be:

![Növekményes másolás munkafolyamat-folyamatábrája](media/load-sap-bw-data/incremental-copy-workflow.png)

Az adatok előállítójának első **lépései** oldalon válassza a **folyamat létrehozása sablonból** lehetőséget a beépített sablon használatához.

1. A **SAP BW** megkeresésével megkeresheti és kiválaszthatja a **növekményes másolást SAP BWról Azure Data Lake Storage Gen2** sablonra. Ez a sablon az Azure Data Lake Storage Gen2ba másolja az adatfájlokat. Hasonló munkafolyamatot is használhat más fogadó típusokba való másoláshoz.

2. A sablon főoldalán válassza ki vagy hozza létre a következő három kapcsolatot, majd válassza a **sablon használata** lehetőséget az ablak jobb alsó sarkában.

   - **Azure Blob Storage**: ebben az útmutatóban az Azure Blob Storage-t használjuk a felső vízjel tárolására, amely a *maximálisan másolt kérelmek azonosítója*.
   - **SAP BW Open hub**: Ez a forrás az adatok másolásához. A részletes konfigurációról az előző teljes másolási útmutatóban olvashat.
   - **Azure Data Lake Storage Gen2**: ez az a fogadó, amelybe az Adatmásolás történik. A részletes konfigurációról az előző teljes másolási útmutatóban olvashat.

   ![Növekményes másolás SAP BW sablonból](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Ez a sablon létrehoz egy folyamatot a következő három tevékenységgel, és sikerrel járt: *lookup*, *adatok másolása* és *web*.

   Lépjen a folyamat **paramétereinek** lapra. Megjelenik az összes szükséges konfiguráció.

   ![Növekményes másolás SAP BW konfigurációból](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Itt adhatja meg, hogy az adatok honnan másolhatók.

   - **Data_Destination_Container**: az Adatmásolás céljának Azure Data Lake Storage Gen2 tárolójának megadásához. Ha a tároló nem létezik, az Data Factory másolási tevékenység a végrehajtás során létrehoz egyet.
  
   - **Data_Destination_Directory**: Itt adhatja meg a mappa elérési útját a Azure Data Lake Storage Gen2 tárolóban az adatmásoláshoz. Ha az elérési út nem létezik, az Data Factory másolási tevékenység létrehoz egy elérési utat a végrehajtás során.
  
   - **HighWatermarkBlobContainer**: határozza meg a tárolót, amely a nagy-vízjel értéket tárolja.

   - **HighWatermarkBlobDirectory**: Itt adhatja meg a mappa elérési útját a tárolóban a magas vízjel értékének tárolásához.

   - **HighWatermarkBlobName**: adja meg a blob nevét, hogy a felső küszöbértéket tárolja, például: `requestIdCache.txt` . A blob Storage-ban lépjen a HighWatermarkBlobContainer + HighWatermarkBlobDirectory + HighWatermarkBlobName megfelelő elérési útjára, például: *Container/Path/requestIdCache.txt*. Hozzon létre egy blobot 0 tartalommal.

      ![Blob tartalma](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: ebben a sablonban a webtevékenység használatával hívja meg a Azure Logic Appst, hogy a blob Storage-ban állítsa be a nagy vízjel értékét. Vagy a Azure SQL Database használatával is tárolhatja. A tárolt eljárási tevékenység használatával frissítse az értéket.

      Először létre kell hoznia egy logikai alkalmazást, ahogy az alábbi képen látható. Ezután illessze be a **http post URL-címét**.

      ![Logic app-konfiguráció](media/load-sap-bw-data/logic-app-config.png)

      1. Nyissa meg az Azure Portalt. Válasszon ki egy új **Logic apps** szolgáltatást. Válassza a **+ üres logikai alkalmazás** lehetőséget, hogy megnyissa **Logic apps Designer** alkalmazást.

      2. Hozzon létre egy triggert, **Ha HTTP-kérés érkezik**. A HTTP-kérés törzsét a következőképpen adhatja meg:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Adja hozzá a **blob létrehozása** műveletet. A **mappa elérési útja** és a **blob neve mezőben** adja meg a korábban a *HighWatermarkBlobContainer + HighWatermarkBlobDirectory* és a *HighWatermarkBlobName* szolgáltatásban konfigurált értékeket.

      4. Kattintson a **Mentés** gombra. Ezután másolja át a **http post URL-cím** értékét, amelyet a Data Factory-folyamatban használni szeretne.

4. Miután megadta a Data Factory folyamat paramétereit,   >  a Futtatás ellenőrzéséhez kattintson a **Befejezés** gombra, hogy érvényesítse a konfigurációt. Vagy válassza a **Közzététel** lehetőséget az összes módosítás közzétételéhez, majd válassza az **trigger hozzáadása** parancsot a Futtatás végrehajtásához.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW nyitott hub-cél konfigurációi

Ez a szakasz a SAP BW oldal konfigurációját mutatja be, hogy az SAP BW Open hub-összekötőt használja Data Factory az adatmásoláshoz.

### <a name="configure-delta-extraction-in-sap-bw"></a>A különbözet kinyerésének konfigurálása SAP BW

Ha a korábbi másolási és növekményes másolási, vagy csak növekményes másolásra van szüksége, állítsa be SAP BW a különbözeti kinyerést.

1. Hozza létre az Open hub célhelyét. A OHD az SAP Transaction RSA1 hozhatja létre, amely automatikusan létrehozza a szükséges átalakítási és adatátviteli folyamatot. Használja a következő beállításokat:

   - **Objektumtípus**: bármilyen objektumtípust használhat. Itt a **InfoCube** -t használjuk példaként.
   - **Cél típusa**: válassza az **adatbázis tábla** lehetőséget.
   - **A tábla kulcsa**: válassza a **technikai kulcs** lehetőséget.
   - **Kinyerés**: válassza **az adatok megőrzése és rekordok beszúrása táblázatba** lehetőséget.

   ![SAP BW OHD-különbözet kinyerése párbeszédpanel létrehozása](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![SAP BW OHD delta2 kibontása párbeszédpanel](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Növelheti a párhuzamosan futó SAP-munkafolyamatok számát a DTP esetében:

   ![A képernyőfelvétel a párhuzamos feldolgozás beállításait jeleníti meg, ahol kiválaszthatja a D T P párhuzamos folyamatainak számát.](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Ütemezze a DTP-t a feldolgozási láncokban.

   Egy adatkockához tartozó Delta DTP csak akkor működik, ha a szükséges sorok nincsenek tömörítve. Győződjön meg arról, hogy a BW-kocka tömörítése nem fut, mielőtt a DTP az Open hub-táblára kerül. Ennek a legegyszerűbb módja, ha integrálni szeretné a DTP-t a meglévő folyamat-láncokba. A következő példában a DTP (a OHD) be van helyezve a folyamat láncba az *Igazítás* (Összesítés kumulatív) és az *összeomlás* (kocka tömörítése) lépések között.

   ![SAP BW folyamat folyamatábrájának létrehozása](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Teljes kivonás konfigurálása SAP BW

A különbözeti adatok kinyerése mellett érdemes lehet azonos SAP BW InfoProvider teljes kibontását is használni. Ez általában akkor érvényes, ha teljes másolást szeretne végezni, de nem növekményes, vagy ha újra szeretné [szinkronizálni a különbözeti kinyerést](#resync-delta-extraction).

Ugyanahhoz a OHD nem tartozhat egynél több DTP. Ezért létre kell hoznia egy további OHD a különbözet kinyerése előtt.

![SAP BW OHD teljes létrehozása](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

A teljes terhelés OHD válassza a különböző beállítások lehetőséget, mint a különbözeti kinyeréshez:

- A OHD-ben: állítsa be az **adatok törlésére és a rekordok beszúrására** szolgáló **kibontási** beállítást. Ellenkező esetben a rendszer többször is kinyeri az adatmennyiséget, amikor egy BW-feldolgozási láncban megismétli a DTP-t.

- A DTP: **kibontási mód** beállítása **teljes** értékre. Az automatikusan létrehozott DTP- **t az OHD** létrehozása után azonnal **teljes** értékre kell módosítani, ahogy az a következő képen látható:

   ![A "teljes" kinyeréshez konfigurált SAP BW OHD-párbeszédpanel létrehozása](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- A Data Factory BW Open hub-összekötőben: kapcsolja ki a **legutóbbi kérelem kizárása** beállítást. Ellenkező esetben semmi nem lesz kibontva.

A teljes DTP-t általában manuálisan futtathatja. Vagy létrehozhat egy folyamat láncot a teljes DTP számára. Ez általában egy különálló lánc, amely független a meglévő folyamat-láncoktól. Mindkét esetben *Győződjön meg arról, hogy a DTP a Data Factory másolással való elindítását megelőzően elvégezte a kinyerést*. Ellenkező esetben a rendszer csak a részleges adatfájlokat másolja.

### <a name="run-delta-extraction-the-first-time"></a>A Delta kinyerésének első futtatása

Az első Delta kinyerése technikailag *teljes kinyerés*. Alapértelmezés szerint a SAP BW Open hub-összekötő kizárja az utolsó kérést, amikor az Adatmásolást végzi. Az első különbözeti kinyeréshez az Data Factory másolási tevékenység nem nyer ki adatgyűjtést, amíg egy későbbi DTP nem hoz létre különbözeti adatkérési azonosítót a táblában. Ezt a forgatókönyvet kétféleképpen lehet elkerülni:

- Kapcsolja ki a **legutóbbi kérelem kizárása** beállítást az első különbözet kinyeréséhez. Győződjön meg arról, hogy az első Delta DTP elkészült, mielőtt első alkalommal elindítja a különbözeti kinyerést.
-  A különbözeti kinyerés újraszinkronizálására szolgáló eljárást a következő szakaszban leírtak szerint hajtsa végre.

### <a name="resync-delta-extraction"></a>Különbözet kibontásának újraszinkronizálása

Az alábbi forgatókönyvek SAP BW kockákban lévő adatok módosítását végzik, de a különbözeti DTP nem veszi figyelembe a következőket:

- Szelektív törlés SAP BW (a sorok bármely szűrési feltétel használatával)
- SAP BW kérelmek törlése (hibás kérelmek)

Az SAP Open hub rendeltetése nem egy adatmart által vezérelt adattároló (az összes SAP BW támogatási csomag 2015 óta). Így a OHD lévő adatok módosítása nélkül törölheti az adatait egy adatkockából. Ezután újra kell szinkronizálnia a kocka Data Factory:

1. Teljes kivonást futtathat Data Factoryban (az SAP-ban teljes DTP használatával).
2. Törölje az Open hub tábla összes sorát a Delta DTP-hez.
3. Állítsa be a Delta DTP állapotát a **beolvasáshoz**.

Ezt követően az összes további Delta DTPs és a Data Factory különbözeti kinyerése a várt módon működik.

A Delta DTP állapotának **beolvasásához** a következő lehetőség használatával manuálisan futtathatja a KÜLÖNBÖZETi DTP-t:

*Nincs Adatátvitel; Különbözeti állapot a forrásban: beolvasva*

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg SAP BW Open hub-összekötő támogatásával:

> [!div class="nextstepaction"]
>[SAP Business Warehouse – nyitott hub-összekötő](connector-sap-business-warehouse-open-hub.md)
