---
title: 'Architektúra: globális átviteli hálózati architektúra'
titleSuffix: Azure Virtual WAN
description: Ismerje meg, hogy az Azure Virtual WAN hogyan teszi lehetővé a globális átviteli hálózatok architektúráját a felhőalapú munkaterhelések közötti teljes körű és bármilyen kapcsolódási kapcsolat engedélyezésével.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: cherylmc
ms.openlocfilehash: cfb75b6383d8ca449b4bc54b9d21cb16b3a4ad40
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428183"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Globális átviteli hálózati architektúra és virtuális WAN

A modern vállalatok a felhőben és a helyszíni környezetekben a Hyper-elosztott alkalmazások, az adatközpontok és a felhasználók közötti mindennapos kapcsolatokat igénylik. A vállalatok a globális átviteli hálózati architektúrát a felhőalapú modern, globális vállalati IT-lábnyom megszilárdítására, összekapcsolására és szabályozására használják.

A globális átviteli hálózat architektúrája egy olyan klasszikus sugaras kapcsolati modellen alapul, ahol a felhőben üzemeltetett hálózat "hub" lehetővé teszi az olyan végpontok közötti tranzitív kapcsolódást, amelyek különböző típusú "küllők" között terjeszthetők.

Ebben a modellben a küllő a következő lehet:
* Virtuális hálózat (virtuális hálózatok)
* Fizikai ág helye
* Távoli felhasználó
* Internet

![hub és küllő](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**1. ábra: globális árutovábbítási hub és küllős hálózat**

Az 1. ábrán a globális átviteli hálózat logikai nézete látható, ahol a földrajzilag elosztott felhasználók, fizikai helyek és virtuális hálózatok a felhőben üzemeltetett hálózati elosztón keresztül kapcsolódnak egymáshoz. Ez az architektúra lehetővé teszi a logikai egyugrásos átviteli kapcsolatok használatát a hálózati végpontok között.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Globális átviteli hálózat virtuális WAN-kapcsolattal

Az Azure Virtual WAN egy Microsoft által felügyelt felhőalapú hálózati szolgáltatás. A szolgáltatás által alkotott összes hálózati összetevőt a Microsoft üzemelteti és kezeli. A virtuális WAN-ról további információt a [virtuális WAN áttekintése](virtual-wan-about.md) című cikkben talál.

Az Azure Virtual WAN lehetővé teszi a globális átviteli hálózatok architektúráját azáltal, hogy a virtuális hálózatok, a fiókirodákban, az SaaS-és a Pásti-alkalmazásokban, valamint a felhasználókon elérhető, globálisan elosztott Felhőbeli számítási feladatok teljes körű és bármilyen módon elérhető kapcsolatát engedélyezi.

![Azure Virtual WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**2. ábra: globális tranzit hálózat és virtuális WAN**

Az Azure Virtual WAN architektúrában a virtuális WAN-hubok az Azure-régiókban vannak kiépítve, amelyekhez az ágakat, a virtuális hálózatok és a távoli felhasználókat is összekapcsolhatjuk. A fizikai fiókirodák prémium vagy standard szintű ExpressRoute vagy helyek közötti VPN-en keresztül csatlakoznak a központhoz, a virtuális hálózatok pedig VNet-kapcsolatokkal csatlakoznak a központhoz, a távoli felhasználók pedig közvetlenül kapcsolódhatnak a hubhoz a felhasználói VPN (pont – hely VPN) használatával. A Virtual WAN támogatja a régiók közötti VNet kapcsolatot is, ahol az egyik régióban lévő VNet egy másik régióban lévő virtuális WAN-hubhoz is csatlakoztathatók.

Létrehozhat egy virtuális WAN-t úgy, hogy létrehoz egy virtuális WAN-központot a régiójában, amely a legnagyobb számú küllőt (ágakat, virtuális hálózatok, felhasználókat) és a más régiókban lévő küllőket csatlakoztatja a hubhoz. Ez jó megoldás, ha egy nagyvállalati lábnyom főleg egy régióban, néhány távoli küllővel.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>Központ – központ kapcsolat

A vállalati Felhőbeli lábnyom több Felhőbeli régióra is kiterjed, és az optimális (késés-Wise) a felhőnek a fizikai helyükhöz és a felhasználókhoz legközelebb eső régióból való elérésére szolgál. A globális átviteli hálózati architektúra egyik kulcsfontosságú alapelve a régiók közötti kapcsolat engedélyezése a Felhőbeli és a helyszíni hálózati végpontok között. Ez azt jelenti, hogy egy adott régióban a felhőhöz csatlakoztatott ágakból érkező forgalom egy másik régióban is elérheti az [Azure globális hálózata](https://azure.microsoft.com/global-infrastructure/global-network/)által engedélyezett, a csomópontok közötti kapcsolatot használó régiókat vagy VNet.

![régiók közötti](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**3. ábra: virtuális WAN régiók közötti kapcsolat**

Ha egy virtuális WAN-ban több hub is engedélyezve van, a hubok automatikusan csatlakoznak a hub – hub kapcsolatokon keresztül, így lehetővé téve a globális kapcsolódást a több régióban elosztott ágak és virtuális hálózatok között. 

Emellett a virtuális WAN összes részét képező hubok különböző regionális hozzáférési és biztonsági házirendekhez társíthatók. További információ: [biztonsági és házirend-vezérlés](#security) a cikk későbbi részében.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Bármilyen kapcsolat

A globális átviteli hálózati architektúra a virtuális WAN-hubokon keresztül bármilyen kapcsolódást tesz lehetővé. Ez az architektúra kiküszöböli vagy csökkenti a küllők közötti teljes rácsvonal vagy részleges háló kapcsolat szükségességét, amelyek összetettebbek a létrehozáshoz és a karbantartáshoz. Emellett a hub-és küllős és a mesh hálózatok útválasztási vezérlése könnyebben konfigurálható és kezelhető.

Bármely – bármely kapcsolat (globális architektúra kontextusában) lehetővé teszi, hogy a vállalat globálisan elosztott felhasználókkal, ágakkal, adatközpontokkal, virtuális hálózatok és alkalmazásokkal kapcsolódjon egymáshoz az "árutovábbítási" központ (ok) n keresztül. Az Azure Virtual WAN globális árutovábbítási rendszerrel működik.

![bármilyen](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**4. ábra: virtuális WAN-forgalom elérési útjai**

Az Azure Virtual WAN a következő globális átviteli csatlakozási útvonalakat támogatja. A zárójelben lévő betűk a 4. ábrán láthatók.

* Ág – VNet (a)
* Ág – ág (b)
  * ExpressRoute Global Reach és virtuális WAN
* Távoli felhasználó – VNet (c)
* Távoli felhasználó – ág (d)
* VNet – VNet (e)
* Ág – központ – hub – ág (f)
* Ág – központ – hub – VNet (g)
* VNet – hub-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Ág – VNet (a) és ág – VNet közötti régió (g)

A VNet az Azure Virtual WAN által támogatott elsődleges elérési út. Ez az elérési út lehetővé teszi, hogy az Azure virtuális hálózatok üzembe helyezett Azure IAAS Enterprise-munkaterhelésekhez csatlakozhasson ágakat. Az ágak a ExpressRoute vagy a helyek közötti VPN használatával csatlakoztathatók a virtuális WAN-hoz. A VNet-kapcsolatokon keresztül a virtuális WAN-központokhoz csatlakozó virtuális hálózatok áthaladó forgalom. A virtuális WAN esetében nem szükséges explicit [átjáró-átvitel](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) , mert a virtuális WAN automatikusan engedélyezi az átjárónak a fiókirodába való átvitelét. Tekintse meg a [virtuális WAN-partnerek](virtual-wan-configure-automation-providers.md) című cikket, amely bemutatja, hogyan CSATLAKOZTATHATÓ egy SD-WAN CPE a virtuális WAN-hoz.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach és virtuális WAN

A ExpressRoute egy privát és rugalmas módszer a helyi hálózatok Microsoft Cloudhoz való összekapcsolására. A Virtual WAN támogatja az Express Route Circuit-kapcsolatokat. Ha egy fiókirodai helyet a Virtual WAN-hoz csatlakoztat, az Express Route 1) Prémium vagy standard 2. szintű áramkört igényel, Global Reach engedélyezett helyen kell lennie.

A ExpressRoute Global Reach a ExpressRoute kiegészítő szolgáltatása. A Global Reach segítségével összekapcsolhatja a ExpressRoute-áramköröket, hogy magánhálózat legyen a helyszíni hálózatok között. Azok az ágak, amelyek az Azure Virtual WAN-hoz csatlakoznak a ExpressRoute használatával, az ExpressRoute Global Reach kell kommunikálni egymással.

Ebben a modellben minden olyan ág, amely a ExpressRoute használatával csatlakozik a virtuális WAN-hubhoz, a virtuális hálózatok a VNet útvonalon keresztül csatlakozhat. Az elágazási forgalom nem kerül át a központba, mert az ExpressRoute Global Reach az Azure WAN-on keresztüli optimális elérési utat tesz lehetővé.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Ág – ág (b) és ág – ág közötti régió (f)

Az ágak ExpressRoute áramkörök és/vagy helyek közötti VPN-kapcsolatok használatával csatlakoztathatók egy Azure-beli virtuális WAN-hubhoz. Az ágakat csatlakoztathatja a virtuális WAN-hubhoz, amely az ág legközelebb eső régiójában található.

Ez a beállítás lehetővé teszi, hogy a vállalatok az Azure gerincet használják az ágak összekapcsolásához. Bár ez a funkció elérhető, érdemes mérlegelni a fiókirodák Azure-beli virtuális WAN-kapcsolaton keresztül történő csatlakoztatásának előnyeit, valamint a privát WAN használatát.  

> [!NOTE]
> Ág – ág kapcsolat letiltása a virtuális WAN-ban – a virtuális WAN konfigurálható a fiókirodák közötti kapcsolat letiltására. Ez a configuation letiltja a VPN (S2S és P2S) és az Express Route Connected-helyek közötti továbbítást. Ez a konfiguráció nem befolyásolja a vnet és a vnet-vnet útvonalat, valamint a kapcsolódást. A beállítás konfigurálása az Azure Portal használatával: a virtuális WAN konfigurálása menüben válassza a beállítás: ág-ág-Letiltva lehetőséget. 

### <a name="remote-user-to-vnet-c"></a>Távoli felhasználó – VNet (c)

Engedélyezheti a közvetlen, biztonságos távoli hozzáférést az Azure-hoz pont – hely kapcsolat használatával egy távoli felhasználói ügyfélről egy virtuális WAN-ra. A vállalati távoli felhasználóknak már nem kell hajtű a felhőbe a vállalati VPN használatával.

### <a name="remote-user-to-branch-d"></a>Távoli felhasználó – ág (d)

A távoli felhasználó – ág elérési út lehetővé teszi, hogy a távoli felhasználók, akik pont – hely kapcsolattal csatlakoznak az Azure-hoz a helyszíni számítási feladatokhoz és alkalmazásokhoz a felhőn keresztül történő átvitelsel. Ez az elérési út biztosítja a távoli felhasználók számára a rugalmasságot az Azure-ban és a helyszínen üzembe helyezett munkaterhelések eléréséhez. A vállalatok engedélyezhetik a központi felhőalapú biztonságos távelérés szolgáltatást az Azure Virtual WAN-ban.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet – VNet tranzit (e) és VNet – VNet régió (h)

A VNet – VNet tranzit lehetővé teszi, hogy a virtuális hálózatok a több virtuális hálózatok-ben megvalósított többrétegű alkalmazások összekapcsolásához csatlakozhasson egymáshoz. A virtuális hálózatok egymással is összekapcsolhatók a VNet-közvetítéssel, és ez olyan esetekben lehet megfelelő, amikor az VWAN hub-on keresztül történő átvitel nem szükséges.


## <a name="force-tunneling-and-default-route-in-azure-virtual-wan"></a><a name="DefaultRoute"></a>Kényszerített bújtatás és alapértelmezett útvonal az Azure Virtual WAN-ban

A kényszerített bújtatás engedélyezéséhez konfigurálja az alapértelmezett útvonal engedélyezése VPN-, ExpressRoute-vagy Virtual Network-kapcsolaton a virtuális WAN-ban beállítást.

Egy virtuális központ propagál egy megtanult alapértelmezett útvonalat egy virtuális hálózat/helyek közötti VPN/ExpressRoute kapcsolathoz, ha az alapértelmezett jelző engedélyezése beállítás engedélyezve van a kapcsolaton. 

Ez a jelző akkor látható, ha a felhasználó szerkeszt egy virtuális hálózati kapcsolat, egy VPN-kapcsolat vagy egy ExpressRoute-kapcsolat. Alapértelmezés szerint ez a jelző le van tiltva, ha egy hely vagy egy ExpressRoute áramkör egy hubhoz van csatlakoztatva. Alapértelmezés szerint engedélyezve van, ha egy virtuális hálózati kapcsolat hozzáadásával csatlakozik egy VNet egy virtuális hubhoz. Az alapértelmezett útvonal nem a virtuális WAN-hubhoz származik; a rendszer az alapértelmezett útvonalat propagálja, ha a virtuális WAN-központ már megtanulta a tűzfal központi telepítésének eredményeképpen, vagy ha egy másik csatlakoztatott hely kényszerített bújtatást engedélyez.

## <a name="security-and-policy-control"></a><a name="security"></a>Biztonság és házirend-vezérlés

Az Azure Virtual WAN-hubok összekötik az összes hálózati végpontot a hibrid hálózaton, és lehetséges, hogy az összes átviteli hálózati forgalom megjelenik. A virtuális WAN-hubok biztonságos virtuális hubokba alakíthatók, ha a Azure Firewallt a VWAN-hubokon belül telepíti, hogy engedélyezze a felhőalapú biztonságot, a hozzáférést és a házirend-vezérlést. A virtuális WAN-hubokban található Azure-tűzfalak összehangolása a Azure Firewall Manager által végezhető el.

A [Azure Firewall Manager](../firewall-manager/index.yml) biztosítja a globális átviteli hálózatok biztonságának kezeléséhez és méretezéséhez szükséges képességeket. A Azure Firewall Manager lehetővé teszi, hogy központilag kezelhesse az útválasztást, a globális házirendek felügyeletét, a speciális internetes biztonsági szolgáltatásokat harmadik felekkel, valamint a Azure Firewall.

![biztonságos virtuális központ Azure Firewall](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**5. ábra: biztonságos virtuális központ Azure Firewall**

A virtuális WAN Azure Firewall a következő globálisan biztonságos tranzit-csatlakozási útvonalakat támogatja. A zárójelben lévő betűk az 5. ábrán láthatók.

* VNet – VNet biztonságos átvitel (e)
* VNet vagy harmadik féltől származó biztonsági szolgáltatás (i)
* Ág – Internet vagy harmadik féltől származó biztonsági szolgáltatás (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet – VNet biztonságos átvitel (e)

A VNet – VNet biztonságos továbbítás lehetővé teszi, hogy a virtuális hálózatok a virtuális WAN-központban lévő Azure Firewall keresztül kapcsolódjon egymáshoz.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet vagy harmadik féltől származó biztonsági szolgáltatás (i)

A VNet lehetővé teszi a virtuális hálózatok számára, hogy a virtuális WAN-központban lévő Azure Firewall keresztül kapcsolódjon az internethez. Az internetre irányuló, harmadik féltől származó biztonsági szolgáltatásokon keresztüli adatforgalom nem a Azure Firewallon keresztül áramlik. A vnet elérési útját a támogatott külső biztonsági szolgáltatással is konfigurálhatja a Azure Firewall Manager használatával.  

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Ág – Internet vagy harmadik féltől származó biztonsági szolgáltatás (j)
Az ág – Internet lehetővé teszi, hogy az ágak az internethez kapcsolódjanak a virtuális WAN-központ Azure Firewallján keresztül. Az internetre irányuló, harmadik féltől származó biztonsági szolgáltatásokon keresztüli adatforgalom nem a Azure Firewallon keresztül áramlik. A Azure Firewall Manager használatával az ág – Internet elérési utat a támogatott külső biztonsági szolgáltatással is konfigurálhatja. 

### <a name="branch-to-branch-secured-transit-cross-region-f"></a>Ág és ág közötti biztonságos tranzit régió (f)

Az ágak ExpressRoute-áramkörök és/vagy helyek közötti VPN-kapcsolatok használatával csatlakoztathatók egy biztonságos virtuális hubhoz Azure Firewallhoz. Az ágakat csatlakoztathatja a virtuális WAN-hubhoz, amely az ág legközelebb eső régiójában található.

Ez a beállítás lehetővé teszi, hogy a vállalatok az Azure gerincet használják az ágak összekapcsolásához. Bár ez a funkció elérhető, érdemes mérlegelni a fiókirodák Azure-beli virtuális WAN-kapcsolaton keresztül történő csatlakoztatásának előnyeit, valamint a privát WAN használatát.  

> [!NOTE]
> A tűzfalon keresztüli forgalom helyközi feldolgozása jelenleg nem támogatott. A hubok közötti forgalom a biztonságos virtuális központban található megfelelő ágra lesz irányítva, azonban a forgalom megkerüli a Azure Firewall az egyes csomópontokon.

### <a name="branch-to-vnet-secured-transit-g"></a>Ág – VNet biztonságos átvitel (g)

A VNet biztonságos átvitel lehetővé teszi, hogy az ágak a virtuális WAN-központtal azonos régióban lévő virtuális hálózatokkal, valamint egy másik régióban lévő virtuális WAN-hubhoz csatlakoztatott másik virtuális hálózattal kommunikáljanak egymással.

> [!NOTE]
> Az Inter-hub tűzfallal való használata jelenleg nem támogatott. A hubok közötti forgalom közvetlenül megkerüli a Azure Firewall az egyes csomópontokon.  Az azonos régióban található virtuális hálózatra irányuló kapcsolódáson keresztüli adatforgalmat a biztonságos központ Azure Firewall fogja feldolgozni.


### <a name="how-do-i-enable-default-route-00000-in-a-secured-virtual-hub"></a>Alapértelmezett útvonal (0.0.0.0/0) Hogyan engedélyezése biztonságos virtuális központban

A virtuális WAN-központban üzembe helyezett Azure Firewall (biztonságos virtuális központ) alapértelmezett útválasztóként konfigurálható az internethez vagy a megbízható biztonsági szolgáltatóhoz minden olyan ág számára, amely (VPN vagy Express Route használatával csatlakozik), küllős virtuális hálózatok és felhasználók (P2S VPN-en keresztül). Ezt a konfigurációt a Azure Firewall Manager használatával kell elvégezni.  Lásd: forgalom irányítása a központba az ágak (beleértve a felhasználók) és az virtuális hálózatok közötti összes forgalom konfigurálásához a Azure Firewall használatával. 

Ez egy két lépésből álló konfiguráció:

1. Konfigurálja az internetes forgalom útválasztását a biztonságos virtuális hub Route Setting menü használatával. Olyan virtuális hálózatok és ágakat konfigurálhat, amelyek a tűzfalon keresztül küldhetnek forgalmat az internetre.

2. Konfigurálhatja, hogy mely kapcsolatok (vnet és ág) irányítsák át a forgalmat az internetre (0.0.0.0/0) az Azure FW-ben a hub vagy a megbízható biztonsági szolgáltató használatával. Ez a lépés biztosítja, hogy az alapértelmezett útvonal propagálva legyen a kiválasztott ágakra és virtuális hálózatok, amelyek a kapcsolatokon keresztül csatlakoznak a virtuális WAN-hubhoz. 

### <a name="force-tunneling-traffic-to-on-premises-firewall-in-a-secured-virtual-hub"></a>A helyszíni tűzfal felé irányuló adatforgalom kényszerítése biztonságos virtuális központban

Ha a virtuális központ már megtanulta egy alapértelmezett útvonalat (a BGP-n keresztül), akkor ez az alapértelmezett útvonal felülbírálható az Azure Firewall Manager-beállítás által megismert alapértelmezett útvonalon. Ebben az esetben az virtuális hálózatok-ből és az internetre irányuló ágakból beérkező összes forgalom a Azure Firewall vagy a megbízható biztonsági szolgáltatóhoz lesz irányítva.

> [!NOTE]
> Jelenleg nincs lehetőség a helyszíni tűzfal vagy a Azure Firewall (és a megbízható biztonsági szolgáltató) kiválasztására a virtuális hálózatok, ágakból vagy felhasználókból származó, interneten keresztül kötött forgalomhoz. Az Azure Firewall Manager beállításban megismert alapértelmezett útvonal mindig előnyben részesített az egyik ág által megismert alapértelmezett útvonalon.


## <a name="next-steps"></a>Következő lépések

Hozzon létre egy virtuális WAN-kapcsolaton keresztüli kapcsolatokat, és telepítse a Azure Firewallt az VWAN hub (ok) ban.

* [Helyek közötti kapcsolatok virtuális WAN használatával](virtual-wan-site-to-site-portal.md)
* [ExpressRoute-kapcsolatok virtuális WAN használatával](virtual-wan-expressroute-portal.md)
* [Azure Firewall Manager az Azure FW üzembe helyezéséhez a VWAN-ben](../firewall-manager/index.yml)
