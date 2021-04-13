---
title: 'Gyors útmutató: események küldése és fogadása a go-Azure Event Hubs'
description: 'Gyors útmutató: Ez a cikk bemutatja, hogyan hozhat létre olyan go-alkalmazást, amely eseményeket küld az Azure Event Hubsból.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: ededd042dadd6059bd7b2d689cff78fe1bd6af0b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311090"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Rövid útmutató: események küldése vagy fogadása Event Hubsról a go használatával
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag azt ismerteti, hogyan írhat go-alkalmazásokat események küldésére vagy események fogadására az Event hub-ból. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- A go telepítése helyileg. Ha szükséges, kövesse [ezeket az utasításokat](https://golang.org/doc/install) .
- Aktív Azure-fiók. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot][].
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. A [Azure Portal](https://portal.azure.com) használatával hozzon létre Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást.

## <a name="send-events"></a>Események küldése
Ebből a szakaszból megtudhatja, hogyan hozhat létre olyan go-alkalmazást, amely eseményeket küld az Event hub-nak. 

### <a name="install-go-package"></a>Go-csomag telepítése

Szerezze be a go-csomagot Event Hubs a vagy a rendszerhez `go get` `dep` . Például:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Csomagok importálása a kódban

A go-csomagok importálásához használja az alábbi kódrészletet:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Hozzon létre egy új egyszerű szolgáltatásnevet az Azure- [szolgáltatás létrehozása az Azure CLI 2,0-vel](/cli/azure/create-an-azure-service-principal-azure-cli)című témakör utasításait követve. Mentse a megadott hitelesítő adatokat a környezetében a következő nevekkel. A Go nyelvhez készült Azure SDK és a Event Hubs csomagok is előre konfigurálva vannak, hogy megkeressék ezeket a változók neveit:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Most hozzon létre egy engedélyezési szolgáltatót az Event Hubs-ügyfél számára, amely ezeket a hitelesítő adatokat használja:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Event Hubs-ügyfél létrehozása

A következő kód egy Event Hubs ügyfelet hoz létre:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Kód írása az üzenetek küldéséhez

Az alábbi kódrészletben a (1) használatával interaktív üzeneteket küldhet egy terminálról, vagy (2) a programon belüli üzenetek küldéséhez:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Kiegészítő funkciók

Az Event hub-beli partíciók azonosítóinak beolvasása:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Futtassa az alkalmazást az események az Event hub-ba való küldéséhez. 

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="receive-events"></a>Események fogadása

### <a name="create-a-storage-account-and-container"></a>Storage-fiók és-tároló létrehozása

Az esemény-adatfolyamban lévő partíciók és ellenőrzőpontok bérletei az Azure Storage-tárolók használatával vannak megosztva a fogadók között. Hozzon létre egy Storage-fiókot és egy tárolót a go SDK-val, de az [Azure Storage-fiókok ismertetése](../storage/common/storage-account-create.md)című témakör útmutatását követve létrehozhat egyet is.

A go SDK-val tároló összetevők létrehozására szolgáló minták a [Go Samples](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) adattárában és az oktatóanyagnak megfelelő mintában érhetők el.

### <a name="go-packages"></a>Csomagok keresése

Az üzenetek fogadásához szerezze be a go-csomagokat Event Hubs a vagy a következővel `go get` `dep` :

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Csomagok importálása a kódban

A go-csomagok importálásához használja az alábbi kódrészletet:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Hozzon létre egy új egyszerű szolgáltatásnevet az Azure- [szolgáltatás létrehozása az Azure CLI 2,0-vel](/cli/azure/create-an-azure-service-principal-azure-cli)című témakör utasításait követve. Mentse a megadott hitelesítő adatokat a környezetben a következő nevekkel: mindkét Go nyelvhez készült Azure SDK és Event Hubs csomag előre konfigurálva van, hogy megkeresse ezeket a változók nevét.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Ezután hozzon létre egy engedélyezési szolgáltatót az Event Hubs-ügyfél számára, amely ezeket a hitelesítő adatokat használja:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Metaadatok struktúrájának beolvasása

Az Azure go SDK használatával az Azure-környezettel kapcsolatos metaadatokat szerezhet be. A későbbi műveletek ezt a struktúrát használják a helyes végpontok megkereséséhez.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Hitelesítő adatok létrehozása segítő 

Hozzon létre egy hitelesítőadat-segítőt, amely az előző Azure Active Directory (HRE) hitelesítő adatokat használja a megosztott hozzáférési aláírás (SAS) hitelesítő adatainak létrehozásához a tároláshoz. Az utolsó paraméter azt jelzi, hogy ez a konstruktor ugyanazt a környezeti változót használja, mint a korábban használt:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Egy ellenőrzési mutató és egy bérbeadó létrehozása 

Hozzon létre egy olyan **címbérletet**, amely egy partíció egy adott fogadónak való bérletéhez felelős, és egy **ellenőrző mutató**, amely az üzenet-adatfolyamhoz tartozó ellenőrzőpontok írásához felelős, hogy más fogadók a megfelelő eltolástól kezdhetik az olvasást.

Jelenleg egyetlen **StorageLeaserCheckpointer** érhető el, amely ugyanazt a tárolót használja a bérletek és ellenőrzőpontok kezeléséhez. A Storage-fiók és a tárolók nevein kívül a **StorageLeaserCheckpointer** szüksége van az előző lépésben létrehozott hitelesítő adatokra és az Azure Environment struct-ra, hogy megfelelően hozzáférhessen a tárolóhoz.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Az Event Processor Host kiépítése

Most már rendelkezik a EventProcessorHost létrehozásához szükséges darabokkal, az alábbiak szerint. Ugyanazt a **StorageLeaserCheckpointer** használja, mint a lízing és a pipa mutató, a korábban leírtaknak megfelelően:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Kezelő létrehozása 

Most hozzon létre egy kezelőt, és regisztrálja az Event Processor Host szolgáltatással. A gazdagép indításakor a rendszer ezt és a többi megadott kezelőt is alkalmazza a bejövő üzenetekre:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Kód írása az üzenetek fogadásához

Minden beállítással megkezdheti az Event processzor-gazdagépet, `Start(context)` hogy folyamatosan fusson, vagy csak addig, amíg az `StartNonBlocking(context)` üzenetek elérhetők.

Ez az oktatóanyag az alábbiak szerint kezdődik és fut: Példa a GitHub-minta használatára `StartNonBlocking` :

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Következő lépések
Olvassa el a következő cikkeket:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.yml)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[ingyenes fiók]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
