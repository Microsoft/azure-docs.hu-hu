---
title: Újdonságok Kibocsátási megjegyzések – Azure Blockchain Service
description: Ismerje meg az Azure Blockchain szolgáltatás újdonságait, például a legújabb kibocsátási megjegyzéseket, a verziókat, az ismert problémákat és a közelgő változásokat.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 6a3113a2d28e704b188d701da13493ecd8263cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94335026"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Újdonságok az Azure Blockchain szolgáltatásban?

> Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` az RSS-hírcsatorna olvasójának [ ![ RSS-hírcsatorna olvasójának ikonja](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522).

Az Azure Blockchain szolgáltatás folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- Új képességek
- Verziófrissítések
- Ismert problémák

---

## <a name="june-2020"></a>2020. június

### <a name="version-upgrades"></a>Verziófrissítések

- A kvórum verziója a 2.6.0-re frissül. A 2.6.0-es verzióban aláírt privát tranzakciókat is küldhet. A privát tranzakciók küldésével kapcsolatos további információkért tekintse meg a [KVÓRUM API dokumentációját](https://docs.goquorum.consensys.net/en/latest/Reference/APIs/ContractExtensionAPIs/#apis).
- A Tessera verziója a 0.10.5-re frissül.

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>A szerződés mérete és a tranzakció mérete 128 KB-ra nőtt

Típus: konfiguráció módosítása

A szerződés mérete (MaxCodeSize) 128 KB-ra nőtt, így nagyobb méretű intelligens szerződések helyezhetők üzembe. Emellett a tranzakció mérete (txnSizeLimit) 128 KB-ra nőtt. A konfigurációs változások az Azure Blockchain szolgáltatásban az 19 2020. június után létrehozott új konzorciumokra vonatkoznak.

### <a name="trietimeout-value-reduced"></a>TrieTimeout érték csökkentve

Típus: konfiguráció módosítása

A TrieTimeout érték csökkentve lett, hogy a memóriában tárolt állapot gyakrabban legyen lemezre írva. Az alacsonyabb érték azt jelenti, hogy a csomópontok gyorsabb helyreállítását biztosítják egy csomópont összeomlásakor előforduló ritka esetekben.

## <a name="may-2020"></a>2020. május

### <a name="version-upgrades"></a>Verziófrissítések

- Ubuntu verziófrissítése 18,04-re
- Kvórum verziófrissítése a 2.5.0-re
- A Tessera verziófrissítésének 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Az Azure Blockchain szolgáltatás támogatja a rawPrivate-tranzakciók küldését

Típus: funkció

Az ügyfél a fiókon kívül is aláírhat privát tranzakciókat a csomóponton.

### <a name="two-phase-member-provisioning"></a>Kétfázisú tag kiépítés

Típus: fejlesztés

Két fázis segít optimalizálni az olyan forgatókönyveket, amelyekben egy tag egy hosszú, meglévő konzorciumban jön létre. A tagsági infrastruktúra az első fázisban van kiépítve. A második fázisban a tag szinkronizálva van a blockchain. A kétfázisú kiépítés segít elkerülni a tagoknak az időtúllépések miatti sikertelen létrehozását.

## <a name="known-issues"></a>Ismert problémák

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>az ETH. estimateGas függvény kivételt jelez a kvórum v 2.6.0-ben

A kvórum v 2.6.0-ben az *ETH. estimateGas* függvény meghívása anélkül, hogy a további *érték* paraméter megadása miatt a *metódus kezelője összeomlott* . A kvórum csapatának értesítése megtörtént, és a javítás várhatóan július 2020. A javítás elérhetővé tételéhez a következő megkerülő megoldásokat használhatja:

- Kerülje az *ETH. estimateGas* használatát, mivel ez hatással lehet a teljesítményre. További információ az ETH. estimateGas teljesítménnyel kapcsolatos problémákról: az [ETH meghívása. a estimateGas függvény csökkenti a teljesítményt](#calling-ethestimategas-function-reduces-performance). Adja meg a gáz értékét minden egyes tranzakcióhoz. A legtöbb kódtár az ETH. estimateGas-t hívja meg, ha nincs megadva olyan gáz értéke, amely a kvórum v 2.6.0 összeomlását okozza.
- Ha az *ETH. estimateGas* meghívására van szüksége, a kvórum csapata azt javasolja, hogy megkerülő megoldásként adja át a további paraméter *értékét* *0* -ként.

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>A bányászat leáll, ha kevesebb mint négy érvényesítő csomópont

Az üzemi hálózatoknak legalább négy érvényesítő csomóponttal kell rendelkezniük. A kvórum javasolja, hogy a IBFT összeomlási hibatűrésének (3F + 1) kielégítéséhez legalább négy érvényesítő csomópontra van szükség. Négy érvényesítő csomópont beszerzéséhez legalább két Azure Blockchain Service *standard* szintű csomópontnak kell lennie. A standard csomópontok két érvényesítő csomóponttal vannak kiépítve.  

Ha az Azure Blockchain szolgáltatásban található Blockchain-hálózat nem rendelkezik négy érvényesítő csomóponttal, akkor előfordulhat, hogy a bányászat leáll a hálózaton. A bányászatot a feldolgozott blokkokra vonatkozó riasztás beállításával észlelheti. Egy kifogástalan állapotú hálózatban a feldolgozott blokk öt percenként 60 blokk lesz.

Az Azure Blockchain Service csapata a csomópont újraindítását is csökkenti. Az ügyfeleknek meg kell nyitniuk egy támogatási kérést a csomópont újraindításához. Az Azure Blockchain Service csapata a bányászati problémák automatikus észlelésére és kijavítására dolgozik.

Használja a *standard* szintű üzemi szintű üzembe helyezést. A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához *használja az alapszintű* csomagot. Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>A Blockchain Data Manager standard szintű csomópontot igényel

Ha Blockchain Data Manager használ, használja a *standard* szintet. Az alapszintű *csomag* csak 4 GB memóriát tartalmaz. Ezért nem lehet méretezni a Blockchain-Data Manager és a rajta futó egyéb szolgáltatások által igényelt használatra.

A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához *használja az alapszintű* csomagot. Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Nagy mennyiségű feloldási fiók hívása geth okoz

Nagy mennyiségű feloldási fiók hívása a tranzakció elküldésekor a tranzakciós csomópont geth összeomlását okozhatja. Ennek eredményeképpen le kell állítania a tranzakciók betöltését. Ellenkező esetben a függőben lévő tranzakciós várólista növekszik.

A geth egy percen belül automatikusan újraindul. A csomóponttól függően a szinkronizálás hosszú időt is igénybe vehet. Az Azure Blockchain Service csapata olyan archiválási funkciót tesz lehetővé, amely csökkenti a szinkronizálás időpontját.

A geth összeomlások azonosításához az Blockchain üzeneteiben lévő hibaüzenetek naplóit is megtekintheti. Azt is megtekintheti, hogy a feldolgozott blokkok csökkentik-e a függőben lévő tranzakciók növekedését.

A probléma megoldásához a fiók zárolásának feloldásához egy paranccsal küldje el az aláírt tranzakciókat az aláíratlan tranzakciók küldése helyett. A már külsőleg aláírt tranzakciók esetében nincs szükség a fiók zárolásának feloldására.

Ha aláíratlan tranzakciókat szeretne küldeni, a feloldási parancsban a 0 értékre való küldéssel oldja fel a fiók végtelen idejét. Az összes tranzakció elküldése után visszaállíthatja a fiókot.  

Az Azure Blockchain szolgáltatás által használt geth paraméterek a következők: Ezek a paraméterek nem módosíthatók.

- Isztambuli blokk időtartama: 5 MP
- Maximális méret: 700000000
- Ceil-gáz korlátja: 800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>A privát tranzakciók nagy mennyisége csökkenti a teljesítményt

Ha az Azure Blockchain szolgáltatás alapszintű és privát tranzakcióit használja, a Tessera összeomlást eredményezhet.

A Tessera-összeomlás észleléséhez tekintse át a Blockchain-alkalmazás naplóit, és keresse meg az üzenetet `Tessera crashed. Restarting Tessera...` .

Az Azure Blockchain szolgáltatás összeomlás esetén újraindítja a Tesserat. Az újraindítás körülbelül egy percet vesz igénybe.

Ha nagy mennyiségű privát tranzakciót küld, használja a *standard* szintet. A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához *használja az alapszintű* csomagot. Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.

### <a name="calling-ethestimategas-function-reduces-performance"></a>Az ETH. estimateGas függvény meghívása csökkenti a teljesítményt

Az *ETH. estimateGas* függvény többszöri meghívása csökkenti a tranzakciók másodpercenkénti számát. Az összes tranzakció beküldéséhez ne használjon *ETH. estimateGas* függvényt. Az *ETH. estimateGas* függvény a memória-igényes.

Ha lehetséges, használjon egy konzervatív gáz értéket a tranzakciók elküldéséhez, és csökkentse az *ETH. estimateGas* használatát.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Az intelligens szerződések nem kötött hurkoi csökkentik a teljesítményt

Kerülje a nem kötött hurkokat az intelligens szerződésekben, mivel azok csökkenthetik a teljesítményt. További információkat találhat az alábbi forrásokban:

- [A nem kötött hurkok elkerülése](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Az intelligens szerződéssel kapcsolatos ajánlott biztonsági eljárások](https://github.com/ConsenSys/smart-contract-best-practices)
- [A kvórum által biztosított intelligens szerződési irányelvek](https://docs.goquorum.consensys.net/en/stable/Concepts/Security/Framework/DecentralizedApplication/SmartContractsSecurity/)
- [Irányelvek a szilárdtestek által biztosított gázárak és hurkok esetében](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)