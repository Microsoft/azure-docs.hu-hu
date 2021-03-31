---
title: Azure Blockchain Workbench – hibaelhárítás
description: Azure Blockchain Workbench előzetes verziójú alkalmazások hibáinak megoldása.
ms.date: 10/14/2019
ms.topic: troubleshooting
ms.reviewer: brendal
ms.openlocfilehash: 20c0f9bdd6f820a73b1ba6660de805268c0d8714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85212853"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Az Azure Blockchain Workbench előzetes verziójának hibaelhárítása

A fejlesztői hibakeresést és támogatást segítő PowerShell-szkriptek is elérhetők. A szkript összefoglalást hoz létre, és részletes naplókat gyűjt a hibaelhárításhoz. Az összegyűjtött naplók a következők:

* Blockchain-hálózat, például Ethereum
* Blockchain Workbench-szolgáltatások
* Application Insights
* Azure monitoring (Azure Monitor naplók)

Az információ segítségével meghatározhatja a következő lépéseket, és meghatározhatja a problémák kiváltó okát.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Hibaelhárítási parancsfájl

A PowerShell hibaelhárítási szkriptje elérhető a GitHubon. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/blockchain/archive/master.zip), vagy a klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>A szkript futtatása
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Futtassa a `collectBlockchainWorkbenchTroubleshooting.ps1` szkriptet a naplók összegyűjtéséhez, és hozzon létre egy zip-fájlt, amely a hibaelhárítási információk mappáját tartalmazza. Például:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
A parancsfájl a következő paramétereket fogadja el:

| Paraméter  | Leírás | Kötelező |
|---------|---------|----|
| SubscriptionID | SubscriptionID az összes erőforrás létrehozásához vagy megkereséséhez. | Yes |
| ResourceGroupName | Azon Azure-erőforráscsoport neve, ahol a Blockchain Workbench telepítve van. | Yes |
| Outputdirectory paraméterben | A kimenet létrehozásának elérési útja. ZIP-fájl. Ha nincs megadva, az alapértelmezett érték az aktuális könyvtár. | No |
| LookbackHours | A telemetria húzásakor használandó órák száma. Az alapértelmezett érték 24 óra. A maximális érték 90 óra | No |
| OmsSubscriptionId | Az előfizetés-azonosító, amelyben a Azure Monitor naplók telepítve vannak. Csak akkor adja meg ezt a paramétert, ha a blockchain-hálózat Azure Monitor naplói a Blockchain Workbench erőforráscsoportén kívül vannak telepítve.| No |
| OmsResourceGroup |Az az erőforráscsoport, amelyben a Azure Monitor naplók telepítve vannak. Csak akkor adja meg ezt a paramétert, ha a blockchain-hálózat Azure Monitor naplói a Blockchain Workbench erőforráscsoportén kívül vannak telepítve.| No |
| OmsWorkspaceName | A Log Analytics munkaterület neve. Csak akkor adja meg ezt a paramétert, ha a blockchain-hálózat Azure Monitor naplói a Blockchain Workbench erőforráscsoportén kívül vannak telepítve. | No |

## <a name="what-is-collected"></a>Mit gyűjt?

A kimeneti ZIP-fájl a következő mappastruktúrát tartalmazza:

| Mappa vagy fájl | Description  |
|---------|---------|
| \Summary.txt | A System összefoglalása |
| \Metrics\blockchain | Metrikák a blockchain |
| \Metrics\Workbench | A Workbench mérőszámai |
| \Details\Blockchain | Részletes naplók a blockchain |
| \Details\Workbench | A Workbench részletes naplói |

Az összegző fájl pillanatképet készít az alkalmazás általános állapotáról és állapotáról. Az összefoglalás ajánlott műveleteket, Kiemelt hibákat és metaadatokat biztosít a futó szolgáltatásokról.

A **metrikák** mappa a különböző rendszerösszetevők metrikáit tartalmazza az idő múlásával. A kimeneti fájl például a `\Details\Workbench\apiMetrics.txt` különböző hibakódok összegzését, valamint a begyűjtési időszakon belüli válaszidőt tartalmazza. A **részletek** mappa részletes naplókat tartalmaz a Workbench vagy a mögöttes blockchain-hálózat konkrét problémáinak elhárításához. A tartalmaz például `\Details\Workbench\Exceptions.csv` egy listát a rendszeren bekövetkezett legutóbbi kivételekről, ami hasznos lehet az intelligens szerződésekkel vagy a blockchain folytatott interakciókkal kapcsolatos hibák elhárításához. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench Application Insights hibaelhárítási útmutató](https://aka.ms/workbenchtroubleshooting)
