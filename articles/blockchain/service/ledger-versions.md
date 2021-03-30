---
title: Azure Blockchain Service Ledger-verziók, javítás, & frissítés
description: Az Azure Blockchain Service támogatott főkönyvek verzióinak áttekintése. A rendszerek javításához és frissítéséhez szükséges szabályzatokat is beleértve.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85807740"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Támogatott Azure Blockchain Service Ledger-verziók

Az Azure Blockchain szolgáltatás az Azure Blockchain Service-ben konzorciumként azonosított Ethereum-alapú [kvórumot](https://www.goquorum.com/developers) használ, amely az ismert résztvevők egy csoportján belüli privát tranzakciók feldolgozására lett tervezve.

Az Azure Blockchain szolgáltatás jelenleg a [kvórum](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) és a [Tessera Transaction Manager](https://github.com/jpmorganchase/tessera)verzióját támogatja.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése

A kvórumon belüli verziószámozás a fő, a másodlagos és a javítási kiadásokon keresztül történik. Ha például a kvórum verziója 2.0.1, a kiadás típusa a következőképpen lesz kategorizálva:

|Jelentős | Kismérvű  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Az Azure Blockchain szolgáltatás a kvórumról elérhetővé tett 30 napon belül automatikusan frissíti a kvórum javítását a meglévő futó tagoknak.

## <a name="availability-of-new-ledger-versions"></a>Új Főkönyv-verziók rendelkezésre állása

Az Azure Blockchain szolgáltatás a kvórum főkönyvének legújabb fő és alverzióit a kvórum gyártójától 60 napon belül elérhetővé teszi. Egy új tag és konzorcium kiépítés esetén legfeljebb négy kisebb kiadást biztosítunk a konzorciumok számára. A verzióról a verzióra való verziófrissítés jelenleg nem támogatott. Ha például a 2. x verziót futtatja, a 3. x verzióra való frissítés jelenleg nem támogatott. Hasonlóképpen, ha az 2,2-es verziót futtatja, a 2,3-es verzióra való frissítés jelenleg nem támogatott.

## <a name="how-to-check-quorum-ledger-version"></a>A kvórum Főkönyv verziójának ellenőrzése

A kvórum verzióját az Azure Blockchain-szolgáltatás tagján is megtekintheti, ha a csomópontot a geth használatával vagy a diagnosztikai naplók megtekintésével csatlakoztatja.

### <a name="using-geth"></a>A geth használata

Csatolja az Azure Blockchain szolgáltatás-csomópontját a geth használatával. Például: `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`.

A csomópont csatlakoztatása után a geth a következő kimenethez hasonló módon jelenti a kvórum verzióját:

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

A geth használatával kapcsolatos további információkért lásd: gyors útmutató [: a geth használata Azure Blockchain szolgáltatásbeli tranzakciós csomóponthoz való csatoláshoz](connect-geth.md).

### <a name="using-diagnostic-logs"></a>Diagnosztikai naplók használata

Ha engedélyezi a diagnosztikai naplókat, a rendszer a kvórum verzióját jelenti a tranzakciós csomópontok számára. A következő csomópont-információs naplófájl például tartalmazza a kvórum verzióját.

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

A diagnosztikai naplókkal kapcsolatos további információkért lásd: az [Azure Blockchain szolgáltatás figyelése Azure monitoron keresztül](monitor-azure-blockchain-service.md#diagnostic-settings).

## <a name="how-to-check-genesis-file-content"></a>A Genesis-fájl tartalmának beadása

A blockchain-csomópont Genesis-fájljának tartalmának vizsgálatához a következő Ethereum JavaScript API-t használhatja:

``` bash
admin.nodeInfo.protocols
```
Az API-t geth-konzol vagy web3-könyvtár használatával hívhatja meg. A geth használatával kapcsolatos további információkért lásd: gyors útmutató [: a geth használata Azure Blockchain szolgáltatásbeli tranzakciós csomóponthoz való csatoláshoz](connect-geth.md).

## <a name="next-steps"></a>Következő lépések

[Korlátok az Azure Blockchain szolgáltatásban](limits.md)
