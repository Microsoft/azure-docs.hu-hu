---
title: Az Azure Blockchain Workbenchtel való integrációhoz használható üzenetek használata
description: Az Azure Blockchain Workbench előzetes verziójának más rendszerekkel való integrálására szolgáló üzenetek használatának áttekintése.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74324514"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Az Azure Blockchain Workbench üzenetkezelési integrációja

REST API biztosítása mellett az Azure Blockchain Workbench üzenetkezelésen alapuló integrációt is biztosít. A Workbench a Azure Event Gridon keresztül teszi közzé a Főkönyv-központú eseményeket, lehetővé téve az alsóbb rétegbeli felhasználók számára az adatbevitelt, vagy az események alapján hajthat végre Azoknál az ügyfeleknél, amelyek megbízható üzenetkezelést igényelnek, az Azure Blockchain Workbench egy Azure Service Bus-végponthoz is kézbesít üzeneteket.

## <a name="input-apis"></a>Bemeneti API-k

Ha a külső rendszerekből származó tranzakciókat kíván létrehozni a felhasználók létrehozásához, a szerződések létrehozásához és a szerződések frissítéséhez, akkor az üzenetkezelési bemeneti API-kkal tranzakciókat végezhet a főkönyvben. Lásd: [üzenetküldési integrációs minták](https://aka.ms/blockchain-workbench-integration-sample) a bemeneti API-kat bemutató minta számára.

A jelenleg elérhető bemeneti API-k a következők.

### <a name="create-user"></a>Felhasználó létrehozása

Létrehoz egy új felhasználót.

A kérelemhez a következő mezők szükségesek:

| **Név**             | **Leírás**                                      |
|----------------------|------------------------------------------------------|
| Kérelemazonosító            | Ügyfél által megadott GUID                                |
| firstName            | A felhasználó vezetékneve                              |
| lastName             | A felhasználó vezetékneve                               |
| emailAddress         | A felhasználó e-mail címe                           |
| externalId           | A felhasználó Azure AD-objektumának azonosítója                      |
| connectionId         | A blockchain-kapcsolatok egyedi azonosítója |
| messageSchemaVersion | Üzenetkezelési séma verziója                            |
| messageName          | **CreateUserRequest**                               |

Példa:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Az Blockchain Workbench egy választ ad vissza a következő mezőkkel:

| **Név**              | **Leírás**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| Kérelemazonosító             | Ügyfél által megadott GUID |
| userId                | A létrehozott felhasználó azonosítója |
| userChainIdentifier   | A blockchain-hálózaton létrehozott felhasználó címe. A Ethereum-ben a címe a felhasználó **láncának** címe. |
| connectionId          | A blockchain-kapcsolatok egyedi azonosítója|
| messageSchemaVersion  | Üzenetkezelési séma verziója |
| messageName           | **CreateUserUpdate** |
| status                | A felhasználói létrehozási kérelem állapota.  Ha sikerült, az érték **sikeres.** Hiba esetén az érték **sikertelen**.     |
| additionalInformation | Az állapot alapján megadott további információk |

Példa sikeres **felhasználói válasz létrehozására** a Blockchain Workbenchből:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Ha a kérelem sikertelen volt, a rendszer további információkat tartalmaz a hiba részleteiről.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Szerződés létrehozása

Új szerződést hoz létre.

A kérelemhez a következő mezők szükségesek:

| **Név**             | **Leírás**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| Kérelemazonosító            | Ügyfél által megadott GUID |
| userChainIdentifier  | A blockchain-hálózaton létrehozott felhasználó címe. A Ethereum ez a címe a felhasználó **láncának** címe. |
| applicationName      | Az alkalmazás neve |
| version              | Az alkalmazás verziója. Kötelező, ha az alkalmazásnak több verziója is engedélyezve van. Ellenkező esetben a verzió megadása nem kötelező. Az alkalmazás verziószámozásával kapcsolatos további információkért lásd: az [Azure Blockchain Workbench alkalmazás verziószámozása](version-app.md). |
| workflowName         | A munkafolyamat neve |
| parameters           | Paraméterek bemenete a szerződések létrehozásához |
| connectionId         | A blockchain-kapcsolatok egyedi azonosítója |
| messageSchemaVersion | Üzenetkezelési séma verziója |
| messageName          | **CreateContractRequest** |

Példa:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Az Blockchain Workbench egy választ ad vissza a következő mezőkkel:

| **Név**                 | **Leírás**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| Kérelemazonosító                | Ügyfél által megadott GUID                                                             |
| contractId               | A szerződés egyedi azonosítója az Azure Blockchain Workbenchben |
| contractLedgerIdentifier | A Szerződés címe a főkönyvben                                            |
| connectionId             | A blockchain-kapcsolatok egyedi azonosítója                               |
| messageSchemaVersion     | Üzenetkezelési séma verziója                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | A szerződés-létrehozási kérelem állapota.  Lehetséges értékek: **elküldve**, **véglegesítve**, **hiba**.  |
| additionalInformation    | Az állapot alapján megadott további információk                              |

Példa a Blockchain Workbench által benyújtott **Szerződés-létrehozási** válaszra:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Példa egy véglegesített **létrehozási szerződésre** válasz a Blockchain Workbenchből:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Ha a kérelem sikertelen volt, a rendszer további információkat tartalmaz a hiba részleteiről.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Szerződés létrehozása művelet

Létrehoz egy új szerződési műveletet.

A kérelemhez a következő mezők szükségesek:

| **Név**                 | **Leírás**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Kérelemazonosító                | Ügyfél által megadott GUID |
| userChainIdentifier      | A blockchain-hálózaton létrehozott felhasználó címe. A Ethereum ez a címe a felhasználó **láncának** címe. |
| contractLedgerIdentifier | A Szerződés címe a főkönyvben |
| version                  | Az alkalmazás verziója. Kötelező, ha az alkalmazásnak több verziója is engedélyezve van. Ellenkező esetben a verzió megadása nem kötelező. Az alkalmazás verziószámozásával kapcsolatos további információkért lásd: az [Azure Blockchain Workbench alkalmazás verziószámozása](version-app.md). |
| workflowFunctionName     | A munkafolyamat-függvény neve |
| parameters               | Paraméterek bemenete a szerződések létrehozásához |
| connectionId             | A blockchain-kapcsolatok egyedi azonosítója |
| messageSchemaVersion     | Üzenetkezelési séma verziója |
| messageName              | **CreateContractActionRequest** |

Példa:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Az Blockchain Workbench egy választ ad vissza a következő mezőkkel:

| **Név**              | **Leírás**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| Kérelemazonosító             | Ügyfél által megadott GUID|
| contractId            | A szerződés egyedi azonosítója az Azure Blockchain Workbenchben |
| connectionId          | A blockchain-kapcsolatok egyedi azonosítója |
| messageSchemaVersion  | Üzenetkezelési séma verziója |
| messageName           | **CreateContractActionUpdate** |
| status                | A szerződés műveleti kérelmének állapota. Lehetséges értékek: **elküldve**, **véglegesítve**, **hiba**.                         |
| additionalInformation | Az állapot alapján megadott további információk |

Példa a beküldött **create szerződésre műveletre** válasz a Blockchain Workbenchből:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Példa egy véglegesített **létrehozási műveletre** válasz a Blockchain Workbenchből:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Ha a kérelem sikertelen volt, a rendszer további információkat tartalmaz a hiba részleteiről.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Bemeneti API-hibakódok és üzenetek

**4000-es hibakód: hibás kérési hiba**
- Érvénytelen connectionId
- A CreateUserRequest deszerializálása nem sikerült
- A CreateContractRequest deszerializálása nem sikerült
- A CreateContractActionRequest deszerializálása nem sikerült
- A (z) {Application Name} által azonosított alkalmazás nem létezik
- A (z) {Application Name} által azonosított alkalmazás nem rendelkezik munkafolyamattal
- A UserChainIdentifier nem létezik
- A (főkönyvi azonosító szerint {azonosított) egyezmény nem létezik
- A (z) {Ledger azonosítóval azonosított {{1} azonosítójú szerződés nem rendelkezik a (z) {workflow Function Name} függvénnyel
- A UserChainIdentifier nem létezik

**4090-es hibakód: ütközési hiba**
- A felhasználó már létezik
- A szerződés már létezik
- A szerződési művelet már létezik

**5000-es hibakód: belső kiszolgálóhiba**
- Kivételek üzenetei

## <a name="event-notifications"></a>Eseményértesítések

Az eseményekre vonatkozó értesítések segítségével értesítheti a felhasználókat és az alsóbb rétegbeli eseményeket, amelyek a Blockchain Workbenchben történnek, illetve az Blockchain-hálózatban, amelyhez csatlakozik. Az eseményekre vonatkozó értesítések közvetlenül a kódban is felhasználhatók, vagy olyan eszközökkel használhatók, mint például a Logic Apps és a flow az adatforgalom alsóbb rétegbeli rendszerekre történő aktiválásához.

A beérkező különféle üzenetek részleteiért lásd az [értesítési üzenetek referenciáját](#notification-message-reference) .

### <a name="consuming-event-grid-events-with-azure-functions"></a>Event Grid események felhasználása a Azure Functions

Ha a felhasználó a Blockchain Workbenchben megjelenő eseményekről szeretne értesítést kapni Event Gridról, a Azure Functions használatával Event Grid eseményeket használhat.

1. Hozzon létre egy **Azure-függvényalkalmazás** a Azure Portalban.
2. Hozzon létre egy új függvényt.
3. Keresse meg Event Grid sablonját. Megjelenik az üzenet olvasásához szükséges alapszintű sablon kódja. Szükség szerint módosítsa a kódot.
4. Mentse a függvényt. 
5. Válassza ki az Blockchain Workbench erőforráscsoporthoz tartozó Event Grid.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Event Grid események felhasználása a Logic Apps

1. Hozzon létre egy új **Azure logikai alkalmazást** a Azure Portal.
2. Amikor megnyitja az Azure Logic app alkalmazást a portálon, a rendszer kérni fogja, hogy válasszon ki egy triggert. Válassza **a Azure Event Grid – erőforrás-esemény bekövetkezésekor** lehetőséget.
3. Amikor megjelenik a Munkafolyamat-tervező, a rendszer kérni fogja, hogy jelentkezzen be.
4. Válassza ki az előfizetést. Az erőforrást **Microsoft. EventGrid. témakörökként**. Válassza ki **az erőforrás nevét az** Azure Blockchain Workbench erőforráscsoporthoz tartozó erőforrás nevéből.
5. Válassza ki az Blockchain Workbench erőforráscsoporthoz tartozó Event Grid.

## <a name="using-service-bus-topics-for-notifications"></a>Service Bus témakörök használata az értesítésekhez

Service Bus témakörökkel értesítheti a felhasználókat a Blockchain Workbenchben előforduló eseményekről. 

1. Keresse meg a Workbench erőforráscsoporthoz tartozó Service Bus.
2. Válassza a **Témakörök** lapfület.
3. Kimenő forgalom kiválasztása **– témakör**.
4. Hozzon létre egy új előfizetést ehhez a témakörhöz. Szerezze be a kulcsot.
5. Hozzon létre egy programot, amely előfizet az előfizetésből származó eseményekre.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Service Bus üzenetek fogyasztása Logic Apps

1. Hozzon létre egy új **Azure logikai alkalmazást** a Azure Portal.
2. Amikor megnyitja az Azure Logic app alkalmazást a portálon, a rendszer kérni fogja, hogy válasszon ki egy triggert. Írja be a **Service Bus** kifejezést a keresőmezőbe, és válassza ki a kívánt interakció típusát a Service Bus. Például **Service Bus – ha üzenet érkezik egy témakör-előfizetésbe (automatikusan befejeződött)**.
3. Amikor megjelenik a Munkafolyamat-tervező, a Service Bus kapcsolódási adatait kell megadnia.
4. Válassza ki az előfizetését, és adja meg a **Workbench-External** témakört.
5. Fejlessze az alkalmazáshoz tartozó logikát, amely a triggerből származó üzenetet használja.

## <a name="notification-message-reference"></a>Értesítési üzenet referenciája

A **messageName** függően az értesítési üzenetek a következő típusú üzenetek egyikével rendelkeznek.

### <a name="block-message"></a>Üzenet letiltása

Az egyes blokkokról tartalmaz információkat. A *BlockMessage* tartalmaz egy szakaszt a blokk szintű információkkal, valamint egy szakaszt a tranzakciós adatokkal.

| Név | Leírás |
|------|-------------|
| letiltás | [Blokkoló adatokat](#block-information) tartalmaz |
| transactions | A blokk gyűjtési [tranzakciójának információit](#transaction-information) tartalmazza. |
| connectionId | A kapcsolatok egyedi azonosítója |
| messageSchemaVersion | Üzenetkezelési séma verziója |
| messageName | **BlockMessage** |
| additionalInformation | További információk |

#### <a name="block-information"></a>Adatok blokkolása

| Név              | Leírás |
|-------------------|-------------|
| blockId           | A blokk egyedi azonosítója az Azure Blockchain Workbenchben |
| blockNumber       | Egy blokk egyedi azonosítója a főkönyvben |
| blockHash         | A blokk kivonata |
| previousBlockHash | Az előző blokk kivonata |
| blockTimestamp    | A blokk időbélyege |

#### <a name="transaction-information"></a>Tranzakciós adatok

| Név               | Leírás |
|--------------------|-------------|
| Tranzakcióazonosító      | A tranzakció egyedi azonosítója az Azure Blockchain Workbenchben |
| transactionHash    | A tranzakció kivonata a főkönyvben |
| a               | Egyedi azonosító a főkönyvben a tranzakció forrásaként |
| felhasználóként a(z)                 | A tranzakció célhelyéhez tartozó főkönyvben szereplő egyedi azonosító |
| provisioningStatus | A tranzakció kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek: </br>0 – a tranzakciót az API hozta létre az adatbázisban.</br>1 – a tranzakció elküldése a főkönyvbe</br>2 – a tranzakció sikeresen véglegesítve lett a főkönyvben</br>3 vagy 4 – a tranzakciót nem sikerült véglegesíteni a főkönyvben</br>5 – a tranzakció sikeresen véglegesítve lett a főkönyvben |

Példa a Blockchain Workbench egyik *BlockMessage* :

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Szerződési üzenet

A szerződésre vonatkozó információkat tartalmaz. Az üzenet tartalmaz egy szakaszt a szerződés tulajdonságaival, valamint egy szakaszt a tranzakciós adatokkal. A tranzakció szakasz tartalmazza az adott blokkra vonatkozó szerződést módosító összes tranzakciót.

| Név | Leírás |
|------|-------------|
| blockId | A blokk egyedi azonosítója az Azure Blockchain Workbenchben |
| blockHash | A blokk kivonata |
| modifyingTransactions | A szerződést [módosító tranzakciók](#modifying-transaction-information) |
| contractId | A szerződés egyedi azonosítója az Azure Blockchain Workbenchben |
| contractLedgerIdentifier | A szerződés egyedi azonosítója a főkönyvben |
| contractProperties | [A szerződés tulajdonságai](#contract-properties) |
| isNewContract | Azt jelzi, hogy a szerződés újonnan létrejött-e. A lehetséges értékek a következők: true (igaz): Ez a szerződés egy új szerződést hozott létre. hamis: Ez a szerződés egy szerződési frissítés. |
| connectionId | A kapcsolatok egyedi azonosítója |
| messageSchemaVersion | Üzenetkezelési séma verziója |
| messageName | **ContractMessage** |
| additionalInformation | További információk |

#### <a name="modifying-transaction-information"></a>Tranzakciós adatok módosítása

| Név               | Leírás |
|--------------------|-------------|
| Tranzakcióazonosító | A tranzakció egyedi azonosítója az Azure Blockchain Workbenchben |
| transactionHash | A tranzakció kivonata a főkönyvben |
| a | Egyedi azonosító a főkönyvben a tranzakció forrásaként |
| felhasználóként a(z) | A tranzakció célhelyéhez tartozó főkönyvben szereplő egyedi azonosító |

#### <a name="contract-properties"></a>Szerződés tulajdonságai

| Név               | Leírás |
|--------------------|-------------|
| workflowPropertyId | A munkafolyamat-tulajdonság egyedi azonosítója az Azure Blockchain Workbenchben |
| name | A munkafolyamat-tulajdonság neve |
| érték | A munkafolyamat-tulajdonság értéke |

Példa a Blockchain Workbench egyik *ContractMessage* :

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Esemény üzenet: szerződési függvény hívása

Információt tartalmaz egy szerződési függvény meghívásakor, például a függvény nevét, a paraméterek bemenetét és a függvény hívóját.

| Név | Leírás |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| hívó                      | [Hívó adatai](#caller-information) |
| contractId                  | A szerződés egyedi azonosítója az Azure Blockchain Workbenchben |
| contractLedgerIdentifier    | A szerződés egyedi azonosítója a főkönyvben |
| Függvénynév                | A függvény neve |
| parameters                  | [Paraméteradatok](#parameter-information) |
| tranzakció                 | Tranzakciós adatok |
| inTransactionSequenceNumber | A blokkban lévő tranzakció sorozatszáma |
| connectionId                | A kapcsolatok egyedi azonosítója |
| messageSchemaVersion        | Üzenetkezelési séma verziója |
| messageName                 | **EventMessage** |
| additionalInformation       | További információk |

#### <a name="caller-information"></a>Hívó adatai

| Név | Leírás |
|------|-------------|
| típus | A hívó típusa, például egy felhasználó vagy egy szerződés |
| id | A hívó egyedi azonosítója az Azure Blockchain Workbenchben |
| ledgerIdentifier | A hívó egyedi azonosítója a főkönyvben |

#### <a name="parameter-information"></a>Paraméteradatok

| Név | Leírás |
|------|-------------|
| name | Paraméter neve |
| érték | Paraméter értéke |

#### <a name="event-message-transaction-information"></a>Esemény-üzenet tranzakciós adatai

| Név               | Leírás |
|--------------------|-------------|
| Tranzakcióazonosító      | A tranzakció egyedi azonosítója az Azure Blockchain Workbenchben |
| transactionHash    | A tranzakció kivonata a főkönyvben |
| a               | Egyedi azonosító a főkönyvben a tranzakció forrásaként |
| felhasználóként a(z)                 | A tranzakció célhelyéhez tartozó főkönyvben szereplő egyedi azonosító |

Példa EventMessage- *ContractFunctionInvocation* a Blockchain Workbenchből:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Esemény üzenete: alkalmazás betöltése

Információt tartalmaz, ha egy alkalmazás a Workbenchbe van feltöltve, például a feltöltött alkalmazás neve és verziója.

| Név | Leírás |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Az alkalmazás egyedi azonosítója az Azure Blockchain Workbenchben |
| applicationName | Alkalmazásnév |
| applicationDisplayName | Alkalmazás megjelenítendő neve |
| applicationVersion | Alkalmazás verziója |
| applicationDefinitionLocation | Az alkalmazás konfigurációs fájljának URL-címe |
| contractCodes | Az alkalmazáshoz tartozó [szerződések kódjának](#contract-code-information) gyűjteménye |
| applicationRoles | Alkalmazás- [szerepkörök](#application-role-information) gyűjteménye az alkalmazáshoz |
| applicationWorkflows | Az alkalmazáshoz tartozó [alkalmazás-munkafolyamatok](#application-workflow-information) gyűjteménye |
| connectionId | A kapcsolatok egyedi azonosítója |
| messageSchemaVersion | Üzenetkezelési séma verziója |
| messageName | **EventMessage** |
| additionalInformation | Az itt elérhető további információk az alkalmazás-munkafolyamatok állapotait és az átmeneti információkat tartalmazzák. |

#### <a name="contract-code-information"></a>Szerződési kód adatai

| Név | Leírás |
|------|-------------|
| id | A szerződéssablon egyedi azonosítója az Azure Blockchain Workbenchben |
| ledgerId | A Főkönyv egyedi azonosítója az Azure Blockchain Workbenchben |
| location | Az URL-cím, amelyben a szerződési kód fájl található |

#### <a name="application-role-information"></a>Alkalmazás-szerepkör adatai

| Név | Leírás |
|------|-------------|
| id | Az alkalmazás szerepkör egyedi azonosítója az Azure Blockchain Workbenchben |
| name | Az alkalmazás szerepkörének neve |

#### <a name="application-workflow-information"></a>Alkalmazás-munkafolyamat adatai

| Név | Leírás |
|------|-------------|
| id | Az alkalmazás-munkafolyamat egyedi azonosítója az Azure Blockchain Workbenchben |
| name | Alkalmazás-munkafolyamat neve |
| displayName | Alkalmazás-munkafolyamat megjelenítendő neve |
| funkciók | [Az alkalmazás-munkafolyamathoz tartozó függvények](#workflow-function-information) gyűjteménye|
| tagállamok | [Az alkalmazás-munkafolyamathoz tartozó állapotok](#workflow-state-information) gyűjteménye |
| properties | Alkalmazás- [munkafolyamat tulajdonságai – információk](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Munkafolyamat-függvény adatai

| Név | Leírás |
|------|-------------|
| id | Az alkalmazás-munkafolyamat funkció egyedi azonosítója az Azure Blockchain Workbenchben |
| name | Függvény neve |
| parameters | A függvény paraméterei |

##### <a name="workflow-state-information"></a>Munkafolyamat-állapot adatai

| Név | Leírás |
|------|-------------|
| name | Állapot neve |
| displayName | Állapot megjelenítendő neve |
| stílus | Állapot stílusa (sikeres vagy sikertelen) |

##### <a name="workflow-property-information"></a>Munkafolyamat-tulajdonságok adatai

| Név | Leírás |
|------|-------------|
| id | Az alkalmazás-munkafolyamat tulajdonság egyedi azonosítója az Azure Blockchain Workbenchben |
| name | Tulajdonság neve |
| típus | Tulajdonság típusa |

Példa EventMessage- *ApplicationIngestion* a Blockchain Workbenchből:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                        "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                        "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Esemény-üzenet: szerepkör-hozzárendelés

Olyan információt tartalmaz, amikor egy felhasználó hozzá van rendelve egy szerepkör a Workbenchben, például a szerepkör-hozzárendelést, valamint a szerepkör és a megfelelő alkalmazás nevét.

| Név | Leírás |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Az alkalmazás egyedi azonosítója az Azure Blockchain Workbenchben |
| applicationName | Alkalmazásnév |
| applicationDisplayName | Alkalmazás megjelenítendő neve |
| applicationVersion | Alkalmazás verziója |
| applicationRole        | Az [alkalmazás szerepkörre](#roleassignment-application-role) vonatkozó információk |
| hozzárendelő               | A [kiosztással](#roleassignment-assigner) kapcsolatos információk |
| megbízott               | A [megbízott](#roleassignment-assignee) információi |
| connectionId           | A kapcsolatok egyedi azonosítója |
| messageSchemaVersion   | Üzenetkezelési séma verziója |
| messageName            | **EventMessage** |
| additionalInformation  | További információk |

#### <a name="roleassignment-application-role"></a>RoleAssignment alkalmazás-szerepkör

| Név | Leírás |
|------|-------------|
| id | Az alkalmazás szerepkör egyedi azonosítója az Azure Blockchain Workbenchben |
| name | Az alkalmazás szerepkörének neve |

#### <a name="roleassignment-assigner"></a>RoleAssignment-hozzárendelés

| Név | Leírás |
|------|-------------|
| id | A felhasználó egyedi azonosítója az Azure Blockchain Workbenchben |
| típus | A hozzárendelő típusa |
| chainIdentifier | A felhasználó egyedi azonosítója a főkönyvben |

#### <a name="roleassignment-assignee"></a>RoleAssignment-megbízott

| Név | Leírás |
|------|-------------|
| id | A felhasználó egyedi azonosítója az Azure Blockchain Workbenchben |
| típus | A megbízott típusa |
| chainIdentifier | A felhasználó egyedi azonosítója a főkönyvben |

Példa EventMessage- *RoleAssignment* a Blockchain Workbenchből:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Következő lépések

- [Intelligens szerződések integrációs mintái](integration-patterns.md)
