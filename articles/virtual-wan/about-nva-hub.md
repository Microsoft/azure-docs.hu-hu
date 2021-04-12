---
title: 'Azure Virtual WAN: a hálózati virtuális berendezés ismertetése a központban'
description: Ebből a cikkből megtudhatja, hogyan fog megjelenni a virtuális WAN hub hálózati virtuális berendezése.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
ms.openlocfilehash: e70b899ea0133704e4ecab42dd4c2a09bca41804
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226311"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Tudnivalók a hálózati virtuális berendezésről egy Azure-beli virtuális WAN-központban (előzetes verzió)

Az Azure Virtual WAN a hálózati partnerekkel együttműködve automatizálja az automatizálást, amely megkönnyíti az ügyfelekhez kapcsolódó berendezések (CPE) összekapcsolását a virtuális központ Azure VPN Gateway-átjárójában. Az Azure a hálózati partnerek kiválasztása révén lehetővé teszi, hogy az ügyfelek közvetlenül a virtuális hubhoz helyezzen üzembe egy külső hálózati virtuális berendezést (NVA). Ez lehetővé teszi, hogy azok az ügyfelek, akik a fiókirodát a virtuális központ ugyanazon NVA szeretnék csatlakoztatni, így képesek kihasználni a teljes körű SD-WAN-képességeket.

A Barracuda Networks és a Cisco Systems az első partnerek, akik közvetlenül a virtuális WAN-központba telepíthetik a NVA.  Tekintse meg a [Barracuda CLOUDGEN WAN](https://www.barracuda.com/products/cloudgenwan), a [Cisco Cloud OnRamp for multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) és a [VMware SD-WAN](https://kb.vmware.com/s/article/82746) című témakört a megfelelő termék-dokumentációhoz. Az Azure több partnerrel dolgozik, ezért várhatóan további ajánlatokat is láthat.

> [!NOTE]
> Csak a virtuális WAN-hubhoz üzembe helyezhető NVA-ajánlatok telepíthetők a virtuális WAN-központba. Nem helyezhetők üzembe egy tetszőleges virtuális hálózatban az Azure-ban.

## <a name="how-does-it-work"></a><a name="how"></a>Hogyan működik?

Az Azure-beli virtuális WAN-hubhoz központilag üzembe helyezhető NVA kifejezetten a virtuális központban való használatra lettek tervezve. Az NVA ajánlatot felügyelt alkalmazásként tesszük közzé az Azure Marketplace-en, és az ügyfelek közvetlenül az Azure Marketplace-en telepíthetik az ajánlatot, vagy a Azure Portal használatával telepíthetik az ajánlatot a virtuális központból.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Folyamat áttekintése":::

Minden partner NVA-ajánlata némileg eltérő élményt és funkcionalitást biztosít az üzembe helyezési követelmények alapján. Vannak azonban olyan dolgok, amelyek az összes partneri ajánlatban közösek a Virtual WAN hub NVA.

* Az Azure Marketplace-en keresztül elérhető felügyelt alkalmazási élmény.
* NVA infrastruktúra-egységen alapuló kapacitás és számlázás.
* Azure Monitoron keresztül felszínre kerülő állapot-mérőszámok.

### <a name="managed-application"></a><a name="managed"></a>Felügyelt alkalmazás

A virtuális WAN-központba üzembe helyezhető összes NVA-ajánlat egy **felügyelt alkalmazással** fog rendelkezni, amely elérhető az Azure Marketplace-en. A felügyelt alkalmazások lehetővé teszik a partnerek számára a következőket:

* Hozzon létre egyéni üzembe helyezési élményt a NVA.
* Adjon meg egy speciális Resource Manager-sablont, amely lehetővé teszi, hogy közvetlenül a virtuális WAN-központban hozza létre a NVA.
* A szoftver licencelésének költségei közvetlenül vagy az Azure Marketplace-en keresztül.
* Egyéni tulajdonságok és erőforrás-mérőszámok közzététele.

A NVA-partnerek különböző erőforrásokat hozhatnak létre a berendezésük üzembe helyezése, a konfiguráció licencelése és a felügyeleti igényektől függően. Ha egy ügyfél létrehoz egy NVA a virtuális WAN-központban, például az összes felügyelt alkalmazáshoz, akkor az előfizetésében két erőforráscsoport jön létre.

* **Felhasználói erőforráscsoport** – a felügyelt alkalmazáshoz tartozó alkalmazás helyőrzőjét fogja tartalmazni. A partnerek ezt felhasználhatják az itt választott ügyfél-tulajdonságok elérhetővé tételéhez.
* **Felügyelt erőforráscsoport** – a felhasználók nem konfigurálhatják és nem változtathatják meg közvetlenül az erőforráscsoport erőforrásait, mivel ezt a felügyelt alkalmazás közzétevője vezérli. Ez az erőforráscsoport a **NetworkVirtualAppliances** erőforrást fogja tartalmazni.

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Felügyelt alkalmazási erőforráscsoportok":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA infrastruktúra-egységek

Amikor létrehoz egy NVA a virtuális WAN-központban, ki kell választania, hogy hány NVA infrastruktúra-egységet kíván telepíteni. Az **NVA infrastruktúra-egység** a virtuális WAN-központban található NVA összesített sávszélesség-kapacitásának egysége. A **NVA infrastruktúra-egység** hasonló a VPN- [méretezési egységekhez](pricing-concepts.md#scale-unit) , mint a kapacitás és a méretezés tekintetében.

* 1 a NVA-infrastruktúra egysége 500 Mbps összesített sávszélességet képvisel a NVA érkező összes fiókirodai kapcsolathoz
* Az Azure a 1-80 NVA infrastruktúra-egységeket támogatja egy adott NVA virtuális központ üzembe helyezéséhez.
* Az egyes partnerek különböző NVA infrastruktúra-egységeket használhatnak, amelyek az összes támogatott NVA-infrastruktúra egységének egy részhalmazát alkotják.

A VPN-méretezési egységekhez hasonlóan, ha *1 NVA infrastrukturális egység = 500 Mbps* értéket ad meg, az azt jelenti, hogy a redundancia két példánya jön létre, amelyek mindegyike 500 Mbps maximális átviteli sebességgel rendelkezik. Ha például öt ág van, amelyek mindegyike 10 Mbps-t használ a fiókirodában, akkor a fő végponton 50 Mbps összesített értékre van szükség. A NVA összesített kapacitásának megtervezését a hub-ágak számának támogatásához szükséges kapacitás kiértékelése után kell elvégezni.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Hálózati virtuális berendezés konfigurációs folyamata

A partnerek úgy dolgoztak, hogy olyan élményt nyújtsanak, amely automatikusan konfigurálja a NVA a telepítési folyamat részeként. Ha a NVA a virtuális hubhoz lett kiépítve, a NVA szükséges további konfigurációkat az NVA Partners portálon vagy a felügyeleti alkalmazáson keresztül kell elvégezni. A NVA való közvetlen hozzáférés nem érhető el.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Helyek és kapcsolatok erőforrásai a NVA

Az Azure VPN Gateway konfigurációktól eltérően nem **kell létrehoznia a telephelyi** erőforrásokat, a **helyek közötti kapcsolati** erőforrásokat, illetve a **pont – hely kapcsolati** erőforrásokat, hogy a fiókirodákat a NVA a virtuális WAN-központban lehessen csatlakoztatni. Mindezt a NVA-partneren keresztül felügyeli.

Továbbra is VNet kapcsolatokat kell létrehoznia a virtuális WAN-központ Azure-beli virtuális hálózatokhoz való csatlakoztatásához.

## <a name="supported-regions"></a><a name="regions"></a>Támogatott régiók

A virtuális központ NVA az alábbi régiókban érhető el előzetes verzióra:

|Geopolitikai régió | Azure-régiók|
|---|---|
| Észak-Amerika| USA nyugati régiója, USA déli középső régiója, USA 2. keleti régiója   |
| Dél-Amerika | Dél-Brazília |
| Európa | Nyugat-Európa, Egyesült Királyság déli régiója|
|  Közel-Kelet | Észak-Egyesült Arab |
| Ázsia | Kelet-Japán |
| Ausztrália | Kelet-Ausztrália |

## <a name="faq"></a>GYIK

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Hálózati Appliance-partner vagyok, és szeretném lekérni a NVA a központban.  Csatlakozhatok ehhez a partneri programhoz?

Sajnos jelenleg nem áll rendelkezésre az új partnereinkkel kapcsolatos kapacitás. Térjen vissza velünk novemberben!

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Telepíthetek bármilyen NVA az Azure Marketplace-ről a virtuális WAN-hubhoz?

Jelenleg csak a [Barracuda CLOUDGEN WAN](https://aka.ms/BarracudaMarketPlaceOffer) és a [Cisco Cloud vWAN alkalmazás](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) érhető el a virtuális WAN-központban való üzembe helyezéshez.

### <a name="what-is-the-cost-of-the-nva"></a>Mi a NVA díja?

A NVA-licencet a NVA gyártótól kell megvásárolnia.  A Barracuda-licenccel rendelkező Barracuda CloudGen WAN-NVA lásd: [Barracuda 's CLOUDGEN WAN oldal](https://www.barracuda.com/products/cloudgenwan). A Cisco jelenleg csak az BYOL (saját licenc használata) licencelési modellt kínálja, amelyet közvetlenül a Cisco-től kell beszereznie. Emellett a Microsofttól a felhasznált NVA infrastruktúra-egységekre, valamint az Ön által használt egyéb erőforrásokra is díjat számítunk fel. További információkért tekintse meg a [díjszabási fogalmakat](pricing-concepts.md).

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Telepíthetek NVA egy alapszintű hubhoz?

Nem. Ha NVA szeretne üzembe helyezni, szabványos hubot kell használnia.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Telepíthetek egy NVA egy biztonságos hubhoz?

Igen. A Barracuda CloudGen WAN üzembe helyezhető Azure Firewallokkal rendelkező hubhoz.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Csatlakoztatok bármilyen CPE-eszközt a fiókirodában a Barracuda CloudGen WAN-NVA a központban?

Nem. A Barracuda CloudGen WAN csak a Barracuda CPE-eszközökkel kompatibilis. További információ a CloudGen WAN-követelményekről: [Barracuda 's CLOUDGEN WAN oldal](https://www.barracuda.com/products/cloudgenwan). A Cisco esetében több SD-WAN CPE-eszköz található, amely kompatibilis. A kompatibilis CPEs a [Cisco Cloud OnRamp for multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) documenation találhat.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Milyen útválasztási forgatókönyvek támogatottak a hub NVA?

A virtuális WAN által támogatott összes útválasztási forgatókönyv támogatott a hub NVA.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a virtuális WAN-ról, tekintse meg a [virtuális WAN áttekintését](virtual-wan-about.md) ismertető cikket.
