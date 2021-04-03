---
title: 'Architektúra: áttelepítés az Azure Virtual WAN-ba'
description: Ismerje meg, hogyan migrálhat az Azure Virtual WAN-ba.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: cherylmc
ms.openlocfilehash: e602905b461e370189cefed706ddc3a47e0199fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91839639"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrálás az Azure Virtual WAN-ba

Az Azure Virtual WAN lehetővé teszi, hogy a vállalatok leegyszerűsítsék a globális kapcsolataikat a Microsoft globális hálózatának kihasználása érdekében. Ez a cikk technikai részleteket tartalmaz azoknak a vállalatoknak, akik egy meglévő ügyfél által felügyelt sugaras topológiából kívánnak áttérni egy olyan kialakításra, amely a Microsoft által felügyelt virtuális WAN-hubokat használja.

További információ arról, hogy az Azure Virtual WAN milyen előnyökkel jár a felhő-központú modern vállalati globális hálózatokat befogadó vállalatok számára: [globális átviteli hálózati architektúra és virtuális WAN](virtual-wan-global-transit-network-architecture.md).

:::image type="content" source="./media/migrate-from-hub-spoke-topology/hub-spoke.png" alt-text="hub és küllő":::
**Ábra: Azure virtuális WAN**

Az Azure hub és a küllős kapcsolat modelljét több ezer ügyfelünk fogadta el az Azure-hálózatkezelés alapértelmezett, tranzitív útválasztási viselkedésének kihasználása érdekében egyszerű és méretezhető felhőalapú hálózatok létrehozásához. Az Azure Virtual WAN ezen fogalmakon alapul, és olyan új képességeket vezet be, amelyek lehetővé teszik a globális kapcsolódási topológiák használatát, nem csupán a helyszíni és az Azure-t, hanem lehetővé teszi, hogy az ügyfelek kihasználják a Microsoft-hálózat méretezését a meglévő globális hálózatok kibővítéséhez.

Ez a cikk bemutatja, hogyan telepíthet át egy meglévő ügyfél által felügyelt sugaras környezetet egy Azure-beli virtuális WAN-alapú topológiára.

## <a name="scenario"></a>Eset

A contoso egy globális pénzügyi szervezet, amely az európai és az ázsiai irodákkal is rendelkezik. Azt tervezzük, hogy meglévő alkalmazásaikat a helyszíni adatközpontból az Azure-ba helyezik át, és az ügyfél által felügyelt sugaras architektúrán alapuló alapszintű kialakítást alakítottunk ki, beleértve a hibrid kapcsolatokhoz tartozó regionális központ virtuális hálózatokat is. A felhőalapú technológiákba való áttérés részeként a hálózati csapat feladata annak biztosítása, hogy a kapcsolat a vállalat felé irányuló továbbításra legyen optimalizálva.

Az alábbi ábra a meglévő globális hálózat magas szintű nézetét mutatja be, beleértve a több Azure-régióhoz való kapcsolódást.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png" alt-text="Contoso meglévő hálózati topológia":::
**Ábra: contoso meglévő hálózati topológia**

A meglévő hálózati topológia a következő pontokat tudja értelmezni:

* A sugaras topológia több régióban is használatos, beleértve a ExpressRoute-áramköröket, amelyekkel a közös magánhálózat (WAN) kapcsolata is elérhető.

* Ezen helyek némelyike közvetlenül az Azure-ba irányuló VPN-alagutakkal is rendelkezik a felhőben üzemeltetett alkalmazások eléréséhez.

## <a name="requirements"></a>Követelmények

A hálózati csapat feladata, hogy olyan globális hálózati modellt nyújtson, amely támogatja a contoso felhőbe való áttelepítését, és optimalizálni kell a költségeket, a méretezést és a teljesítményt. Az összefoglalás területen az alábbi követelmények teljesülnek:

* Az üzemeltetett Felhőbeli alkalmazásokhoz optimalizált elérési úttal rendelkező Head Quarter (HQ) és fiókirodák is elérhetők.
* Szüntesse meg a VPN-leállításhoz szükséges meglévő helyszíni adatközpontok (DC) függőségét, miközben megtartja a következő csatlakozási útvonalakat:
  * **Ág – VNet**: a VPN-kapcsolattal rendelkező irodáknak képesnek kell lenniük a felhőbe áttelepített alkalmazások elérésére a helyi Azure-régióban.
  * **Ág – központ – VNet**: a VPN-kapcsolattal rendelkező irodáknak képesnek kell lenniük a felhőbe áttelepített alkalmazások elérésére a távoli Azure-régióban.
  * **Ág – ág**: a regionális VPN-kapcsolattal rendelkező irodáknak képesnek kell lenniük az egymással való kommunikációra és a EXPRESSROUTE csatlakoztatott HQ/DC-helyekre.
  * **Elágazás** és hub közötti kapcsolat: a globálisan elkülönített VPN-irodáknak képesnek kell lenniük kommunikálni egymással és a EXPRESSROUTE csatlakoztatott HQ-vagy tartományvezérlő-webhelyekkel.
  * **Ág – Internet**: a csatlakoztatott helyeknek képesnek kell lenniük az internettel való kommunikációra. Ezt a forgalmat szűrni és naplózni kell.
  * **VNet – VNet**: az azonos régióban található küllős virtuális hálózatoknak képesnek kell lenniük egymással való kommunikációra.
  * **VNet – hub – VNet**: a különböző régiókban található küllős virtuális hálózatoknak képesnek kell lenniük egymással való kommunikációra.
* Lehetővé teszi a contoso barangoló felhasználói (laptop és telefon) számára a vállalati erőforrások elérését, miközben a vállalati hálózaton nem.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Azure-beli virtuális WAN-architektúra

Az alábbi ábra az Azure Virtual WAN használatával az előző szakaszban részletezett követelmények teljesítése érdekében a frissített célként megadott topológia magas szintű nézetét jeleníti meg.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/vwan-architecture.png" alt-text="Contoso virtuális WAN-architektúra":::
**Ábra: Azure virtuális WAN-architektúra**

Összegzés:

* Az Európai HQ ExpressRoute továbbra is csatlakoztatva van, a helyszíni TARTOMÁNYVEZÉRLŐk teljes mértékben áttelepültek az Azure-ba, és most már leszerelték.
* Az Asia DC és a HQ továbbra is csatlakoztatva van a privát WAN-hoz. Az Azure Virtual WAN mostantól a helyi Carrier hálózat bővítésére és a globális kapcsolatok biztosítására szolgál.
* A Nyugat-Európában és Dél-Kelet-Ázsia Azure-régiókban üzembe helyezett Azure-beli virtuális WAN-hubok a ExpressRoute és a VPN-hez csatlakoztatott eszközökhöz biztosítanak csatlakozási központot.
* A hubok VPN-lezárást is biztosítanak a barangoló felhasználók számára több ügyfélen keresztül, a globális Mesh hálózattal való OpenVPN-kapcsolat használatával, amely lehetővé teszi, hogy ne csak az Azure-ba migrált alkalmazások, hanem a helyszínen maradó erőforrások is elérhetők legyenek.
* Internetkapcsolat az Azure Virtual WAN által biztosított virtuális hálózaton belüli erőforrásokhoz.

Az Azure Virtual WAN által is biztosított távoli helyek internetkapcsolata. A helyi Internet breakout a partner-integráción keresztül támogatott a SaaS-szolgáltatásokhoz, például a Microsoft 365hoz való optimalizált hozzáféréshez.

## <a name="migrate-to-virtual-wan"></a>Migrálás virtuális WAN-ba

Ez a szakasz az Azure Virtual WAN-ba való Migrálás különböző lépéseit mutatja be.

### <a name="step-1-single-region-customer-managed-hub-and-spoke"></a>1. lépés: az ügyfél által felügyelt egyrégiós központ és küllő

Az alábbi ábra a contoso egyetlen régiós topológiáját mutatja be az Azure Virtual WAN bevezetése előtt:

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure1.png" alt-text="Önálló régió topológiája":::
**1. ábra: egyrégiós manuális hub – küllő**

A sugaras megközelítésnek megfelelően az ügyfél által felügyelt hub virtuális hálózat több függvény blokkot tartalmaz:

* Megosztott szolgáltatások (a több küllő által igényelt általános függvények). Példa: a contoso a Windows Server tartományvezérlőket használja az infrastruktúra-szolgáltatás (IaaS) virtuális gépeken.
* Az IP-/útválasztási tűzfalakat egy külső gyártótól származó hálózati virtuális készülék biztosítja, amely küllős, 3. rétegbeli IP-útválasztást tesz lehetővé.
* A bejövő HTTPS-kérelmekre és a virtuális gépeken futó külső proxy szolgáltatásokra, például az internetes erőforrásokhoz való szűrt kimenő hozzáféréshez használt Azure Application Gateway.
* ExpressRoute és VPN virtuális hálózati átjáró a helyszíni hálózatokhoz való kapcsolódáshoz.

### <a name="step-2-deploy-virtual-wan-hubs"></a>2. lépés: virtuális WAN-hubok üzembe helyezése

Helyezzen üzembe egy virtuális WAN-hubot minden régióban. Állítsa be a virtuális WAN-hubot VPN-és ExpressRoute-funkcióval a következő cikkekben leírtak szerint:

* [Oktatóanyag: Helyek közötti kapcsolat létrehozása az Azure Virtual WAN használatával](virtual-wan-site-to-site-portal.md)
* [Oktatóanyag: ExpressRoute-társítás létrehozása az Azure Virtual WAN használatával](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Az Azure Virtual WAN-nak a standard SKU-t kell használnia ahhoz, hogy a jelen cikkben szereplő forgalmi útvonalak némelyikét engedélyezzék.
>

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure2.png" alt-text="Virtuális WAN-hubok üzembe helyezése":::
**2. ábra: az ügyfél által felügyelt központ és küllő a virtuális WAN-Migrálás**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>3. lépés: távoli helyek (ExpressRoute és VPN) összekötése virtuális WAN-kapcsolattal

Kapcsolja össze a virtuális WAN-hubot a meglévő ExpressRoute-áramkörökkel, és állítsa be a helyek közötti VPN-eket az interneten keresztül bármely távoli ág számára.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure3.png" alt-text="Távoli helyek összekötése virtuális WAN-kapcsolattal":::
**3. ábra: az ügyfél által felügyelt központ és küllő a virtuális WAN-Migrálás**

Ezen a ponton a helyszíni hálózati berendezések elkezdik fogadni a virtuális WAN által felügyelt hub-VNet hozzárendelt IP-címtartomány alapján érkező útvonalakat. A távoli VPN-kapcsolattal rendelkező ágak ebben a fázisban két elérési utat látnak a küllős virtuális hálózatok meglévő alkalmazásaihoz. Ezeket az eszközöket úgy kell konfigurálni, hogy továbbra is a bújtatást használják az ügyfél által felügyelt hubhoz, így biztosítva a szimmetrikus útválasztást az átmeneti fázisban.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>4. lépés: a hibrid kapcsolatok tesztelése virtuális WAN-kapcsolaton keresztül

A felügyelt virtuális WAN hub éles kapcsolathoz való használata előtt javasoljuk, hogy állítson be egy teszt küllős virtuális hálózatot és egy virtuális WAN VNet-kapcsolatot. Ellenőrizze, hogy a tesztkörnyezet kapcsolatai a ExpressRoute és a helyek közötti VPN-kapcsolaton keresztül működnek-e, mielőtt folytatná a következő lépéseket.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure4.png" alt-text="Hibrid kapcsolatok tesztelése virtuális WAN-kapcsolaton keresztül":::
**4. ábra: az ügyfél által felügyelt központ és küllő a virtuális WAN-Migrálás**

Ebben a szakaszban fontos tisztában lennie azzal, hogy az eredeti ügyfél által felügyelt hub virtuális hálózat és az új virtuális WAN hub egyaránt ugyanahhoz a ExpressRoute-áramkörhöz csatlakozik. Ennek köszönhetően a forgalom elérési útja lehetővé teszi, hogy mindkét környezetben kommunikálni lehessen. Például az ügyfél által felügyelt virtuális hálózathoz csatolt küllőről érkező forgalom áthalad a ExpressRoute-áramkörhöz használt MSEE-eszközökön az új virtuális WAN-hubhoz való VNet-kapcsolaton keresztül. Ez lehetővé teszi a küllők szakaszos áttelepítését az 5. lépésben.

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>5. lépés: a virtuális WAN-hubhoz való áttérési kapcsolat

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure5.png" alt-text="Áttérési kapcsolat a virtuális WAN-hubhoz":::
**5. ábra: az ügyfél által felügyelt központ és küllő a virtuális WAN-Migrálás**

**a**. A küllős virtuális hálózatokról a régi ügyfél által felügyelt központba való meglévő kapcsolati kapcsolatok törlése. A küllős virtuális hálózatokban lévő alkalmazásokhoz való hozzáférés addig nem érhető el, amíg a-c lépések be nem fejeződik.

**b**. Csatlakoztassa a küllős virtuális hálózatokat a virtuális WAN-hubhoz VNet-kapcsolatokon keresztül.

**c**. Távolítson el minden korábban használt felhasználó által megadott útvonalat (UDR) küllős kommunikációhoz a küllős virtuális hálózatokon belül. Ez az elérési út már engedélyezve van a virtuális WAN hub-ban elérhető dinamikus útválasztással.

**d**. Az ügyfél által felügyelt hubhoz meglévő ExpressRoute-és VPN-átjárók már le vannak szerelve, hogy engedélyezzék a következő lépést (e).

**e**. A régi ügyfél által felügyelt hub (hub Virtual Network) csatlakoztatása a virtuális WAN-hubhoz egy új VNet-kapcsolaton keresztül.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>6. lépés: a régi hub lesz a megosztott szolgáltatások küllő

Most átalakítottuk az Azure-hálózatot, hogy a virtuális WAN hub a központi pont legyen az új topológiában.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure6.png" alt-text="A régi hub a megosztott szolgáltatások részévé válik":::
**6. ábra: az ügyfél által felügyelt hub és a-küllő virtuális WAN-Migrálás**

Mivel a virtuális WAN-központ felügyelt entitás, és nem teszi lehetővé az egyéni erőforrások, például a virtuális gépek üzembe helyezését, a megosztott szolgáltatások blokk már létezik küllős virtuális hálózatként, és a gazdagépek olyan funkciói, mint például az Azure Application Gateway vagy a hálózati virtualizált készülék. A megosztott szolgáltatások környezete és a háttérbe tartozó virtuális gépek közötti forgalom mostantól átirányítja a virtuális WAN által felügyelt hubot.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>7. lépés: a helyszíni kapcsolat optimalizálása a virtuális WAN teljes körű kihasználásához

Ebben a szakaszban a contoso többnyire befejezte az üzleti alkalmazások áttelepítését a Microsoft Cloudba, és csak néhány örökölt alkalmazás van hátra a helyszíni TARTOMÁNYVEZÉRLŐn belül.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure7.png" alt-text="A helyszíni kapcsolat optimalizálása a virtuális WAN teljes körű kihasználásához":::
**7. ábra: az ügyfél által felügyelt hub és a-küllő virtuális WAN-Migrálás**

Az Azure Virtual WAN teljes funkcionalitásának kihasználásához a contoso úgy dönt, hogy leszereli a régi helyszíni VPN-kapcsolatait. A HQ vagy a DC hálózatok elérését továbbra is elérő ágak a Microsoft globális hálózatát az Azure Virtual WAN beépített átviteli útvonalán keresztül tudják továbbítani.

> [!NOTE]
> A ExpressRoute Global Reach szükséges azon ügyfelek számára, akik a Microsoft-gerincet szeretnék használni a ExpressRoute-tranzit ExpressRoute biztosításához (nem látható 7. ábra).
>

## <a name="end-state-architecture-and-traffic-paths"></a>Állapot-architektúra és forgalmi útvonalak

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure8.png" alt-text="Állapot-architektúra és forgalmi útvonalak":::
**Ábra: kettős régió – virtuális WAN**

Ez a szakasz összefoglalja, hogy a topológia hogyan teljesíti az eredeti követelményeket úgy, hogy megkeres néhány példát a forgalmi folyamatokra.

### <a name="path-1"></a>1. elérési út

Az 1. elérési út azt mutatja be, hogy egy S2S VPN-kapcsolattal rendelkező ág forgalma egy Dél-Kelet-Ázsia régióban található Azure-VNet.

A forgalom a következőképpen lesz átirányítva:

* Az ázsiai ág rugalmas S2S BGP-t használó alagutakon keresztül csatlakozik a Dél-Kelet-Ázsia virtuális WAN-hubhoz.

* Az Asia Virtual WAN hub a forgalmat helyileg irányítja a csatlakoztatott VNet.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow1.png" alt-text="1. folyamat":::

### <a name="path-2"></a>2. elérési út

A 2. elérési út a ExpressRoute csatlakoztatott európai HQ és a Dél-Kelet-Ázsia régióban található Azure-VNet közötti forgalmat mutatja.

A forgalom a következőképpen lesz átirányítva:

* Az Európai HQ a ExpressRoute áramkörön keresztül csatlakozik a Nyugat-európai Virtual WAN hub-hoz.

* A virtuális WAN hub – hub globális kapcsolat lehetővé teszi a forgalom átvitelét a távoli régióba csatlakoztatott VNet.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow2.png" alt-text="2. folyamat":::

### <a name="path-3"></a>3. elérési út

A 3. elérési út azt mutatja, hogy az ázsiai helyszíni TARTOMÁNYVEZÉRLŐről érkező forgalom a belső WAN-hoz csatlakozik egy európai S2S csatlakoztatott ágra.

A forgalom a következőképpen lesz átirányítva:

* Az Asia DC a helyi privát WAN-szolgáltatóhoz csatlakozik.

* A ExpressRoute áramkör helyi leállítása a magánhálózati WAN-kapcsolattal a Dél-Kelet-Ázsia virtuális WAN-hubhoz csatlakozik.

* A virtuális WAN hub – hub globális kapcsolat lehetővé teszi a forgalom átvitelét.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow3.png" alt-text="3. folyamat":::

### <a name="path-4"></a>4. elérési út

A 4. elérési út a Dél-Kelet-Ázsia régióban található Azure-VNet származó forgalmat mutatja be a Nyugat-európai régióban lévő Azure-VNet.

A forgalom a következőképpen lesz átirányítva:

* A virtuális WAN hub – hub globális kapcsolat lehetővé teszi az összes csatlakoztatott Azure-virtuális hálózatok natív átvitelét további felhasználói konfiguráció nélkül.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow4.png" alt-text="4. folyamat":::

### <a name="path-5"></a>5. elérési út

Az 5. elérési út a barangoló VPN (P2S) felhasználóinak a Nyugat-európai régióban lévő Azure-VNet való forgalmát mutatja.

A forgalom a következőképpen lesz átirányítva:

* A laptop-és mobileszköz-felhasználók az OpenVPN-ügyféllel transzparens módon csatlakozhatnak a P2S VPN-átjáróhoz Nyugat-Európában.

* A Nyugat-európai Virtual WAN hub a forgalmat helyileg irányítja a csatlakoztatott VNet.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow5.png" alt-text="5. folyamat":::

## <a name="security-and-policy-control-via-azure-firewall"></a>Biztonság és házirend-vezérlés Azure Firewallon keresztül

A contoso már ellenőrizte az összes ág és virtuális hálózatok közötti kapcsolatot a jelen cikk korábbi részében ismertetett követelményekkel összhangban. A biztonság szabályozására és a hálózat elkülönítésére vonatkozó követelmények teljesítése érdekében továbbra is el kell különíteni a forgalmat a hub-hálózaton keresztül. Ezt a funkciót korábban egy hálózati virtuális berendezés (NVA) hajtotta végre. A contoso azt is szeretné, hogy a meglévő proxy szolgáltatásokat leszerelje, és natív Azure-szolgáltatásokat használ a kimenő internetes szűréshez.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/security-policy.png" alt-text="Biztonság és házirend-vezérlés Azure Firewallon keresztül":::
**Ábra: Azure Firewall a virtuális WAN-ban (biztonságos virtuális központ)**

A következő magas szintű lépések szükségesek ahhoz, hogy bevezesse Azure Firewallt a virtuális WAN-hubokba, hogy lehetővé váljon egy egységes házirend-vezérlés. További információ erről a folyamatról és a biztonságos virtuális hubok fogalmáról: [Azure Firewall Manager](../firewall-manager/index.yml).

1. Azure Firewall házirend létrehozása.
2. Tűzfal-házirend csatolása az Azure Virtual WAN hub szolgáltatáshoz. Ez a lépés lehetővé teszi, hogy a meglévő virtuális WAN-központ biztonságos virtuális hubhoz működjön, és üzembe helyezi a szükséges Azure Firewall erőforrásokat.

> [!NOTE]
> A biztonságos virtuális hubok, köztük a régiók közötti forgalom használatára vonatkozó korlátozások vannak. További információ: a [tűzfal kezelőjével kapcsolatos ismert problémák](../firewall-manager/overview.md#known-issues).
>

Az alábbi elérési utak az Azure biztonságos virtuális hubok használatával elérhető csatlakozási útvonalakat mutatják:

### <a name="path-6"></a>6. elérési út

A 6. elérési út a virtuális hálózatok közötti biztonságos forgalmat mutatja ugyanazon a régión belül.

A forgalom a következőképpen lesz átirányítva:

* Az ugyanahhoz a biztonságos virtuális hubhoz csatlakoztatott virtuális hálózatok mostantól a Azure Firewall keresztül irányítják a forgalmat.

* A Azure Firewall szabályzatot alkalmazhat ezekre a folyamatokra.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow6.png" alt-text="6. folyamat":::

### <a name="path-7"></a>7. elérési út

Az elérési út 7 az Azure-VNet az internetre vagy a harmadik féltől származó biztonsági szolgáltatásra irányuló forgalmat mutatja.

A forgalom a következőképpen lesz átirányítva:

* A biztonságos virtuális hubhoz csatlakozó virtuális hálózatok a nyilvános, az internet felé irányuló adatforgalmat a biztonságos központi internet-hozzáférési pont használatával küldhetik el.

* Ez a forgalom a Azure Firewall FQDN-szabályokkal helyileg szűrhető, vagy egy külső biztonsági szolgáltatásba való elküldése a vizsgálathoz.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow7.png" alt-text="7. folyamat":::

### <a name="path-8"></a>8. elérési út

A 8. elérési út a fiókirodából az internetre vagy a harmadik féltől származó biztonsági szolgáltatásra irányuló forgalmat mutatja.

A forgalom a következőképpen lesz átirányítva:

* A biztonságos virtuális hubhoz csatlakozó ágak a biztonságos központi internet-hozzáférési pont használatával küldhetnek forgalmat a nyilvános célhelyekre az interneten.

* Ez a forgalom a Azure Firewall FQDN-szabályokkal helyileg szűrhető, vagy egy külső biztonsági szolgáltatásba való elküldése a vizsgálathoz.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow8.png" alt-text="8. folyamat":::

## <a name="next-steps"></a>Következő lépések

További információ az [Azure Virtual WAN](virtual-wan-about.md)-ról.
