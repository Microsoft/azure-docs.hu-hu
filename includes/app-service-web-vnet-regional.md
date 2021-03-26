---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 7796b94609a9be05fdb72900d0725747440f8042
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582561"
---
A regionális VNet-integráció használata lehetővé teszi, hogy az alkalmazás hozzáférjen:

* Az VNet lévő erőforrások ugyanabban a régióban, mint az alkalmazás.
* A virtuális hálózatok-ben lévő erőforrások az alkalmazás integrált VNet vannak társítva.
* Szolgáltatás végpontjának biztonságos szolgáltatásai.
* Erőforrások az Azure ExpressRoute-kapcsolatokon keresztül.
* A-ben integrált VNet erőforrásai.
* Többek között az Azure ExpressRoute-kapcsolatokat tartalmazó, egymással összetartozó kapcsolatok erőforrásai.
* Privát végpontok 

Ha VNet-integrációt használ az virtuális hálózatok-ben ugyanabban a régióban, akkor a következő Azure hálózati funkciókat használhatja:

* **Hálózati biztonsági csoportok (NSG)**: letilthatja a kimenő forgalmat egy olyan NSG, amely az integrációs alhálózaton van elhelyezve. A bejövő szabályok nem érvényesek, mert nem használhatja a VNet-integrációt az alkalmazáshoz való bejövő hozzáférés biztosításához.
* **Útválasztási táblák (UDR)**: az integrációs alhálózaton elhelyezhető egy útválasztási táblázat, amely a kívánt kimenő forgalmat küldi el.

Az alkalmazás alapértelmezés szerint csak a RFC1918-forgalmat irányítja át a VNet. Ha az összes kimenő forgalmat át szeretné irányítani a VNet, a következő lépésekkel adhatja hozzá a beállítást az `WEBSITE_VNET_ROUTE_ALL` alkalmazásban: 

1. Nyissa meg a **konfigurációs** felhasználói felületet az alkalmazás-portálon. Válassza az **Új alkalmazás beállítása** lehetőséget.
1. Írja be a `WEBSITE_VNET_ROUTE_ALL` **nevet a név** mezőbe, majd írja be `1` a **értéket a Value (érték** ) mezőbe.

   ![Alkalmazásbeállítás megadása][4]

1. Kattintson az **OK** gombra.
1. Kattintson a **Mentés** gombra.

> [!NOTE]
> Ha az összes kimenő forgalmat átirányítja a VNet, a rendszer az integrációs alhálózatra alkalmazott NSG és UDR vonatkozik. Ha a értékre `WEBSITE_VNET_ROUTE_ALL` van állítva `1` , a kimenő forgalom továbbra is az alkalmazás tulajdonságaiban felsorolt címekről lesz elküldve, kivéve, ha olyan útvonalakat ad meg, amelyek máshol nem irányítják a forgalmat.
> 
> A regionális VNet-integráció nem tudja használni a 25-ös portot.

Bizonyos korlátozások vonatkoznak a VNet-integrációnak az azonos régióban található virtuális hálózatok való használatára:

* A globális társ-összekapcsolási kapcsolatok erőforrásai nem érhetők el.
* A szolgáltatás a Premium v2 és a Premium v3 összes App Service skálázási egységében érhető el. Standard szintű, de csak az újabb App Service skálázási egységek esetében érhető el. Ha régebbi méretezési egységet használ, a funkciót csak prémium v2 App Service csomagból használhatja. Ha azt szeretné, hogy a szolgáltatás a standard App Service csomagban is használható legyen, hozzon létre egy prémium szintű v3-os App Service-csomagot. Ezek a csomagok csak a legújabb méretezési egységeken támogatottak. Ha ezt követően szeretné, lekicsinyítheti a méretezést.  
* Az integrációs alhálózatot csak egy App Service csomag használhatja.
* A funkciót nem lehet használni a App Service Environmentban található elkülönített csomagbeli alkalmazások.
* A szolgáltatáshoz egy Azure Resource Manager VNet egy/28 vagy nagyobb, nem használt alhálózatra van szükség.
* Az alkalmazásnak és a VNet ugyanabban a régióban kell lennie.
* A VNet nem törölhető integrált alkalmazással. A VNet törlése előtt távolítsa el az integrációt.
* App Service-csomag esetében csak egy regionális VNet-integráció lehet. Ugyanazon a App Service csomagon belül több alkalmazás is használhatja ugyanazt a VNet.
* Egy alkalmazás vagy csomag előfizetése nem módosítható, amíg van olyan alkalmazás, amely regionális VNet-integrációt használ.
* Az alkalmazás a konfiguráció módosítása nélkül nem tudja feloldani a Azure DNS Private Zones címeit.

A VNet-integráció egy dedikált alhálózattól függ. Az alhálózatok kiépítésekor az Azure-alhálózat elveszíti öt IP-címeit az elejétől. Az egyes csomagokhoz tartozó integrációs alhálózaton egy-egy címnek kell lennie. Amikor négy példányra méretezi az alkalmazást, a rendszer négy címet használ. 

Ha a méret fel-vagy leskálázását választja, a szükséges címterület rövid ideig megduplázódik. Ez hatással van az adott alhálózat méretének valós, elérhető támogatott példányaira. A következő táblázat a CIDR-blokkokban található maximális rendelkezésre álló címeket és a horizontális skálázás hatásait mutatja be:

| CIDR-blokk mérete | Rendelkezésre álló címek maximális száma | Maximális vízszintes skála (példányok)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Feltételezi, hogy egy bizonyos ponton fel kell mérnie a méretet vagy az SKU-t. 

Mivel az alhálózat mérete nem módosítható a hozzárendelés után, olyan alhálózatot használjon, amely elég nagy ahhoz, hogy megfeleljen az alkalmazásnak. Az alhálózati kapacitással kapcsolatos problémák elkerülése érdekében a/26-ot 64-es címmel kell használni.  

Ha azt szeretné, hogy egy másik csomagban lévő alkalmazásai olyan VNet érjenek el, amely már kapcsolódik egy másik csomagban lévő alkalmazásokhoz, válasszon egy másik alhálózatot, mint amit a már meglévő VNet-integráció használ.

A szolgáltatás teljes mértékben támogatott Windows-és Linux-alkalmazásokhoz, beleértve az [Egyéni tárolókat](../articles/app-service/quickstart-custom-container.md)is. Az összes viselkedés ugyanaz, mint a Windows-alkalmazások és a Linux-alkalmazások között.

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A regionális VNet-integráció lehetővé teszi a szolgáltatási végpontok használatát. A szolgáltatásnak az alkalmazásból szolgáltatásbeli végpontokon keresztüli eléréséhez szükséges alapszintű lépések a következők:

1. Konfigurálja a regionális VNet-integrációt a webalkalmazással egy adott alhálózathoz való csatlakozáshoz az integrációhoz.
1. Lépjen a cél szolgáltatáshoz, és konfigurálja a szolgáltatási végpontokat az integrációs alhálózaton.

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

Hálózati biztonsági csoportok használatával blokkolhatja a bejövő és a kimenő forgalmat egy VNet erőforrásaihoz. A regionális VNet-integrációt használó alkalmazások [hálózati biztonsági csoporttal][VNETnsg] letilthatják a VNet vagy az interneten lévő erőforrásokra irányuló kimenő forgalmat. A nyilvános címekre irányuló forgalom letiltásához az Alkalmazásbeállítások beállítás `WEBSITE_VNET_ROUTE_ALL` értékeként kell beállítani `1` . Egy NSG bejövő szabályai nem érvényesek az alkalmazásra, mert a VNet-integráció csak az alkalmazásból érkező kimenő forgalmat érinti.

Az alkalmazás bejövő forgalmának szabályozásához használja a hozzáférési korlátozások funkciót. Az integrációs alhálózatra alkalmazott NSG az integrációs alhálózatra alkalmazott útvonalaktól függetlenül érvényesek. Ha a értéke, `WEBSITE_VNET_ROUTE_ALL` `1` és nincs olyan útvonala, amely hatással van a nyilvános címek forgalmára az integrációs alhálózaton, az összes kimenő forgalom továbbra is az integrációs alhálózathoz rendelt NSG függ. Ha `WEBSITE_VNET_ROUTE_ALL` nincs beállítva, a rendszer csak a RFC1918-forgalomra alkalmazza a NSG.

### <a name="routes"></a>Útvonalak

Az útválasztási táblázatok használatával a kimenő forgalmat az alkalmazásból bárhonnan irányíthatja, bárhol is legyenek. Alapértelmezés szerint az útválasztási táblák csak a RFC1918 vonatkoznak. Ha `WEBSITE_VNET_ROUTE_ALL` a értékre van állítva `1` , a rendszer minden kimenő hívást érint. Az integrációs alhálózaton beállított útvonalak nem érintik a bejövő alkalmazások kéréseire adott válaszokat. A gyakori célhelyek lehetnek tűzfalak vagy átjárók.

Ha a helyszíni összes kimenő forgalmat is át szeretné irányítani, az útválasztási táblázat segítségével elküldheti az összes kimenő forgalmat a ExpressRoute-átjárónak. Ha átirányítja a forgalmat egy átjáróra, ügyeljen arra, hogy a külső hálózatban lévő útvonalakat az összes válasz visszaküldéséhez adja meg.

A Border Gateway Protocol (BGP) útvonalak az alkalmazások forgalmára is hatással vannak. Ha az ExpressRoute-átjáróhoz hasonló BGP-útvonalak vannak, akkor a rendszer az alkalmazás kimenő forgalmát is érinti. Alapértelmezés szerint a BGP-útvonalak csak a RFC1918-forgalmat érintik. Ha a értékre `WEBSITE_VNET_ROUTE_ALL` van állítva `1` , a BGP-útvonalakon az összes kimenő forgalom hatással lehet.

### <a name="azure-dns-private-zones"></a>Privát zónák Azure DNS 

Miután az alkalmazás integrálva van a VNet, ugyanazt a DNS-kiszolgálót használja, amelyhez a VNet konfigurálva van. Alapértelmezés szerint az alkalmazás nem fog működni Azure DNS privát zónákkal. Azure DNS saját zónák használatakor a következő Alkalmazásbeállítások hozzáadására van szükség:

1. `WEBSITE_DNS_SERVER` értékkel `168.63.129.16`
1. `WEBSITE_VNET_ROUTE_ALL` értékkel `1`

Ezek a beállítások elküldik az alkalmazásból a kimenő hívásokat az VNet, és lehetővé teszik, hogy az alkalmazás hozzáférjen egy Azure DNS privát zónához. Ezekkel a beállításokkal az alkalmazás használhatja a Azure DNSt a DNS privát zónájának a munkavégző szinten történő lekérdezésével.  

> [!NOTE]
> Ha egyéni tartományt szeretne hozzáadni egy webalkalmazáshoz egy DNS privát zónával, nem lehetséges a VNET-integráció. Az egyéni tartomány érvényesítése a vezérlő szintjén, nem pedig a munkavégző szinten történik, ami megakadályozza a DNS-rekordok észlelését. Ha egyéni tartományt szeretne használni egy DNS privát zónából, el kell kerülnie az érvényesítést egy [Application Gateway](../articles/app-service/networking/app-gateway-with-service-endpoints.md) vagy [ILB app Service Environment](../articles/app-service/environment/create-ilb-ase.md)használatával.

### <a name="private-endpoints"></a>Privát végpontok

Ha [privát végpontokra][privateendpoints]szeretne hívásokat kezdeményezni, meg kell győződnie arról, hogy a DNS-lekérdezések feloldása a privát végponton megtörténjen. Ezt a viselkedést a következő módszerek egyikével kényszerítheti ki: 

* Integrálható Azure DNS privát zónákkal. Ha a VNet nem rendelkezik egyéni DNS-kiszolgálóval, ez automatikusan megtörténik.
* Felügyelje az alkalmazás által használt DNS-kiszolgáló privát végpontját. Ehhez ismernie kell a magánhálózati végpont címeit, majd egy rekord használatával arra a végpontra kell mutatnia, amelynek az adott címnek a elérésére törekszik.
* Konfigurálja a saját DNS-kiszolgálóját, hogy az továbbítson Azure DNS privát zónákhoz.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
