---
title: Blockchain-alkalmazás létrehozása – Azure Blockchain Workbench
description: Útmutató blockchain-alkalmazások létrehozásához az Azure Blockchain Workbench előzetes verziójához.
ms.date: 08/24/2020
ms.topic: tutorial
ms.reviewer: ravastra
ms.openlocfilehash: add790a069d2e0ea66d84bbd632825cf9331fd38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88784028"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>Oktatóanyag: blockchain-alkalmazás létrehozása az Azure Blockchain Workbenchhez

Az Azure Blockchain Workbench használatával olyan Blockchain-alkalmazásokat hozhat létre, amelyek a konfiguráció és az intelligens szerződés kód által meghatározott többrésztvevős munkafolyamatokat jelképezik.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Blockchain-alkalmazás konfigurálása
> * Intelligens szerződési kód létrehozása
> * Blockchain-alkalmazás hozzáadása a Blockchain Workbenchhez
> * Tagok hozzáadása a blockchain alkalmazáshoz

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Egy Blockchain Workbench üzembe helyezése. További információkért lásd az [Azure Blockchain Workbench üzembe helyezésével](deploy.md) kapcsolatos részleteket.
* Azure Active Directory a Blockchain Workbench-hez társított bérlő felhasználóit. További információ: [Azure ad-felhasználók hozzáadása az Azure Blockchain workbenchben](manage-users.md#add-azure-ad-users).
* Egy Blockchain Workbench rendszergazdai fiók. További információ: [Blockchain Workbench-rendszergazdák hozzáadása az Azure Blockchain workbenchben](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Hello, Blockchain!

Hozzon létre egy alapszintű alkalmazást, amelyben a kérelmező kérelmet küld, és a válaszadó választ küld a kérésnek.
Egy kérés lehet például a "Hello, how is?", és a válasz a következő lehet: "nagy vagyok!". A kérést és a választ is rögzíti a rendszer az alapul szolgáló blockchain.

Kövesse az alkalmazásfájlok létrehozásához szükséges lépéseket, vagy [töltse le a mintát a githubról](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Konfigurációs fájl

A konfigurációs metaadatok a blockchain alkalmazás magas szintű munkafolyamatait és interakciós modelljét határozzák meg. A konfigurációs metaadatok a blockchain alkalmazás munkafolyamat-szakaszainak és interakciós modelljének felelnek meg. A konfigurációs fájlok tartalmával kapcsolatos további információkért tekintse meg az [Azure Blockchain munkafolyamat-konfigurációjának referenciáját](configuration.md).

1. A kedvenc szerkesztőjében hozzon létre egy nevű fájlt `HelloBlockchain.json` .
2. Adja hozzá a következő JSON-t a blockchain alkalmazás konfigurációjának definiálásához.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Mentse a `HelloBlockchain.json` fájlt.

A konfigurációs fájl több szakaszt tartalmaz. Az egyes szakaszok részletei a következők:

### <a name="application-metadata"></a>Alkalmazás metaadatainak

A konfigurációs fájl kezdete az alkalmazással kapcsolatos információkat tartalmaz, beleértve az alkalmazás nevét és leírását.

### <a name="application-roles"></a>Alkalmazás-szerepkörök

Az alkalmazás szerepkörei szakasz azokat a felhasználói szerepköröket határozza meg, akik a blockchain alkalmazásban működhetnek vagy részt vehetnek. A különböző szerepköröket a funkciók alapján határozhatja meg. A kérelem-válasz forgatókönyvben különbséget tesz a kérelmező olyan entitása között, amely a kérelmeket és a válaszadót a válaszokat előállító entitásként állítja elő.

### <a name="workflows"></a>Munkafolyamatok

A munkafolyamatok határozzák meg a szerződés egy vagy több szakaszát és műveletét. A kérelem-válasz forgatókönyvben a munkafolyamat első fázisa (állapot) egy kérelmező (szerepkör) egy kérés (függvény) elküldéséhez műveletet (átmenetet) igényel. A következő szakasz (állapot) egy válaszadó (szerepkör) a válasz (függvény) elküldéséhez műveletet (átmenetet) igényel. Az alkalmazás munkafolyamata olyan tulajdonságokat, függvényeket és állapotokat tartalmazhat, amelyek a szerződések folyamatát írják le.

## <a name="smart-contract-code-file"></a>Intelligens szerződési kód fájlja

Az intelligens szerződések a blockchain alkalmazás üzleti logikáját jelentik. Az Blockchain Workbench jelenleg a Blockchain-főkönyvhez tartozó Ethereum támogatja. A Ethereum a [szilárdságot](https://solidity.readthedocs.io) programozási nyelvként használja az intelligens szerződések önérvényesítő üzleti logikájának írásához.

Az intelligens szerződések a Soliding-ben hasonlóak az objektumorientált nyelvekhez tartozó osztályokhoz. Minden szerződés tartalmaz állapotot és függvényeket az intelligens szerződés szakaszainak és műveleteinek megvalósításához.

A kedvenc szerkesztőjében hozzon létre egy nevű fájlt `HelloBlockchain.sol` .

### <a name="version-pragma"></a>Sorpragmákat verziója

Ajánlott eljárásként jelezze a megcélzott szilárdtest verzióját. A verzió megadásával elkerülhető a jövőbeli szilárdtest-verziókkal való inkompatibilitás.

Adja hozzá a következő verziójú sorpragmákat az `HelloBlockchain.sol` intelligens szerződés kódjának tetején.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Konfigurációs és intelligens szerződési kód kapcsolata

A Blockchain Workbench a konfigurációs fájl és az intelligens szerződés kódjának használatával hoz létre Blockchain-alkalmazást. Kapcsolat van a konfigurációban definiált és az intelligens szerződésben szereplő kóddal. Az alkalmazás létrehozásához a szerződés részleteit, a függvényeket, a paramétereket és a típusokat kell megfeleltetni. A Blockchain Workbench ellenőrzi a fájlokat az alkalmazás létrehozása előtt.

### <a name="contract"></a>Szerződés

Adja hozzá a **Szerződés** fejlécét az `HelloBlockchain.sol` intelligens szerződés kódját tartalmazó fájlhoz.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Állapotváltozók

Az állapot változói az egyes szerződési példányok állapotának értékét tárolják. A szerződésben szereplő állapot változóinak meg kell egyezniük a konfigurációs fájlban meghatározott munkafolyamat-tulajdonságokkal.

Adja hozzá az állapot változóit a szerződéséhez az `HelloBlockchain.sol` intelligens szerződés kódjának fájljában.

``` solidity
    //Set of States
    enum StateType { Request, Respond}

    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;

    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Konstruktor

A konstruktor a munkafolyamat új, intelligens szerződési példányának bemeneti paramétereit definiálja. A konstruktor kötelező paraméterei konstruktor paraméterként vannak definiálva a konfigurációs fájlban. A paraméterek száma, sorrendje és típusa mindkét fájlban egyeznie kell.

A konstruktor függvényben írjon minden olyan üzleti logikát, amelyet el szeretne végezni a szerződés létrehozása előtt. Például inicializálhatja az állapot változóit a kezdő értékekkel.

Adja hozzá a konstruktor függvényt a szerződéséhez az `HelloBlockchain.sol` intelligens szerződés kódjának fájljában.

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>Functions

A függvények az üzleti logika végrehajtható egységei egy adott szerződésen belül. A függvény kötelező paraméterei a konfigurációs fájlban a Function paraméterként vannak definiálva. A paraméterek száma, sorrendje és típusa mindkét fájlban egyeznie kell. A függvények a konfigurációs fájlban lévő Blockchain Workbench-munkafolyamatban lévő átmenetekhez vannak társítva. Az átmenet olyan művelet, amely az alkalmazás munkafolyamatainak a Szerződés által meghatározott következő szakaszába való áthelyezésre szolgál.

Írjon bármilyen üzleti logikát, amelyet végre szeretne hajtani a függvényben. Például egy állapotjelző változó értékének módosítása.

1. Adja hozzá a következő függvényeket a szerződéséhez az `HelloBlockchain.sol` intelligens szerződés kódjának fájljában.

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. Mentse az `HelloBlockchain.sol` intelligens szerződési kódot tartalmazó fájlt.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Blockchain-alkalmazás hozzáadása a Blockchain Workbenchhez

Ha blockchain-alkalmazást szeretne hozzáadni a Blockchain Workbenchhez, töltse fel a konfigurációs és az intelligens szerződés fájljait az alkalmazás definiálásához.

1. Egy böngészőben nyissa meg a Blockchain Workbench webcímet. `https://{workbench URL}.azurewebsites.net/`A webalkalmazás például a Blockchain Workbench telepítésekor jön létre. További információ a Blockchain Workbench-webcím megkereséséről: [Blockchain Workbench webes URL-](deploy.md#blockchain-workbench-web-url) címe
2. Jelentkezzen be [Blockchain Workbench-rendszergazdaként](manage-users.md#manage-blockchain-workbench-administrators).
3. Válassza az  >  **új** alkalmazások elemet. Megjelenik az **új alkalmazás** panel.
4. Válassza a **Szerződés-konfiguráció feltöltése**  >  **Tallózás** lehetőséget, hogy megkeresse a létrehozott konfigurációs fájl **HelloBlockchain.js** . A konfigurációs fájl automatikusan érvényesítve lesz. Az érvényesítési hibák megjelenítéséhez kattintson a **Megjelenítés** hivatkozásra. Az alkalmazás üzembe helyezése előtt javítsa ki az érvényesítési hibákat.
5. Válassza a **szerződéssablon feltöltése**  >  **Tallózás** lehetőséget a **HelloBlockchain. Sol** intelligens szerződés kódjának megkereséséhez. A rendszer automatikusan érvényesíti a kódot. Az érvényesítési hibák megjelenítéséhez kattintson a **Megjelenítés** hivatkozásra. Az alkalmazás üzembe helyezése előtt javítsa ki az érvényesítési hibákat.
6. Válassza a **telepítés** lehetőséget a blockchain alkalmazás létrehozásához a konfiguráció és az intelligens szerződés fájljai alapján.

A blockchain alkalmazás üzembe helyezése néhány percet vesz igénybe. Az üzembe helyezés befejezésekor az új alkalmazás megjelenik az **alkalmazásokban**. 

> [!NOTE]
> A blockchain-alkalmazásokat az [Azure Blockchain Workbench REST API](/rest/api/azure-blockchain-workbench)használatával is létrehozhatja.

## <a name="add-blockchain-application-members"></a>Blockchain-alkalmazás tagjainak hozzáadása

Alkalmazások tagjainak hozzáadása az alkalmazáshoz a szerződések elindításához és a műveletek elvégzéséhez. Az alkalmazás tagjainak hozzáadásához [Blockchain Workbench rendszergazdának](manage-users.md#manage-blockchain-workbench-administrators)kell lennie.

1. Válassza   >  **a Hello, Blockchain!** alkalmazások elemet.
2. Az alkalmazáshoz társított tagok száma az oldal jobb felső sarkában jelenik meg. Új alkalmazás esetén a tagok száma nulla lesz.
3. Válassza ki a **tagok** hivatkozást az oldal jobb felső sarkában. Megjelenik az alkalmazás tagjainak aktuális listája.
4. A tagság listán válassza a **Tagok hozzáadása** elemet.
5. Válassza ki vagy adja meg a hozzáadni kívánt tag nevét. Csak az Blockchain Workbench-bérlőben található Azure AD-felhasználók jelennek meg. Ha a felhasználó nem található, [fel kell vennie az Azure ad-felhasználókat](manage-users.md#add-azure-ad-users).
6. Válassza ki a tag **szerepkörét** . Az első tag esetében válassza a **kérelmező** szerepkört.
7. A **Hozzáadás** gombra kattintva adja hozzá a tagot a társított szerepkörrel az alkalmazáshoz.
8. Adjon hozzá egy másik tagot az alkalmazáshoz a **válaszadó** szerepkörrel.

A felhasználók Blockchain Workbenchben való kezelésével kapcsolatos további információkért lásd: [felhasználók kezelése az Azure Blockchain workbenchben](manage-users.md)

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban egy alapszintű kérést és egy gyorsreagáló alkalmazást hozott létre. Az alkalmazás használatának megismeréséhez folytassa a következő útmutatóval.

> [!div class="nextstepaction"]
> [Blockchain-alkalmazás használata](use.md)
