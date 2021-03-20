---
title: Folyamat változási csatornájának feldolgozása az Azure Blob Storageban | Microsoft Docs
description: Megtudhatja, hogyan dolgozhatja fel az adatcsatorna-naplókat egy .NET-ügyfélalkalmazás esetében
author: normesta
ms.author: normesta
ms.date: 09/08/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-csharp
ms.openlocfilehash: f0e89fdfba852fc056cf48efd1b92daabb272cf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89568251"
---
# <a name="process-change-feed-in-azure-blob-storage"></a>Folyamat változási csatornájának feldolgozása az Azure-ban Blob Storage

A módosítási hírcsatorna tranzakciós naplókat biztosít a blobok és a blob metaadatainak a Storage-fiókban történt változásairól. Ebből a cikkből megtudhatja, hogyan olvashatja el a módosítási hírcsatorna rekordjait a blob Change feed Processor Library használatával.

További információ a változási csatornáról: a [hírcsatorna módosítása az Azure Blob Storageban](storage-blob-change-feed.md).

## <a name="get-the-blob-change-feed-processor-library"></a>A blob Change feed Processor Library letöltése

1. Nyisson meg egy parancssori ablakot (például: Windows PowerShell).
2. A projekt könyvtárából telepítse az [ **Azure. Storage. Blobs. Changefeed** NuGet csomagot](https://www.nuget.org/packages/Azure.Storage.Blobs.ChangeFeed/).

```console
dotnet add package Azure.Storage.Blobs --version 12.5.1
dotnet add package Azure.Storage.Blobs.ChangeFeed --version 12.0.0-preview.4
```
## <a name="read-records"></a>Rekordok beolvasása

> [!NOTE]
> A módosítási hírcsatorna egy nem módosítható és csak olvasható entitás a Storage-fiókban. Tetszőleges számú alkalmazás olvashatja és feldolgozhatja a változási csatornát párhuzamosan és önállóan, a saját kényelmében. A rendszer nem távolítja el a rekordokat a változási hírcsatornából, amikor egy alkalmazás beolvassa őket. Az egyes fogyasztási olvasók olvasási vagy iterációs állapota független, és csak az alkalmazás tartja karban.

Ez a példa a változási hírcsatorna összes rekordját megismétli, hozzáadja őket egy listához, majd visszaadja ezt a listát a hívónak.
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Ez a példa a-konzolra nyomtat néhány értéket a lista egyes rekordjaiból. 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>Rekordok olvasásának folytatása mentett pozícióból

Eldöntheti, hogy elmenti-e az olvasási pozícióját a változási hírcsatornában, majd újra megismétli a rekordokat a későbbi időpontokban. Az olvasási pozíciót úgy mentheti, hogy beolvassa a változási csatorna kurzort. A kurzor egy **karakterlánc** , és az alkalmazása bármilyen módon mentheti a karakterláncot, ami logikus lehet az alkalmazás kialakításához (például egy fájlhoz vagy egy adatbázishoz).

Ez a példa a változási hírcsatorna összes rekordját megismétli, hozzáadja őket egy listához, és menti a kurzort. A listát és a kurzort a rendszer visszaadja a hívónak. 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>Rekordok adatfolyam-feldolgozása

Dönthet úgy is, hogy feldolgozza a módosítási adatcsatornákat, mivel azok véglegesítve vannak a változási hírcsatornában. Lásd a [specifikációkat](storage-blob-change-feed.md#specifications). A változási eseményeket átlagosan 60 másodpercen belül közzétesszük a változási csatornán. Javasoljuk, hogy a lekérdezési időköz megadásakor vegye figyelembe az új módosításokat az adott időszakra vonatkozóan.

Ez a példa rendszeres időközönként lekérdezi a módosításokat.  Ha módosulnak a rekordok, ez a kód feldolgozza ezeket a rekordokat, és menti a változási hírcsatorna kurzorát. Így ha a folyamat leáll, majd újra elindítják, az alkalmazás a kurzor használatával folytathatja a rekordok feldolgozását, ahol az utolsó abbahagyta a műveletet. Ez a példa menti a kurzort egy helyi alkalmazás konfigurációs fájljába, de az alkalmazás bármely olyan formában mentheti azt, amelyik a legmegfelelőbb a forgatókönyvhöz. 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>Rekordok beolvasása egy időtartományon belül

Egy adott időtartományon belüli rekordokat is elolvashatja. Ez a példa a változási hírcsatorna minden olyan rekordját megismétli, amely a 3:00. március 2 2020-kor és a 2:00-i augusztus 7 2020-ig tart, és hozzáadja őket egy listához, majd visszaküldi a listát a hívónak.

### <a name="selecting-segments-for-a-time-range"></a>Szegmensek kiválasztása időtartományhoz

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2020, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 8, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

A megadott kezdési időpontot a legközelebbi órára kerekíti a rendszer, a befejezési időpontot pedig a legközelebbi órára kerekíti. Lehetséges, hogy a felhasználók a kezdés időpontja előtt és a Befejezés időpontja előtt bekövetkezett eseményeket láthatják. Az is lehetséges, hogy a kezdő és a záró időpont között előforduló események nem jelennek meg. Ennek az az oka, hogy az események a kezdési időpontnál korábbi órában vagy a befejezési időpont utáni órában lesznek rögzítve.

## <a name="next-steps"></a>Következő lépések

További információ a hírcsatorna-naplók változásáról. Lásd: [adatcsatorna módosítása az Azure-ban blob Storage](storage-blob-change-feed.md)
