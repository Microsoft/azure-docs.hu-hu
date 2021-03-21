---
title: Az Azure Blockchain szolgáltatás áttekintése
description: Az Azure Blockchain Service áttekintése
ms.date: 03/15/2021
ms.topic: overview
ms.reviewer: patricka
ms.openlocfilehash: 035e2c1df5912f291ad241ade25361f9cc098b22
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573625"
---
# <a name="what-is-azure-blockchain-service"></a>Mi az az Azure Blockchain Service?

Az Azure Blockchain Service egy teljes körűen felügyelt Főkönyv szolgáltatás, amely lehetővé teszi a felhasználók számára, hogy az Azure-ban méretezhető Blockchain-hálózatokat fejlesszenek és működtessenek. Az infrastruktúra felügyeletének és a blockchain hálózati irányításának egységes szabályozásával az Azure Blockchain Service a következőket biztosítja:

* Egyszerű hálózati üzembe helyezés és műveletek
* Beépített konzorciumok felügyelete
* Intelligens szerződések fejlesztése ismerős fejlesztői eszközökkel

Az Azure Blockchain szolgáltatás több Főkönyv protokoll támogatására lett kialakítva. Jelenleg a Ethereum [kvórum](https://www.goquorum.com/) főkönyvének támogatást nyújt az [isztambuli bizánci hibatűrési (IBFT)](https://docs.goquorum.consensys.net/en/stable/Concepts/Consensus/IBFT/) konszenzusi mechanizmus használatával.

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Az alkalmazások fejlesztésére és az üzleti logikára összpontosíthat, és nem kell időt és erőforrásokat lefoglalnia a virtuális gépek és az infrastruktúra kezeléséhez. Emellett továbbra is fejlesztheti alkalmazását az Ön által választott nyílt forráskódú eszközökkel és platformmal anélkül, hogy új ismeretekkel kellene elsajátítani a megoldásokat.

## <a name="network-deployment-and-operations"></a>Hálózat üzembe helyezése és műveletei

Az Azure Blockchain szolgáltatás üzembe helyezése az Azure Blockchain bővítmény használatával történik a Azure Portalon, az Azure CLI-n vagy a Visual Studio code-on keresztül. Az üzembe helyezés egyszerűsítve van, beleértve a tranzakciós és a érvényesítő csomópontok, az Azure Virtual Networks a biztonsági elkülönítés és a szolgáltatás által felügyelt tárolók kiépítését is.  Emellett egy új blockchain-tag telepítésekor a felhasználók is létrehozhatnak vagy csatlakozhatnak konzorciumot.  A konzorciumok lehetővé teszik, hogy a különböző Azure-előfizetések több résztvevője is biztonságosan tudjon kommunikálni egymással egy megosztott blockchain.  Ez az egyszerűsített üzembe helyezés a blockchain hálózati üzembe helyezését nap és perc között csökkenti.

### <a name="performance-and-service-tiers"></a>Teljesítmény-és szolgáltatási szintek

Az Azure Blockchain Service két szolgáltatási szintet kínál: *Alapszintű* és *standard*. Az egyes szintek különböző teljesítményt és képességeket biztosítanak a könnyű fejlesztési és tesztelési feladatoknak a nagy mértékben méretezhető üzemi blockchain való üzembe helyezéséhez. A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához *használja az alapszintű* csomagot. Használja a *standard* szintű üzemi szintű üzembe helyezést. Mindkét réteg tartalmaz legalább egy tranzakciós csomópontot és egy validator csomópontot (alapszintű) vagy két validator csomópontot (standard). 

![Árképzési szintek](./media/overview/pricing-tiers.png)

Amellett, hogy két érvényesítő csomópontot is kínál, a *standard* szint két *virtuális mag* biztosít minden tranzakcióhoz és érvényesítő csomóponthoz, míg az alapszintű *csomag* 1 virtuális mag-konfigurációt biztosít.  A tranzakció-és validator-csomópontok 2 virtuális mag kínálva az 1 virtuális mag a kvórum főkönyvéhez rendelhető, míg a fennmaradó 1 virtuális mag más infrastruktúrához kapcsolódó szolgáltatásokhoz is használható, így biztosítva az optimális teljesítményt az éles blockchain-munkaterhelések számára. A díjszabással kapcsolatos további információkért lásd: az [Azure Blockchain Service díjszabása](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Biztonság és karbantartás

Az első blockchain-tag üzembe helyezése után további tranzakciós csomópontokat adhat hozzá a taghoz.  Alapértelmezés szerint a tranzakciós csomópontok védettek a tűzfalszabályok között, és a hozzáféréshez konfiguráció szükséges.  Emellett az összes tranzakciós csomópont a TLS-n keresztül titkosítja az adatátvitelt.  Több lehetőség létezik a tranzakciós csomópont-hozzáférés biztonságossá tételéhez, beleértve a tűzfalszabályok, az alapszintű hitelesítés, a hozzáférési kulcsok és a Azure Active Directory integrációját. További információ: a [tranzakciós csomópontok konfigurálása](configure-transaction-nodes.md) és [Azure Active Directory hozzáférés konfigurálása](configure-aad.md).

Felügyelt szolgáltatásként az Azure Blockchain Service biztosítja, hogy a Blockchain-tag csomópontjai a legújabb gazda operációs rendszerrel és a Főkönyv szoftveres verem frissítéseivel legyenek javítva, amelyek magas rendelkezésre állásra vannak konfigurálva (csak standard csomag esetén), ami kiküszöböli a hagyományos IaaS Blockchain-csomópontokhoz szükséges DevOps nagy részét.  A javítással és a frissítésekkel kapcsolatos további információkért lásd: az [Azure Blockchain Service Ledger támogatott verziói](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorozás és naplózás

Emellett az Azure Blockchain szolgáltatás részletes mérőszámokat biztosít Azure Monitor szolgáltatáson keresztül, amely betekintést nyújt a csomópontok CPU-, memória-és tárterület-használati adataiba.  A Azure Monitor hasznos betekintést nyújt a blockchain hálózati tevékenységekkel, például a kibányászott tranzakciókkal, a tranzakciós várólista mélységével és az aktív kapcsolatokkal.  A metrikák testreszabhatók úgy, hogy nézeteiket szolgáltassanak a blockchain-alkalmazás szempontjából fontos adatokhoz.  Emellett a küszöbértékeket olyan riasztások segítségével is meghatározhatja, amelyek lehetővé teszik a felhasználók számára olyan műveletek elindítását, mint például az e-mailek vagy SMS-üzenetek elküldése, a logikai alkalmazás, az Azure-függvény futtatása vagy egy egyéni módon definiált webhookra

![A képernyőfelvétel megjeleníti a figyelést, a blokkok, a tranzakciók, a függőben lévő tranzakciók és a kezelt kérelmek értékeit.](./media/overview/metrics.png)

Az Azure Log Analytics segítségével a felhasználók megtekinthetik a kvórum főkönyvéhez kapcsolódó naplókat, illetve egyéb fontos információkat, például a tranzakciós csomópontokhoz való kapcsolódási kísérleteket.

## <a name="built-in-consortium-management"></a>Beépített konzorciumok felügyelete

Az első blockchain-tag üzembe helyezésekor csatlakozhat vagy létrehozhat egy új konzorciumot.  A konzorcium olyan logikai csoport, amely a többrésztvevős folyamatokban lebonyolított blockchain-tagok közötti irányítás és kapcsolat kezelésére szolgál.  Az Azure Blockchain szolgáltatás beépített irányítási ellenőrzéseket biztosít az előre definiált intelligens szerződések segítségével, amelyek meghatározzák, hogy a konzorcium milyen műveleteket végezhet.  Ezek az irányítási vezérlők a konzorcium rendszergazdája által igény szerint testreszabhatók. Új konzorcium létrehozásakor a blockchain-tag a konzorcium alapértelmezett rendszergazdája, amely lehetővé teszi más felek meghívását a konzorciumhoz való csatlakozásra.  A konzorciumot csak akkor lehet csatlakoztatni, ha korábban meghívott.  Konzorciumhoz való csatlakozáskor a blockchain-tag a konzorcium rendszergazdája által bevezetett irányítási szabályozás hatálya alá esik.

![Konzorciumok kezelése](./media/overview/consortium.png)

A konzorciumok felügyeleti műveletei, például a tagok hozzáadása és eltávolítása egy konzorciumból a PowerShell és a REST API használatával érhetők el. Az általános felületek használatával programozott módon kezelheti a konzorciumot, nem pedig a szilárdtest-alapú intelligens szerződések módosítását és elküldését. További információ: [Consortium Management](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Fejlesztés ismert fejlesztési eszközökkel

A nyílt forráskódú kvórum Ethereum főkönyve alapján ugyanúgy fejlesztheti az Azure Blockchain-szolgáltatáshoz tartozó alkalmazásokat, mint a meglévő Ethereum-alkalmazásokhoz. A vezető iparági partnerekkel együttműködve a Visual Studio Code bővítmény lehetővé teszi, hogy a fejlesztők olyan ismerős eszközöket használjanak, mint például a szarvasgomba Suite intelligens szerződések létrehozásához. Az Azure Blockchain Visual Studio Code bővítmény használatával létrehozhat vagy csatlakozhat egy meglévő konzorciumhoz, így az összes IDE-ből létrehozhatja és üzembe helyezheti az intelligens szerződéseit. További információkért lásd: az [Azure Blockchain Development Kit a vs Code piactéren](https://aka.ms/vscodebcextension) és az [Azure Blockchain Development Kit felhasználói útmutatójában](https://aka.ms/vscodebcextensionwiki).

## <a name="publish-blockchain-data"></a>Blockchain-adatbázis közzététele

Az Azure Blockchain Service-hez készült Blockchain-Data Manager az Azure Blockchain szolgáltatás tranzakciós információit rögzíti, átalakítja és kézbesíti az Azure-szolgáltatásokkal való megbízható és skálázható Blockchain Azure Event Grid-integrációt biztosító témaköröknek. A Blockchain Data Manager használatával integrálhatja az alkalmazásokat és az adattárakat. További információ: [Blockchain Data Manager az Azure Blockchain Service](data-manager.md)-ben.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Az Azure Blockchain híreket az Azure [Blockchain blogon](https://azure.microsoft.com/blog/topics/blockchain/) tekintheti meg, ahol naprakészen tarthatja a Blockchain szolgáltatás ajánlatait és az Azure Blockchain Engineering csapata információit.

Ha visszajelzést szeretne küldeni, vagy új szolgáltatásokat szeretne kérni, tegye közzé vagy szavazzon a [blockchain készült Azure feedback fórumán](https://aka.ms/blockchainuservoice)keresztül.

### <a name="community-support"></a>Közösségi támogatás

Folytassa a Microsoft-mérnökökkel és az Azure Blockchain közösségi szakértőivel.

* [A Microsoft Q&az Azure Blockchain szolgáltatásra vonatkozó kérdés oldalát](/answers/topics/azure-blockchain-service.html)
* [A Microsoft technikai közössége](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-service)

## <a name="next-steps"></a>Következő lépések

Első lépésként próbálkozzon egy rövid útmutatóval, vagy további részletekért tekintse meg ezeket az erőforrásokat.
* [Blockchain-tag létrehozása a Azure Portal használatával](create-member.md) vagy [blockchain-tag létrehozása az Azure CLI használatával](create-member-cli.md)
* Kövesse a Microsoft Learn elérési utat a [blockchain-fejlesztéssel való ismerkedéshez](/learn/paths/ethereum-blockchain-development)
* A [kezdők sorozatának](https://channel9.msdn.com/Series/Beginners-Series-to-Blockchain) megtekintése a blockchain
* A Cost-összehasonlítások és a számológépek esetében tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/blockchain-service) .
* Az első alkalmazás létrehozása az [Azure Blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit) használatával
* Az Azure Blockchain VSCode bővítmény [felhasználói útmutatója](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)
