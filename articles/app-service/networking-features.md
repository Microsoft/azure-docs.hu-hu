---
title: Hálózatkezelési funkciók
description: Ismerkedjen meg a Azure App Service hálózatkezelési funkcióiról, és Ismerje meg, hogy mely szolgáltatások szükségesek a biztonsághoz vagy más funkciókhoz.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 730f26039db0f5441563ac7bf5d6b0ab536cbcd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593129"
---
# <a name="app-service-networking-features"></a>Hálózati szolgáltatások App Service

Több módon is telepítheti az alkalmazásokat Azure App Service. Alapértelmezés szerint a App Serviceban üzemeltetett alkalmazások közvetlenül az interneten keresztül érhetők el, és csak az internetről futtatott végpontokat érhetik el. Számos alkalmazás esetében azonban meg kell határoznia a bejövő és a kimenő hálózati forgalmat. A App Service számos szolgáltatása segít az igényeinek kielégítésében. A kihívás az adott probléma megoldásához használható szolgáltatás ismerete. Ennek a cikknek a segítségével meghatározhatja, hogy melyik szolgáltatást kell használni, például a használati esetek alapján.

A Azure App Service két fő központi telepítési típust tartalmaz: 
- A több-bérlős nyilvános szolgáltatás ingyenes, közös, alapszintű, standard, prémium, PremiumV2 és PremiumV3 díjszabási SKU-ban üzemeltet App Service terveket. 
- Az egybérlős App Service Environment (kiszervezett) az elkülönített SKU-App Service csomagokat közvetlenül az Azure-beli virtuális hálózatban üzemelteti. 

Az Ön által használt funkciók attól függnek, hogy a több-bérlős szolgáltatásban vagy egy szolgáltatóban van-e. 

## <a name="multitenant-app-service-networking-features"></a>Több-bérlős App Service hálózatkezelési funkciók 

Azure App Service egy elosztott rendszer. A bejövő HTTP-vagy HTTPS-kérelmeket kezelő szerepköröket a rendszer *előtéri célokra* hívja meg. Az ügyfél munkaterhelését üzemeltető szerepköröket *feldolgozónak* nevezzük. Egy App Service üzemelő példány összes szerepköre több-bérlős hálózaton található. Mivel a App Service skálázási egységben sok különböző ügyfél található, nem csatlakoztatható közvetlenül a hálózathoz a App Service hálózat. 

A hálózatok csatlakoztatása helyett az alkalmazások közötti kommunikáció különböző szempontjait kezelő funkciókra van szükség. Az alkalmazásra *irányuló kéréseket* kezelő funkciók nem használhatók problémák megoldására, *amikor az alkalmazásból kezdeményez hívásokat* . Hasonlóképpen, az alkalmazással kapcsolatos hívások problémáit megoldó funkciók nem használhatók az alkalmazással kapcsolatos problémák megoldásához.  

| Bejövő funkciók | Kimenő funkciók |
|---------------------|-------------------|
| Alkalmazáshoz rendelt címe | Hibrid kapcsolatok |
| Hozzáférési korlátozások | Átjáró – szükséges VNet-integráció |
| Szolgáltatásvégpontok | VNet-integráció |
| Privát végpontok ||

A kivételek kivételével az összes funkciót együtt használhatja. A funkciók megoldásához összekeverheti a problémákat.

## <a name="use-cases-and-features"></a>Használati esetek és funkciók

Előfordulhat, hogy a probléma megoldásához bizonyos használati esetekre van szükség. A legjobb funkció kiválasztása időnként maga a használati eseten kívül esik. A következő bejövő használati esetek arra utalnak, hogyan használhatók a App Service hálózatkezelési funkciói az alkalmazásra irányuló forgalom szabályozásával kapcsolatos problémák megoldásához:
 
| Bejövő használati eset | Szolgáltatás |
|---------------------|-------------------|
| Az alkalmazás IP-alapú SSL-igényeinek támogatása | Alkalmazáshoz rendelt címe |
| Az alkalmazás nem megosztott dedikált bejövő címeinek támogatása | Alkalmazáshoz rendelt címe |
| Az alkalmazáshoz való hozzáférés korlátozása jól meghatározott címekből | Hozzáférési korlátozások |
| Az alkalmazáshoz való hozzáférés korlátozása egy virtuális hálózat erőforrásaiban | Szolgáltatásvégpontok </br> ILB </br> Privát végpontok |
| Az alkalmazás közzététele a virtuális hálózaton található magánhálózati IP-címen | ILB </br> Privát végpontok </br> Magánhálózati IP-cím egy Application Gateway-példány bejövő forgalmához szolgáltatási végpontokkal |
| Alkalmazás biztosítása webalkalmazási tűzfallal (WAF) | Application Gateway és ILB </br> Application Gateway privát végpontokkal </br> Application Gateway szolgáltatásvégpontokkal </br> Azure bejárati ajtó hozzáférési korlátozásokkal |
| Különböző régiókban lévő alkalmazások forgalmának elosztása | Azure bejárati ajtó hozzáférési korlátozásokkal | 
| Azonos régióban lévő forgalom terheléselosztása | [Application Gateway szolgáltatásvégpontokkal][appgwserviceendpoints] | 

A következő kimenő használati esetek azt mutatják be, hogyan használhatók a App Service hálózatkezelési funkciói az alkalmazás kimenő hozzáférési igényeinek megoldásához:

| Kimenő használati eset | Szolgáltatás |
|---------------------|-------------------|
| Egy adott régióban található Azure-beli virtuális hálózat erőforrásainak elérése | VNet-integráció </br> ASE |
| Erőforrásokhoz való hozzáférés egy másik régióban található Azure-beli virtuális hálózaton | Átjáró – szükséges VNet-integráció </br> Bemutató és virtuális hálózati társítás |
| A szolgáltatási végpontokkal védett erőforrások elérése | VNet-integráció </br> ASE |
| Hozzáférés az Azure-hoz nem csatlakoztatott magánhálózati hálózat erőforrásaihoz | Hibrid kapcsolatok |
| Erőforrások elérése az Azure ExpressRoute-áramkörök között | VNet-integráció </br> ASE | 
| Biztonságos kimenő forgalom a webalkalmazásból | VNet-integrációs és hálózati biztonsági csoportok </br> ASE | 
| A kimenő forgalom irányítása a webalkalmazásból | VNet-integráció és-útválasztási táblák </br> ASE | 


### <a name="default-networking-behavior"></a>Alapértelmezett hálózatkezelési viselkedés

Azure App Service skálázási egységek számos ügyfelet támogatnak az egyes telepítések során. Az ingyenes és a közös SKU-csomagok a több-bérlős feldolgozók ügyfeleinek munkaterheléseit üzemeltetik. Az alapszintű és a magasabb szintű csomagok olyan felhasználói munkaterheléseket üzemeltetnek, amelyek csak egy App Service csomaghoz vannak hozzárendelve. Ha standard App Service csomaggal rendelkezik, a tervben szereplő összes alkalmazás ugyanazon a munkavégzőn fog futni. Ha kibővíti a munkavégzőt, az adott App Service-csomag összes alkalmazását egy új feldolgozón replikálja a rendszer a App Service-csomag egyes példányain. 

#### <a name="outbound-addresses"></a>Kimenő címek

A munkavégző virtuális gépeket a App Service csomagok nagy részben osztják meg. Az ingyenes, közös, alapszintű, standard és prémium csomagok mindegyike ugyanazt a feldolgozó VM-típust használja. A PremiumV2-csomag egy másik virtuálisgép-típust használ. A PremiumV3 még egy virtuális gép típusát használja. Ha megváltoztatja a virtuálisgép-családot, a kimenő címek eltérő készletét kapja meg. Ha standard szintről PremiumV2-re méretezi, a kimenő címek megváltoznak. Ha a PremiumV2-ről PremiumV3-re méretezi, a kimenő címek megváltoznak. Egyes régebbi méretezési egységekben a bejövő és a kimenő címek is változnak, ha a standard és a PremiumV2 közötti méretezést végez. 

A kimenő hívásokhoz több cím is használható. Az alkalmazásban a kimenő hívások elküldéséhez használt kimenő címek az alkalmazás tulajdonságainál jelennek meg. Ezeket a címeket a App Service üzemelő példányon ugyanazon munkavégző virtuálisgép-családon futó alkalmazások osztják meg. Ha szeretné megtekinteni az alkalmazás által a méretezési egységben használt összes címet, akkor a rendszer a nevű tulajdonságot `possibleOutboundAddresses` fogja listázni. 

![Az alkalmazás tulajdonságait megjelenítő képernyőkép.](media/networking-features/app-properties.png)

App Service a szolgáltatás felügyeletéhez használt végpontok száma.  Ezeket a címeket külön dokumentumban tesszük közzé, és az `AppServiceManagement` IP-szolgáltatás címkéjén is szerepelnek. A `AppServiceManagement` címke csak olyan app Service-környezetekben használatos, ahol engedélyezni kell a forgalmat. A rendszer a App Service bejövő címeket az `AppService` IP-szolgáltatás címkéjén követi nyomon. Nincs olyan IP-szolgáltatás címkéje, amely az App Service által használt kimenő címeket tartalmazza. 

![A bejövő és a kimenő forgalmat App Service bemutató diagram.](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Alkalmazáshoz rendelt címe 

Az alkalmazáshoz hozzárendelt cím funkció az IP-alapú SSL-képesség indaja. Az SSL beállításával férhet hozzá az alkalmazáshoz. Ezt a funkciót IP-alapú SSL-hívásoknál használhatja. Azt is megteheti, hogy csak az általa használt címeket adja meg az alkalmazásnak. 

![Az alkalmazáshoz hozzárendelt címeket bemutató diagram.](media/networking-features/app-assigned-address.png)

Ha alkalmazáshoz rendelt címeket használ, a forgalom továbbra is ugyanazokat az előtér-szerepköröket használja, amelyek az összes bejövő forgalmat a App Service skálázási egységben kezelik. Az alkalmazáshoz hozzárendelt címeket azonban csak az alkalmazás használja. Használati esetek ehhez a szolgáltatáshoz:

* Az alkalmazáshoz tartozó IP-alapú SSL-igények támogatása.
* Állítson be egy dedikált, nem megosztott alkalmazáshoz tartozó-címeket.

Ha meg szeretné tudni, hogyan állíthat be egy címeket az alkalmazásban, olvassa el a következőt: [TLS/SSL-tanúsítvány hozzáadása Azure app Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Hozzáférési korlátozások 

A hozzáférési korlátozások lehetővé teszik a *bejövő* kérések szűrését. A szűrési művelet azon előtér-szerepkörökön történik, amelyek az alkalmazások futtatását végző feldolgozói szerepkörökből származnak. Mivel az előtér-szerepkörök a dolgozóktól származnak, a hozzáférési korlátozásokat az alkalmazások hálózati szintű védelmeként tekintheti át. 

Ez a funkció lehetővé teszi a prioritási sorrendben kiértékelt engedélyezési és megtagadási szabályok listájának összeállítását. Ez hasonló az Azure Networking hálózati biztonsági csoport (NSG) szolgáltatásához. Ezt a funkciót egy betekintő vagy a több-bérlős szolgáltatásban is használhatja. Ha egy ILB vagy privát végponttal használja, korlátozhatja a hozzáférést a privát címekről.
> [!NOTE]
> Akár 512 hozzáférési korlátozási szabály is konfigurálható alkalmazásként. 

![A hozzáférési korlátozásokat bemutató diagram.](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>IP-alapú hozzáférés korlátozási szabályai

Az IP-alapú hozzáférés korlátozásai szolgáltatás segít, ha korlátozni szeretné az alkalmazás eléréséhez használható IP-címeket. Az IPv4- és IPv6-címek egyaránt támogatottak. A szolgáltatás egyes használati esetei:
* Az alkalmazáshoz való hozzáférés korlátozása jól definiált címekből. 
* A külső terheléselosztási szolgáltatáson vagy más, ismert kimenő IP-címekkel rendelkező hálózati eszközökön érkező forgalomhoz való hozzáférés korlátozása. 

A szolgáltatás engedélyezésével kapcsolatos további információkért lásd: [hozzáférési korlátozások konfigurálása][iprestrictions].

> [!NOTE]
> Az IP-alapú hozzáférés korlátozási szabályai csak akkor kezelik a virtuális hálózati címtartományt, ha az alkalmazás egy App Service Environment. Ha az alkalmazás a több-bérlős szolgáltatásban található, akkor a [szolgáltatás-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md) használatával korlátozza a forgalmat a virtuális hálózatban lévő alhálózatok kiválasztására.

#### <a name="access-restriction-rules-based-on-service-endpoints"></a>Hozzáférési korlátozási szabályok szolgáltatási végpontok alapján 

A szolgáltatási végpontok lehetővé teszik az alkalmazáshoz való *bejövő* hozzáférés zárolását, így a forrás címének a kiválasztott alhálózatokból kell származnia. Ez a funkció az IP-hozzáférési korlátozásokkal együtt működik. A szolgáltatási végpontok nem kompatibilisek a távoli hibakereséssel. Ha a távoli hibakeresést szeretné használni az alkalmazással, az ügyfél nem lehet olyan alhálózatban, amelyen engedélyezve vannak a szolgáltatási végpontok. A szolgáltatási végpontok beállításának folyamata hasonló az IP-hozzáférési korlátozások beállításához használt folyamathoz. Létrehozhat olyan hozzáférési szabályok engedélyezési/megtagadási listáját, amelyek nyilvános címeket és alhálózatokat tartalmaznak a virtuális hálózatokban. 

A szolgáltatás egyes használati esetei:

* Állítson be egy Application Gateway-t az alkalmazásával, és zárolja az alkalmazás felé irányuló bejövő forgalmat.
* Az alkalmazáshoz való hozzáférés korlátozása a virtuális hálózatban lévő erőforrásokra. Ezek az erőforrások tartalmazhatnak virtuális gépeket, ASE, vagy akár más, a VNet-integrációt használó alkalmazásokat is. 

![A szolgáltatás-végpontok Application Gateway használatával történő használatát bemutató diagram.](media/networking-features/service-endpoints-appgw.png)

A szolgáltatási végpontok alkalmazással történő konfigurálásával kapcsolatos további tudnivalókért tekintse meg [Azure app Service hozzáférési korlátozásokat][serviceendpoints].
#### <a name="access-restriction-rules-based-on-service-tags-preview"></a>Hozzáférési korlátozási szabályok a szolgáltatás címkéi alapján (előzetes verzió)
Az [Azure-szolgáltatás címkéi][servicetags] az Azure-szolgáltatásokhoz tartozó IP-címek jól definiált készletei. A szolgáltatás-címkék csoportba foglalják a különböző Azure-szolgáltatásokban használt IP-tartományokat, és gyakran az adott régióra is kiterjednek. Ez lehetővé teszi a *bejövő* forgalom szűrését adott Azure-szolgáltatásokból. 

A címkék és további információk teljes listájáért látogasson el a Service tag fenti hivatkozására. A szolgáltatás engedélyezésével kapcsolatos további információkért lásd: [hozzáférési korlátozások konfigurálása][iprestrictions].
#### <a name="http-header-filtering-for-access-restriction-rules-preview"></a>Http-fejléc szűrése hozzáférési korlátozási szabályokhoz (előzetes verzió)
Az egyes hozzáférés-korlátozási szabályokhoz további HTTP-fejléceket adhat hozzá. Ez lehetővé teszi a bejövő kérések és a szűrők további vizsgálatát a HTTP-fejlécek megadott értékei alapján. Minden fejléc legfeljebb 8 értéket tartalmazhat. A következő HTTP-fejlécek jelenleg támogatottak: 
* X – továbbított – a következőhöz:
* X-továbbított-gazdagép
* X – Azure – FDID
* X-FD-HealthProbe

A HTTP-fejléc szűrésének egyes használati esetei a következők:
* Az állomásnév továbbítására szolgáló proxykiszolgálók hozzáférésének korlátozása
* Egy adott Azure-beli előtérben lévő példányhoz való hozzáférés korlátozása szolgáltatás-címkézési szabállyal és X-Azure-FDID fejléc korlátozásával
### <a name="private-endpoint"></a>Privát végpont

A privát végpont egy olyan hálózati adapter, amely az Azure Private-kapcsolaton keresztül csatlakozik a webalkalmazáshoz. A privát végpont egy magánhálózati IP-címet használ a virtuális hálózatról, amely hatékonyan hozza a webalkalmazást a virtuális hálózatba. Ez a funkció csak a webalkalmazás *bejövő* folyamatai számára érhető el.
További információ: [privát végpontok használata az Azure Web App][privateendpoints]szolgáltatáshoz.

A szolgáltatás egyes használati esetei:

* Korlátozza az alkalmazáshoz való hozzáférést egy virtuális hálózat erőforrásaiban. 
* Tegye elérhetővé az alkalmazást egy magánhálózati IP-címen a virtuális hálózaton. 
* Az alkalmazás védetté WAF.

A privát végpontok meggátolják az adatkiszűrése, mert az egyetlen dolog, amelyet a privát végponton érhet el, az az alkalmazás, amellyel konfigurálva van. 
 
### <a name="hybrid-connections"></a>Hibrid kapcsolatok

App Service Hibrid kapcsolatok lehetővé teszi, hogy az alkalmazások *kimenő* hívásokat hajtanak végre a megadott TCP-végpontokon. A végpont lehet helyszíni, virtuális hálózat vagy bárhol, amely lehetővé teszi az Azure-ba irányuló kimenő adatforgalmat az 443-es porton. A szolgáltatás használatához telepítenie kell egy hibridkapcsolat-kezelő nevű továbbító ügynököt egy Windows Server 2012 vagy újabb gazdagépre. Hibridkapcsolat-kezelő el kell tudnia érni Azure Relay a 443-as porton. A hibridkapcsolat-kezelő a portálon App Service Hibrid kapcsolatok KEZELŐFELÜLETén töltheti le. 

![A Hibrid kapcsolatok hálózati folyamatot bemutató diagram.](media/networking-features/hybrid-connections.png)

App Service Hibrid kapcsolatok az Azure Relay Hibrid kapcsolatok képességre épül. A App Service a szolgáltatás egy speciális formáját használja, amely csak a kimenő hívásokat támogatja az alkalmazásból egy TCP-gazdagépre és-portra. Ezt a gazdagépet és portot csak azon a gazdagépen kell feloldani, ahol a hibridkapcsolat-kezelő telepítve van. 

Ha az alkalmazás a App Serviceban DNS-lekérdezést végez a hibrid kapcsolatban megadott gazdagépen és porton, akkor a forgalom automatikusan átirányítja a hibrid kapcsolatokat, és nem hibridkapcsolat-kezelő. További információ: [App Service hibrid kapcsolatok][hybridconn].

Ez a funkció általában a következőket használja:

* Hozzáférés az Azure-hoz VPN-vagy ExpressRoute-kapcsolattal nem rendelkező magánhálózatok erőforrásaihoz.
* Támogatja a helyszíni alkalmazások App Service való áttelepítését anélkül, hogy a támogató adatbázisokat kellene áthelyeznie.  
* Nagyobb biztonság biztosítása egyetlen gazdagép és egy hibrid kapcsolaton keresztüli port számára. A legtöbb hálózati szolgáltatás nyitott hozzáférést biztosít a hálózathoz. A Hibrid kapcsolatok segítségével csak egyetlen gazdagépet és portot érhet el.
* A többi kimenő kapcsolati módszer által nem tárgyalt forgatókönyvek.
* App Service fejlesztését úgy végezheti el, hogy lehetővé tegye az alkalmazások számára a helyszíni erőforrások egyszerű használatát. 

Mivel ez a funkció lehetővé teszi a helyszíni erőforrásokhoz való hozzáférést a bejövő tűzfal nélküli furatok nélkül, népszerű a fejlesztők számára. A többi kimenő App Service hálózatkezelési funkció az Azure Virtual Networkhoz kapcsolódik. Hibrid kapcsolatok nem függ a virtuális hálózattól. A szolgáltatás szélesebb körű hálózati igényekhez is felhasználható. 

Vegye figyelembe, hogy App Service Hibrid kapcsolatok nem ismeri, hogy mit csinál. Így használhatja egy adatbázis, egy webszolgáltatás vagy egy tetszőleges TCP-szoftvercsatorna elérésére a nagyszámítógépeken. A szolgáltatás lényegében a TCP-csomagokat alagutakra irányítja. 

Hibrid kapcsolatok népszerű a fejlesztéshez, de éles alkalmazásokban is használatban van. Nagyszerű megoldás egy webszolgáltatáshoz vagy adatbázishoz való hozzáféréshez, de nem megfelelő olyan helyzetekben, amelyek sok kapcsolat létrehozását igénylik. 

### <a name="gateway-required-vnet-integration"></a>Átjáró – szükséges VNet-integráció 

Átjáró – kötelező App Service VNet-integráció lehetővé teszi, hogy az alkalmazás *kimenő* kéréseket hozzon egy Azure-beli virtuális hálózatba. A szolgáltatás úgy működik, hogy csatlakoztatja a gazdagépet, amely egy pont – hely típusú VPN-kapcsolaton keresztül fut egy Virtual Network átjárón a virtuális hálózaton. A szolgáltatás konfigurálásakor az alkalmazás megkapja az egyes példányokhoz rendelt pont – hely címek egyikét. Ez a funkció lehetővé teszi, hogy a klasszikus vagy Azure Resource Manager virtuális hálózatok erőforrásaihoz hozzáférjen bármely régióban. 

![Az átjáróhoz szükséges VNet-integrációt bemutató diagram.](media/networking-features/gw-vnet-integration.png)

Ez a szolgáltatás megoldja a más virtuális hálózatok erőforrásaihoz való hozzáférés problémáját. Akár egy virtuális hálózaton keresztül, akár más virtuális hálózatokhoz, akár helyileg is csatlakozhat. Nem működik a ExpressRoute csatlakoztatott virtuális hálózatokkal, de a helyek közötti VPN-kapcsolattal rendelkező hálózatokkal működik. Általában nem célszerű ezt a szolgáltatást egy App Service Environment (betanító) alkalmazásból használni, mert a betanító már a virtuális hálózatban van. Használati esetek ehhez a szolgáltatáshoz:

* Hozzáférés az Azure Virtual Networks privát IP-címeihez tartozó erőforrásokhoz. 
* Helyszíni erőforrásokhoz való hozzáférés, ha helyek közötti VPN van. 
* Erőforrások elérése a társ virtuális hálózatokban. 

Ha ez a funkció engedélyezve van, az alkalmazás a célként megadott virtuális hálózattal konfigurált DNS-kiszolgálót fogja használni. A szolgáltatással kapcsolatos további információkért lásd: [app Service VNet-integráció][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>VNet-integráció

Az átjárók számára szükséges VNet-integráció hasznos, de nem oldja meg az erőforrások ExpressRoute közötti elérésének problémáját. Ahhoz, hogy a ExpressRoute-kapcsolatokon keresztül is elérhetők legyenek, az alkalmazások számára szükséges, hogy a szolgáltatás-végpont által védett szolgáltatásokhoz hívásokat lehessen tenni. Egy másik VNet-integrációs képesség is megfelel ezeknek az igényeknek. 

Az új VNet-integrációs funkció lehetővé teszi, hogy az alkalmazás hátterét egy olyan erőforrás-kezelő virtuális hálózatban helyezze el, amely ugyanabban a régióban található, mint az alkalmazás. Ez a funkció nem érhető el olyan App Service Environmentról, amely már egy virtuális hálózatban van. Használati esetek ehhez a szolgáltatáshoz:

* Erőforrások elérése ugyanabban a régióban található Resource Manager-alapú virtuális hálózatokban.
* A szolgáltatás-végpontokkal védett erőforrások elérése. 
* Hozzáférés az ExpressRoute-vagy VPN-kapcsolatokon keresztül elérhető erőforrásokhoz.
* Segítsen az összes kimenő forgalom biztonságossá tételében. 
* Minden kimenő forgalom kényszerített bújtatása. 

![A VNet-integrációt bemutató diagram.](media/networking-features/vnet-integration.png)

További információ: [app Service VNet-integráció][vnetintegration].

### <a name="app-service-environment"></a>App Service-környezet 

Az App Service Environment (bevezetési) a virtuális hálózaton futó Azure App Service egybérlős telepítése. Néhány esetben ez a szolgáltatás:

* A virtuális hálózat erőforrásainak elérése.
* Erőforrások elérése a ExpressRoute között.
* Tegye elérhetővé alkalmazásait privát címként a virtuális hálózaton. 
* Erőforrások elérése szolgáltatási végpontok között. 

A bevezetési szolgáltatással nem kell olyan szolgáltatásokat használnia, mint a VNet-integráció vagy a szolgáltatási végpontok, mert a betanító már a virtuális hálózatban van. Ha olyan erőforrásokat szeretne elérni, mint például az SQL vagy az Azure Storage szolgáltatás-végpontokon keresztül, engedélyezze a szolgáltatási végpontokat a szolgáltatói alhálózaton. Ha a virtuális hálózat erőforrásaihoz szeretne hozzáférni, nincs szükség további konfigurálásra. Ha a ExpressRoute-on keresztül szeretné elérni az erőforrásokat, akkor már a virtuális hálózatban van, és nem kell semmit konfigurálnia a betanító vagy az alkalmazásokban. 

Mivel a ILB-beadásban lévő alkalmazások elérhetővé tehetők egy magánhálózati IP-címen, egyszerűen hozzáadhat WAF-eszközöket, hogy csak az internethez használni kívánt alkalmazásokat tegye elérhetővé, és segít megőrizni a REST biztonságát. Ez a szolgáltatás megkönnyíti a többszintes alkalmazások fejlesztését. 

Bizonyos dolgok jelenleg nem lehetségesek a több-bérlős szolgáltatásban, de lehetséges, hogy egy központból származnak. Íme néhány példa:

* Tegye elérhetővé alkalmazásait privát IP-címen.
* Az összes olyan hálózati vezérlővel biztonságossá teheti a kimenő forgalmat, amely nem része az alkalmazásnak.
* Alkalmazások üzemeltetése egyetlen bérlős szolgáltatásban. 
* Vertikális felskálázás több példányra, mint amennyi lehetséges a több-bérlős szolgáltatásban. 
* Privát HITELESÍTÉSSZOLGÁLTATÓI Ügyféltanúsítványok betöltése az alkalmazások számára privát CA által védett végpontokkal.
* A TLS 1,1 kényszerítése a rendszerben üzemeltetett összes alkalmazásra anélkül, hogy le tudja tiltani az alkalmazás szintjén. 
* Adjon meg egy dedikált kimenő IP-címeket a beadásban lévő összes olyan alkalmazáshoz, amely nem az ügyfelekkel van megosztva. 

![Egy virtuális hálózatban lévő beadást bemutató diagram.](media/networking-features/app-service-environment.png)

A benyújtó a legjobb történet az elkülönített és a dedikált alkalmazások üzemeltetéséhez, de a felügyeleti kihívásokat is magában foglalja. A következő szempontokat kell figyelembe vennie, mielőtt működési elősegítő szolgáltatást alkalmazzon:
 
 * A rendszer a virtuális hálózatán belül fut, de a virtuális hálózaton kívüli függőségekkel rendelkezik. Ezeket a függőségeket engedélyezni kell. További tudnivalókért tekintse [meg az App Service Environment hálózatkezelési szempontjait][networkinfo].
 * A beosztási szolgáltatás nem azonnal, mint a több-bérlős szolgáltatás. A skálázási igényeket a reaktív méretezés helyett előre kell mérni. 
 * A benyújtó bekerülési díja magasabb. Ahhoz, hogy a lehető legtöbbet hozza ki a bevezetésből, érdemes sok munkaterhelést beépíteni egy kisegítő eszközre, ahelyett, hogy kis erőfeszítésekhez kellene használnia.
 * A központba tartozó alkalmazások nem tudják szelektíven korlátozni a hozzáférést a központhoz tartozó egyes alkalmazásokhoz, másokat nem.
 * A Bekapcsolási pont egy alhálózatban található, és minden hálózati szabály az adott központba irányuló és onnan érkező összes forgalomra érvényes. Ha csak egy alkalmazás bejövő forgalmi szabályait szeretné hozzárendelni, használja a hozzáférési korlátozásokat. 

## <a name="combining-features"></a>Szolgáltatások egyesítése 

A több-bérlős szolgáltatáshoz feljegyzett funkciók a bonyolultabb használati esetek megoldásához is használhatók. A leggyakoribb használati esetek közül kettőt itt ismertetünk, de ezek csak példák. A különféle funkciók megismerésével szinte minden rendszerarchitektúra-igényt megtalálhat.

### <a name="place-an-app-into-a-virtual-network"></a>Alkalmazás elhelyezése egy virtuális hálózaton

Megtudhatja, hogyan helyezhet üzembe egy alkalmazást egy virtuális hálózaton. Ha egy virtuális hálózatba helyezi az alkalmazást, az alkalmazás bejövő és kimenő végpontjai a virtuális hálózaton belül vannak. A probléma megoldásához a bevezető a legjobb módszer. A funkciók összevonásával azonban a több-bérlős szolgáltatáson belül is kielégítheti a legtöbb igényét. Például a következő módon üzemeltetheti a csak intranetes alkalmazásokat privát bejövő és kimenő címekkel:

* Privát bejövő és kimenő címmel rendelkező Application Gateway létrehozása.
* A bejövő forgalom védelme az alkalmazáshoz a szolgáltatási végpontokkal. 
* Az új VNet-integrációs funkció használata, hogy az alkalmazás háttérrendszer a virtuális hálózatban legyen. 

Ez a telepítési stílus nem ad dedikált IP-címeket az internetre irányuló kimenő forgalom számára, vagy az alkalmazásból érkező összes kimenő forgalom zárolásának lehetőségét. A szolgáltatás sok lehetőséget nyújt Önnek, hogy csak a beszerzést kérje. 

### <a name="create-multitier-applications"></a>Többplatformos alkalmazások létrehozása

A többrétegű alkalmazások olyan alkalmazások, amelyekben az API-háttérbeli alkalmazások csak az előtér-rétegből érhetők el. Többrétegű alkalmazások létrehozása kétféleképpen végezhető el. A VNet-integráció használatával mindkettővel csatlakozhat az előtér-webalkalmazáshoz egy virtuális hálózatban lévő alhálózathoz. Ez lehetővé teszi, hogy a webalkalmazás hívásokat hajtson végre a virtuális hálózatban. Miután az előtér-alkalmazás csatlakozott a virtuális hálózathoz, el kell döntenie, hogyan zárolja az API-alkalmazáshoz való hozzáférést. A következőket teheti:

* Az előtér-és az API-alkalmazást ugyanabban a ILB-központban üzemeltetheti, és egy Application Gateway használatával elérhetővé teheti az előtér-alkalmazást az interneten.
* Üzemelteti az előtért a több-bérlős szolgáltatásban, a háttérben pedig egy ILB-előfizetést.
* A több-bérlős szolgáltatásban az előtér-és az API-alkalmazást is üzemeltetheti.

Ha az előtér-és API-alkalmazást egy többplatformos alkalmazáshoz is üzemelteti, a következőket teheti:

- Az API-alkalmazás közzététele a virtuális hálózat privát végpontjai használatával:

  ![Diagram, amely bemutatja a privát végpontok használatát egy kétrétegű alkalmazásban.](media/networking-features/multi-tier-app-private-endpoint.png)

- A szolgáltatási végpontok használatával biztosíthatja, hogy az API-alkalmazás felé irányuló bejövő forgalom csak az előtér-webalkalmazás által használt alhálózatból származik:

  ![A szolgáltatás-végpontok használatát bemutató diagram, amely segít az alkalmazások biztonságossá tételében.](media/networking-features/multi-tier-app.png)

Íme néhány szempont, amely segít eldönteni, hogy melyik módszert kell használnia:

* Szolgáltatási végpontok használata esetén csak az API-alkalmazás felé irányuló forgalmat kell biztonságossá tenni az integrációs alhálózaton. Ez segít az API-alkalmazás biztonságossá tételében, de továbbra is rendelkezhet az kiszűrése származó adatokkal az App Service-ben lévő más alkalmazásokba.
* Privát végpontok használatakor két alhálózattal rendelkezik a Play-ben, ami bonyolultságot kölcsönöz. Emellett a privát végpont egy legfelső szintű erőforrás, és felügyeleti terhelést is jelent. A privát végpontok használatának előnye, hogy nincs lehetősége az adatkiszűrése. 

Mindkét metódus több előtér-végponttal is működik. Kis méretekben a szolgáltatási végpontok könnyebben használhatók, mivel egyszerűen engedélyezheti a szolgáltatási végpontokat az API-alkalmazáshoz az előtér-integrációs alhálózaton. Több előtér-alkalmazás hozzáadásakor minden API-alkalmazást módosítania kell az integrációs alhálózattal rendelkező szolgáltatási végpontok belefoglalásához. Ha privát végpontokat használ, összetettebbek lesznek, de a privát végpontok beállítása után nem kell semmit módosítania az API-alkalmazásokban. 

### <a name="line-of-business-applications"></a>Üzletági alkalmazások

Az üzletági (LOB) alkalmazások olyan belső alkalmazások, amelyek általában nem érhetők el az internetről. Ezeket az alkalmazásokat a vállalati hálózatokon belül kell meghívni, ahol a hozzáférés szigorúan szabályozható. Ha ILB-t használ, könnyen üzemeltetheti az üzletági alkalmazásait. Ha a több-bérlős szolgáltatást használja, használhat privát végpontokat, vagy használhatja a szolgáltatási végpontokat az Application Gateway szolgáltatással együtt. A magánhálózati végpontok használata helyett két ok van az Application Gateway és a szolgáltatási végpontok használatára:
* WAF-védelemmel kell rendelkeznie a LOB-alkalmazásokban.
* Terheléselosztást szeretne betölteni a LOB-alkalmazások több példányára.

Ha ezen igények egyike sem érvényes, akkor jobb, ha privát végpontokat használ. A App Serviceban elérhető privát végpontokkal a virtuális hálózat privát címein közzéteheti az alkalmazásait. A virtuális hálózatban elhelyezhető privát végpont ExpressRoute és VPN-kapcsolatokon keresztül is elérhető. 

A privát végpontok konfigurálása privát címen teszi elérhetővé az alkalmazásokat, de a DNS-t úgy kell konfigurálnia, hogy elérje ezt a internetcímet a helyszíni környezetből. Ennek a konfigurációnak a végrehajtásához továbbítania kell a privát végpontokat tartalmazó Azure DNS privát zónát a helyszíni DNS-kiszolgálókra. Azure DNS privát zónák nem támogatják a zónák továbbítását, de a zónák továbbítását a DNS-kiszolgáló használatával is támogathatja erre a célra. A [DNS-továbbító](https://azure.microsoft.com/resources/templates/301-dns-forwarder/) sablon megkönnyíti a Azure DNS saját zónájának továbbítását a helyszíni DNS-kiszolgálókra.

## <a name="app-service-ports"></a>App Service portok

App Service vizsgálatakor számos olyan portot talál, amelyek elérhetők a bejövő kapcsolatokhoz. A több-bérlős szolgáltatásban a portok elérését nem lehet letiltani vagy szabályozni. Itt láthatók a kitett portok listája:

| Használat | Port vagy portok |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Kezelés | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  A Visual Studio távoli hibakeresése  |  4020, 4022, 4024 |
|  Web Deploy szolgáltatás | 8172 |
|  Infrastruktúra-használat | 7654, 1221 |

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md
[servicetags]: ../virtual-network/service-tags-overview.md