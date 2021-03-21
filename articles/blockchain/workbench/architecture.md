---
title: Az Azure Blockchain Workbench-architektúra
description: Az Azure Blockchain Workbench előzetes verziójú architektúrájának és összetevőinek áttekintése.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 1fff70ef2eeb1dc27d33769fd48fe5923f56717b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021605"
---
# <a name="azure-blockchain-workbench-architecture"></a>Az Azure Blockchain Workbench-architektúra

Az Azure Blockchain Workbench előzetes verziója leegyszerűsíti az alkalmazások fejlesztését azáltal, hogy számos Azure-összetevőt használó megoldást biztosít a Blockchain. Az Blockchain Workbench az Azure Marketplace-en is üzembe helyezhető megoldási sablonnal. A sablon lehetővé teszi a telepítendő modulok és összetevők, például a blockchain stack, az ügyfélalkalmazás típusa és a IoT-integráció támogatását. Az üzembe helyezést követően a Blockchain Workbench hozzáférést biztosít egy webalkalmazáshoz, iOS-alkalmazáshoz és Android-alkalmazáshoz.

![Blockchain Workbench-architektúra](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identitás és hitelesítés

A Blockchain Workbench használatával a konzorcium Azure Active Directory (Azure AD) használatával is összevonása vállalati identitásait. A Workbench új felhasználói fiókokat hoz létre a láncbeli identitásokhoz az Azure AD-ben tárolt vállalati identitásokkal. Az identitás-hozzárendelés lehetővé teszi a hitelesített bejelentkezést az ügyfél API-jai és alkalmazásai számára, és a szervezetek hitelesítési házirendjeit használja. A Workbench azt is lehetővé teszi, hogy a vállalati identitásokat egy adott intelligens szerződésben meghatározott szerepkörökhöz rendelje. Emellett a Workbench olyan mechanizmust is biztosít, amely meghatározza, hogy a szerepkörök milyen műveleteket végezhetnek, és milyen időpontban.

A Blockchain Workbench üzembe helyezése után a felhasználók az ügyfélalkalmazások, a REST-alapú ügyfél-API vagy az üzenetkezelési API használatával kommunikálhatnak a Blockchain Workbench szolgáltatással. Az interakciókat minden esetben hitelesíteni kell, Azure Active Directory (Azure AD) vagy az eszközre vonatkozó hitelesítő adatok használatával.

A felhasználók összevonása a konzorcium Azure AD-beli identitását úgy, hogy e-mailben meghívót küldenek a résztvevőknek e-mail-címére. Bejelentkezéskor ezeket a felhasználókat a név, a jelszó és a házirendek alapján hitelesíti a rendszer. Például a szervezet kétfaktoros hitelesítése.

Az Azure AD az összes olyan felhasználó felügyeletére szolgál, akik hozzáférnek a Blockchain Workbenchhez. Az intelligens szerződéshez csatlakozó összes eszköz az Azure AD-vel is társítva van.

Az Azure AD-t a felhasználók egy speciális rendszergazdai csoportba való hozzárendelésére is használják. A rendszergazda csoportba tartozó felhasználók hozzáférést kapnak a Blockchain Workbenchben található jogokhoz és műveletekhez, beleértve a szerződések telepítését és a felhasználók számára a szerződések eléréséhez szükséges engedélyeket. A csoporton kívüli felhasználók nem férhetnek hozzá a rendszergazdai műveletekhez.

## <a name="client-applications"></a>Ügyfélalkalmazások

A Workbench automatikusan generált ügyfélalkalmazások számára biztosít webes és mobil (iOS, Android) alkalmazásokat, amelyek segítségével ellenőrizheti, tesztelheti és tekintheti meg a blockchain-alkalmazásokat. Az alkalmazás felülete dinamikusan jön létre az intelligens szerződés metaadatai alapján, és bármilyen használati esetet képes fogadni. Az ügyfélalkalmazások felhasználói kezelőfelületet továbbítanak a Blockchain Workbench által generált teljes blockchain-alkalmazásokhoz. Az ügyfélalkalmazások Azure Active Directory (Azure AD) segítségével hitelesítik a felhasználókat, majd az intelligens szerződés üzleti környezetéhez igazított felhasználói élményt nyújtanak. A felhasználói élmény lehetővé teszi az új, intelligens szerződéssel rendelkező példányok létrehozását a jogosult személyek számára, majd bemutatják bizonyos típusú tranzakciók végrehajtását az üzleti folyamat megfelelő pontjaiban az intelligens szerződés által képviselt módon.

A webalkalmazásban a jogosult felhasználók hozzáférhetnek a felügyeleti konzolhoz. A konzol az Azure AD-ben a rendszergazda csoport tagjai számára érhető el, és a következő funkciókhoz biztosít hozzáférést:

* A Microsoft által biztosított intelligens szerződések központi telepítése népszerű forgatókönyvekhez. Például egy adatátviteli forgatókönyv.
* Saját intelligens szerződések feltöltése és üzembe helyezése.
* Egy adott szerepkör kontextusában rendeljen hozzá egy felhasználói hozzáférést az intelligens szerződéshez.

További információkért tekintse meg az [Azure Blockchain Workbench minta ügyfélalkalmazások a githubon](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile)című témakört.

## <a name="gateway-service-api"></a>Az átjáró szolgáltatás API-ját

A Blockchain Workbench egy REST-alapú átjárószolgáltatás API-t tartalmaz. Egy blockchain írásakor az API létrehoz és kézbesít üzeneteket egy esemény-közvetítőnek. Ha az API kéri az adatkérést, a rendszer a lekérdezéseket a láncon kívüli adatbázisba küldi. Az adatbázis tartalmaz egy replikát a láncban lévő adatokról és metaadatokról, amelyek környezeti és konfigurációs adatokat biztosítanak a támogatott intelligens szerződések számára. A lekérdezések a nem Láncos replikából származó szükséges adatokat a szerződés metaadatainak által tájékozott formátumban adják vissza.

A fejlesztők hozzáférhetnek az átjáró szolgáltatás API-hoz a blockchain-megoldások létrehozásához és integrálásához anélkül, hogy a Blockchain Workbench ügyfélalkalmazások támaszkodnak.

> [!NOTE]
> Az API hitelesített hozzáférésének engedélyezéséhez két ügyfélalkalmazás van regisztrálva Azure Active Directoryban. Azure Active Directory külön alkalmazás-regisztrációt igényel minden egyes alkalmazás-típushoz (natív és webes).

## <a name="message-broker-for-incoming-messages"></a>Üzenetküldés a bejövő üzenetekhez

Azok a fejlesztők, akik közvetlenül az Blockchain Workbenchbe szeretnének üzeneteket küldeni, közvetlenül a Service Bus küldhetnek üzeneteket. Például az üzenetek API-t a rendszerek közötti integrációhoz vagy IoT eszközökhöz lehet használni.

## <a name="message-broker-for-downstream-consumers"></a>Üzenetsor a továbbfelhasználó számára

Az alkalmazás életciklusa során események történnek. Az eseményeket az átjáró API vagy a Főkönyv is aktiválhatja. Az eseményekre vonatkozó értesítések az alárendelt kódokat az esemény alapján indíthatják el.

Az Blockchain Workbench automatikusan két típusú eseményt helyez üzembe. Az blockchain-események egy fogyasztót aktiválnak a távoli láncú SQL-tárolók feltöltéséhez. A másik felhasználó a dokumentumok feltöltésével és tárolásával kapcsolatos API-k által generált események metaadatait rögzíti.

## <a name="message-consumers"></a>Üzenet fogyasztói

 Az üzenet felhasználója Service Bus üzeneteket fogad. Az üzenetek felhasználói számára az alapul szolgáló eseményvezérelt modell lehetővé teszi a további szolgáltatások és rendszerek kiterjesztését. Például hozzáadhat támogatást az CosmosDB feltöltéséhez vagy az üzenetek kiértékeléséhez az Azure streaming Analytics használatával. A következő szakaszok ismertetik az Blockchain Workbenchben található üzenetekhez tartozó felhasználókat.

### <a name="distributed-ledger-consumer"></a>Elosztott Főkönyv fogyasztója

Az elosztott Főkönyv technológiájának (DLT) üzenetei tartalmazzák a blockchain írni kívánt tranzakciók metaadatait. A fogyasztó lekéri az üzeneteket, és leküldi az adatait egy Transaction Builder, aláíró és útválasztó számára.

### <a name="database-consumer"></a>Adatbázis-fogyasztó

Az adatbázis felhasználója Service Bus üzeneteket fogad, és leküldi az adatok egy csatolt adatbázisba, például egy Azure SQL Database-adatbázisba.

### <a name="storage-consumer"></a>Storage-fogyasztó

A tároló fogyasztó üzenetet küld a Service Busről, és egy csatolt tárolóba küldi le az adatait. Például a kivonatoló dokumentumok tárolása az Azure Storage-ban.

## <a name="transaction-builder-and-signer"></a>Transaction Builder és aláíró

Ha a bejövő üzenet-átvitelszervezőn lévő üzenetet a blockchain kell írni, azt a DLT fogyasztó dolgozza fel. A DLT fogyasztó egy szolgáltatás, amely a metaadatokat tartalmazó üzenetet kérdezi le egy végrehajtandó kívánt tranzakcióhoz, majd elküldi az információt a *Transaction Builder és az aláíró* számára. A *Transaction Builder és az aláíró* egy blockchain-tranzakciót állít össze az adathalmaz és a kívánt blockchain-cél alapján. Az összeállítás után a tranzakció aláírása megtörtént. A titkos kulcsok tárolása Azure Key Vault történik.

 A Blockchain Workbench lekéri a megfelelő titkos kulcsot a Key Vaultból, és a Key Vaulton kívül aláírja a tranzakciót. Az aláírást követően a tranzakció a tranzakciós útválasztók és a főkönyvek számára lesz elküldve.

## <a name="transaction-routers-and-ledgers"></a>Tranzakciós útválasztók és főkönyvek

A tranzakciós útválasztók és a főkönyvek aláírt tranzakciókat készítenek, és a megfelelő blockchain irányítják azokat. Az Blockchain Workbench jelenleg Ethereum támogatja a célként megadott Blockchain.

## <a name="dlt-watcher"></a>DLT-figyelő

Az elosztott Főkönyv technológiája (DLT) figyelő figyeli a Blockchain Workbenchhez csatolt blokk-láncokban előforduló eseményeket.
Az események az egyéni felhasználókra és rendszerekre vonatkozó információkat tükrözik. Például az új szerződési példányok létrehozása, a tranzakciók végrehajtása és az állapot változásai. Az eseményeket a rendszer rögzíti és elküldi a kimenő üzenet-közvetítőnek, így azokat a továbbfelhasználó is felhasználhatja.

Az SQL-felhasználó például figyeli az eseményeket, felhasználja őket, és feltölti az adatbázist a belefoglalt értékekkel. A másolás lehetővé teszi, hogy az adatláncban lévő adattárak replikáját egy láncon kívüli tárolóban tárolja.

## <a name="azure-sql-database"></a>Azure SQL Database

A Blockchain Workbenchhez csatolt adatbázis tárolja a Blockchain tárolt adatokhoz tartozó szerződés-definíciókat, konfigurációs metaadatokat és az SQL-hez elérhető replikát. Ezeket az adatok könnyen lekérdezhető, megjeleníthető vagy elemezhető az adatbázishoz való közvetlen hozzáféréssel. A fejlesztők és más felhasználók az adatbázist jelentéskészítési, elemzési vagy egyéb adatközpontos integrációhoz használhatják. Például a felhasználók megjeleníthetik a tranzakciós adataikat Power BI használatával.

Ez a távoli láncú tárterület lehetővé teszi, hogy a nagyvállalati szervezetek SQL-alapú lekérdezéseket blockchain-főkönyvben ne kelljen lekérdezni. Emellett azáltal, hogy a standard sémán alapuló, a blockchain-alapú technológiákat is támogatja, a láncon kívüli tárolók lehetővé teszik a jelentések és egyéb összetevők újrafelhasználását a projektek, forgatókönyvek és szervezetek között.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage a szerződésekhez kapcsolódó szerződések és metaadatok tárolására szolgál.

A beszerzési megrendelések és a hajóraklevél, a hírekben és az orvosi képekben használt képekig a folytonosságot, beleértve a rendőrségi szervek kameráit és a főbb mozgóképeket, a dokumentumok sok blockchain-központú forgatókönyvben játszanak szerepet. A dokumentumok nem alkalmasak közvetlenül a blockchain való elhelyezésre.

Az Blockchain Workbench lehetővé teszi dokumentumok vagy más médiatartalmak hozzáadását a Blockchain üzleti logikával. A dokumentum vagy a médiatartalom kivonata a blockchain tárolódik, és a tényleges dokumentum-vagy médiatartalom az Azure Storage-ban tárolódik. A rendszer a kapcsolódó tranzakciós adatokat a bejövő üzenet-átvitelszervezőbe továbbítja, becsomagolja, aláírja és átirányítja a blockchain. Ez a folyamat elindítja az eseményeket, amelyek a kimenő üzenet-átvitelszervezőben vannak megosztva. Az SQL-adatbázis felhasználja ezeket az információkat, és az ADATBÁZISba küldi a későbbi lekérdezéshez. Az alárendelt rendszerek ezeket az eseményeket is felhasználhatják, hogy a megfelelő módon működjenek.

## <a name="monitoring"></a>Figyelés

A Workbench Application Insights és Azure Monitor használatával biztosítja az alkalmazások naplózását. Application Insights a Blockchain Workbench összes naplózott adatának tárolására szolgál, és hibákat, figyelmeztetéseket és sikeres műveleteket tartalmaz. A fejlesztők a Blockchain Workbenchtel kapcsolatos problémák hibakereséséhez használhatják a Application Insights. 

Azure Monitor információt nyújt a blockchain-hálózat állapotáról. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench üzembe helyezése](./deploy.md)
