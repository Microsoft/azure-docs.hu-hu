---
title: 'Hozzon létre egy kapcsolatot a virtuális hálózatok: Classic: Azure Portal között'
description: A PowerShell és a Azure Portal együttes használatával összekapcsolhatja az Azure-beli virtuális hálózatokat.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103220"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>VNet-VNet közötti kapcsolatok konfigurálása (klasszikus)

Ebből a cikkből megtudhatja, hogyan hozhat létre VPN Gateway-kapcsolatot a virtuális hálózatok között. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="A klasszikus VNet-VNet architektúrát bemutató ábra":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

A cikkben ismertetett lépések a klasszikus üzemi modellre és a Azure Portal vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Klasszikus](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Virtuális hálózatok összekötése különböző üzemi modellekben](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>Tudnivalók a virtuális hálózatok közötti kapcsolatokról

A virtuális hálózat egy másik virtuális hálózathoz (VNet – VNet) való csatlakoztatása a klasszikus üzemi modellben a VPN Gateway használatával hasonló a virtuális hálózat helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához.

A csatlakoztatott virtuális hálózatok különböző előfizetésekben és különböző régiókban lehet. A VNet kombinálva VNet a többhelyes konfigurációkkal való kommunikációt. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="A kapcsolatokat bemutató diagram":::

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Miért érdemes összekapcsolni a virtuális hálózatokat?

A virtuális hálózatokat a következő okokból érdemes összekapcsolni:

* **Georedundancia és földrajzi jelenlét több régióban**

  * Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
  * A Azure Load Balancer és a Microsoft vagy harmadik féltől származó fürtözési technológiával több Azure-régióban is beállíthatja a kiválóan elérhető számítási feladatokat. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.
* **Regionális többrétegű alkalmazások erős elkülönítési határral**

  * Ugyanazon a régión belül több virtuális hálózatok csatlakoztatott többrétegű alkalmazásokat is beállíthat, amelyek erős elkülönítéssel és a rétegek közötti kommunikációval rendelkeznek.
* **Több előfizetés, szervezeten belüli kommunikáció az Azure-ban**

  * Ha több Azure-előfizetéssel is rendelkezik, a különböző előfizetésekhez tartozó számítási feladatokat a virtuális hálózatok között biztonságosan összekapcsolhatja.
  * Vállalatok vagy szolgáltatók számára engedélyezheti a szervezeten belüli kommunikációt a biztonságos VPN-technológiával az Azure-ban.

A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, [A virtuális hálózatok közötti kapcsolatokra vonatkozó szempontok](#faq) című részben talál.

## <a name="prerequisites"></a>Előfeltételek

A legtöbb lépésben a portált használjuk, de a PowerShell használatával kell létrehoznia a virtuális hálózatok közötti kapcsolatokat. A kapcsolatok nem hozhatók létre a Azure Portal használatával, mert a portálon nincs lehetőség a megosztott kulcs megadására. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>Tervezés

Fontos, hogy döntse el, milyen tartományokat fog használni a virtuális hálózatok konfigurálásához. Ehhez a konfigurációhoz meg kell győződnie arról, hogy egyik VNet tartomány sem fedi egymást egymással, sem pedig azokkal a helyi hálózatokkal, amelyekhez csatlakoznak.

### <a name="vnets"></a><a name="vnet"></a>Virtuális hálózatok

Ebben a gyakorlatban a következő példában szereplő értékeket használjuk:

**TestVNet1 értékei**

Név: TestVNet1<br>
Címterület: 10.11.0.0/16, 10.12.0.0/16 (nem kötelező)<br>
Alhálózat neve: default<br>
Alhálózati címtartomány: 10.11.0.0/24<br>
Erőforráscsoport: ClassicRG<br>
Hely: East US<br>
GatewaySubnet: 10.11.1.0/27

**TestVNet4 értékei**

Név: TestVNet4<br>
Címterület: 10.41.0.0/16, 10.42.0.0/16 (nem kötelező)<br>
Alhálózat neve: default<br>
Alhálózati címtartomány: 10.41.0.0/24<br>
Erőforráscsoport: ClassicRG<br>
Hely: West US<br>
GatewaySubnet: 10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>Kapcsolatok

Az alábbi táblázat egy példát mutat be, hogyan fogja összekapcsolásra a virtuális hálózatok. A tartományokat csak iránymutatásként használja. Jegyezze fel a virtuális hálózatok tartományait. Ezekre az információkra szüksége lesz a későbbi lépésekhez.

Ebben a példában a TestVNet1 egy "VNet4Local" nevű helyi hálózati telephelyhez csatlakozik. A VNet4Local beállításai tartalmazzák a TestVNet4 tartozó címek előtagjait.
Az egyes VNet helyi helye a másik VNet. A következő példában szereplő értékek használatosak a konfigurációhoz:

**Példa**

| Virtual Network | Címtartomány | Hely | Kapcsolódás helyi hálózati helyhez |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA keleti régiója |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA nyugati régiója |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>Virtuális hálózatok létrehozása

Ebben a lépésben két klasszikus virtuális hálózatot hoz létre, a TestVNet1 és a TestVNet4. Ha gyakorlatként használja ezt a cikket, használja a példában szereplő [értékeket](#vnet).

**A virtuális hálózatok létrehozásakor vegye figyelembe az alábbi beállításokat:**

* **Virtual Network címterület** – a Virtual Network címterület lapon adja meg a virtuális hálózathoz használni kívánt címtartományt. Ezek a dinamikus IP-címek lesznek hozzárendelve a virtuális gépekhez és más, a virtuális hálózaton üzembe helyezett szerepkörökhöz.<br>A kiválasztott címterület nem fedik át a többi virtuális hálózatok vagy a helyszíni helyek számára, amelyhez ez a VNet csatlakozni fog.

* **Hely** – a virtuális hálózat létrehozásakor társítsa azt egy Azure-beli helyhez (régióhoz). Ha például azt szeretné, hogy a virtuális hálózaton üzembe helyezett virtuális gépek fizikailag az USA nyugati régiójában legyenek, válassza ki ezt a helyet. A virtuális hálózathoz társított hely nem módosítható a létrehozása után.

**A virtuális hálózatok létrehozása után a következő beállításokat adhatja hozzá:**

* **Címterület** – ehhez a konfigurációhoz nem szükséges további címtartomány, de a VNet létrehozása után további címtartományt is hozzáadhat.

* **Alhálózatok** – további alhálózatok nem szükségesek ehhez a konfigurációhoz, de előfordulhat, hogy a virtuális gépeket egy olyan alhálózaton szeretné használni, amely elkülönül a többi szerepkör-példánytól.

* **DNS-kiszolgálók** – adja meg a DNS-kiszolgáló nevét és IP-címét. A beállítás nem hoz létre DNS-kiszolgálót. Lehetővé teszi, hogy megadja azokat a DNS-kiszolgálókat, amelyeket névfeloldásra kíván használni ennél a virtuális hálózatnál.

### <a name="to-create-a-classic-virtual-network"></a>Klasszikus virtuális hálózat létrehozása

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>Helyek és átjárók konfigurálása

Az Azure az egyes helyi hálózati helyekben megadott beállítások alapján határozza meg, hogyan irányítsa át a forgalmat a virtuális hálózatok között. Minden VNet arra a helyi hálózatra kell mutatnia, amelyhez át szeretné irányítani a forgalmat. Meg kell határoznia, hogy melyik nevet szeretné használni az egyes helyi hálózati helyekre való hivatkozáshoz. A legjobb megoldás, ha valami leírót használ.

Például a TestVNet1 egy "VNet4Local" nevű helyi hálózati webhelyhez csatlakozik. A VNet4Local beállításai tartalmazzák a TestVNet4 tartozó címek előtagjait.

Ne feledje, hogy az egyes VNet helyi helye a másik VNet.

| Virtual Network | Címtartomány | Hely | Kapcsolódás helyi hálózati helyhez |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA keleti régiója |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA nyugati régiója |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>Hely konfigurálása

A helyi hely általában a használat helyszínét jelenti. Tartalmazza azon VPN-eszköz IP-címét, amelyhez kapcsolatot szeretne létesíteni, valamint azokat az IP-címtartományokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva.

1. A VNet lapjának **Beállítások** területén válassza a **helyek közötti kapcsolatok** lehetőséget.
1. A helyek közötti kapcsolatok lapon válassza a **+ Hozzáadás** lehetőséget.
1. A **VPN-kapcsolat és-átjáró konfigurálása** lapon a **kapcsolat típusa** beállításnál hagyja kiválasztva a **helyek** közötti beállítást.

   * **VPN-átjáró IP-címe**: Ez a helyszíni hálózaton található VPN-eszköz nyilvános IP-címe. Ebben a gyakorlatban egy dummy-címet helyezhet el, mivel még nem rendelkezik a másik helyhez tartozó VPN-átjáró IP-címével. Például: 5.4.3.2. Később, miután konfigurálta az átjárót a másik VNet, módosíthatja ezt az értéket.

   * **Ügyfél címterület:** Az átjárón keresztül a másik VNet irányítani kívánt IP-címtartományok listázása. Több címtartományt is felvehet. Ügyeljen arra, hogy az itt megadott tartományok ne legyenek átfedésben olyan egyéb hálózatok tartományaival, amelyekhez a virtuális hálózat csatlakozik, illetve magának a virtuális hálózatnak a címtartományaival.
1. A lap alján ne válassza a felülvizsgálat + létrehozás lehetőséget. Ehelyett válassza a **Tovább: átjáró>** elemet.

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>Virtuális hálózati átjáró konfigurálása

1. Az **átjáró** lapon válassza ki a következő értékeket:

   * **Méret:** Ez a virtuális hálózati átjáró létrehozásához használt átjáró SKU. A klasszikus VPN-átjárók a régi (örökölt) átjáró-termékváltozatokat használják. Az átjárók örökölt termékváltozatairól [a virtuális hálózati átjárók termékváltozatainak (régi termékváltozatok) használatát bemutató](vpn-gateway-about-skus-legacy.md) cikkben talál további információt. Ehhez a gyakorlathoz válassza a **standard** lehetőséget.

   * **Útválasztási típus:** Válassza ki az átjáró útválasztási típusát. Ez VPN-típus néven is ismert. Fontos, hogy a megfelelő típust válassza, mert az átjárót nem lehet egyik típusról a másikra konvertálni. A VPN-eszköznek kompatibilisnek kell lennie a kiválasztott útválasztási típussal. További információ az útválasztási típusról: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#vpntype). A „RouteBased” és „PolicyBased” VPN-típusokkal további cikkek is foglalkozhatnak. A „Dynamic” (Dinamikus) a „RouteBased”, a „Static” (Statikus) a „PolicyBased” típusra utal. Ehhez a konfigurációhoz válassza a **dinamikus** lehetőséget.

   * **Átjáró-alhálózat:** A megadott átjáró-alhálózat mérete a létrehozni kívánt VPN-átjáró konfigurációjától függ. Bár akár /29-es átjáróalhálózatot is létrehozhat, javasolt /27-eset vagy /28-asat használni. Ez nagyobb, több címet tartalmazó alhálózatot hoz létre. Nagyobb átjáróalhálózat használatával elegendő IP-cím áll rendelkezésre az esetleges jövőbeni konfigurációk megvalósításához.

1. A beállítások ellenőrzéséhez kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre. Válassza a **Létrehozás** elemet a telepítéshez. Akár 45 percet is igénybe vehet, hogy virtuális hálózati átjárót hozzon létre a kiválasztott átjáró-SKU-tól függően.
1. Az átjáró létrehozása után folytassa a következő lépéssel.

### <a name="configure-testvnet4-settings"></a>TestVNet4-beállítások konfigurálása

Ismételje meg a [hely és az átjáró létrehozásához](#localsite) szükséges lépéseket a TestVNet4 konfigurálásához, és szükség esetén helyettesítse be az értékeket. Ha gyakorlatként végzi ezt, használja a [példában szereplő értékeket](#planning).

## <a name="update-local-sites"></a><a name="updatelocal"></a>Helyi helyek frissítése

Miután mindkét virtuális hálózatok létrehozta a virtuális hálózati átjárókat, módosítania kell a helyi hely tulajdonságait a VPN- **átjáró IP-címéhez**.

|VNet neve|Csatlakoztatott hely|Átjáró IP-címe|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|VPN Gateway IP-címe TestVNet4|
|TestVNet4|VNet1Local|VPN Gateway IP-címe TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>1. rész – a virtuális hálózati átjáró nyilvános IP-címének beolvasása

1. A VNet navigáljon az **erőforráscsoporthoz** , és válassza ki a virtuális hálózatot.
1. A virtuális hálózat lapján, a jobb oldali **Essentials** ablaktáblán keresse meg az **átjáró IP-címét** , és másolja a vágólapra.

### <a name="part-2---modify-the-local-site-properties"></a>2. rész – a helyi hely tulajdonságainak módosítása

1. A helyek közötti kapcsolatok területen válassza ki a kapcsolatot. Például: SiteVNet4.
1. A helyek közötti kapcsolat **Tulajdonságok** lapján válassza a **helyi hely szerkesztése** lehetőséget.
1. A **VPN-átjáró IP-címe** mezőben illessze be az előző szakaszban MÁSOLt VPN-átjáró IP-címét.
1. Válassza az **OK** lehetőséget.
1. A mező frissült a rendszeren. Ezzel a módszerrel további IP-címet is hozzáadhat a helyhez.

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>3. rész – a többi VNet lépéseinek megismétlése

Ismételje meg a TestVNet4 lépéseit.

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>Konfigurációs értékek beolvasása

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>Kapcsolatok létrehozása

Ha az összes korábbi lépés befejeződött, beállíthatja az IPsec/IKE előmegosztott kulcsokat, és létrehozhatja a kapcsolódást. Ez a lépés a PowerShellt használja. A klasszikus üzemi modellhez tartozó VNet-VNet kapcsolatok nem konfigurálhatók a Azure Portalban, mert a megosztott kulcs nem adható meg a portálon.

A példákban figyelje meg, hogy a megosztott kulcs pontosan ugyanaz. A megosztott kulcsnak mindig egyeznie kell. Ügyeljen rá, hogy a példákban szereplő értékeket a virtuális hálózatok és a helyi hálózati helyek pontos neveire cserélje le.

1. Hozza létre a TestVNet1–TestVNet4 kapcsolatot. Ügyeljen rá, hogy módosítsa az értékeket.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. Hozza létre a TestVNet4–TestVNet1 kapcsolatot.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
   ```
3. Várjon, amíg a kapcsolatok inicializálása megtörtént. Az átjáró inicializálása után az állapot "sikeres".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq-and-considerations"></a><a name="faq"></a>Gyakori kérdések és megfontolások

Ezek a szempontok a klasszikus virtuális hálózatokra és a klasszikus virtuális hálózati átjáróra vonatkoznak.

* A virtuális hálózatok lehetnek azonos vagy eltérő előfizetésekben.
* A virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban (helyeken).
* A felhőalapú szolgáltatás vagy egy terheléselosztási végpont nem terjedhet ki a virtuális hálózatok között még akkor sem, ha azok össze vannak kapcsolva.
* Több virtuális hálózat összekapcsolása nem igényel VPN-eszközöket.
* A VNet – VNet támogatja az Azure-beli virtuális hálózatok csatlakoztatását. Nem támogatja a virtuális hálózatokra telepített virtuális gépek és felhőalapú szolgáltatások összekapcsolását.
* A VNet – VNet dinamikus útválasztási átjárók szükségesek. Az Azure statikus útválasztási átjárók nem támogatottak.
* A virtuális hálózati kapcsolat használható többhelyes virtuális VPN-ekkel együtt. A virtuális hálózati VPN-átjárók számára legfeljebb 10 VPN-alagút lehet, amely más virtuális hálózatokhoz vagy helyszíni helyekhez csatlakozik.
* A virtuális hálózatok címterei és a helyszíni helyi hálózati helyek nem lehetnek egymással átfedésben. Az átfedésben lévő címterület a virtuális hálózatok létrehozását vagy a netcfg konfigurációs fájljainak a meghibásodáshoz való feltöltését eredményezi.
* A virtuális hálózatok párjai közötti redundáns alagutak nem támogatottak.
* A VNet összes VPN-alagútja, beleértve a P2S VPN-eket is, megoszthatja a VPN-átjáró számára rendelkezésre álló sávszélességet, valamint az Azure-ban megegyező VPN Gateway-üzemidőt.
* A VNet-VNet forgalom az Azure-gerincen halad át.

## <a name="next-steps"></a>Következő lépések

Ellenőrizze a kapcsolatokat. Lásd: [VPN Gateway-kapcsolatok ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).
