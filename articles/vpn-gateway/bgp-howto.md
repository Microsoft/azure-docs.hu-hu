---
title: 'A BGP konfigurálása VPN Gatewayhoz: portál'
titleSuffix: Azure VPN Gateway
description: Ez a cikk végigvezeti a BGP Azure VPN Gateway-vel való konfigurálásának lépésein a PowerShell használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/22/2021
ms.author: yushwang
ms.openlocfilehash: 479a8fac111be6e5b1ae2c6ea21fff801ba26f83
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863581"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>A BGP konfigurálása Azure VPN Gateway-átjárók esetén

Ez a cikk bemutatja, hogyan engedélyezheti a BGP-t a telephelyek közötti helyek közötti (S2S) VPN-kapcsolaton és egy VNet-VNet kapcsolaton keresztül a Azure Portal használatával.

## <a name="about-bgp"></a><a name="about"></a>A BGP ismertetése

A BGP az interneten gyakran használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információcserére két vagy több hálózat között. A BGP lehetővé teszi, hogy az Azure VPN-átjárók és a helyszíni VPN-eszközök BGP-társak vagy szomszédok legyenek az Exchange "Routes" szolgáltatásban, amely tájékoztatja mindkét átjárót az előtagok rendelkezésre állásáról és elérhetőségéről, hogy átugorjon az érintett átjárók vagy útválasztók számára. A BGP lehetővé teszi a több hálózat közötti tranzit útválasztást azon útvonalak propagálásával az összes többi BGP-társ számára, amelyeket a BGP-átjáró az egyik BGP-társtól vesz át.

További információ a BGP előnyeiről és a BGP használatának technikai követelményeiről és szempontjairól: a BGP és az [Azure VPN Gateway közötti áttekintés](vpn-gateway-bgp-overview.md).

## <a name="getting-started"></a>Első lépések

A cikk mindegyik része segítséget nyújt egy alapszintű Építőelem kialakításához, amely lehetővé teszi a BGP hálózati kapcsolaton keresztüli engedélyezését. Ha mindhárom rész elkészült, az 1. ábrán látható módon hozza létre a topológiát.

**1. ábra**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="A hálózati architektúrát és a beállításokat bemutató ábra" border="false":::

A részeket kombinálva egyesítheti az igényeinek megfelelő összetettebb, több ugrást biztosító átviteli hálózatot.

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>1. rész: a BGP konfigurálása a virtuális hálózati átjárón

Ebben a szakaszban egy virtuális hálózatot hoz létre és konfigurál, valamint egy virtuális hálózati átjárót hoz létre és konfigurál a BGP-paraméterekkel, és beszerezheti az Azure BGP-társ IP-címét. A 2. diagram a jelen szakasz lépéseinek használatakor használandó konfigurációs beállításokat jeleníti meg.

**2. ábra**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="A virtuális hálózati átjáró beállításait bemutató ábra" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. TestVNet1 létrehozása és konfigurálása

Ebben a lépésben létrehozza és konfigurálja a TestVNet1. Az Azure Virtual Network és a VPN Gateway létrehozásához és konfigurálásához kövesse az [átjáró létrehozása oktatóanyagot](./tutorial-create-gateway-portal.md) . Használja az alábbi képernyőképek hivatkozási beállításait.

* Virtuális hálózat:

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="TestVNet1 a megfelelő címek előtagjaival":::

* Alhálózatok:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="TestVNet1 alhálózatok":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. a TestVNet1 VPN Gateway létrehozása BGP-paraméterekkel

Ebben a lépésben létrehoz egy VPN-átjárót a megfelelő BGP-paraméterekkel.

1. A Azure Portal navigáljon az **Virtual Network Gateway** -erőforráshoz a piactéren, és válassza a **Létrehozás** lehetőséget.

1. Adja meg a paramétereket az alább látható módon:

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="VNG1 létrehozása":::

1. A lap Kiemelt **BGP-konfiguráció** szakaszában adja meg a következő beállításokat:

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="A BGP konfigurálása":::

   * Válassza a **BGP konfigurálása**  -  **engedélyezve** lehetőséget a BGP konfigurációs szakasz megjelenítéséhez.

   * Töltse ki az ASN-t (autonóm System Number).

   * Az **Azure APIPA BGP IP-cím** mezője nem kötelező. Ha a helyszíni VPN-eszközök a BGP-vel kapcsolatos APIPA-címeket használnak, ki kell választania egy, az Azure által fenntartott APIPA-címtartományből származó, a **169.254.21.0** -ről **169.254.22.255**-re vonatkozó címeket. Ez a példa az 169.254.21.11-t használja.

   * Ha aktív-aktív VPN-átjárót hoz létre, akkor a BGP szakasz egy további, **második egyéni Azure APIPA-os BGP IP-címet** jelenít meg. Az engedélyezett APIPA-tartományból (**169.254.21.0** – **169.254.22.255**) Válasszon másik IP-címet. A második IP-címnek az első címtől eltérőnek kell lennie.

   > [!IMPORTANT]
   >
   > * Alapértelmezés szerint az Azure automatikusan hozzárendel egy magánhálózati IP-címet a GatewaySubnet előtag-tartományhoz, az Azure-beli VPN-átjárón lévő Azure BGP IP-címként. Az egyéni Azure APIPA BGP-címnek akkor van szüksége, ha a helyszíni VPN-eszközök a BGP-IP-címként használják az APIPA-címet (169.254.0.1 – 169.254.255.254). Az Azure VPN Gateway akkor választja ki az egyéni APIPA-címet, ha a megfelelő helyi hálózati átjáró erőforrás (helyszíni hálózat) APIPA-címmel rendelkezik a BGP-társ IP-címével. Ha a helyi hálózati átjáró egy normál IP-címet (nem APIPA-t) használ, az Azure VPN Gateway a magánhálózati IP-címet a GatewaySubnet tartományból fogja visszaállítani.
   >
   > * Az APIPA BGP-címek nem lehetnek átfedésben a helyszíni VPN-eszközök és az összes csatlakoztatott Azure VPN-átjáró között.
   >
   > * Ha az Azure VPN Gateway-átjárók APIPA-címeket használnak, az átjárók nem kezdeményeznek BGP-társas munkameneteket az APIPA-forrás IP-címeivel. A helyszíni VPN-eszköznek BGP-társas kapcsolatokat kell kezdeményeznie.
   >

1. Válassza az **ellenőrzés + létrehozás** lehetőséget az érvényesítés futtatásához. Az érvényesítést követően a **Létrehozás** gombra kattintva telepítheti a VPN-átjárót. Az átjárók teljes létrehozása és üzembe helyezése akár 45 percet is igénybe vehet. A telepítési állapotot az átjáró áttekintés lapján tekintheti meg.

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. az Azure BGP-társ IP-címeinek beszerzése

Az átjáró létrehozása után beszerezheti a BGP-társ IP-címeit az Azure VPN Gateway-ben. Ezek a címek szükségesek a helyszíni VPN-eszközök konfigurálásához a BGP-munkamenetek létrehozásához az Azure VPN Gateway használatával.

1. Navigáljon a virtuális hálózati átjáró erőforráshoz, és válassza a **konfiguráció** lapot a BGP konfigurációs adatainak megtekintéséhez, ahogy az alábbi képernyőképen is látható. Ezen az oldalon megtekintheti az Azure-beli VPN-átjárón található összes BGP-konfigurációs információt: az ASN-t, a nyilvános IP-címet, valamint a kapcsolódó BGP-társ IP-címeket az Azure oldalon (alapértelmezett és APIPA).

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="BGP-átjáró":::

1. A **konfiguráció** lapon a következő konfigurációs módosításokat végezheti el:

   * Szükség esetén frissítheti az ASN-t vagy az APIPA-alapú BGP IP-címet.
   * Ha aktív-aktív VPN-átjáróval rendelkezik, ezen az oldalon a második Azure VPN Gateway-példány nyilvános IP-címe, alapértelmezett és APIPA BGP IP-címe jelenik meg.

1. Ha módosította a módosításokat, válassza a **Mentés** lehetőséget, hogy véglegesítse a módosításokat az Azure-beli VPN-átjárón.

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>2. rész: a BGP konfigurálása a létesítmények közötti S2S-kapcsolatokon

Létesítmények közötti kapcsolat létrehozásához létre kell hoznia egy *helyi hálózati átjárót* , amely a helyszíni VPN-eszközt jelöli, valamint egy *kapcsolatot* a VPN-átjáró a helyi hálózati átjáróval való csatlakoztatásához a [helyek közötti kapcsolat létrehozása](./tutorial-site-to-site-portal.md)című részben leírtak szerint. Ez a cikk a BGP konfigurációs paramétereinek megadásához szükséges további tulajdonságokat tartalmazza.

**3. ábra**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="Az IPsec-t bemutató diagram" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. a BGP konfigurálása a helyi hálózati átjárón

Ebben a lépésben a BGP-t konfigurálja a helyi hálózati átjárón. Példaként használja az alábbi képernyőképet. A képernyőképen a helyi hálózati átjáró (site5) látható a 3. ábrán megadott paraméterekkel.

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="A BGP konfigurálása a helyi hálózati átjáróhoz":::

#### <a name="important-configuration-considerations"></a>Fontos konfigurációs szempontok

* Az ASN-nek és a BGP-társ IP-címnek egyeznie kell a helyszíni VPN-útválasztó konfigurációjával.
* A **címtartomány** csak akkor hagyható üresen, ha a BGP használatával csatlakozik ehhez a hálózathoz. Az Azure VPN Gateway belsőleg hozzá fogja adni a BGP-társ IP-címének útvonalát a megfelelő IPsec-alagúthoz. Ha **nem** használja a BGP-t az Azure VPN Gateway és az adott hálózat között, meg **kell** adnia a **címtartomány** érvényes címe-előtagjainak listáját.
* Ha szükséges, használhat egy **APIPA IP-címet** (169.254. x. x) a helyszíni BGP-társ IP-címével. Emellett az Azure VPN Gateway esetében is meg kell adnia egy APIPA IP-címet, amelyet a jelen cikk korábbi részében ismertetett, ellenkező esetben a BGP-munkamenet nem tud kapcsolatot létesíteni ehhez a kapcsolathoz.
* Megadhatja a BGP konfigurációs adatait a helyi hálózati átjáró létrehozásakor, vagy hozzáadhat vagy módosíthat BGP-konfigurációt a helyi hálózati átjáró erőforrás **konfiguráció** lapján.

**Példa**

Ez a példa egy APIPA-címet (169.254.100.1) használ a helyszíni BGP-társ IP-címeként:

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="Helyi hálózati átjáró APIPA és BGP":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. S2S-alapú kapcsolatok konfigurálása BGP-kompatibilis

Ebben a lépésben létrehoz egy új, BGP-t használó-kapcsolatokat. Ha már van internetkapcsolata, és engedélyezni szeretné a BGP-t, [frissítheti a meglévő kapcsolatokat](#update).

#### <a name="to-create-a-connection-with-bgp-enabled"></a>A BGP-t használó kapcsolatok létrehozása

Ha a BGP-vel való új kapcsolódást szeretne létrehozni, akkor a **Kapcsolódás hozzáadása** lapon adja meg az értékeket, majd jelölje be a **BGP** engedélyezése beállítást a következő kapcsolatban:. A kapcsolat létrehozásához válassza az **OK** lehetőséget.

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="IPsec létesítmények közötti kapcsolatok BGP-vel":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>Meglévő kapcsolatok frissítése

Ha módosítani szeretné a BGP beállítást egy kapcsolatban, lépjen a kapcsolódási erőforrás **konfiguráció** lapjára, majd a következő példában látható módon állítsa be a **BGP** kapcsolót. A módosítások mentéséhez kattintson a **Mentés** gombra.

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="A BGP frissítése egy kapcsolatban":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>3. rész: a BGP konfigurálása VNet-VNet kapcsolatokra

A BGP VNet-VNet kapcsolaton való engedélyezéséhez vagy letiltásához szükséges lépések megegyeznek a [2. részben](#crosspremises)található S2S lépésekkel. Engedélyezheti a BGP-t a kapcsolatok létrehozásakor, vagy frissítheti a konfigurációt egy meglévő VNet-VNet-kapcsolatban.

>[!NOTE] 
>A BGP nélküli VNet-VNet kapcsolat csak a két csatlakoztatott virtuális hálózatok irányuló kommunikációt korlátozza. Engedélyezze a BGP-t, hogy átengedje a továbbítási útválasztási képességet a két virtuális hálózatok más S2S vagy VNet-VNet kapcsolataira.
>

A **4. ábrára** hivatkozó kontextus esetében, ha a BGP-t le kell tiltani a TestVNet2 és a TestVNet1 között, a TestVNet2 nem fogja megtanulni a helyszíni hálózat, a site5 útvonalait, ezért nem tudott kommunikálni az 5. hellyel. Miután engedélyezte a BGP-t, ahogy az a 4. ábrán is látható, mindhárom hálózat képes lesz kommunikálni az IPsec és a VNet közötti kapcsolaton keresztül.

**4. ábra**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Teljes hálózatot bemutató diagram" border="false":::

## <a name="next-steps"></a>Következő lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/windows/quick-create-portal.md).