---
title: Hozzáférés az Azure-beli virtuális hálózatokhoz
description: Az integrációs szolgáltatási környezetek (ISEs) súgójának áttekintése az Azure Virtual Networks (virtuális hálózatok) szolgáltatáshoz való hozzáféréshez
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 4d83609eea57c2350881360ef757b1a291627c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374728"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Hozzáférés az Azure Virtual Network-erőforrásokhoz a Azure Logic Apps integrációs szolgáltatási környezetek (ISEs) használatával

Időnként a logikai alkalmazásoknak olyan biztonságos erőforrásokhoz, például virtuális gépekhez és más rendszerekhez vagy szolgáltatásokhoz kell hozzáférnie, amelyek egy Azure-beli [virtuális hálózathoz](../virtual-network/virtual-networks-overview.md)tartoznak vagy kapcsolódnak. A hozzáférés beállításához [létrehozhat egy *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Az ISE a Logic Apps szolgáltatás egy példánya, amely dedikált erőforrásokat használ, és külön fut a "globális" több-bérlős Logic Apps szolgáltatástól. Az ISE-ben tárolt adatmennyiség ugyanabban a régióban marad, ahol az ISE-t [létrehozhatja és üzembe helyezheti](https://azure.microsoft.com/global-infrastructure/data-residency/).

Egyes Azure-beli virtuális hálózatok például privát végpontokat használnak, amelyek az [Azure privát hivatkozásán](../private-link/private-link-overview.md)keresztül állíthatók be, hogy hozzáférést biztosítson az Azure-beli (például Azure Storage, Azure Cosmos DB vagy Azure SQL Database, partner-szolgáltatásokhoz vagy az Azure-ban üzemeltetett Customer Services szolgáltatásokhoz. Ha a logikai alkalmazásoknak hozzá kell férniük a privát végpontokat használó virtuális hálózatokhoz, létre kell hoznia, telepítenie és futtatnia kell ezeket a logikai alkalmazásokat egy ISE-ben.

Ha ISE-t hoz létre, az Azure *befecskendez* vagy üzembe helyezi az ISE-t az Azure-beli virtuális hálózatában. Ezt az ISE-t használhatja azon logikai alkalmazások és integrációs fiókok helyeként, amelyeknek hozzáférésre van szükségük.

![Integrációs szolgáltatási környezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Ez az Áttekintés további információkat tartalmaz arról, hogy [Miért érdemes ISE-t használni](#benefits), a [dedikált és a több-bérlős Logic Apps szolgáltatás közötti különbségeket](#difference), valamint azt, hogy miként lehet közvetlenül hozzáférni az Azure-beli virtuális hálózaton belüli vagy azzal összekapcsolt erőforrásokhoz.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Miért érdemes ISE-t használni?

A Logic apps önálló dedikált példányban való futtatása segít csökkenteni a más Azure-bérlők által az alkalmazások teljesítményére gyakorolt hatást, más néven a ["zajos szomszédok" hatást](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Az ISE a következő előnyöket is biztosítja:

* Közvetlen hozzáférés a virtuális hálózathoz tartozó vagy ahhoz csatlakozó erőforrásokhoz

  Az ISE-ben létrehozott és futtatott Logic Apps-alkalmazások az [ISE-ben futó, kifejezetten megtervezett összekötőket](../connectors/apis-list.md#ise-connectors)használhatnak. Ha egy helyi rendszer vagy adatforrás esetében egy ISE-összekötő van, közvetlenül is csatlakozhat a helyszíni [adatátjáró](../logic-apps/logic-apps-gateway-connection.md)használata nélkül. További információkért lásd a jelen témakör későbbi, a [több-bérlős](#difference) és [a helyszíni rendszerekhez való hozzáférését](#on-premises) ismertető szakaszt.

* A virtuális hálózathoz nem csatlakoztatott vagy azokon kívüli erőforrásokhoz való folyamatos hozzáférés

  Az ISE-ben létrehozott és futtatott logikai alkalmazások továbbra is használhatják a több-bérlős Logic Apps szolgáltatásban futó összekötőket, ha az ISE-specifikus összekötő nem érhető el. További információ: dedikált és [több-bérlős](#difference).

* A saját statikus IP-címei, amelyek a több-bérlős szolgáltatásban a Logic apps által megosztott statikus IP-címektől eltérnek. Beállíthat egy nyilvános, statikus és kiszámítható kimenő IP-címet is a célszámítógépeken való kommunikációhoz. Így nem kell további tűzfal-megnyitásokat beállítania a célszámítógépeken az egyes ISE-rendszerek esetében.

* A futtatási időtartam, a tárterület megőrzése, az átviteli sebesség, a HTTP-kérés és a válasz időkorlátja, az üzenetek mérete és az egyéni összekötőre vonatkozó kérelmek száma nagyobb. További információ: [Azure Logic apps korlátai és konfigurálása](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dedikált és több-bérlős

Ha egy ISE-ben hoz létre és futtat logikai alkalmazásokat, ugyanazokat a felhasználói élményeket és hasonló képességeket kapja, mint a több-bérlős Logic Apps szolgáltatás. Az összes olyan beépített eseményindítót, műveletet és felügyelt összekötőt használhatja, amelyek elérhetők a több-bérlős Logic Apps szolgáltatásban. Egyes felügyelt összekötők további ISE-verziókat is biztosítanak. Az ISE-összekötők és a nem ISE összekötők közötti különbség a futtatásuk helyétől, valamint a Logic app Designerben lévő címkéktől, amikor egy ISE-n belül dolgozik.

![Olyan összekötők, amelyeken címkék találhatók az ISE-ben](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* A beépített eseményindítók és műveletek, például a HTTP **, az alapcímkét** jelenítik meg, és a logikai alkalmazással megegyező ISE-ben futnak.

* Az **ISE** -címkét megjelenítő felügyelt összekötők kifejezetten ISEs-hez készültek, és *mindig ugyanabban az ISE-ben futnak, mint a logikai alkalmazás*. Íme például néhány [összekötő, amely az ISE-verziókat kínálja](../connectors/apis-list.md#ise-connectors):<p>

  * Azure Blob Storage, File Storage és Table Storage
  * Azure Service Bus, Azure Queues, Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid és Azure Monitor naplók
  * FTP, SFTP-SSH, fájlrendszer és SMTP
  * SAP, IBM MQ, IBM DB2 és IBM 3270
  * SQL Server, Azure szinapszis Analytics, Azure Cosmos DB
  * AS2, X12 és EDIFACT

  Ritka kivételek esetén, ha egy helyi rendszer vagy adatforrás számára elérhető ISE-összekötő áll rendelkezésre, közvetlenül a helyszíni [adatátjáró](../logic-apps/logic-apps-gateway-connection.md)használata nélkül is csatlakozhat. További információ: [hozzáférés a helyszíni rendszerekhez](#on-premises) a témakör későbbi részében.

* Azok a felügyelt összekötők, amelyek nem jelenítik meg az **ISE** -címkét, továbbra is működnek az ISE-ben lévő Logic apps Ezek az összekötők *mindig a több-bérlős Logic apps szolgáltatásban futnak*, nem az ISE-ben.

* Az ISE-en kívül létrehozott egyéni összekötők, függetlenül attól, hogy szükség van-e a helyszíni [adatátjáróra](../logic-apps/logic-apps-gateway-connection.md), továbbra is működnek-e a Logic apps-alkalmazásokkal egy ISE- *n* belül. Az *ISE* -ben létrehozott egyéni összekötők azonban nem fognak működni a helyszíni adatátjáróval. További információ: [hozzáférés a helyszíni rendszerekhez](#on-premises).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Hozzáférés a helyszíni rendszerekhez

Az ISE-ben futó logikai alkalmazások közvetlenül hozzáférhetnek a helyszíni rendszerekhez és az Azure-beli virtuális hálózatokhoz kapcsolódó adatforrásokhoz az alábbi elemek használatával:<p>

* A HTTP-trigger vagy-művelet, amely az **alapvető** címkét jeleníti meg

* Az **ISE** -összekötő, ha elérhető, helyszíni rendszer vagy adatforrás esetén

  Ha az ISE-összekötő elérhető, a [helyszíni adatátjáró](../logic-apps/logic-apps-gateway-connection.md)nélkül is közvetlenül hozzáférhet a rendszerhez vagy az adatforráshoz. Ha azonban a SQL Server egy ISE-ből kell elérnie, és Windows-hitelesítést használ, akkor az összekötő nem ISE verzióját és a helyszíni adatátjárót kell használnia. Az összekötő ISE-verziója nem támogatja a Windows-hitelesítést. További információ: Ise- [Összekötők](../connectors/apis-list.md#ise-connectors) és [Csatlakozás egy integrációs szolgáltatási környezetből](../connectors/apis-list.md#integration-service-environment).

* Egyéni összekötő

  * Az ISE-en kívül létrehozott egyéni összekötők, függetlenül attól, hogy szükség van-e a helyszíni [adatátjáróra](../logic-apps/logic-apps-gateway-connection.md), továbbra is működnek-e a Logic apps-alkalmazásokkal egy ISE- *n* belül.

  * Az *ISE* -ben létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Ezek az összekötők azonban közvetlenül hozzáférhetnek a helyi rendszerekhez és olyan adatforrásokhoz, amelyek az ISE-t üzemeltető virtuális hálózathoz tartoznak vagy azokhoz kapcsolódnak. Így az ISE-ben lévő Logic apps-alkalmazásoknak általában nincs szükségük az adatátjáróra az adott erőforrásokhoz való hozzáféréskor.

A helyszíni rendszerek és az ISE-összekötőket nem tartalmazó adatforrások eléréséhez a virtuális hálózaton kívül esik, vagy nem csatlakozik a virtuális hálózathoz, továbbra is a helyszíni adatátjárót kell használnia. Az ISE-n belüli logikai alkalmazások továbbra is használhatják a **Core** vagy **ISE** címkével nem rendelkező összekötőket. Ezek az összekötők a több-bérlős Logic Apps szolgáltatásban futnak, nem pedig az ISE-ben. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU-i

Az ISE létrehozásakor kiválaszthatja a fejlesztői SKU-t vagy prémium SKU-t. Ez az SKU-beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható. Az alábbi két SKU közötti különbségeket láthatja:

* **Fejlesztő**

  A olyan alacsonyabb költségeket biztosító ISE-t biztosít, amelyet a felderítéshez, a kísérletezéshez, a fejlesztéshez és a teszteléshez használhat, nem pedig éles környezetben vagy teljesítmény-teszteléshez. A fejlesztői SKU beépített eseményindítókat és műveleteket, standard szintű összekötőket, vállalati összekötőket és egyetlen [ingyenes rétegbeli](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiókot tartalmaz a [rögzített havi díjakhoz](https://azure.microsoft.com/pricing/details/logic-apps). 

  > [!IMPORTANT]
  > Ez az SKU nem rendelkezik szolgáltatói szerződéssel (SLA), vertikális Felskálázási képességgel vagy redundanciával az újrahasznosítás során, ami azt jelenti, hogy késések vagy leállások merülhetnek fel. Előfordulhat, hogy a háttérbeli frissítések időnként megszakítják a szolgáltatást.

  A kapacitásra és a korlátozásokra vonatkozó információkért lásd: [az ISE korlátai a Azure Logic Appsban](logic-apps-limits-and-config.md#integration-service-environment-ise). A ISEs számlázási működésének megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing).

* **Prémium**

  Egy olyan ISE-t biztosít, amelyet éles és teljesítményű teszteléshez használhat. A prémium SKU SLA-támogatást, beépített eseményindítókat és műveleteket, standard összekötőket, vállalati összekötőket, egyetlen [standard szintű](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiókot, vertikális Felskálázási képességet és redundanciát biztosít a [rögzített havi díj](https://azure.microsoft.com/pricing/details/logic-apps)ellenében.

  A kapacitásra és a korlátozásokra vonatkozó információkért lásd: [az ISE korlátai a Azure Logic Appsban](logic-apps-limits-and-config.md#integration-service-environment-ise). A ISEs számlázási működésének megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE-végponti hozzáférés

Az ISE létrehozásakor dönthet úgy, hogy belső vagy külső hozzáférési végpontokat használ. A kiválasztott beállítás határozza meg, hogy az ISE-beli Logic apps-beli kérelem vagy webhook-eseményindítók fogadhatnak-e hívásokat a virtuális hálózaton kívülről is. Ezek a végpontok arra is hatással vannak, hogy milyen módon férhet hozzá a Logic apps futtatási előzményeinek bemenetekhez és kimenetekhez.

> [!IMPORTANT]
> A hozzáférési végpontot csak az ISE létrehozása során választhatja ki, és később nem módosíthatja ezt a beállítást.

* **Belső**: a privát végpontok lehetővé teszik az ISE-beli logikai alkalmazások meghívását, ahol megtekintheti és elérheti a Logic apps futtatási előzményeinek bemeneteit és kimeneteit *csak a virtuális hálózatán belülről*.

  > [!IMPORTANT]
  > Ha ezeket a webhook-alapú eseményindítókat kell használnia, használjon külső végpontokat, *nem* pedig belső végpontokat az ISE létrehozásakor:
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (ISE-verzió)
  > 
  > Győződjön meg arról is, hogy van hálózati kapcsolat a privát végpontok és azon számítógép között, ahonnan el szeretné érni a futtatási előzményeket. Ellenkező esetben, ha megpróbálja megtekinteni a logikai alkalmazás futási előzményeit, hibaüzenet jelenik meg, amely szerint "váratlan hiba történt. Nem sikerült beolvasni a következőt: ".
  >
  > ![Azure Storage-műveleti hiba történt a forgalom tűzfalon keresztüli küldésének képtelensége miatt](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Például az ügyfélszámítógép létezhet az ISE virtuális hálózatán belül, vagy egy olyan virtuális hálózaton belül, amely az ISE virtuális hálózatára van csatlakoztatva a peering vagy egy virtuális magánhálózat használatával. 

* **Külső**: a nyilvános végpontok lehetővé teszik olyan logikai alkalmazások meghívását az ISE-ben, ahol megtekintheti és elérheti a Logic apps alkalmazásban található bemeneteket és kimeneteket *a virtuális hálózaton kívülről*. Ha hálózati biztonsági csoportokat (NSG) használ, győződjön meg róla, hogy be van állítva a bejövő szabályokkal, hogy engedélyezze a futtatási előzmények bemeneteit és kimeneteit. További információ: az [ISE hozzáférésének engedélyezése](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Annak megállapításához, hogy az ISE belső vagy külső hozzáférési végpontot használ-e, az ISE menüjében, a **Beállítások** területen válassza a **Tulajdonságok** lehetőséget, és keresse meg a **hozzáférési végpont** tulajdonságot:

![ISE hozzáférési végpontjának keresése](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Díjszabási modell

A Logic apps, a beépített triggerek, a beépített műveletek és az ISE-ben futó összekötők egy rögzített díjszabási csomagot használnak, amely eltér a fogyasztáson alapuló díjszabástól. További információ: [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrációs fiókok ISE-vel

Az integrációs fiókokat az integrációs szolgáltatási környezetben (ISE) belüli Logic apps-alkalmazásokkal is használhatja. Ezeknek az integrációs fiókoknak azonban *ugyanazt az ISE* -t kell használniuk, mint a társított logikai alkalmazások. Az ISE-beli Logic apps csak azokra az integrációs fiókokra hivatkozhat, amelyek ugyanabban az ISE-ban találhatók. Integrációs fiók létrehozásakor kiválaszthatja az ISE-t az integrációs fiókjának helyeként. Ha szeretné megtudni, hogyan működik a díjszabás és a számlázás az ISE-integrációs fiókok esetében, tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/). A határértékekkel kapcsolatos információkért lásd: [integrációs fiók korlátai](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="next-steps"></a>Következő lépések

* [Kapcsolódás Azure-beli virtuális hálózatokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* További információ az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg az [Azure-szolgáltatások virtuális hálózati integrációját](../virtual-network/virtual-network-for-azure-services.md)
