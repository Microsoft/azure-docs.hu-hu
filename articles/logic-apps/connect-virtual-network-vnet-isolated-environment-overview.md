---
title: Hozzáférés az Azure-beli virtuális hálózatokhoz
description: Áttekintés arról, hogy az integrációs szolgáltatási környezetek (ISE-k) hogyan segítik a logikai alkalmazásokat az Azure-beli virtuális hálózatok (VNET-ek) elérésében
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 3070083040424b877159955dc2138f15319f05c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766388"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Hozzáférés az Azure Virtual Network-erőforrásokhoz Azure Logic Apps integrációs szolgáltatási környezetek (ISE-k) használatával

Előfordulhat, hogy a logikai alkalmazásoknak hozzáférésre van szükségük olyan biztonságos erőforrásokhoz, mint a virtuális gépek és más rendszerek vagy szolgáltatások, amelyek egy Azure-beli virtuális hálózaton belül vagy ahhoz [csatlakoznak.](../virtual-network/virtual-networks-overview.md) A hozzáférés beállítására létrehozhat egy integrációs szolgáltatási környezetet [(ISE).  ](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) Az ISE a Logic Apps szolgáltatás egy példánya, amely dedikált erőforrásokat használ, és a "globális" több-bérlős Logic Apps fut. Az ISE-adatok ugyanabban a régióban maradnak, ahol az [ISE-t hozza létre és telepíti.](https://azure.microsoft.com/global-infrastructure/data-residency/)

Egyes Azure-beli virtuális hálózatok például privát végpontokat használnak, amelyeket [az Azure Private Link-val](../private-link/private-link-overview.md)állíthat be, és hozzáférést biztosítanak az Azure PaaS-szolgáltatásokhoz, például az Azure Storage-hoz, az Azure Cosmos DB-hoz vagy Azure SQL Database-hoz, partnerszolgáltatásokhoz vagy az Azure-ban üzemeltetett ügyfélszolgálathoz. Ha a logikai alkalmazásoknak hozzá kell férnie a privát végpontokat felhasználó virtuális hálózatokhoz, ezeket a logikai alkalmazásokat egy ISE-környezetben kell létrehoznia, üzembe helyeznie és futtatnia.

ISE létrehozásakor az *Azure* az ISE-t az Azure-beli virtuális hálózatba injektálja vagy telepíti. Ezután használhatja ezt az ISE-t a hozzáférést szükséges logikai alkalmazások és integrációs fiókok helyeként.

![Integrációs szolgáltatási környezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Ez az áttekintés további információkat tartalmaz arról, hogy miért érdemes HASZNÁLNI az [ISE-t,](#benefits)a dedikált és a [több-bérlős Logic Apps-szolgáltatás](#difference)közötti különbségeket, valamint azt, hogy hogyan férhet hozzá közvetlenül az Azure-beli virtuális hálózaton belüli vagy ahhoz kapcsolódó erőforrásokhoz.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Miért érdemes ISE-t használni?

A logikai alkalmazások saját dedikált példányon való futtatása csökkenti a más Azure-bérlőknek az alkalmazások teljesítményére gyakorolt hatását, más néven [a "zajos szomszédok" hatást.](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Az ISE az alábbi előnyöket is biztosítja:

* Közvetlen hozzáférés a virtuális hálózaton belüli vagy ahhoz kapcsolódó erőforrásokhoz

  Az ISE-ban létrehozott és futtatott logikai alkalmazások olyan, kifejezetten az ISE-ban futó összekötőket [használhatnak.](../connectors/managed.md#ise-connectors) Ha létezik [ISE-összekötő](../logic-apps/logic-apps-gateway-connection.md)egy helyszíni rendszerhez vagy adatforráshoz, a helyszíni adatátjáró használata nélkül is csatlakozhat közvetlenül. További információt a jelen témakör későbbi, Dedikált és [több-bérlős](#difference) és Helyszíni rendszerekhez [való](#on-premises) hozzáférése témakörében láthat.

* Folyamatos hozzáférés a virtuális hálózaton kívüli vagy ahhoz nem kapcsolódó erőforrásokhoz

  Az ISE-n létrehozott és futtatott logikai alkalmazások továbbra is használhatnak több-bérlős Logic Apps-szolgáltatásban futó összekötőket, ha nem áll rendelkezésre ISE-specifikus összekötő. További információ: Dedikált és [több-bérlős.](#difference)

* Saját statikus IP-címei, amelyek elkülönülnek a több-bérlős szolgáltatás logikai alkalmazásai által megosztott statikus IP-címektől. Egyetlen nyilvános, statikus és kiszámítható kimenő IP-címet is be lehet állítani a célrendszerekkel való kommunikációhoz. Így nem kell további tűzfal-megnyitásokat beállítania az egyes ISE-hez a célrendszereken.

* Megnövelt korlát a futtatás időtartamára, a tárterület-megőrzésre, az átviteli sebességre, a HTTP-kérésekre és -válaszidőkre, az üzenetméretekre és az egyéni összekötő-kérelmekre. További információ: [A](logic-apps-limits-and-config.md)Azure Logic Apps.

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dedikált és több-bérlős

Amikor logikai alkalmazásokat hoz létre és futtat egy ISE-n, ugyanazokkal a felhasználói felületekkel és hasonló képességekkel rendelkezik, mint a több-bérlős Logic Apps szolgáltatás. A több-bérlős szolgáltatásban elérhető összes beépített eseményindítót, műveletet és felügyelt összekötőt Logic Apps használhatja. Egyes felügyelt összekötők további ISE-verziókat kínálnak. Az ISE-összekötők és a nem ISE-összekötők közötti különbség abban áll fenn, hogy hol futnak, és az isE-beli munka során a Logikaialkalmazás-tervezőben található címkék.

![ISE-hez címkékkel és anélkül is használható összekötők](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* A beépített eseményindítók és műveletek, például a HTTP megjelenítik a **CORE** címkét, és ugyanabban az ISE-ben futnak, mint a logikai alkalmazás.

* Az ISE címkét megjelenítő felügyelt összekötők kifejezetten **ISE-khez** vannak tervezve, és mindig ugyanabban az *ISE-ban* futnak, mint a logikai alkalmazás. Íme például néhány [összekötő, amelyek ISE-verziókat kínálnak:](../connectors/managed.md#ise-connectors)<p>

  * Azure Blob Storage, File Storage és Table Storage
  * Azure Service Bus, Azure Queues, Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid és Azure Monitor naplók
  * FTP, SFTP-SSH, fájlrendszer és SMTP
  * SAP, IBM MQ, IBM DB2 és IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 és EDIFACT

  Ritka kivételekkel, ha egy HELYSZÍNI rendszerhez vagy adatforráshoz is elérhető ISE-összekötő, a helyszíni adatátjáró használata nélkül is [csatlakozhat közvetlenül.](../logic-apps/logic-apps-gateway-connection.md) További információt a jelen témakör későbbi, Hozzáférés a [helyszíni](#on-premises) rendszerekhez témakörében található.

* Azok a felügyelt összekötők, amelyek nem jelenítik meg az **ISE** címkét, továbbra is működnek az ISE-beli logikai alkalmazások esetében. Ezek az *összekötők mindig a több-bérlős Logic Apps futnak,* nem az ISE-n.

* Az *ISE-n* kívül létrehozott egyéni összekötők, függetlenül attól, hogy szükségük [van-e](../logic-apps/logic-apps-gateway-connection.md)a helyszíni adatátjáróra, továbbra is működnek az ISE-n belüli logikai alkalmazásokhoz. Az *ISE-környezetben* létrehozott egyéni összekötők azonban nem működnek a helyszíni adatátjáróval. További információ: Hozzáférés helyszíni [rendszerekhez.](#on-premises)

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Hozzáférés a helyszíni rendszerekhez

Az ISE környezetben futó logikai alkalmazások közvetlenül hozzáférhetnek az Azure-beli virtuális hálózaton belüli vagy ahhoz kapcsolódó helyszíni rendszerekhez és adatforrásokhoz a következő elemek használatával:<p>

* A HTTP-eseményindító vagy -művelet, amely megjeleníti a **CORE** címkét

* Az **ISE-összekötő** ( ha elérhető) helyszíni rendszerhez vagy adatforráshoz

  Ha elérhető ISE-összekötő, a helyszíni adatátjáró nélkül is közvetlenül elérheti a rendszert vagy [az adatforrást.](../logic-apps/logic-apps-gateway-connection.md) Ha azonban egy ISE-SQL Server kell hozzáférnie, és Windows-hitelesítést kell használnia, az összekötő nem ISE verzióját és a helyszíni adatátjárót kell használnia. Az összekötő ISE-verziója nem támogatja a Windows-hitelesítést. További információ: [ISE-összekötők](../connectors/managed.md#ise-connectors) és [Csatlakozás integrációs szolgáltatási környezetből.](../connectors/managed.md#integration-account-connectors)

* Egyéni összekötő

  * Az *ISE-n* kívül létrehozott egyéni összekötők, függetlenül attól, hogy szükség [van-e](../logic-apps/logic-apps-gateway-connection.md)a helyszíni adatátjáróra, továbbra is használhatók az ISE-n belüli logikai alkalmazásokhoz.

  * Az *ISE-környezetben* létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Ezek az összekötők azonban közvetlenül elérhetik a helyszíni rendszereket és adatforrásokat, amelyek az ISE-t tartalmazó virtuális hálózaton belül vagy ahhoz csatlakoznak. Az ISE-beli logikai alkalmazásoknak tehát általában nincs szükségük az adatátjáróra az erőforrások elérésekor.

A helyszíni rendszerek és adatforrások eléréséhez, amelyek nem csatlakoznak ISE-összekötőkhöz, kívül vannak a virtuális hálózaton, vagy nem csatlakoznak a virtuális hálózathoz, továbbra is a helyszíni adatátjárót kell használnia. Az ISE-beli logikai alkalmazások továbbra is használhatjanak CORE vagy ISE címkével nem ellátott **összekötőket.**  Ezek az összekötők nem az ISE- Logic Apps, hanem a több-bérlős Logic Apps futnak. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE-SKUs

Az ISE létrehozásakor kiválaszthatja a Fejlesztői termékváltozatot vagy a Prémium termékváltozatot. Ez a termékváltozat lehetőség csak az ISE létrehozásakor érhető el, és később nem módosítható. A következő különbségek vannak a következő SKUS-k között:

* **Fejlesztő**

  Alacsonyabb költségű ISE-t biztosít, amely feltárásra, kísérletekre, fejlesztésre és tesztelésre használható, éles vagy teljesítményteszteléshez azonban nem. A fejlesztői termékváltozat beépített eseményindítókat és műveleteket, standard összekötőket, [](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) vállalati összekötőket és egy ingyenes szintű integrációs fiókot tartalmaz rögzített [havi áron.](https://azure.microsoft.com/pricing/details/logic-apps) 

  > [!IMPORTANT]
  > Ez a termékváltozat nem rendelkezik szolgáltatásiszint-szerződéssel (SLA), skálázható felskálás képességgel vagy redundanciával az újrahasznosítás során, ami azt jelenti, hogy késést vagy állásidőt tapasztalhat. A háttérfrissítések időnként megszakítják a szolgáltatást.

  Kapacitással és korlátozásokkal kapcsolatos információkért lásd: [ISE limits in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Az ISE-k számlázásának működését az Logic Apps [díjszabási modellben olvashatja.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

* **Prémium**

  Olyan ISE-t biztosít, amely használható az éles környezetben és a teljesítményt tesztelő szolgáltatásban. A Prémium termékváltozat tartalmazza az SLA-támogatást, a beépített triggereket és műveleteket, a Standard összekötőket, az Enterprise-összekötőket, az egyetlen [Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) szintű integrációs fiókot, a felskálás képességet és a redundanciát a rögzített havi áron történő újrahasznosítás [során.](https://azure.microsoft.com/pricing/details/logic-apps)

  Kapacitással és korlátozásokkal kapcsolatos információkért lásd: [ISE limits in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Az ISE-k számlázásának működését az Logic Apps [díjszabási modellben olvashatja.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE-végpont elérése

Az ISE létrehozásakor választhat belső vagy külső hozzáférési végpontok használata között. A választás határozza meg, hogy az ISE logikai alkalmazásában a kérelem- vagy webhook-eseményindítók fogadhatnak-e hívásokat a virtuális hálózaton kívülről. Ezek a végpontok a logikai alkalmazások futtatáselőzményeiből származó bemenetek és kimenetek elérésére is hatással vannak.

> [!IMPORTANT]
> A hozzáférési végpontot csak az ISE létrehozása során választhatja ki, és ezt a beállítást később nem módosíthatja.

* **Belső:** A privát végpontok lehetővé teszik a logikai alkalmazások hívását az ISE-ban, ahol a logikai alkalmazások futtatáselőzményeiből származó bemeneteket és kimeneteket csak a virtuális hálózaton belülről lehet megtekinteni és *elérni.*

  > [!IMPORTANT]
  > Ha ezeket a webhook-alapú eseményindítókat kell használnia, külső végpontokat *használjon,* ne belső végpontokat, amikor létrehozza az ISE-t:
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (ISE-verzió)
  > 
  > Győződjön meg arról is, hogy rendelkezik hálózati kapcsolattal a privát végpontok és a számítógép között, amelyről hozzá szeretne férni a futtatás előzményeihez. Ellenkező esetben a logikai alkalmazás futási előzményeinek megtekintésekor a következő hibaüzenet jelenik meg: "Váratlan hiba. Nem sikerült beolvasása".
  >
  > ![Azure Storage-művelethiba, amely miatt nem lehet tűzfalon keresztül forgalmat küldeni](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Az ügyfélszámítógép létezhet például az ISE virtuális hálózatán belül vagy egy olyan virtuális hálózaton, amely társviszony-létesítésen vagy virtuális magánhálózaton keresztül csatlakozik az ISE virtuális hálózatához. 

* **Külső:** A nyilvános végpontok lehetővé teszik a logikai alkalmazások hívását az ISE-n belül, ahol megtekintheti és elérheti a logikai alkalmazások futtatáselőzményeiből származó bemeneteket és kimeneteket a virtuális *hálózaton kívülről.* Ha hálózati biztonsági csoportokat (NSG-ket) használ, győződjön meg arról, hogy bejövő szabályokkal vannak beállítva, hogy hozzáférést engedélyezzenek a futtatás előzményeinek bemeneteihez és kimeneteihez. További információ: Hozzáférés engedélyezése az [ISE-hez.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

Annak meghatározásához, hogy az ISE belső vagy külső hozzáférési végpontot használ-e, az ISE menüjének Beállítások területén válassza a **Tulajdonságok** lehetőséget, és keresse meg a **Hozzáférési végpont tulajdonságot:**

![ISE hozzáférési végpont megkeresve](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Díjszabási modell

A logikai alkalmazások, a beépített eseményindítók, a beépített műveletek és az ISE-ben futó összekötők rögzített, a használatalapú díjszabási csomagtól eltérő díjszabást használnak. További információ: Logic Apps [modell.](../logic-apps/logic-apps-pricing.md#fixed-pricing) A díjszabást a [díjszabást Logic Apps meg.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrációs fiókok ISE-val

Az integrációs fiókokat logikai alkalmazásokkal használhatja integrációs szolgáltatási környezetben (ISE). Ezeknek az integrációs fiókoknak azonban ugyanazt az *ISE-t kell használniuk,* mint a csatolt logikai alkalmazásoknak. Az ISE logikai alkalmazásai csak olyan integrációs fiókokra hivatkoznak, amelyek ugyanabban az ISE-ban vannak. Integrációs fiók létrehozásakor kiválaszthatja az ISE-t az integrációs fiók helyeként. Az ISE-hez rendelkező integrációs fiókok díjszabásának és számlázásának a Logic Apps [útmutatóban olvashat.](../logic-apps/logic-apps-pricing.md#fixed-pricing) A díjszabást a [díjszabást Logic Apps tekintse meg.](https://azure.microsoft.com/pricing/details/logic-apps/) A korlátozásokkal kapcsolatos információkért lásd: [Integrációs fiókok korlátai.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="next-steps"></a>Következő lépések

* [Csatlakozás Azure-beli virtuális hálózatokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* További információ az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Tudnivalók az [Azure-szolgáltatások virtuális hálózati integrációjáról](../virtual-network/virtual-network-for-azure-services.md)
