---
title: Az Azure Blockchain Service Development áttekintése
description: Bevezetés az Azure Blockchain Service-megoldások fejlesztéséhez.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80348381"
---
# <a name="azure-blockchain-service-development-overview"></a>Az Azure Blockchain Service Development áttekintése

Az Azure Blockchain szolgáltatással konzorciumi Blockchain-hálózatokat hozhat létre, amelyek lehetővé teszik a nagyvállalati forgatókönyvek, például az eszközök nyomon követését, a digitális jogkivonatot, a lojalitást és a jutalmat, az ellátási lánc pénzügyi A következő fejezetek bemutatják az Azure Blockchain Service Development szolgáltatást a vállalati Blockchain megoldások megvalósításához.

## <a name="connecting-to-azure-blockchain-service"></a>Csatlakozás az Azure Blockchain szolgáltatáshoz

A blockchain-hálózatokhoz különböző típusú ügyfelek tartoznak, beleértve a teljes csomópontokat, a könnyű csomópontokat és a távoli ügyfeleket. Az Azure Blockchain szolgáltatás olyan Blockchain-hálózatot épít, amely csomópontokat tartalmaz. A Blockchain-fejlesztéshez különböző ügyfeleket használhat az Azure Blockchain Service-hez való átjáróként. Az Azure Blockchain szolgáltatás alapszintű hitelesítési vagy hozzáférési kulcsot biztosít fejlesztési végpontként. A következő népszerű ügyfeleket használhatja a csatlakozási lehetőséggel.

### <a name="visual-studio-code"></a>Visual Studio Code

A konzorcium tagjaihoz az Azure Blockchain Development Kit Visual Studio Code bővítmény használatával csatlakozhat. A konzorciumhoz való csatlakozás után intelligens szerződéseket állíthat össze, építhet ki és helyezhet üzembe egy Azure Blockchain Service Consortium-tag számára.

A kifinomult vállalati blockchain megoldások fejlesztése érdekében fejlesztési keretrendszerre van szükség a különböző blockchain-hálózatokhoz való kapcsolódáshoz és az intelligens szerződési életciklusok kezeléséhez. A legtöbb projekt legalább két blockchain-csomóponttal kommunikál. A fejlesztők a fejlesztés során helyi blockchain használnak. Ha az alkalmazás készen áll a tesztelésre vagy a kiadásra, a fejlesztő üzembe helyezi a blockchain-hálózatot. Például a fő nyilvános Ethereum hálózat vagy az Azure Blockchain szolgáltatás. A Visual Studio Code Ethereum-bővítményéhez készült Azure Blockchain Development Kit a szarvasgombát használja. A szarvasgomba népszerű blockchain-fejlesztési keretrendszer a decentralizált alkalmazások Ethereum blokkláncok történő írásához, fordításához, üzembe helyezéséhez és teszteléséhez. Azt is meggondolhatja, hogy a szarvasgomba olyan keretrendszer, amellyel zökkenőmentesen integrálható az intelligens szerződések fejlesztése és a hagyományos webes fejlesztés.

További információ: gyors útmutató [: a Visual Studio Code használata egy Azure Blockchain Service Consortium-hálózathoz való kapcsolódáshoz](connect-vscode.md).

### <a name="metamask"></a>MetaMask

A MetaMask egy böngészőalapú mobiltárca (távoli ügyfél), RPC-ügyfél és alapszintű szerződés Explorer. A többi böngészőbeli pénztárcától eltérően a MetaMask egy web3-példányt helyez el a böngésző JavaScript-környezetében, amely egy olyan RPC-ügyfélként működik, amely különböző Ethereum-blokkláncok (*mainnet*, *Ropsten TestNet*, *Kovan TestNet*, helyi RPC csomópont stb.) csatlakozik. Az Azure Blockchain szolgáltatáshoz való kapcsolódáshoz egyszerűen beállíthatja az egyéni RPC-t, és megkezdheti a Blockchain fejlesztését a Remix használatával.

További információ [: gyors üzembe helyezés és a MetaMask használata intelligens szerződések létrehozásához és telepítéséhez](connect-metamask.md)

### <a name="geth"></a>Geth

A geth az a parancssori felület, amely a Go-ban implementált teljes Ethereum-csomópont futtatására szolgál. Nem kell teljes csomópontot futtatnia, de elindíthatja az interaktív konzolt, amely JavaScript-futtatókörnyezeti környezetet biztosít a JavaScript API-k számára az Azure Blockchain szolgáltatással való kommunikációhoz.

További információ: gyors útmutató [: a geth használata Azure Blockchain szolgáltatásbeli tranzakciós csomóponthoz való csatoláshoz](connect-geth.md).

## <a name="ethereum-quorum-private-transactions"></a>Ethereum kvórum – privát tranzakciók

A kvórum egy Ethereum elosztott Főkönyv protokoll, amely tranzakciós plusz szerződéses adatvédelmet és új konszenzusi mechanizmusokat is alkalmaz. A legfontosabb fejlesztések Go-Ethereum többek között:

* **Adatvédelem** – a kvórum az állami és a magánjellegű állapot elkülönítésével támogatja a privát tranzakciókat és a privát szerződéseket, és a személyes adatoknak a hálózati résztvevők felé irányított továbbítására szolgáló, társ-társ titkosítású üzenetváltást használ.
* **Alternatív konszenzusi mechanizmusok** – nem szükségesek a működést igazoló vagy a bizonyítékokra vonatkozó konszenzus az engedéllyel rendelkező hálózatokhoz. A kvórum több konszenzusos mechanizmust kínál, amelyek olyan konzorciumi láncok számára lettek kialakítva, mint például a TUTAJ és a IBFT.  Az Azure Blockchain Service a IBFT konszenzusi mechanizmust használja.
* **Társ-jogosultság** – a csomópont-és társ-jogosultságok intelligens szerződések használatával biztosítják, hogy csak ismert felek csatlakozhassanak a hálózathoz.
* **Nagyobb teljesítmény** – a kvórum nagyobb teljesítményt nyújt, mint a nyilvános geth.

## <a name="block-explorers"></a>Felfedezők letiltása

A blokkoló felfedezők olyan online blockchain böngészők, amelyek egyedi blokk tartalmakat, tranzakciós címeket és előzményeket jelenítenek meg. Az alapszintű blokkolási információk az Azure Blockchain szolgáltatásban Azure Monitoron keresztül érhetők el. Ha azonban részletesebb információra van szüksége a fejlesztés során, a blokkoló felfedezők hasznosak lehetnek.  A következő blokkoló felfedezők működnek az Azure Blockchain szolgáltatással:

* [Empirusz Azure Blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) a Web3 Labs-ből
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

A Blockchain Data Manager és Azure Cosmos DB használatával is létrehozhatja a saját blokk-kezelőt, lásd [: oktatóanyag: az adatAzure Cosmos DBba való adatküldés a Blockchain Data Manager használatával](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>TPS mérése

Mivel a blockchain több vállalati forgatókönyvben is használatos, a másodpercenkénti tranzakciók (TPS) sebessége fontos a szűk keresztmetszetek és a rendszer elégtelenségének elkerülése érdekében. A magas tranzakciós sebességet nehéz fenntartani egy decentralizált blockchain belül. A pontos TPS-mérést befolyásolhatja különböző tényezők, például a kiszolgálói szál, a tranzakciós várólista mérete, a hálózati késés és a biztonság. Ha a fejlesztés során mérni kell a TPS sebességet, egy népszerű, nyílt forráskódú eszköz is [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Következő lépések

Próbáljon ki egy rövid útmutatót az Azure Blockchain Development Kit használatával a Ethereum számára az Azure Blockchain Service-hez készült konzorciumhoz való csatlakoztatáshoz.

> [!div class="nextstepaction"]
> [A Visual Studio Code használata az Azure Blockchain szolgáltatáshoz való kapcsolódáshoz](connect-vscode.md)