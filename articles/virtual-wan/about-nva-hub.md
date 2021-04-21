---
title: 'Azure Virtual WAN: About Network Virtual Appliance in the hub (A hálózati virtuális berendezés a központban)'
description: Ebben a cikkben megismeri a hálózati virtuális berendezéseket a Virtual WAN központban.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
ms.openlocfilehash: 7c3ae14cd409e7bfc9be77c1a593964b73a12ddc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791048"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>A hálózati virtuális berendezés Azure Virtual WAN (előzetes verzió)

Azure Virtual WAN partnerhálózati partnerekkel együttműködésben olyan automatizálást építettek ki, amely megkönnyíti az ügyfél-előtéri berendezések (CPE) csatlakoztatását a virtuális központban lévő Azure VPN-átjáróhoz. Az Azure néhány hálózati partnerrel működik együtt, hogy lehetővé tegye az ügyfelek számára egy külső hálózati virtuális berendezés (NVA) közvetlen üzembe helyezését a virtuális központban. Ez lehetővé teszi, hogy azok az ügyfelek, akik a fiókirodai CPE-t a virtuális központ azonos márkájú NVA-jára szeretnék csatlakoztatni, hogy kihasználják a jogvédett, végpontok között SD-WAN képességeket.

A Barracuda Networks és a Cisco Systems az első olyan partnerek, amelyek közvetlenül az Virtual WAN üzembe helyezhető NVA-Virtual WAN biztosítják.  A megfelelő termékdokumentációért lásd: [Barracuda CloudGen WAN,](https://www.barracuda.com/products/cloudgenwan) [Cisco Cloud OnRamp többfelhős](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) és [VMware SD-WAN-hez.](https://kb.vmware.com/s/article/82746) Az Azure több partnerrel dolgozik együtt, ezért más ajánlatok is várhatók.

> [!NOTE]
> Csak azok az NVA-ajánlatok helyezhetők üzembe a Virtual WAN központban, amelyek üzembe helyezhetők Virtual WAN központban. Nem helyezhetők üzembe tetszőleges virtuális hálózatban az Azure-ban.

## <a name="how-does-it-work"></a><a name="how"></a>Hogyan működik?

A közvetlenül a Azure Virtual WAN üzembe helyezhető NVA-k kifejezetten a virtuális központban való alkalmazásra vannak tervezve. Az NVA-ajánlat felügyelt alkalmazásként van közzétéve a Azure Marketplace-ban, és az ügyfelek közvetlenül az Azure Marketplace-ból is üzembe helyezhetik az ajánlatot, vagy üzembe helyezhetik az ajánlatot a virtuális központból a Azure Portal.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Folyamat áttekintése":::

Az egyes partnerek NVA-ajánlata az üzembe helyezési követelményeiktől függően némileg eltérő felhasználói élményt és funkciókat biztosít. Vannak azonban olyan dolgok, amelyek az NVA összes partnerajánlatában közösek a Virtual WAN központban.

* A felügyelt alkalmazások nyújtotta felhasználói élmény Azure Marketplace.
* NVA-infrastruktúraegység-alapú kapacitás és számlázás.
* Az állapotmetrikák a Azure Monitor.

### <a name="managed-application"></a><a name="managed"></a>Felügyelt alkalmazás

A Virtual WAN központban üzembe helyezhető összes NVA-ajánlathoz rendelkezésre áll egy felügyelt alkalmazás, amely **elérhető** a Azure Marketplace. A felügyelt alkalmazások lehetővé teszik a partnerek számára a következőket:

* Egyéni üzembe helyezési folyamat létrehozása az NVA-jukhoz.
* Adjon meg egy speciális Resource Manager sablont, amely lehetővé teszi számukra az NVA közvetlen létrehozásához a Virtual WAN központban.
* A szoftverlicencelési költségek számlázása közvetlenül vagy Azure Marketplace.
* Egyéni tulajdonságok és erőforrás-mérők elérhetővé tevése.

Az NVA-partnerek különböző erőforrásokat hozhatnak létre a berendezés üzembe helyezésétől, a konfigurációs licenceléstől és a felügyeleti igényektől függően. Amikor egy ügyfél létrehoz egy NVA-t a Virtual WAN központban, mint minden felügyelt alkalmazás, két erőforráscsoport jön létre az előfizetésében.

* **Ügyfél erőforráscsoportja** – Ez tartalmazni fog egy alkalmazás helyőrzőt a felügyelt alkalmazáshoz. A partnerek ezzel bármilyen ügyféltulajdonság elérhetővé teheti őket itt.
* **Felügyelt erőforráscsoport** – Az ügyfelek nem konfigurálhatnak vagy módosíthat erőforrásokat közvetlenül ebben az erőforráscsoportban, mivel ezt a felügyelt alkalmazás közzétevője vezérli. Ez az erőforráscsoport tartalmazza a **NetworkVirtualAppliances erőforrást.**

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Felügyelt alkalmazások erőforráscsoportja":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA-infrastruktúraegységek

Amikor létrehoz egy NVA-t a Virtual WAN központban, ki kell választania, hogy hány NVA-infrastruktúrával szeretné üzembe helyezni. Az **NVA infrastruktúraegység** az NVA összesített sávszélesség-kapacitásának egysége a Virtual WAN központban. Az **NVA infrastruktúraegység** a kapacitás és a méretezés szempontjából a VPN-méretezési egységhez hasonlít. [](pricing-concepts.md#scale-unit)

* 1 Az NVA-infrastruktúraegység 500 Mbps összesített sávszélességet jelent az ehhez az NVA-ba érkező összes fiókirodai kapcsolathoz, óránként 0,25 USD költséggel.
* Az Azure 1–80 NVA infrastruktúraegységet támogat egy adott NVA virtuális központ üzembe helyezéséhez.
* Minden partner különböző NVA-infrastruktúraegység-csomagokat kínálhat, amelyek az összes támogatott NVA-infrastruktúraegység-konfiguráció részkészletét jelentik.

A VPN-méretezési egységekhez hasonlóan, ha az *1 NVA Infrastructure Unit = 500 Mbps* értéket választjuk, az azt jelenti, hogy két példány jön létre a redundancia érdekében, amelyek maximális átviteli sebessége 500 Mb/s. Ha például öt ága van, amelyek mindegyikének 10 Mbps a ága, akkor a fej végén 50 Mb/s-os összesítésre lesz szükség. Az NVA összesített kapacitásának megtervezése a központ ágai számának támogatásához szükséges kapacitás felmérése után történik.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Hálózati virtuális berendezés konfigurációs folyamata

A partnerek olyan felhasználói élményt kínálnak, amely az üzembe helyezési folyamat részeként automatikusan konfigurálja az NVA-t. Miután az NVA ki lett építve a virtuális központban, az NVA-hoz esetlegesen szükséges további konfigurációkat az NVA-partnerek portálján vagy felügyeleti alkalmazásán keresztül kell megadni. Az NVA-hoz való közvetlen hozzáférés nem érhető el.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Hely- és kapcsolat-erőforrások NVA-okkal

Az Azure VPN Gateway-konfigurációktól eltérően nem  kell helyerőforrásokat, helyek és  helyek között kapcsolati erőforrásokat vagy pont–hely kapcsolati erőforrásokat létrehoznia ahhoz, hogy az ághelyeket az Virtual WAN központban található NVA-hoz csatlakoztassa.  Mindezt az NVA-partner kezeli.

Továbbra is létre kell hoznia hub–VNet kapcsolatokat az Virtual WAN azure-beli virtuális hálózatokhoz való csatlakoztatásához.

## <a name="supported-regions"></a><a name="regions"></a>Támogatott régiók

A virtuális központban lévő NVA előzetes verziója a következő régiókban érhető el:

|Geopolitikai régió | Azure-régiók|
|---|---|
| Észak-Amerika| Közép-Kanada, Kelet-Kanada, USA középső régiója, USA keleti régiója, USA 2. keleti régiója, USA északi középső régiója, USA nyugati középső régiója, USA nyugati régiója, USA 2. nyugati régiója |
| Dél-Amerika | Dél-Brazília, Délkelet-Brazília |
| Európa | Közép-Franciaország, Dél-Franciaország, Észak-Németország, Nyugat-Németország, Észak-Európa, Kelet-Németország, Nyugat-Németország, Észak-Svájc, Nyugat-Svájc, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa|
|  Közel-Kelet | Egyesült Arab Emírségek északi részén |
| Ázsia |  Kelet-Ázsia, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea déli régiója, Délkelet-Ázsia | 
| Ausztrália | Délkelet-Ausztrália, Kelet-Ausztrália, Ausztrália középső régiója, Ausztrália 2. középső régiója|
| Afrika | Dél-Afrika északi régiója |
| India | Dél-India, Nyugat-India, Közép-India | 
## <a name="faq"></a>GYIK

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Hálózati készülékpartner vagyok, és szeretném az NVA-t a központba behozni.  Csatlakozhatok ehhez a partnerprogramhoz?

Sajnos jelenleg nem tudunk új partneri ajánlatokat bevetni. Novemberben hozzánk is vissza kell mennie!

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Üzembe helyezhetek bármilyen NVA-t Azure Marketplace a Virtual WAN központban?

Jelenleg csak a [Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer)  [Cisco Cloud vWAN-alkalmazás](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) és [a VMware Sd-WAN](https://aka.ms/vmwareMarketplaceLink) érhető el az Virtual WAN központba.

### <a name="what-is-the-cost-of-the-nva"></a>Mi az NVA költsége?

Az NVA-t az NVA szállítójától kell megvásárolni.  A Barracuda CloudGen WAN NVA Barracuda-licencről a [Barracuda CloudGen WAN-oldalán található.](https://www.barracuda.com/products/cloudgenwan) A Cisco jelenleg csak a BYOL (saját licenc használatának) licencelési modelljét kínálja, amelyet közvetlenül a Cisco-tól kell beszerzetni. Emellett díjat kell fizetni a Microsofttól a felhasznált NVA-infrastruktúraegységekért és az ön által használt egyéb erőforrásokért. További információ: [Díjszabási fogalmak.](pricing-concepts.md)

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Üzembe helyezhetek NVA-t alapszintű központban?

Nem. Ha NVA-t szeretne üzembe helyezni, standard hubot kell használnia.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Üzembe helyezhetek NVA-t biztonságos központban?

Igen. A partner NVA-i üzembe helyezhetők egy központi Azure Firewall.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Csatlakoztatok bármilyen CPE-eszközt a fiókirodában a barracuda CloudGen WAN NVA-hoz a központban?

Nem. A Barracuda CloudGen WAN csak a Barracuda CPE-eszközökkel kompatibilis. A CloudGen WAN követelményeivel kapcsolatos további információkért lásd a [Barracuda CloudGen WAN-oldalát.](https://www.barracuda.com/products/cloudgenwan) A Cisco több SD-WAN CPE-eszközt is biztosít, amelyek kompatibilisek. Tekintse meg a [Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) documenation for compatable CPE (Cisco Cloud OnRamp többfelhős szolgáltatáshoz) a kompatibilis CPE-ket.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Milyen útválasztási forgatókönyvek támogatottak az NVA-val a központban?

A központi NVA Virtual WAN minden útválasztási forgatókönyv támogatott.

## <a name="next-steps"></a>Következő lépések

További információt a Virtual WAN a Virtual WAN [cikkben](virtual-wan-about.md) talál.
