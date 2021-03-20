---
title: A Ethereum Blockchain-összekötő használata a Azure Logic Apps-Azure Blockchain szolgáltatással
description: A Ethereum Blockchain-összekötővel a Azure Logic Apps használatával aktiválhatja az intelligens szerződési funkciókat, és reagálhat az intelligens szerződési eseményekre.
ms.date: 08/31/2020
ms.topic: how-to
ms.reviewer: caleteet
ms.openlocfilehash: 411337908553e58c252a0ed1a42d17f76195c720
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763791"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>A Ethereum Blockchain-összekötő használata a Azure Logic Apps

Az Ethereum Blockchain- [Azure Logic apps](../../logic-apps/index.yml) [összekötővel](/connectors/blockchainethereum/) intelligens szerződéses műveleteket hajthat végre, és reagálhat az intelligens szerződési eseményekre. Ez a cikk azt ismerteti, hogy miként lehet a Ethereum Blockchain-összekötővel Blockchain adatokat küldeni egy másik szolgáltatásnak, vagy meghívhat egy Blockchain-függvényt. Tegyük fel például, hogy olyan REST-alapú szolgáltatást szeretne létrehozni, amely egy blockchain-főkönyvből származó információkat ad vissza. Logikai alkalmazás használatával elfogadhatja azokat a HTTP-kérelmeket, amelyek a blockchain-főkönyvben tárolt információkat kérdezik le.

## <a name="prerequisites"></a>Előfeltételek

- Fejezze be az előfeltételként szükséges útmutatót [: a Visual Studio Code használatával csatlakozhat egy Azure Blockchain Service Consortium-hálózathoz](connect-vscode.md). A rövid útmutató végigvezeti a [Ethereum készült Azure Blockchain Development Kit](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) telepítésének és a Blockchain-fejlesztési környezet beállításának lépésein.
- Ha még nem Azure Logic Apps, érdemes áttekinteni a Microsoft Learn modulok [Bevezetését Azure Logic apps](/learn/modules/intro-to-logic-apps/) és egy [API-t meghívni egy Logic apps munkafolyamatból egy egyéni összekötő használatával](/learn/modules/logic-apps-and-custom-connectors/).

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

A Azure Logic Apps segítségével az üzleti folyamatok és munkafolyamatok ütemezhetők és automatizálható, ha a rendszerek és szolgáltatások integrálására van szükség. Először létre kell hoznia egy olyan logikát, amely a Ethereum Blockchain-összekötőt használja.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Integráció** > **Logikai alkalmazás** lehetőséget.
1. A **logikai alkalmazás létrehozása** területen adja meg a logikai alkalmazás létrehozási helyének részleteit. Ha elkészült, válassza a **Létrehozás** lehetőséget.

    A logikai alkalmazások létrehozásával kapcsolatos további információkért lásd: [automatizált munkafolyamatok létrehozása Azure Logic Appsokkal](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Miután az Azure üzembe helyezte az alkalmazást, válassza ki a logikai alkalmazás erőforrását.
1. A Logic Apps Designer **sablonok** területén válassza az **üres logikai alkalmazás** lehetőséget.

Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. A Logic Apps-motor az eseményindító minden elindulásakor létrehoz egy logikaialkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot.

A Ethereum Blockchain-összekötő egyetlen triggerrel és számos művelettel rendelkezik. A használt trigger vagy művelet a forgatókönyvtől függ. Kövesse a jelen cikk című szakaszát, amely a legjobban megfelel a forgatókönyvének.

Ha a munkafolyamat:

* Eseményindítót [használ](#use-the-event-trigger), ha a blockchain esemény történik.
* Egy intelligens szerződés lekérdezése vagy üzembe helyezése, [műveletek használata](#use-actions).
* Egy gyakori forgatókönyvet követ, amely [létrehoz egy munkafolyamatot a fejlesztői készlet használatával](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Az esemény eseményindítójának használata

Akkor használja a Ethereum Blockchain-eseményindítókat, ha azt szeretné, hogy egy logikai alkalmazás egy intelligens szerződési esemény után fusson. Tegyük fel például, hogy e-mailt szeretne küldeni egy intelligens szerződési függvény meghívásakor.

1. A Logic Apps Designerben válassza ki a Ethereum Blockchain-összekötőt.
1. Az **Eseményindítók** lapon válassza ki, **hogy mikor történjen egy intelligens szerződési esemény**.
1. Módosítsa vagy [hozzon létre egy API-kapcsolódást](#create-an-api-connection) az Azure Blockchain szolgáltatáshoz.
1. Adja meg az események kereséséhez használni kívánt intelligens szerződés részleteit.

    ![Logic Apps Designer az Event trigger tulajdonságaival](./media/ethereum-logic-app/event-properties.png)

    | Tulajdonság | Leírás |
    |----------|-------------|
    | **Egyezményes ABI** | A szerződéses alkalmazás bináris felülete (ABI) meghatározza az intelligens szerződési felületeket. További információ: [Get the szerződés ABI](#get-the-contract-abi). |
    | **Intelligens Szerződés címe** | A Szerződés címe az intelligens szerződési cél címe a Ethereum blockchain. További információ: [a szerződési címek beszerzése](#get-the-contract-address). |
    | **Esemény neve** | Válasszon ki egy intelligens szerződési eseményt az ellenõrzéshez. Az esemény elindítja a logikai alkalmazást. |
    | **Intervallum** és **gyakoriság** | Válassza ki, hogy milyen gyakran szeretné megkeresni az eseményt. |

1. Kattintson a **Mentés** gombra.

A logikai alkalmazás befejezéséhez hozzáadhat egy új lépést, amely a Ethereum Blockchain esemény eseményindítóján alapuló műveletet hajt végre. Küldjön például egy e-mailt.

## <a name="use-actions"></a>Műveletek használata

Használja a Ethereum Blockchain műveleteit, ha azt szeretné, hogy egy logikai alkalmazás végrehajtson egy műveletet a Blockchain-főkönyvben. Létrehozhat például egy olyan REST-alapú szolgáltatást, amely intelligens szerződési funkciót hív meg, ha egy logikai alkalmazás HTTP-kérést végez.

Az összekötő műveleteihez trigger szükséges. A Ethereum Blockchain-összekötő műveletét a következő lépésként használhatja egy trigger után, például egy HTTP-kérelem-triggert a szolgáltatáshoz.

1. A Logic Apps Designerben válasszon egy triggert követő **új lépést** .
1. Válassza ki a Ethereum Blockchain-összekötőt.
1. A **műveletek** lapon válassza ki az elérhető műveletek egyikét.

    ![Logic Apps Designer with Actions Properties](./media/ethereum-logic-app/action-properties.png)

1. Módosítsa vagy [hozzon létre egy API-kapcsolódást](#create-an-api-connection) az Azure Blockchain szolgáltatáshoz.
1. A választott művelettől függően adja meg az intelligens szerződés funkciójának következő részleteit.

    | Tulajdonság | Leírás |
    |----------|-------------|
    | **Egyezményes ABI** | A szerződési ABI definiálja az intelligens szerződési felületeket. További információ: [Get the szerződés ABI](#get-the-contract-abi). |
    | **Szerződés bytecode** | A lefordított intelligens szerződés bytecode. További információ: [a szerződés bytecode beszerzése](#get-the-contract-bytecode). |
    | **Intelligens Szerződés címe** | A Szerződés címe az intelligens szerződési cél címe a Ethereum blockchain. További információ: [a szerződési címek beszerzése](#get-the-contract-address). |
    | **Intelligens szerződés függvényének neve** | Válassza ki az intelligens szerződés funkciójának nevét a művelethez. A lista a szerződési ABI-ban található részletekből van feltöltve. |

    Az intelligens szerződési függvény nevének kiválasztása után megjelenhetnek a függvények paramétereinek kötelező mezői. Adja meg a forgatókönyvhöz szükséges értékeket vagy dinamikus tartalmat.

Ezután használhatja a logikai alkalmazást. A Logic app-esemény elindítása után a Ethereum Blockchain művelet fut. Egy HTTP-kérési trigger például egy Ethereum blockchain műveletet futtat egy intelligens szerződési állapot lekérdezéséhez. Ez a lekérdezés egy HTTP-választ eredményez, amely visszaadja az értéket.

## <a name="generate-a-workflow"></a>Munkafolyamat létrehozása

A Visual Studio Code Ethereum készült Azure Blockchain Development Kit képes logikai alkalmazás-munkafolyamatokat előállítani a gyakori forgatókönyvekhez. Négy forgatókönyv érhető el:

* Adatközzététel Azure SQL Database-példányba
* Esemény-közzététel Azure Event Grid vagy Azure Service Bus egy példányához
* Jelentés közzététele
* REST-alapú szolgáltatás

 Az Azure Blockchain Development Kit a szarvasgomba segítségével egyszerűsíti a Blockchain fejlesztését. Egy intelligens szerződésen alapuló logikai alkalmazás létrehozásához szükség van egy szarvasgombás-megoldásra az intelligens szerződéshez. Az Azure Blockchain Service Consortium-hálózathoz is csatlakoznia kell. További információ: a [Visual Studio Code használata az Azure Blockchain Service Consortium Network rövid útmutatóhoz való kapcsolódáshoz](connect-vscode.md).

Például a következő lépések egy REST-alapú, a gyors üzembe helyezési **HelloBlockchain** intelligens szerződésen alapuló Service Logic app-alkalmazást hoznak elő:

1. A Visual Studio Code Explorer oldalsávján bontsa ki a **szerződések** mappát a megoldásban.
1. Kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, és válassza az **intelligens szerződések előállítása** a menüből lehetőséget.

    ![Visual Studio Code ablaktábla az intelligens szerződések kiválasztására szolgáló Service-sel](./media/ethereum-logic-app/generate-logic-app.png)

1. A parancs palettáján válassza a **logikai alkalmazás** lehetőséget.
1. Adja meg a **szerződési címeket**. További információ: [a szerződési címek beszerzése](#get-the-contract-address).
1. Válassza ki a logikai alkalmazáshoz tartozó Azure-előfizetést és erőforráscsoportot.

    A Logic app-konfiguráció és a kód fájljai a **generatedLogicApp** könyvtárban jönnek létre.

1. Tekintse meg a **generatedLogicApp/HelloBlockchain** könyvtárat. Minden egyes intelligens szerződési funkció, esemény és tulajdonság esetében létezik egy logikai alkalmazás JSON-fájlja.
1. Nyissa meg a **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.js** fájlt, és másolja a tartalmat.

    ![A másolandó kódot tartalmazó JSON-fájl](./media/ethereum-logic-app/requestmessage.png)

1. A logikai alkalmazásban válassza a **logikai alkalmazás kódjának megtekintése** lehetőséget. Cserélje le a meglévő JSON-t a generált Logic app JSON-ra.

    ![Logikai alkalmazás kódjának nézete új lecserélt alkalmazás kódjával](./media/ethereum-logic-app/code-view.png)

1. Válassza ki a **tervezőt** a Tervező nézetre váltáshoz.
1. A logikai alkalmazás a forgatókönyv alapvető lépéseit tartalmazza. Azonban frissítenie kell a Ethereum Blockchain-összekötő konfigurációs adatait.
1. Válassza a **kapcsolatok** lépést, és módosítsa vagy [hozzon létre egy API-kapcsolatot](#create-an-api-connection) az Azure Blockchain szolgáltatással.

    ![Tervező nézet a kapcsolatok kijelölésével](./media/ethereum-logic-app/microservice-logic-app.png)

1. Ezután használhatja a logikai alkalmazást. A REST-alapú webszolgáltatás teszteléséhez adjon ki egy HTTP POST-kérelmet a logikai alkalmazás kérésének URL-címére. Másolja a **http-post URL-** tartalmát a **http-kérelem fogadása** lépésből.

    ![Logic Apps Designer panel a HTTP POST URL-címmel](./media/ethereum-logic-app/post-url.png)

1. Hozzon létre egy HTTP POST-kérelmet a cURL használatával. Cserélje le a helyőrző szövegét az *\<HTTP POST URL\>* előző lépés URL-címére.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    A cURL parancs a logikai alkalmazás válaszát adja vissza. Ebben az esetben a válasz a **RequestMessage** intelligens szerződési függvény kimenete.

    ![Kód kimenete a RequestMessage intelligens szerződés függvényből](./media/ethereum-logic-app/curl.png)

A fejlesztői csomag használatával kapcsolatos további információkért tekintse meg az [Azure Blockchain Development Kit for Ethereum wiki oldalt](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>API-kapcsolatok létrehozása

A Ethereum Blockchain-összekötőhöz egy blockchain API-csatlakozás szükséges. Az API-összekötő több logikai alkalmazáshoz is használható. Bizonyos tulajdonságok szükségesek, mások pedig a forgatókönyvtől függenek.

> [!IMPORTANT]
> A blockchain tranzakcióinak létrehozásához titkos kulcs vagy fiók címe és jelszava szükséges. Csak egy hitelesítési űrlapra van szükség. Nem kell megadnia a titkos kulcs és a fiók adatait is. A szerződések lekérdezéséhez nincs szükség tranzakcióra. Ha a szerződési állapotot lekérdező műveleteket használ, a titkos kulcs vagy a fiók címe és jelszava nem szükséges.

Ha segítségre van szüksége az Azure Blockchain-szolgáltatáshoz való kapcsolódáshoz, az alábbi listában a forgatókönyvtől függően lehetséges tulajdonságok lehetnek.

| Tulajdonság | Leírás |
|----------|-------------|
|**Kapcsolatok neve** | Az API-kapcsolatok neve. Kötelező. |
|**Ethereum RPC-végpont** | Az Azure Blockchain szolgáltatás tranzakciós csomópontjának HTTP-címe. Kötelező. További információ: [az RPC-végpont beszerzése](#get-the-rpc-endpoint). |
|**Titkos kulcs** | Ethereum fiók titkos kulcsa. A tranzakcióhoz titkos kulcs vagy fiók címe és jelszava szükséges. További információ: [Get the Private Key (titkos kulcs beszerzése](#get-the-private-key)). |
|**Fiók címe** | Az Azure Blockchain-szolgáltatási tag fiókjának címe. A tranzakcióhoz titkos kulcs vagy fiók címe és jelszava szükséges. További információkért lásd: [a fiók címe beszerzése](#get-the-account-address). |
|**Fiók jelszava** | A fiók jelszava a tag létrehozásakor van beállítva. A jelszó alaphelyzetbe állításával kapcsolatos információkért lásd: [Ethereum-fiók](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Az RPC-végpont beszerzése

Az Azure Blockchain szolgáltatás RPC-végpontjának címe Blockchain hálózathoz való kapcsolódáshoz szükséges. A végpont címe az Azure Blockchain Development Kit for Ethereum vagy a Azure Portal használatával szerezhető be.

**A fejlesztői csomag használata:**

1. Az **Azure Blockchain Service** -ben a Visual Studio Code-ban kattintson a jobb gombbal a konzorciumra.
1. Válassza az **RPC-végponti címek másolása** lehetőséget.

    ![A Visual Studio Code ablaktábla, amely a konzorciumot mutatja az RPC-végpontok másolásának kiválasztásával](./media/ethereum-logic-app/devkit-rpc.png)

    A rendszer átmásolja az RPC-végpontot a vágólapra.

**A Azure Portal használata:**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az Azure Blockchain-szolgáltatás tagját. Válassza a **tranzakciós csomópontok** és az alapértelmezett tranzakció csomópontja hivatkozást.

    ![Tranzakciós csomópontok lap (alapértelmezett csomópont) kijelölése](./media/ethereum-logic-app/transaction-nodes.png)

1. Válassza a **kapcsolati karakterláncok**  >  **hozzáférési kulcsok** elemet.
1. Másolja a végponti címeket a https-ről **(1. hozzáférési kulcs)** vagy a https-re **(2. hozzáférési kulcs)**.

    ![Azure Portal a kapcsolati karakterlánc elérési kulcsaival](./media/ethereum-logic-app/connection-string.png)

    Az RPC-végpont a HTTPS URL-cím, amely tartalmazza az Azure Blockchain-szolgáltatás tagjának tranzakciós csomópontjának címét és hozzáférési kulcsát.

## <a name="get-the-private-key"></a>A titkos kulcs beszerzése

A Ethereum-fiók titkos kulcsával hitelesítheti a tranzakciókat a blockchain. A Ethereum-fiók nyilvános és titkos kulcsai egy 12 szavas hívóbetűje jönnek létre. A Ethereum készült Azure Blockchain Development Kit létrehoz egy hívóbetűje az Azure Blockchain Service Consortium egyik tagjához való csatlakozáskor. A végpont címe a fejlesztői csomag bővítményének használatával szerezhető be.

1. A Visual Studio Code-ban nyissa meg a Command paletta (F1) parancsot.
1. Válassza a **Blockchain: titkos kulcs lekérése** elemet.
1. Válassza ki azt a hívóbetűje, amelyet a konzorcium tagjához való csatlakozáskor mentett.

    ![A hívóbetűje kiválasztását biztosító parancssori paletta](./media/ethereum-logic-app/private-key.png)

    A titkos kulcsot a rendszer a vágólapra másolja.

## <a name="get-the-account-address"></a>Fiók címe beolvasása

Ha tranzakciót küld a blockchain, a fiók és a jelszó használatával hitelesítheti a hitelesítést. A jelszó beállítása a tag létrehozásakor történik.

1. A Azure Portal nyissa meg az Azure Blockchain szolgáltatás áttekintés lapját.
1. Másolja a **tag fiókjának** a címeit.

    ![Áttekintő lap a tag fiók címével](./media/ethereum-logic-app/member-account.png)

A fiókra és a jelszóra vonatkozó további információkért lásd: [Ethereum-fiók](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>A szerződési ABI beszerzése

A szerződési ABI definiálja az intelligens szerződési felületeket. Leírja, hogyan kezelheti az intelligens szerződést. A szerződési ABI-t a Ethereum készült Azure Blockchain Development Kit használatával szerezheti be. Azt is megteheti, hogy a Solidion compiler által létrehozott szerződési metaadatokat tartalmazó fájlból származik.

**A fejlesztői csomag használata:**

Ha a fejlesztői készletet vagy a szarvasgombát használta az intelligens szerződés kiépítéséhez, a bővítmény használatával másolhatja át a szerződéses ABI-t a vágólapra.

1. A Visual Studio Code Explorer ablaktáblán bontsa ki a szilárdtest-projekt **Build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadatainak JSON-fájljára. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Válassza a **Szerződés másolása ABI** elemet.

    ![A Visual Studio Code panel és a másolási szerződés ABI kiválasztása](./media/ethereum-logic-app/abi-devkit.png)

    A rendszer átmásolja a szerződéses ABI-t a vágólapra.

**A szerződés metaadatainak fájljának használata:**

1. Nyissa meg a konszolidációs projekt **Build/szerződések** mappájában található szerződéses metaadat-fájlt. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Keresse meg az **ABI** SZAKASZT a JSON-fájlban.
1. Másolja az **ABI** JSON-tömböt.

    ![ABI-kód a szerződés metaadatainak fájljában](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>A szerződés bytecode beszerzése

A szerződés bytecode a Ethereum virtuális gép által végrehajtott lefordított intelligens szerződés. A szerződés bytecode a Ethereum készült Azure Blockchain Development Kit használatával kérheti le. Azt is megteheti, hogy a szilárdtest-fordítóból is beszerezhető.

**A fejlesztői csomag használata:**

Ha a fejlesztői készletet vagy a szarvasgombát használta az intelligens szerződés kiépítéséhez, a bővítményt használhatja a szerződés bytecode másolásához a vágólapra.

1. A Visual Studio Code Explorer ablaktáblán bontsa ki a szilárdtest-projekt **Build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadatainak JSON-fájljára. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Válassza a **Szerződés másolása bytecode** elemet.

    ![A Visual Studio Code panel és a másolási szerződés bytecode kiválasztása](./media/ethereum-logic-app/bytecode-devkit.png)

    A rendszer átmásolja a bytecode a vágólapra.

**A szerződés metaadatainak fájljának használata:**

1. Nyissa meg a konszolidációs projekt **Build/szerződések** mappájában található szerződéses metaadat-fájlt. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Keresse meg a **bytecode** elemet a JSON-fájlban.
1. Másolja a **bytecode** értéket.

    ![A Visual Studio Code panel a metaadatokban található bytecode](./media/ethereum-logic-app/bytecode-metadata.png)

**A szilárdtest-fordító használata:**

A parancs használatával `solc --bin <smart contract>.sol` létrehozza a szerződés bytecode.

## <a name="get-the-contract-address"></a>A szerződési címnek beszerzése

A Szerződés címe az intelligens szerződési cél címe a Ethereum blockchain. Ez a címe egy intelligens szerződés tranzakciós vagy lekérdezési állapotának elküldésére használható. A Szerződés címe a szarvasgomba-áttelepítési kimenetből vagy a szerződés metaadatainak fájljából kérhető le.

**A szarvasgomba-áttelepítés kimenetének használata:**

A szarvasgomba az intelligens szerződés üzembe helyezése után megjeleníti a szerződés címeit. Másolja a **Szerződés címeit** a kimenetből.

![A szarvasgomba áttelepítési kimenete a szerződéses címen a Visual Studio Code-ban](./media/ethereum-logic-app/contract-address-truffle.png)

**A szerződés metaadatainak fájljának használata:**

1. Nyissa meg a konszolidációs projekt **Build/szerződések** mappájában található szerződéses metaadat-fájlt. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Keresse meg a **Networks** SZAKASZT a JSON-fájlban.
1. A magánhálózati hálózatokat egész számú hálózati azonosító azonosítja. A címek értékének megkeresése a hálózat szakaszon belül.
1. Másolja a **címtartomány** értékét.

![Metaadatok a Visual Studio Code-ban található címtartomány értékével](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Következő lépések

Tekintse meg a videó gyakori forgatókönyveit a [Logic Appsával](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
