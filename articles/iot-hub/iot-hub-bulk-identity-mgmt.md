---
title: Azure IoT Hub-eszközök identitásának importálása/exportálása | Microsoft Docs
description: Az Azure IoT Service SDK használata az eszközök identitásának importálására és exportálására szolgáló tömeges műveletek futtatásához az Identity registryben. Az importálási műveletek lehetővé teszik az eszköz-identitások tömeges létrehozását, frissítését és törlését.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e7a725b78fa828ce1286e212ee7de0205968156
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536079"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>IoT Hub-eszközidentitások tömeges importálása vagy exportálása

Minden IoT hub rendelkezik egy azonosító beállításjegyzékkel, amellyel eszközönkénti erőforrásokat hozhat létre a szolgáltatásban. Az Identity Registry Emellett lehetővé teszi az eszközre irányuló végpontokhoz való hozzáférés szabályozását. Ez a cikk azt ismerteti, hogyan importálhat és exportálhat eszköz-identitásokat egy identitás-beállításjegyzékből és-ból. Ha meg szeretne tekinteni egy működő mintát a C#-ban, és megtudhatja, hogyan használhatja ezt a képességet, amikor egy másik régióba klónozást végez, tekintse meg [az IoT hub klónozásával](iot-hub-how-to-clone.md)foglalkozó témakört.

> [!NOTE]
> IoT Hub a közelmúltban több régióban adta hozzá a virtuális hálózatok támogatását. Ez a szolgáltatás biztosítja az importálási és exportálási műveleteket, és nem szükséges kulcsokat továbbítani a hitelesítéshez.  Kezdetben a virtuális hálózatok támogatása csak a következő régiókban érhető el: *WestUS2*, *EastUS* és *SouthCentralUS*. Ha többet szeretne megtudni a virtuális hálózatok támogatásáról és a megvalósításához szükséges API-hívásokról, tekintse meg a [virtuális hálózatok IoT hub támogatását](virtual-network-support.md)ismertető témakört.

Az importálási és exportálási műveletek olyan *feladatok* kontextusában lépnek életbe, amelyek lehetővé teszik a tömeges szolgáltatási műveletek végrehajtását egy IoT hubhoz.

A **RegistryManager** osztály a **ExportDevicesAsync** és a **ImportDevicesAsync** metódusokat tartalmazza, amelyek a **feladatok** keretrendszerét használják. Ezek a módszerek lehetővé teszik az IoT hub összes azonosítójának beállításjegyzékének teljes körű exportálását, importálását és szinkronizálását.

Ebből a témakörből megtudhatja, hogyan végezheti el a **RegistryManager** osztály és a **feladat** rendszerét az IoT hub azonosító-beállításjegyzékbe irányuló és onnan érkező eszközök tömeges importálására és exportálására. Az Azure IoT Hub Device Provisioning Service használatával az emberi beavatkozás nélkül is engedélyezheti a nulla érintéses, igény szerinti üzembe helyezést egy vagy több IoT-hubhoz. További információt a [kiépítési szolgáltatás dokumentációjában](../iot-dps/index.yml)talál.

## <a name="what-are-jobs"></a>Mik azok a feladatok?

Az Identity Registry műveletei a **feladat** rendszerét használják a művelet során:

* A normál futásidejű műveletekhez képest potenciálisan hosszú végrehajtási idő van.

* Nagy mennyiségű adattal tér vissza a felhasználó számára.

A művelet eredményének egyetlen API-hívása helyett a művelet aszinkron módon létrehoz egy **feladatot** az adott IoT hub számára. A művelet ezután azonnal visszaadja a **JobProperties** objektumot.

A következő C# kódrészlet bemutatja, hogyan hozhat létre exportálási feladatot:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Ha a C#-kódban a **RegistryManager** osztályt szeretné használni, adja hozzá a **Microsoft. Azure. Devices** NuGet-csomagot a projekthez. A **RegistryManager** osztály a **Microsoft. Azure. Devices** névtérben található.

A **RegistryManager** osztály segítségével lekérdezheti a **feladatok** állapotát a visszaadott **JobProperties** metaadatainak használatával. A **RegistryManager** osztály egy példányának létrehozásához használja a **CreateFromConnectionString** metódust.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Az IoT hub kapcsolódási karakterláncának megkereséséhez a Azure Portalban:

- Keresse meg az IoT-központot.

- Válassza a **megosztott hozzáférési szabályzatok** lehetőséget.

- Válasszon ki egy házirendet, és vegye figyelembe a szükséges engedélyeket.

- Másolja a ConnectionString elemet a képernyő jobb oldalán lévő panelről.

A következő C# kódrészlet bemutatja, hogyan lehet öt másodpercenként lekérdezni, hogy a feladatok végrehajtása befejeződött-e:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

> [!NOTE]
> Ha a Storage-fiók olyan tűzfal-konfigurációval rendelkezik, amely korlátozza IoT Hub kapcsolatát, érdemes lehet a [Microsoft megbízható első féltől származó kivételt](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) használni (az IoT-hubok számára elérhető régiókban, felügyelt szolgáltatás identitásával).


## <a name="device-importexport-job-limits"></a>Eszközök importálási/exportálási feladatainak korlátai

Az összes IoT Hub-szinten egyszerre csak 1 aktív eszköz-importálási vagy-exportálási feladatot lehet engedélyezni. A IoT Hub a feladatok működésének mértékét is korlátozza. További információ: [Reference-IoT hub kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md).

## <a name="export-devices"></a>Eszközök exportálása

A **ExportDevicesAsync** metódus használatával exportálhatja egy IoT hub-identitás teljes egészét egy Azure Storage blob-tárolóba egy közös hozzáférési aláírás (SAS) használatával. A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../storage/common/storage-sas-overview.md).

Ez a módszer lehetővé teszi, hogy megbízható biztonsági másolatokat hozzon létre az eszköz adatairól egy Ön által vezérelt blob-tárolóban.

A **ExportDevicesAsync** metódushoz két paraméter szükséges:

* Egy blob-tároló URI-JÁT tartalmazó *karakterlánc* . Az URI-nak tartalmaznia kell egy SAS-jogkivonatot, amely írási hozzáférést biztosít a tárolóhoz. A feladatsor létrehoz egy blokk-blobot ebben a tárolóban a szerializált exportálási eszköz adatai tárolásához. Az SAS-tokennek tartalmaznia kell a következő engedélyeket:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* Egy *logikai* érték, amely azt jelzi, hogy ki szeretné-e zárni a hitelesítési kulcsokat az exportálási adatokból. **Hamis érték** esetén a hitelesítési kulcsok szerepelnek az exportálási kimenetben. Ellenkező esetben a kulcsok **Null értékként** lesznek exportálva.

A következő C# kódrészlet bemutatja, hogyan indíthat el egy olyan exportálási feladatot, amely tartalmazza az eszköz hitelesítési kulcsait az adatok exportálásakor, majd a lekérdezés befejezését:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

A művelet a megadott blob-tárolóban lévő kimenetét a **devices.txt** nevű blokk-blobként tárolja. A kimeneti adatokat JSON-szerializált eszköz tartalmazza, és soronként egy eszköz van.

A következő példa a kimeneti adatokat mutatja be:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Ha egy eszközhöz Twin-érték tartozik, akkor a rendszer a Twin-adategységeket is exportálja az eszköz adattal együtt. Az alábbi példa ezt a formátumot mutatja be. A "twinETag" sorban lévő összes, egészen a végéig eltelt adatok.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Ha ehhez az adatkódokhoz szeretne hozzáférni, egyszerűen deszerializálhatja ezeket az adatfájlokat a **ExportImportDevice** osztály használatával. A következő C# kódrészlet bemutatja, hogyan olvashatja el a korábban egy blokk blobba exportált eszköz adatait:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Eszközök importálása

A **RegistryManager** osztály **ImportDevicesAsync** metódusa lehetővé teszi tömeges importálási és szinkronizálási műveletek végrehajtását egy IoT hub-identitás beállításjegyzékében. A **ExportDevicesAsync** metódushoz hasonlóan a **ImportDevicesAsync** metódus is a **feladatok** keretrendszerét használja.

Ügyeljen arra, hogy a **ImportDevicesAsync** metódust használja, mert az új eszközök az Identity registryben való kiépítés mellett a meglévő eszközöket is frissítheti és törölheti.

> [!WARNING]
> Egy importálási művelet nem vonható vissza. Mindig készítsen biztonsági másolatot a meglévő adatokról a **ExportDevicesAsync** metódus használatával egy másik blob-tárolóra, mielőtt tömeges módosításokat hajt végre az identitás-beállításjegyzékben.

A **ImportDevicesAsync** metódus két paramétert vesz igénybe:

* Egy olyan *karakterlánc* , amely egy [Azure Storage](../storage/index.yml) blob-tároló URI-ját tartalmazza a feladathoz *bemenetként* való használatra. Az URI azonosítónak tartalmaznia kell egy olyan SAS-jogkivonatot, amely olvasási hozzáférést biztosít a tárolóhoz. Ennek a tárolónak tartalmaznia kell egy **devices.txt** nevű blobot, amely az azonosító beállításjegyzékbe importálandó szerializált eszköz adatait tartalmazza. Az importálási adatoknak ugyanabban a JSON-formátumban kell tartalmazniuk az eszköz adatait, amelyet a **ExportImportDevice** -feladathoz használ, amikor létrehoz egy **devices.txt** blobot. Az SAS-tokennek tartalmaznia kell a következő engedélyeket:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* Egy olyan *karakterlánc* , amely egy [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) blob-tároló URI-ját tartalmazza, amelyet a feladatokból *kimenetként* kíván használni. A művelet egy blokk-blobot hoz létre ebben a tárolóban a befejezett importálási **feladattal** kapcsolatos hibák tárolására. Az SAS-tokennek tartalmaznia kell a következő engedélyeket:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> A két paraméter ugyanarra a blob-tárolóra mutathat. A különálló paraméterek egyszerűen lehetővé teszik az adatok hatékonyabb szabályozását, mivel a kimeneti tároló további engedélyeket igényel.

A következő C# kódrészlet bemutatja, hogyan indíthat el egy importálási feladatot:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Ezzel a módszerrel a Twin-eszközökre vonatkozó adatimportálási módszer is használható. Az adatbevitel formátuma megegyezik a **ExportDevicesAsync** szakaszban látható formátummal. Így újra importálhatja az exportált adatfájlokat. A **$metadata** megadása nem kötelező.

## <a name="import-behavior"></a>Importálási viselkedés

A **ImportDevicesAsync** metódussal a következő tömeges műveleteket végezheti el az identitás-beállításjegyzékben:

* Új eszközök tömeges regisztrálása
* Meglévő eszközök tömeges törlése
* Tömeges állapot módosításai (eszközök engedélyezése vagy letiltása)
* Új eszköz-hitelesítési kulcsok tömeges kiosztása
* Eszköz-hitelesítési kulcsok tömeges automatikus újragenerálása
* Twin-adatmennyiség tömeges frissítése

Az előző műveletek bármely kombinációját egyetlen **ImportDevicesAsync** -híváson belül is végrehajthatja. Regisztrálhat például új eszközöket, és egyszerre törölheti vagy frissítheti a meglévő eszközöket. Ha a **ExportDevicesAsync** metódussal együtt használja, teljes mértékben áttelepítheti az összes eszközt az egyik IoT-központból egy másikba.

Ha az importálási fájl kettős metaadatokat tartalmaz, akkor ez a metaadatok felülírják a meglévő kettős metaadatokat. Ha az importfájl nem tartalmaz Twin metaadatokat, csak a `lastUpdateTime` metaadatok frissülnek az aktuális időponttal.

Az eszközök importálási folyamatának vezérléséhez használja az összes eszköz szerializálási adatkészletének opcionális **importMode** tulajdonságát. A **importMode** tulajdonság a következő beállításokkal rendelkezik:

| importMode | Description |
| --- | --- |
| **createOrUpdate** |Ha egy eszköz nem létezik a megadott **azonosítóval**, az újonnan regisztrálva van. <br/>Ha az eszköz már létezik, a rendszer felülírja a meglévő adatokat a megadott bemeneti adatokkal anélkül, hogy a **ETAG** értéket kellene megadnia. <br> A felhasználó opcionálisan megadhatja a Twin-és az eszközre vonatkozó adatkészleteket is. A Twin ETAG, ha meg van adva, az eszköz ETAG függetlenül dolgozza fel. Ha a meglévő Twin ETAG nem egyeznek, a rendszer hibát ír a naplófájlba. |
| **létrehozása** |Ha egy eszköz nem létezik a megadott **azonosítóval**, az újonnan regisztrálva van. <br/>Ha az eszköz már létezik, a rendszer hibát ír a naplófájlba. <br> A felhasználó opcionálisan megadhatja a Twin-és az eszközre vonatkozó adatkészleteket is. A Twin ETAG, ha meg van adva, az eszköz ETAG függetlenül dolgozza fel. Ha a meglévő Twin ETAG nem egyeznek, a rendszer hibát ír a naplófájlba. |
| **frissítése** |Ha egy eszköz már létezik a megadott **azonosítóval**, a rendszer felülírja a meglévő adatokat a megadott bemeneti adatokkal anélkül, hogy a **ETAG** értéket kellene megadnia. <br/>Ha az eszköz nem létezik, a rendszer hibát ír a naplófájlba. |
| **updateIfMatchETag** |Ha egy eszköz már létezik a megadott **azonosítóval**, a rendszer csak akkor írja felül a meglévő adatokat, ha van **ETAG** egyezés. <br/>Ha az eszköz nem létezik, a rendszer hibát ír a naplófájlba. <br/>Ha a **ETAG** nem egyezik, a rendszer hibát ír a naplófájlba. |
| **createOrUpdateIfMatchETag** |Ha egy eszköz nem létezik a megadott **azonosítóval**, az újonnan regisztrálva van. <br/>Ha az eszköz már létezik, a rendszer a meglévő adatokat csak akkor írja felül a megadott bemeneti adatokkal, ha van **ETAG** egyezés. <br/>Ha a **ETAG** nem egyezik, a rendszer hibát ír a naplófájlba. <br> A felhasználó opcionálisan megadhatja a Twin-és az eszközre vonatkozó adatkészleteket is. A Twin ETAG, ha meg van adva, az eszköz ETAG függetlenül dolgozza fel. Ha a meglévő Twin ETAG nem egyeznek, a rendszer hibát ír a naplófájlba. |
| **törlése** |Ha egy eszköz már létezik a megadott **azonosítóval**, a rendszer törli a **ETAG** érték figyelmen kívül hagyásával. <br/>Ha az eszköz nem létezik, a rendszer hibát ír a naplófájlba. |
| **deleteIfMatchETag** |Ha egy eszköz már létezik a megadott **azonosítóval**, a rendszer csak akkor törli, ha van **ETAG** egyezés. Ha az eszköz nem létezik, a rendszer hibát ír a naplófájlba. <br/>Ha a ETag nem egyezik, a rendszer hibát ír a naplófájlba. |

> [!NOTE]
> Ha a szerializálási adathalmaz explicit módon nem határoz meg **importMode** jelzőt az eszközhöz, az alapértelmezett érték a **createOrUpdate** az importálási művelet során.

## <a name="import-devices-example--bulk-device-provisioning"></a>Eszközök importálása példa – tömeges eszköz kiépítés

A következő C#-kódrészlet azt szemlélteti, hogyan lehet több eszköz identitást előállítani:

* Hitelesítő kulcsok belefoglalása.
* Az eszköz adatainak megírása egy blokk-blobba.
* Importálja az eszközöket az Identity registrybe.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Eszközök importálása példa – tömeges törlés

A következő mintakód bemutatja, hogyan törölheti a hozzáadott eszközöket az előző kód mintájának használatával:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>A tároló SAS URI-azonosítójának beolvasása

A következő mintakód bemutatja, hogyan hozhatja ki a blob-tárolók olvasási, írási és törlési engedélyekkel rendelkező [sas URI-ját](../storage/common/storage-sas-overview.md) :

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan végezheti el a tömeges műveleteket egy IoT hub azonosító-beállításjegyzékében. Számos művelet, többek között az eszközök egyik központból a másikba való áthelyezésének módja, a [IoT hub-ban regisztrált eszközökön található, a IoT hub klónozásának módja](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub). 

A klónozási cikknek van egy működő mintája, amely a IoT C#-mintákban található a következő oldalon: [Azure IoT-minták c#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/)-hoz, a projekt pedig ImportExportDevicesSample. Letöltheti a mintát, és kipróbálhatja; az IoT Hub-cikkek [klónozása](iot-hub-how-to-clone.md) című cikkben talál útmutatást.

Az Azure IoT Hub kezelésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Az IoT Hub monitorozása](monitor-iot-hub.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)

Ha szeretné megtekinteni a IoT Hub Device Provisioning Service használatát a nulla érintéses, igény szerinti kiépítés engedélyezéséhez, olvassa el a következő témakört: 

* [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml)