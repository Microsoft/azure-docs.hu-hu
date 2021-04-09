---
title: Az Azure Event Hubs rögzített adatok beolvasása egy Python-alkalmazásból (legújabb)
description: Ebből a cikkből megtudhatja, hogyan írhat Python-kódot az Event hub számára elküldett adatok rögzítéséhez, és beolvashatja az Azure Storage-fiókból a rögzített események adatait.
ms.topic: quickstart
ms.date: 01/04/2021
ms.openlocfilehash: acc2ce04add5fd837e9edc789e9616a9f04fb4b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97883197"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub"></a>Az Azure Storage-ban tárolt Event Hubs-információk rögzítése és olvasása a Python használatával (Azure-eventhub)

Beállíthat egy Event hub-t úgy, hogy az Event hub-nak elküldett, Azure Storage-fiókba vagy Azure Data Lake Storage 1. vagy 2. generációs adattárolóba beérkező adatkészletek rögzítve legyenek. Ez a cikk bemutatja, hogyan írhat Python-kódokat események küldéséhez az Event hub-ba, és beolvashatja a rögzített adatok **Azure Blob Storage**-ból való beolvasását. További információ erről a szolgáltatásról: [Event Hubs rögzítési funkciók áttekintése](event-hubs-capture-overview.md).

Ez a rövid útmutató az [Azure PYTHON SDK](https://azure.microsoft.com/develop/python/) használatával mutatja be a rögzítési funkciót. A *Sender.py* alkalmazás a szimulált környezeti telemetria JSON formátumban küldi az Event hubokba. Az Event hub úgy van konfigurálva, hogy a rögzítési funkció használatával írja ezeket az adattárakba a blob Storage-ba. A *capturereader.py* alkalmazás beolvassa ezeket a blobokat, és létrehoz egy hozzáfűzési fájlt az egyes eszközökhöz. Az alkalmazás ezután a CSV-fájlokba írja az adatot.

Ebben a rövid útmutatóban a következőket hajtja végre: 

> [!div class="checklist"]
> * Hozzon létre egy Azure Blob Storage-fiókot és-tárolót a Azure Portal.
> * Hozzon létre egy Event Hubs névteret a Azure Portal használatával.
> * Hozzon létre egy Event hub-t a rögzítési funkcióval, és kapcsolja össze a Storage-fiókkal.
> * Adatküldés az Event hub-ba egy Python-szkript használatával.
> * Fájlok olvasása és feldolgozása Event Hubs rögzítésből egy másik Python-parancsfájl használatával.

## <a name="prerequisites"></a>Előfeltételek

- A Python és a PIP, valamint a következő csomagok vannak telepítve. A cikkben szereplő kód ezen verziók alapján lett tesztelve. 
    - Python 3.7
    - Azure-eventhub 5.2.0
    - Azure-Storage – blob 12.6.0
    - Avro – python3 1.10.1
- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.  
- Aktív Event Hubs névtér és Event hub.
[Hozzon létre egy Event Hubs névteret és egy Event hubot a névtérben](event-hubs-create.md). Jegyezze fel a Event Hubs névtér nevét, az Event hub nevét és a névtér elsődleges elérési kulcsát. A hozzáférési kulcs beszerzéséhez tekintse meg [Event Hubs kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)című témakört. Az alapértelmezett kulcsnév a *RootManageSharedAccessKey*. Ebben a rövid útmutatóban csak az elsődleges kulcsra van szükség. Nincs szüksége a kapcsolatok karakterláncára.  
- Egy Azure Storage-fiók, egy blob-tároló a Storage-fiókban, valamint egy kapcsolódási sztring a Storage-fiókhoz. Ha nem rendelkezik ezekkel az elemekkel, tegye a következőket:  
    1. [Azure-tárfiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)  
    1. [BLOB-tároló létrehozása a Storage-fiókban](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [A Storage-fiókhoz tartozó kapcsolódási karakterlánc lekérése](../storage/common/storage-configure-connection-string.md)

    Ügyeljen arra, hogy a rövid útmutató későbbi használatához a kapcsolódási karakterláncot és a tároló nevét jegyezze fel.  
- Az Event hub rögzítési funkciójának engedélyezése. Ehhez kövesse a [Event Hubs rögzítés engedélyezése a Azure Portal használatával](event-hubs-capture-enable-through-portal.md)című témakör utasításait. Válassza ki a Storage-fiókot és az előző lépésben létrehozott BLOB-tárolót. Az Event hub létrehozásakor is engedélyezheti a szolgáltatást.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Hozzon létre egy Python-szkriptet az események az Event hub-ba való küldéséhez
Ebben a szakaszban egy Python-szkriptet hoz létre, amely 200 eseményt küld (10 eszköz * 20 eseményt) az Event hub-ba. Ezek az események JSON formátumban eljuttatott környezeti beolvasási példák. 

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code][Visual Studio Code]-ot.
2. Hozzon létre egy *Sender.py* nevű szkriptet. 
3. Illessze be a következő kódot a *Sender.py*. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Cserélje le a következő értékeket a parancsfájlokban:  
    * Cserélje le a helyére `EVENT HUBS NAMESPACE CONNECTION STRING` a Event Hubs névtérhez tartozó kapcsolatok karakterláncát.  
    * Cserélje le az helyére `EVENT HUB NAME` az Event hub nevét.  
5. Futtassa a szkriptet az események az Event hubhoz való küldéséhez.  
6. A Azure Portalon ellenőrizheti, hogy az Event hub fogadta-e az üzeneteket. Váltson az **üzenetek** nézetre a **metrikák** szakaszban. Frissítse a lapot a diagram frissítéséhez. Eltarthat néhány másodpercig, amíg az oldal megjeleníti az üzenetek fogadását. 

    [![Annak ellenőrzése, hogy az Event hub fogadta-e az üzeneteket](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Python-szkript létrehozása a rögzítési fájlok olvasásához
Ebben a példában a rögzített adattárolók tárolása az Azure Blob Storage-ban történik. Az ebben a szakaszban található szkript beolvassa a rögzített adatfájlokat az Azure Storage-fiókjából, és CSV-fájlokat hoz létre, amelyekkel egyszerűen megnyithatja és megtekintheti azokat. Az alkalmazás aktuális munkakönyvtárában 10 fájl jelenik meg. Ezek a fájlok a 10 eszközre vonatkozó környezeti beolvasásokat fogják tartalmazni. 

1. A Python-szerkesztőben hozzon létre egy *capturereader.py* nevű szkriptet. Ez a szkript beolvassa a rögzített fájlokat, és létrehoz egy fájlt minden eszköz számára, hogy csak az adott eszközre írja az adatokat.
2. Illessze be a következő kódot a *capturereader.py*. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Cserélje le az értékét `AZURE STORAGE CONNECTION STRING` Az Azure Storage-fiókhoz tartozó kapcsolatok karakterláncára. Az ebben a rövid útmutatóban létrehozott tároló neve *rögzítve* lesz. Ha más nevet használt a tárolóhoz, a *rögzítés* helyére írja be a Storage-fiókban található tároló nevét. 

## <a name="run-the-scripts"></a>Parancsfájlok futtatása
1. Nyisson meg egy parancssort, amely a Python elérési útjában található, majd futtassa ezeket a parancsokat a Python előfeltételként szükséges csomagok telepítéséhez:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```

    > [!NOTE]
    > A cikkben szereplő kód ezen verziók alapján lett tesztelve. 
    > - Python 3.7
    > - Azure-eventhub 5.2.0
    > - Azure-Storage – blob 12.6.0
    > - Avro – python3 1.10.1
2. Módosítsa a könyvtárat arra a könyvtárba, ahová a *Sender.py* és a *capturereader.py* mentette, majd futtassa a következő parancsot:
   
   ```
   python sender.py
   ```
   
   Ez a parancs egy új Python-folyamatot indít el a küldő futtatásához.
3. Várjon néhány percet a rögzítés futtatására, majd írja be az alábbi parancsot az eredeti parancssorablakba:
   
   ```
   python capturereader.py
   ```

   Ez a rögzítési processzor a helyi könyvtár használatával tölti le az összes blobot a Storage-fiókból és-tárolóból. Feldolgozza azokat, amelyek nem üresek, és az eredményeket CSV-fájlként írja a helyi könyvtárba.

## <a name="next-steps"></a>Következő lépések
Tekintse [meg a Python-mintákat a githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: ./event-hubs-about.md
