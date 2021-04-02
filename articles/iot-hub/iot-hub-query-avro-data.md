---
title: Avro-adatbázis lekérdezése Azure Data Lake Analytics használatával | Microsoft Docs
description: Az üzenettörzs tulajdonságai segítségével átirányíthatja az eszköz telemetria a blob Storage-ba, és lekérdezheti a blob Storage-ba írt Avro formátumát.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 3cfe75edcf338f5248baf396147a5b77803fbfb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97655934"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Avro-adatbázis lekérdezése Azure Data Lake Analytics használatával

Ez a cikk azt ismerteti, hogyan lehet lekérdezni a Avro adatait az Azure-IoT Hub az Azure-szolgáltatások felé irányuló üzenetek hatékony továbbításához. Az [üzenet-útválasztás](iot-hub-devguide-messages-d2c.md) lehetővé teszi az adatszűrést az üzenet tulajdonságai, az üzenettörzs, az eszköz kettős címkék és az eszközök Twin tulajdonságai alapján történő Rich lekérdezések használatával. Az üzenet-útválasztás lekérdezési képességeivel kapcsolatos további tudnivalókért tekintse meg az [üzenet-útválasztási lekérdezés szintaxisáról](iot-hub-devguide-routing-query-syntax.md)szóló cikket.

A kihívás az volt, hogy amikor az Azure IoT Hub az üzeneteket az Azure Blob Storage-ba irányítja, alapértelmezés szerint a IoT Hub Avro formátumban írja be a tartalmat, amely tartalmaz egy üzenettörzs-tulajdonságot és egy üzenet tulajdonságot is. A Avro formátuma nem használatos más végpontokhoz. Bár a Avro formátuma kiválóan használható az adat-és az üzenetek megőrzése érdekében, az Adatlekérdezési feladatnak kell lennie. Az összehasonlításban a JSON-vagy CSV-formátum sokkal egyszerűbb az adatlekérdezéshez. IoT Hub mostantól támogatja az adatírást a blob Storage-ba a JSON-ban, valamint a AVRO.

További információ: az [Azure Storage használata útválasztási végpontként](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).

A nem összehasonlítható Big-adattípusok és-formátumok megoldásához, valamint a probléma megoldásához használja az Adatátalakítási és-méretezési funkciók számos Big-adatmintázatát. Az egyik minta, a "pay per Query", Azure Data Lake Analytics, amely a cikk középpontjában áll. Bár a lekérdezés egyszerűen végrehajtható a Hadoop-ben vagy más megoldásokban, Data Lake Analytics gyakran alkalmasabb ehhez a "pay per Query" megközelítéshez.

A U-SQL-ben a Avro "Extractor". További információ: [U-SQL Avro példa](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Avro-adatbázis lekérdezése és exportálása CSV-fájlba

Ebben a szakaszban lekérdezi a Avro, és exportálja azt egy CSV-fájlba az Azure Blob Storage-ban, de az adattárakban vagy adattárakban is könnyedén elhelyezheti az adattárakat.

1. Állítsa be az Azure IoT Hub az Azure Blob Storage-végpontra történő adatirányításhoz az üzenetek kiválasztásához az üzenet törzsében található tulajdonság használatával.

   ![Az "egyéni végpontok" szakasz](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Az útválasztási szabályok](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   További információ az útvonalakról és az egyéni végpontokról: [IoT hub üzenet-útválasztása](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Győződjön meg arról, hogy az eszközön szerepel a kódolás, a tartalomtípus és a szükséges adatértékek a Tulajdonságok vagy az üzenet törzsében, a termék dokumentációjában hivatkozottak szerint. Ha ezeket az attribútumokat Device Explorerban tekinti meg, ahogy az itt látható, ellenőrizheti, hogy helyesen vannak-e beállítva.

   ![Az Event hub-adatpanel](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Azure Data Lake Store-példány és egy Data Lake Analytics-példány beállítása. Az Azure IoT Hub nem Data Lake Store példányhoz irányítja, de egy Data Lake Analytics példányhoz egy szükséges.

   ![Data Lake Store és Data Lake Analytics példányok](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. Az Data Lake Analyticsban konfigurálja az Azure Blob Storage-t további tárolóként, ugyanazt a blob Storage-tárolót, amelyet az Azure IoT Hub az adatútvonalakat átirányítja.

   ![Az "adatforrások" panel](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Ahogy azt a [U-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)is tárgyalta, négy dll-fájlra van szüksége. Töltse fel ezeket a fájlokat az Data Lake Store-példányban található helyre.

   ![Négy feltöltött DLL fájl](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. A Visual Studióban hozzon létre egy U-SQL-projektet.

   ![U-SQL-projekt létrehozása](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Illessze be a következő parancsfájl tartalmát az újonnan létrehozott fájlba. Módosítsa a három kiemelt szakaszt: a Data Lake Analytics fiókját, a hozzá tartozó DLL-fájl elérési útját és a Storage-fiók helyes elérési útját.

   ![A módosítandó három szakaszt](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   A tényleges U-SQL-szkript egy CSV-fájlba történő egyszerű kimenethez:

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    A következő szkript futtatásához Data Lake Analytics öt percet vett igénybe, amely 10 analitikus egységre és feldolgozott 177-fájlokra korlátozódott. Az eredmény az alábbi képen látható CSV-fájl kimenetében látható:

    ![A CSV-fájl kimenetének eredményei](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![CSV-fájlba konvertált kimenet](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    A JSON elemzéséhez folytassa a 8. lépéssel.

8. A legtöbb IoT-üzenet JSON formátumú. A következő sorok hozzáadásával elemezheti az üzenetet egy JSON-fájlba, amely lehetővé teszi a WHERE záradék hozzáadását, és csak a szükséges adatokat jeleníti meg.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                message["event"] AS msgevent,
                message["object"] AS msgobject,
                message["status"] AS msgstatus,
                message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    A kimenet megjeleníti a parancs egyes elemeinek oszlopát `SELECT` .

    ![Az egyes elemek oszlopait ábrázoló kimenet](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan kérdezheti le a Avro adatait az Azure IoT Hubról az Azure-szolgáltatásokra irányuló üzenetek hatékony továbbításához.

Az IoT Hubt használó teljes körű megoldások részletes ismertetését az [Azure IoT megoldás-gyorsító dokumentációjában](../iot-accelerators/index.yml)találja.

Ha többet szeretne megtudni a IoT Hub-megoldások fejlesztéséről, tekintse meg a [IoT hub fejlesztői útmutatót](iot-hub-devguide.md).

Az IoT Hub üzenet-útválasztásával kapcsolatos további tudnivalókért tekintse meg az [üzenetek küldése és fogadása IoT hub](iot-hub-devguide-messaging.md)használatával című témakört.
