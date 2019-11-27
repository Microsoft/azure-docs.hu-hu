---
title: Az Azure Blockchain Workbench REST API-k használata
description: Az Azure Blockchain Workbench előzetes verziójának használatának forgatókönyvei REST API
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 188bbb9a9f6d289a7950ff74596352dff36e79f2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324205"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Az Azure Blockchain Workbench előzetes verziójának használata REST API

Az Azure Blockchain Workbench előzetes verziójának REST API a fejlesztők és az informatikai szakemberek számára lehetővé teszi, hogy gazdag integrációkat építsen ki az alkalmazások Blockchain. Ez a dokumentum bemutatja, hogy hogyan használhatja a Workbench REST API-jának számos fő metódusát. Tegyük fel például, hogy egy fejlesztő egyéni blockchain-ügyfelet szeretne létrehozni. Ez a blockchain-ügyfél lehetővé teszi a bejelentkezett felhasználók számára, hogy megtekintsék és használják a hozzájuk rendelt blockchain-alkalmazásaikat. Az ügyféllel a felhasználók megtekinthetik a szerződéspéldányokat, és műveleteket végezhetnek az intelligens szerződéseken. Az ügyfél a Workbench REST APIt használja a bejelentkezett felhasználó környezetében a következő műveletek elvégzéséhez:

* Alkalmazások listázása
* Az egyes alkalmazások munkafolyamatainak listázása
* Az egyes munkafolyamatok intelligensszerződés-példányainak listázása
* Az egyes szerződések elérhető műveleteinek listázása
* Az egyes szerződések egyes műveleteinek végrehajtása

A forgatókönyvekben használt példa blockchain-alkalmazások [letölthetők a githubról](https://github.com/Azure-Samples/blockchain).

## <a name="blockchain-workbench-api-endpoint"></a>Blockchain Workbench API-végpont

Az Blockchain Workbench API-k egy végponton keresztül érhetők el az üzembe helyezéshez. Az API-végpont URL-címének beszerzése az üzemelő példányhoz:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza az **erőforráscsoportok**lehetőséget.
1. Válassza ki a telepített Blockchain Workbench erőforráscsoport nevét.
1. A **típus** oszlop fejlécének kiválasztásával betűrendbe rendezheti a listát típus szerint.
1. Két erőforrás van **app Service**típussal. Válassza ki **app Service** típusú erőforrást *az "* -API" utótaggal.
1. A App Service **áttekintésében**másolja az **URL-címet** , amely a telepített Blockchain Workbench API-végpontjának URL-címét jelöli.

    ![App Service API-végpont URL-címe](media/use-api/app-service-api.png)

## <a name="list-applications"></a>Alkalmazások listázása

Miután a felhasználó bejelentkezett a blockchain-ügyfélbe, az első feladat, hogy beolvassa az összes Blockchain Workbench alkalmazást a felhasználó számára. Ebben a példában a felhasználó két alkalmazáshoz tud hozzáférni:

1. [Eszközök átvitele](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Hűtött szállítás](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Használja az [Applications GET API-t](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

A válasz felsorolja az összes olyan blockchain-alkalmazást, amelyhez a felhasználó hozzáfér a Blockchain Workbenchben. A Blockchain Workbench-rendszergazdák minden Blockchain-alkalmazást megkapnak. A nem Workbench rendszergazdák megkapják az összes olyan blokkláncok, amelyhez legalább egy társított alkalmazási szerepkör vagy egy kapcsolódó intelligens szerződés-példány szerepkör tartozik.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Az egyes alkalmazások munkafolyamatainak listázása

Miután a felhasználó kiválasztja a megfelelő blockchain alkalmazást (például az **eszköz átvitelét**), a blockchain-ügyfél lekéri az adott blockchain-alkalmazás összes munkafolyamatát. A felhasználók ezután kiválaszthatják a megfelelő munkafolyamatot, majd láthatják az adott munkafolyamathoz tartozó összes intelligensszerződés-példányt. Minden egyes blokklánc-alkalmazás rendelkezik egy vagy több munkafolyamattal, és minden munkafolyamathoz tartozik nulla vagy több intelligensszerződés-példány. Olyan blockchain-ügyfélalkalmazás esetén, amelyeknek csak egy munkafolyamata van, javasoljuk, hogy hagyja ki a felhasználói élmény folyamatát, amely lehetővé teszi a felhasználók számára a megfelelő munkafolyamat kiválasztását. Ebben az esetben az **Asset-átvitelnek** csak egy munkafolyamata van, más néven az **eszközök átvitele**.

Használja az [Applications Workflows GET API-t](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

A válasz felsorolja az adott blokkláncalkalmazás összes olyan munkafolyamatát, amelyhez a felhasználó hozzáfér a Blockchain Workbenchben. A Blockchain Workbench-rendszergazdák minden Blockchain-munkafolyamatot megkapnak. A nem Workbench rendszergazdák minden olyan munkafolyamatot megkapnak, amelyhez legalább egy társított alkalmazási szerepkör tartozik, vagy egy intelligens szerződési példány szerepkörhöz vannak társítva.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Az egyes munkafolyamatok intelligensszerződés-példányainak listázása

Miután a felhasználó kiválasztja a megfelelő munkafolyamatot, ez az adategység- **átvitel**, a blockchain-ügyfél lekéri a megadott munkafolyamat összes intelligens szerződési példányát. Ezeket az információkat a munkafolyamat összes intelligens szerződési példányának megjelenítésére használhatja. Vagy lehetővé teheti a felhasználók számára, hogy a megjelenített intelligens szerződési példányok bármelyikére bemutassanak. A jelen példában tegyük fel, hogy egy felhasználó szeretne műveleteket végezni az intelligensszerződés-példányok egyikével.

Használja a [Contracts GET API-t](/rest/api/azure-blockchain-workbench/contractsv2/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

A válasz felsorolja az adott munkafolyamathoz tartozó összes intelligensszerződés-példányt. A Workbench-rendszergazdák az összes intelligens szerződési példányt megkapják. A nem Workbench rendszergazdák minden olyan intelligens szerződési példányt megkapnak, amelyhez legalább egy társított alkalmazási szerepkör tartozik, vagy egy intelligens szerződési példány szerepkörhöz van társítva.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Az egyes szerződések elérhető műveleteinek listázása

Ha a felhasználó úgy dönt, hogy részletesen belevetik a szerződést, a blockchain-ügyfél ezután megjelenítheti az elérhető felhasználói műveleteket a szerződés állapota alapján. Ebben a példában a felhasználó megtekinti egy frissen létrehozott intelligens szerződés összes elérhető műveletét:

* Módosítás: A felhasználó módosíthatja egy objektum leírását és árát.
* Megszakítás: lehetővé teszi a felhasználó számára az eszköz szerződésének befejezését.

Használja a [Contract Action GET API-t](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

A válasz felsorolja az összes olyan műveletet, amelyhez a felhasználó hozzáférhet az adott intelligensszerződés-példány aktuális állapotában. A felhasználók megkapják az összes elérhető műveletet, ha rendelkeznek alkalmazás-szerepkörrel vagy intelligensszerződés-példányi szerepkörrel az adott intelligensszerződés-példány aktuális állapotához.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Az egyes szerződések egyes műveleteinek végrehajtása

A felhasználó ezután eldöntheti, hogy milyen műveletet kíván végezni az adott intelligensszerződés-példányon. Ebben az esetben vegye figyelembe azt a forgatókönyvet, amelyben a felhasználó módosítani szeretné egy eszköz leírását és árát a következő művelethez:

* Leírás: „Az én frissített autóm”
* Ár: 543210

Használja a [Contract Action POST API-t](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost):

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

A felhasználók csak olyan műveleteket tudnak végrehajtani, amelyhez rendelkeznek alkalmazás-szerepkörrel vagy intelligensszerződés-példányi szerepkörrel az adott intelligensszerződés-példány aktuális állapotára vonatkozóan. Ha a POST művelet sikeres, HTTP 200 OK választ fog kapni választörzs nélkül.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench REST API leírása](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)