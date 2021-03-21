---
title: Megbízható párhuzamos várólista az Azure Service Fabric
description: A megbízható párhuzamos várólista egy nagy átviteli sebességű üzenetsor, amely párhuzamos enqueues és-várólistákat tesz lehetővé.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: d6852982621d3efd3f4a8597a2959fceb13abd12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784291"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Az Azure Service Fabric megbízható párhuzamos várólista bemutatása
A megbízható párhuzamos üzenetsor egy aszinkron, tranzakciós és replikált üzenetsor, amely magas párhuzamosságot biztosít a sorba helyezni és a dequeuing műveletekhez. A szolgáltatás úgy lett kialakítva, hogy magas átviteli sebességet és kis késleltetést biztosítson a [megbízható üzenetsor](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) által biztosított szigorú FIFO-sorrend kihasználása mellett, és ehelyett a lehető legjobb rendezést biztosítja.

## <a name="apis"></a>API-k

|Egyidejű üzenetsor                |Reliable Concurrent Queue                                         |
|--------------------------------|------------------------------------------------------------------|
| void sorba helyezni (T-tétel)           | Feladat EnqueueAsync (ITransaction TX, T elem)                       |
| bool TryDequeue (kimenő eredmény)  | Feladat< ConditionalValue < T > > TryDequeueAsync (ITransaction TX)  |
| int szám ()                    | hosszú darabszám ()                                                     |

## <a name="comparison-with-reliable-queue"></a>Összehasonlítás [megbízható üzenetsor](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1)

A megbízható párhuzamos üzenetsor a [megbízható üzenetsor](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1)alternatívájaként is elérhető. Olyan esetekben kell használni, ahol nem szükséges szigorú FIFO-sorrend, mivel a FIFO garantálja a kompromisszumot a párhuzamosságtal.  A [megbízható üzenetsor](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) zárolásokkal kényszeríti ki a FIFO-sorrendet, és legfeljebb egy tranzakciót sorba helyezni, és legfeljebb egy tranzakciót használhat egyszerre. Az összehasonlításban a megbízható párhuzamos üzenetsor ellazítja a megrendelési korlátozást, és lehetővé teszi, hogy az egyidejű tranzakciók száma a sorba helyezni és a dequeuing műveletekkel párhuzamos legyen. A legjobb megoldás, ha azonban egy megbízható párhuzamos várólistában két érték relatív sorrendjét nem lehet garantálni.

A megbízható párhuzamos üzenetsor nagyobb átviteli sebességet és kisebb késést biztosít, mint a [megbízható üzenetsor](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) , amikor több egyidejű tranzakció hajtja végre a enqueues és/vagy a várólistákat.

A megbízható párhuzamos várólista esetében az [üzenetsor](https://en.wikipedia.org/wiki/Message_queue) -használati eset a példa. Ebben a forgatókönyvben egy vagy több üzenet gyártója hozza létre és adja hozzá az elemeket a várólistához, és egy vagy több üzenet felhasználója lekéri az üzeneteket a várólistából, és feldolgozza azokat. Több gyártó és fogyasztó is működhet egymástól függetlenül, egyidejű tranzakciók használatával a várólista feldolgozása céljából.

## <a name="usage-guidelines"></a>Használati irányelvek
* A várólista azt várja, hogy a várólistában lévő elemek alacsony megőrzési időtartammal rendelkezzenek. Ez azt eredményezi, hogy az elemek hosszú ideje nem maradnak a várólistán.
* A várólista nem garantálja a szigorú FIFO-sorrendet.
* A várólista nem olvassa be a saját írásait. Ha egy elem egy tranzakción belül várólistán lévő, az egy tranzakción belül nem jelenik meg a dequeuing számára.
* A kihelyezett sorok nem elszigeteltek egymástól. Ha *a (a* ) elem a tranzakció *txnA* van elvégezve, bár a *txnA* nincs véglegesítve, akkor az *a* elem nem látható az egyidejű tranzakció *txnB*.  Ha a *txnA* megszakad, *a* *txnB* azonnal láthatóvá válik.
* A *TryPeekAsync* viselkedését *TryDequeueAsync* használatával lehet megvalósítani, majd megszakítani a tranzakciót. Ennek a viselkedésnek a példája a programozási minták szakaszban található.
* A Count nem tranzakciós. Felhasználhatja a várólista elemeinek számát, de egy időpontot is jelent, és nem hivatkozhat rá.
* A nem várólistán lévő elemek költséges feldolgozását a tranzakció aktív állapotában nem kell végrehajtani, hogy elkerülje a hosszú ideig futó tranzakciókat, amelyek hatással lehetnek a rendszer teljesítményére.

## <a name="code-snippets"></a>Kódtöredékek
Nézzük meg néhány kódrészletet és a várt kimeneteket. Ebben a szakaszban a kivételek kezelését figyelmen kívül hagyja a rendszer.

### <a name="instantiation"></a>Példányosítás
Egy megbízható párhuzamos várólista példányának létrehozása hasonló a többi megbízható gyűjteményhez.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Íme néhány kódrészlet a EnqueueAsync használatához, amelyet a várt kimenetek követnek.

- *1. eset: egyetlen sorba helyezni feladat*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladat sikeresen befejeződött, és nem volt egyidejű tranzakció, amely módosítja a várólistát. A felhasználó számíthat arra, hogy a várólista a következő megrendelések valamelyikében található elemeket tartalmazza:

> 10, 20
> 
> 20, 10


- *2. eset: párhuzamos sorba helyezni feladat*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladatok sikeresen befejeződtek, és a feladatok párhuzamosan futnak, és nincsenek más párhuzamos tranzakciók a várólista módosításával. Nem hozható létre következtetés a várólistában lévő elemek sorrendjéről. Ebben a kódrészletben az elemek a 4! egyikében is megjelenhetnek. lehetséges rendezések.  A várólista megpróbálja megtartani az elemeket az eredeti (várólistán lévő) sorrendben, de az egyidejű műveletek vagy hibák miatt kénytelen lehet átrendezni őket.


### <a name="dequeueasync"></a>DequeueAsync
Íme néhány kódrészlet a TryDequeueAsync használatához, amelyet a várt kimenetek követnek. Tegyük fel, hogy a várólista már fel van töltve az üzenetsor következő elemeivel:
> 10, 20, 30, 40, 50, 60

- *1. eset: egyetlen várólista-feladat*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladat sikeresen befejeződött, és nem volt egyidejű tranzakció, amely módosítja a várólistát. Mivel a várólista elemeinek sorrendjét nem lehet megtenni, az elemek közül bármelyik közül hármat el lehet végezni, bármilyen sorrendben. A várólista megpróbálja megtartani az elemeket az eredeti (várólistán lévő) sorrendben, de az egyidejű műveletek vagy hibák miatt kénytelen lehet átrendezni őket.  

- *2. eset: párhuzamos várólista-feladat*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladatok sikeresen befejeződtek, és a feladatok párhuzamosan futnak, és nincsenek más párhuzamos tranzakciók a várólista módosításával. Mivel a várólistában lévő elemek sorrendjéről nem hozhatók létre következtetések, a *dequeue1* és a *dequeue2* listája minden esetben két elemet tartalmaz, bármilyen sorrendben.

Ugyanez az tétel *nem* jelenik meg mindkét listán. Ezért ha a dequeue1 *10*, *30*, akkor a dequeue2 *20*, *40*.

- *3. eset: rendezés a tranzakció megszakításával*

Ha egy tranzakciót az in-Flight dequeuing szolgáltatással szakít meg, a rendszer visszaállítja az elemeket a várólista élén. Az elemek a várólista élén történő visszahelyezésének sorrendje nem garantált. Nézzük meg a következő kódot:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Tegyük fel, hogy az elemek a következő sorrendben lettek elvégezve:
> 10, 20

A tranzakció megszakításakor az elemek a következő megrendelések bármelyikében visszakerülnek a várólista fejre:
> 10, 20
> 
> 20, 10

Ugyanez érvényes minden olyan esetre, ahol a tranzakció *véglegesítése* sikertelen volt.

## <a name="programming-patterns"></a>Programozási minták
Ebben a szakaszban megvizsgálunk néhány programozási mintát, amelyek hasznosak lehetnek a megbízható párhuzamos várólista használatával.

### <a name="batch-dequeues"></a>Kötegek várólistái
Egy javasolt programozási minta a fogyasztói feladathoz, hogy a kötegelt feladatok elvégzését egy időben egy sorból végezze. A felhasználó dönthet úgy, hogy az egyes kötegek és a kötegek méretének késleltetését szabályozza. A következő kódrészlet ezt a programozási modellt mutatja be. Vegye figyelembe, hogy ebben a példában a feldolgozás a tranzakció véglegesítése után történik, így ha hiba történt a feldolgozás során, a feldolgozatlan elemek nem lesznek feldolgozva.  Azt is megteheti, hogy a feldolgozás a tranzakció hatókörébe esik, azonban negatív hatással lehet a teljesítményre, és megköveteli a már feldolgozott elemek kezelését.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Best-Effort Notification-Based feldolgozás
Egy másik érdekes programozási minta a Count API-t használja. Itt bemutatjuk a várakozási sor legjobb munkafolyamaton alapuló feldolgozását. A várólisták száma sorba helyezni vagy várólista-feladatok szabályozására is használható.  Vegye figyelembe, hogy ahogy az előző példában is, mivel a feldolgozás a tranzakción kívül történik, a feldolgozatlan elemek elvesznek, ha hiba történik a feldolgozás során.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Best-Effort Drain
Az adatstruktúra egyidejű jellegéből adódóan nem garantálható a várólista kiürítése.  Előfordulhat, hogy még akkor is előfordulhat, ha a várólistán nincs felhasználói művelet, mert a TryDequeueAsync egy adott hívása nem ad vissza olyan tételt, amely korábban várólistán lévő és véglegesítve lett.  A várólistán lévő elem garantált, hogy *végül* láthatóvá válik a deüzenetsor számára, de sávon kívüli kommunikációs mechanizmus nélkül, egy független fogyasztó nem tudja, hogy a várólista állandó állapotba került, még akkor is, ha minden termelő le lett állítva, és nem engedélyezett új sorba helyezni művelet. Így a kiürítési művelet az alábbiakban ismertetett legjobb erőfeszítést mutatja.

A felhasználónak le kell állítania az összes további gyártó és fogyasztó feladatát, és várnia kell, amíg a folyamatban lévő tranzakciók véglegesítve vagy megszakítva lettek, mielőtt a rendszer kiüríti a várólistát.  Ha a felhasználó ismeri a várólistában lévő elemek várt számát, beállíthat egy értesítést, amely azt jelzi, hogy az összes elem el lett-e küldve.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Betekintés
A megbízható párhuzamos várólista nem biztosítja a *TryPeekAsync* API-t. A felhasználók betekintést kaphatnak a *TryDequeueAsync* használatával, majd megszakítják a tranzakciót. Ebben a példában a deüzenetsor-sorok feldolgozása csak akkor történik meg, ha az elem értéke nagyobb, mint *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Be kell olvasni
* [Reliable Services rövid útmutató](service-fabric-reliable-services-quick-start.md)
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Értesítések Reliable Services](service-fabric-reliable-services-notifications.md)
* [Biztonsági mentés és visszaállítás Reliable Services (vész-helyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [Megbízható állapot-kezelő konfigurációja](service-fabric-reliable-services-configuration.md)
* [Első lépések Service Fabric web API-szolgáltatásokkal](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Az Reliable Services programozási modell speciális használata](./service-fabric-reliable-services-lifecycle.md)
* [Fejlesztői referenciák megbízható gyűjteményekhez](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
