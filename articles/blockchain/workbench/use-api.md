---
title: Az Azure Blockchain Workbench REST API-k használata
description: Az Azure Blockchain Workbench előzetes verziójának használatának forgatókönyvei REST API
ms.date: 03/05/2020
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 696f1f2f96034f7a044f6a39182774c02804518f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004840"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Az Azure Blockchain Workbench előzetes verziójának használata REST API

Az Azure Blockchain Workbench előzetes verziójának REST API a fejlesztők és az informatikai szakemberek számára lehetővé teszi, hogy gazdag integrációkat építsen ki az alkalmazások Blockchain. Ez a cikk a Workbench REST API használatának számos forgatókönyvét ismerteti. Tegyük fel például, hogy olyan egyéni blockchain-ügyfelet szeretne létrehozni, amely lehetővé teszi a bejelentkezett felhasználók számára a hozzárendelt blockchain-alkalmazások megtekintését és kezelését. Az ügyfél a Blockchain Workbench API használatával megtekintheti a szerződések példányait, és műveleteket végezhet az intelligens szerződéseken.

## <a name="blockchain-workbench-api-endpoint"></a>Blockchain Workbench API-végpont

Az Blockchain Workbench API-k egy végponton keresztül érhetők el az üzembe helyezéshez. Az API-végpont URL-címének beszerzése az üzemelő példányhoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza az **erőforráscsoportok** lehetőséget.
1. Válassza ki a telepített Blockchain Workbench erőforráscsoport nevét.
1. A **típus** oszlop fejlécének kiválasztásával betűrendbe rendezheti a listát típus szerint.
1. Két erőforrás van **app Service** típussal. Válassza ki **app Service** típusú erőforrást *az "* -API" utótaggal.
1. A App Service **áttekintésében** másolja az **URL-címet** , amely a telepített Blockchain Workbench API-végpontjának URL-címét jelöli.

    ![App Service API-végpont URL-címe](media/use-api/app-service-api.png)

## <a name="authentication"></a>Hitelesítés

Az Blockchain Workbench REST APIra irányuló kérelmeket Azure Active Directory (Azure AD) védi.

Ahhoz, hogy a REST API-khoz hitelesített kérelmet lehessen készíteni, az ügyfél kódjának az API meghívása előtt érvényes hitelesítő adatokkal kell rendelkeznie a hitelesítéshez. A hitelesítés a különböző szereplők által az Azure AD-ben van koordinálva, és a hitelesítést igazoló [hozzáférési jogkivonattal](../../active-directory/develop/developer-glossary.md#access-token) látja el az ügyfelet. Ezután a jogkivonat a REST API kérelmek HTTP-engedélyezési fejlécében lesz elküldve. Az Azure AD-hitelesítéssel kapcsolatos további tudnivalókért tekintse meg [a Azure Active Directory fejlesztők számára](../../active-directory/develop/index.yml)című témakört.

A hitelesítéssel kapcsolatos példákért tekintse meg [REST API mintákat](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) .

## <a name="using-postman"></a>A Postman használata

Ha a Workbench API-kkal szeretne tesztelni vagy kísérletezni, a [Poster](https://www.postman.com) használatával API-hívásokat indíthat az üzembe helyezéshez. [Töltse le a WORKBENCH API-kérések mintájának Poster-gyűjteményét](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) a githubról. A példa API-kérések hitelesítésével és használatával kapcsolatos részletekért tekintse meg a README fájlt.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Két API-hívást használ egy Blockchain Workbench-alkalmazás létrehozásához. Ezt a metódust csak Workbench-rendszergazda felhasználók végezhetik el.

Az alkalmazások JSON-fájljának feltöltéséhez és az alkalmazás AZONOSÍTÓjának beszerzéséhez használja az API-k [közzététele](/rest/api/azure-blockchain-workbench/applications/applicationspost) lehetőséget.

### <a name="applications-post-request"></a>Alkalmazások POST kérelem

A **appFile** paraméter segítségével küldje el a konfigurációs fájlt a kérelem törzsének részeként.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Alkalmazások utáni válasz

A rendszer a válaszban visszaadja a létrehozott alkalmazás AZONOSÍTÓját. A következő API meghívásakor szüksége lesz az alkalmazás AZONOSÍTÓJÁRA, hogy társítsa a konfigurációs fájlt a fájlhoz.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Szerződési kód POST kérelem

Használja az Applications [SZERZŐDÉSSABLON post API](/rest/api/azure-blockchain-workbench/applications/contractcodepost) -ját az alkalmazás-azonosító feltöltésével az alkalmazás szilárdsági kódjának feltöltéséhez. A hasznos adatok lehetnek egyetlen szilárdtest-fájlok vagy a szilárdtest fájlokat tartalmazó tömörített fájlok.

Cserélje le a következő értékeket:

| Paraméter | Érték |
|-----------|-------|
| ApplicationId | Visszatérési érték az alkalmazások utáni API-ból. |
| {ledgerId} | A Főkönyv indexe. Az érték általában 1. Az értékhez tartozó [Főkönyvi táblázatot](data-sql-management-studio.md) is megtekintheti. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Szerződés kódja POST Response

Ha ez sikeres, a válasz tartalmazza a létrehozott szerződés kódjának AZONOSÍTÓját a [ContractCode táblából](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Szerepkörök hozzárendelése felhasználókhoz

Az [alkalmazások szerepkör-hozzárendelések közzététele API](/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) -val az alkalmazás-azonosító, a felhasználói azonosító és az alkalmazás-szerepkör azonosítója segítségével hozzon létre egy felhasználó – szerepkör leképezést a megadott blockchain alkalmazásban. Ezt a metódust csak Workbench-rendszergazda felhasználók végezhetik el.

### <a name="role-assignments-post-request"></a>Szerepkör-hozzárendelések POST kérelem

Cserélje le a következő értékeket:

| Paraméter | Érték |
|-----------|-------|
| ApplicationId | Visszatérési érték az alkalmazások utáni API-ból. |
| UserId | Felhasználói azonosító értéke a [felhasználói táblából](data-sql-management-studio.md). |
| {applicationRoleId} | Az alkalmazás-szerepkör AZONOSÍTÓjának értéke a [ApplicationRole tábla](data-sql-management-studio.md)alkalmazás-azonosítójával. |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Szerepkör-hozzárendelések POST Response

Ha ez sikeres, a válasz tartalmazza a létrehozott szerepkör-hozzárendelési azonosítót a [RoleAssignment táblából](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Alkalmazások listázása

Az [alkalmazások letöltése API](/rest/api/azure-blockchain-workbench/applications/applicationsget) -val lekérheti az összes Blockchain Workbench-alkalmazást a felhasználó számára. Ebben a példában a bejelentkezett felhasználó két alkalmazáshoz fér hozzá:

- [Objektumok átvitele](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Hűtött szállítás](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Alkalmazások GET kérelem

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Az alkalmazások választ kapnak

A válasz felsorolja az összes olyan blockchain-alkalmazást, amelyhez a felhasználó hozzáfér a Blockchain Workbenchben. A Blockchain Workbench-rendszergazdák minden Blockchain-alkalmazást megkapnak. A nem Workbench rendszergazdák lekérik az összes olyan blockchain-alkalmazást, amelyhez legalább egy társított alkalmazási szerepkör vagy egy kapcsolódó intelligens szerződés-példány szerepkör tartozik.

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

Az [alkalmazások munkafolyamatainak BEolvasása API](/rest/api/azure-blockchain-workbench/applications/workflowsget) -val egy adott blockchain-alkalmazás összes munkafolyamatát listázhatja, amelyhez a felhasználó hozzáfér a blockchain workbenchben. Minden egyes blokklánc-alkalmazás rendelkezik egy vagy több munkafolyamattal, és minden munkafolyamathoz tartozik nulla vagy több intelligensszerződés-példány. Olyan blockchain-ügyfélalkalmazás esetén, amelyeknek csak egy munkafolyamata van, javasoljuk, hogy hagyja ki a felhasználói élmény folyamatát, amely lehetővé teszi a felhasználók számára a megfelelő munkafolyamat kiválasztását.

### <a name="application-workflows-request"></a>Alkalmazás-munkafolyamatok kérelme

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Alkalmazás-munkafolyamatok válasza

A Blockchain Workbench-rendszergazdák minden Blockchain-munkafolyamatot megkapnak. A nem Workbench rendszergazdák minden olyan munkafolyamatot megkapnak, amelyhez legalább egy társított alkalmazási szerepkör tartozik, vagy egy intelligens szerződési példány szerepkörhöz vannak társítva.

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

## <a name="create-a-contract-instance"></a>Szerződési példány létrehozása

A [szerződések v2 utáni API](/rest/api/azure-blockchain-workbench/contractsv2/contractpost) -t használva hozzon létre egy új, intelligens szerződési példányt egy munkafolyamathoz. A felhasználók csak akkor hozhatnak létre új intelligens szerződési példányt, ha a felhasználó egy alkalmazás-szerepkörhöz van társítva, amely a munkafolyamathoz intelligens szerződési példányt indíthat.

> [!NOTE]
> Ebben a példában a rendszer az API 2-es verzióját használja. A 2. verziójú szerződések API-jai részletesebben biztosítják a társított ProvisioningStatus mezőket.

### <a name="contracts-post-request"></a>Szerződések POST kérelem

Cserélje le a következő értékeket:

| Paraméter | Érték |
|-----------|-------|
| WorkflowId | A munkafolyamat-azonosító értéke a szerződés ConstructorID a [munkafolyamat-táblából](data-sql-management-studio.md). |
| {contractCodeId} | A [ContractCode táblához](data-sql-management-studio.md)tartozó SZERZŐDÉSSABLON-azonosító érték. Korrelálja az alkalmazás AZONOSÍTÓját és a létrehozni kívánt szerződés-példányhoz tartozó főkönyvi azonosítót. |
| ConnectionId | A kapcsolatok AZONOSÍTÓjának értéke a [kapcsolatok táblából](data-sql-management-studio.md). |

A kérelem törzse esetében az alábbi információk alapján állítsa be az értékeket:

| Paraméter | Érték |
|-----------|-------|
| workflowFunctionID | AZONOSÍTÓ a [WorkflowFunction táblából](data-sql-management-studio.md). |
| workflowActionParameters | A konstruktornak átadott paraméterek neve érték párok. Minden paraméternél használja a workflowFunctionParameterID értéket a [WorkflowFunctionParameter](data-sql-management-studio.md) táblából. |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Szerződések POST Response

Ha a művelet sikeres, a szerepkör-hozzárendelések API visszaadja a ContractActionID a [ContractActionParameter táblából](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Az egyes munkafolyamatok intelligensszerződés-példányainak listázása

A [szerződések](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) használatával az API-t a munkafolyamatok összes intelligens szerződési példányának megjelenítéséhez használhatja. Vagy lehetővé teheti a felhasználók számára, hogy a megjelenített intelligens szerződési példányok bármelyikére bemutassanak.

### <a name="contracts-request"></a>Szerződések kérése

A jelen példában tegyük fel, hogy egy felhasználó szeretne műveleteket végezni az intelligensszerződés-példányok egyikével.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Szerződések válasza

A válasz felsorolja a megadott munkafolyamat összes intelligens szerződési példányát. A Workbench-rendszergazdák az összes intelligens szerződési példányt megkapják. A nem Workbench rendszergazdák minden olyan intelligens szerződési példányt megkapnak, amelyhez legalább egy társított alkalmazási szerepkör tartozik, vagy egy intelligens szerződési példány szerepkörhöz van társítva.

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

A szerződés állapota alapján az elérhető felhasználói műveletek megjelenítéséhez használja a [szerződéses művelet BEolvasása API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) -t. 

### <a name="contract-action-request"></a>Szerződésre vonatkozó műveleti kérelem

Ebben a példában a felhasználó az összes elérhető műveletet megtekinti egy új intelligens szerződés létrehozásához.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Szerződési művelet válasza

A válasz felsorolja az összes olyan műveletet, amelyhez a felhasználó hozzáférhet az adott intelligensszerződés-példány aktuális állapotában.

* Módosítás: A felhasználó módosíthatja egy objektum leírását és árát.
* Megszakítás: lehetővé teszi a felhasználó számára az eszköz szerződésének befejezését.

A felhasználók megkapják az összes elérhető műveletet, ha rendelkeznek alkalmazás-szerepkörrel vagy intelligensszerződés-példányi szerepkörrel az adott intelligensszerződés-példány aktuális állapotához.

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

A megadott intelligens szerződési példányra vonatkozó művelet végrehajtásához használja az API-t az [egyezményes művelet után](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) .

### <a name="contract-action-post-request"></a>Szerződési művelet POST kérelem

Ebben az esetben vegye figyelembe azt a forgatókönyvet, amelyben a felhasználó módosítani szeretné egy eszköz leírását és árát.

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

A felhasználók csak olyan műveleteket tudnak végrehajtani, amelyhez rendelkeznek alkalmazás-szerepkörrel vagy intelligensszerződés-példányi szerepkörrel az adott intelligensszerződés-példány aktuális állapotára vonatkozóan.

### <a name="contract-action-post-response"></a>Szerződési művelet POST Response

Ha a POST művelet sikeres, HTTP 200 OK választ fog kapni választörzs nélkül.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Következő lépések

Az Blockchain Workbench API-kkal kapcsolatos tudnivalókat az [Azure Blockchain Workbench REST API dokumentációjában](/rest/api/azure-blockchain-workbench)talál.
