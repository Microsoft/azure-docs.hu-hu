---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 06/08/2020
ms.author: ccompy
ms.openlocfilehash: 14b9d9fe0eb9dfe2f25373c2d87d9b4af15dd0d9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371933"
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

* **Hálózati biztonsági csoportok (NSG)** : letilthatja a kimenő forgalmat egy olyan NSG, amely az integrációs alhálózaton van elhelyezve. A bejövő szabályok nem érvényesek, mert nem használhatja a VNet-integrációt az alkalmazáshoz való bejövő hozzáférés biztosításához.
* **Útválasztási táblák (UDR)** : az integrációs alhálózaton elhelyezhető egy útválasztási táblázat, amely a kívánt kimenő forgalmat küldi el.

Az alkalmazás alapértelmezés szerint csak a RFC1918-forgalmat irányítja át a VNet. Ha az összes kimenő forgalmat át szeretné irányítani a VNet, alkalmazza az alkalmazás beállítását WEBSITE_VNET_ROUTE_ALL az alkalmazásra. Az alkalmazás beállításának konfigurálása:

1. Nyissa meg a **konfigurációs** felhasználói felületet az alkalmazás-portálon. Válassza az **Új alkalmazás beállítása** lehetőséget.
1. Írja **WEBSITE_VNET_ROUTE_ALL** be a WEBSITE_VNET_ROUTE_ALL **nevet a név** mezőbe, és írja be az **1** **értéket az érték** mezőbe.

   ![Alkalmazásbeállítás megadása][4]

1. Kattintson az **OK** gombra.
1. Kattintson a **Mentés** gombra.

> [!NOTE]
> Ha az összes kimenő forgalmat átirányítja a VNet, a rendszer az integrációs alhálózatra alkalmazott NSG és UDR vonatkozik. Ha az összes kimenő forgalmat átirányítja a VNet, a kimenő címek továbbra is az alkalmazás tulajdonságaiban felsorolt kimenő címek lesznek, kivéve, ha olyan útvonalakat ad meg, amelyek máshová nem küldik a forgalmat.

Bizonyos korlátozások vonatkoznak a VNet-integrációnak az azonos régióban található virtuális hálózatok való használatára:

* A globális társ-összekapcsolási kapcsolatok erőforrásai nem érhetők el.
* A szolgáltatás csak a PremiumV2 App Service csomagokat támogató újabb Azure App Service skálázási egységekből érhető el. Vegye figyelembe, hogy *Ez nem jelenti azt, hogy az alkalmazásnak egy PremiumV2 díjszabási szinten kell futnia* , csak azt, hogy egy app Service-csomagon kell futnia, ahol a PremiumV2 lehetőség elérhető (ami azt jelenti, hogy ez egy újabb méretezési egység, ahol ez a VNet-integrációs szolgáltatás is elérhető).
* Az integrációs alhálózatot csak egy App Service csomag használhatja.
* A funkciót nem lehet használni a App Service Environmentban található elkülönített csomagbeli alkalmazások.
* A szolgáltatáshoz egy nem használt alhálózat szükséges, amely a/27 32-es vagy nagyobb méretű egy Azure Resource Manager VNet.
* Az alkalmazásnak és a VNet ugyanabban a régióban kell lennie.
* A VNet nem törölhető integrált alkalmazással. A VNet törlése előtt távolítsa el az integrációt.
* Csak a virtuális hálózatok integrálható az alkalmazással megegyező előfizetésben.
* App Service-csomag esetében csak egy regionális VNet-integráció lehet. Ugyanazon a App Service csomagon belül több alkalmazás is használhatja ugyanazt a VNet.
* Egy alkalmazás vagy csomag előfizetése nem módosítható, amíg van olyan alkalmazás, amely regionális VNet-integrációt használ.
* Az alkalmazás a konfiguráció módosítása nélkül nem tudja feloldani a Azure DNS Private Zones címeit

Az egyes csomag-példányok esetében egy-egy-egy-egy címnek Ha öt példányra méretezi az alkalmazást, akkor öt címet használ a rendszer. Mivel az alhálózat mérete nem módosítható a hozzárendelés után, olyan alhálózatot kell használnia, amely elég nagy ahhoz, hogy megfeleljen az alkalmazásnak. Az ajánlott méret a/26, 64 címmel. Az a/26, 64 címmel rendelkező Prémium csomag 30 példányban. Ha felfelé vagy lefelé méretezi a tervet, a rövid ideig kétszer annyi címre van szüksége.

Ha azt szeretné, hogy egy másik csomagban lévő alkalmazásai olyan VNet érjenek el, amely már kapcsolódik egy másik csomagban lévő alkalmazásokhoz, válasszon egy másik alhálózatot, mint amelyet a meglévő VNet-integráció használ.

A szolgáltatás teljes mértékben támogatott Windows-és Linux-alkalmazásokhoz, beleértve az [Egyéni tárolókat](../articles/app-service/quickstart-custom-container.md)is. Az összes viselkedés ugyanaz, mint a Windows-alkalmazások és a Linux-alkalmazások között.

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A regionális VNet-integráció lehetővé teszi a szolgáltatási végpontok használatát. Ha szolgáltatási végpontokat szeretne használni az alkalmazással, a regionális VNet-integráció segítségével csatlakozhat egy kiválasztott VNet, majd a szolgáltatási végpontokat az integrációhoz használt alhálózaton a célként megadott szolgáltatással konfigurálhatja. Ha ezt követően egy szolgáltatáshoz szeretne hozzáférni a szolgáltatási végpontokon keresztül:

1. a regionális VNet-integráció konfigurálása a webalkalmazással
1. Lépjen a célhely szolgáltatáshoz, és konfigurálja a szolgáltatási végpontokat az integrációhoz használt alhálózattal.

### <a name="network-security-groups"></a>Hálózati biztonsági csoportok

Hálózati biztonsági csoportok használatával blokkolhatja a bejövő és a kimenő forgalmat egy VNet erőforrásaihoz. A regionális VNet-integrációt használó alkalmazások [hálózati biztonsági csoporttal][VNETnsg] letilthatják a VNet vagy az interneten lévő erőforrásokra irányuló kimenő forgalmat. A nyilvános címekre irányuló forgalom letiltásához az alkalmazás beállítását WEBSITE_VNET_ROUTE_ALL 1-re kell beállítani. Egy NSG bejövő szabályai nem érvényesek az alkalmazásra, mert a VNet-integráció csak az alkalmazásból érkező kimenő forgalmat érinti.

Az alkalmazás bejövő forgalmának szabályozásához használja a hozzáférési korlátozások funkciót. Az integrációs alhálózatra alkalmazott NSG az integrációs alhálózatra alkalmazott útvonalaktól függetlenül érvényesek. Ha WEBSITE_VNET_ROUTE_ALL értéke 1, és nincs olyan útvonala, amely hatással van a nyilvános címek forgalmára az integrációs alhálózaton, az összes kimenő forgalom továbbra is az integrációs alhálózathoz rendelt NSG függ. Ha WEBSITE_VNET_ROUTE_ALL nincs beállítva, a rendszer csak a RFC1918-forgalomra alkalmazza a NSG.

### <a name="routes"></a>Útvonalak

Az útválasztási táblázatok használatával a kimenő forgalmat az alkalmazásból bárhonnan irányíthatja, bárhol is legyenek. Alapértelmezés szerint az útválasztási táblák csak a RFC1918 vonatkoznak. Ha a WEBSITE_VNET_ROUTE_ALL 1 értékre állítja, az összes kimenő hívást érinti. Az integrációs alhálózaton beállított útvonalak nem érintik a bejövő alkalmazások kéréseire adott válaszokat. A gyakori célhelyek lehetnek tűzfalak vagy átjárók.

Ha a helyszíni összes kimenő forgalmat is át szeretné irányítani, az útválasztási táblázat segítségével elküldheti az összes kimenő forgalmat a ExpressRoute-átjárónak. Ha átirányítja a forgalmat egy átjáróra, ügyeljen arra, hogy a külső hálózatban lévő útvonalakat az összes válasz visszaküldéséhez adja meg.

A Border Gateway Protocol (BGP) útvonalak az alkalmazások forgalmára is hatással vannak. Ha az ExpressRoute-átjáróhoz hasonló BGP-útvonalak vannak, akkor az alkalmazás kimenő forgalmát fogja érinteni. Alapértelmezés szerint a BGP-útvonalak csak a RFC1918-forgalmat érintik. Ha WEBSITE_VNET_ROUTE_ALL értéke 1, az összes kimenő forgalmat érintheti a BGP-útvonalak.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

Miután az alkalmazás integrálva van a VNet, ugyanazt a DNS-kiszolgálót használja, amelyhez a VNet konfigurálva van. Alapértelmezés szerint az alkalmazás nem fog működni Azure DNS Private Zones. A Azure DNS Private Zones való együttműködéshez a következő Alkalmazásbeállítások hozzáadására van szükség:

1. WEBSITE_DNS_SERVER értékkel 168.63.129.16
1. 1. értékkel rendelkező WEBSITE_VNET_ROUTE_ALL

Ezek a beállítások az alkalmazásból érkező összes kimenő hívást elküldik a VNet. Emellett lehetővé teszi, hogy az alkalmazás a Azure DNS használja a saját DNS zóna munkavégző szinten történő lekérdezésével. Ezt a funkciót akkor kell használni, ha egy futó alkalmazás egy saját DNS zónához fér hozzá.

> [!NOTE]
>Egyéni tartomány saját DNS zónát használó webalkalmazáshoz való hozzáadása nem lehetséges a VNET-integráció. Az egyéni tartomány érvényesítése a vezérlő szintjén, nem pedig a munkavégző szinten történik, ami megakadályozza a DNS-rekordok észlelését. Ha saját DNS zónából szeretne egyéni tartományt használni, az érvényesítést Application Gateway vagy ILB App Service Environment használatával kell kihagyni.



### <a name="private-endpoints"></a>Privát végpontok

Ha [privát végpontokra][privateendpoints]szeretne hívásokat kezdeményezni, akkor integrálnia kell Azure DNS Private Zones, vagy az alkalmazás által használt DNS-kiszolgálón kell kezelnie a privát végpontot. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
