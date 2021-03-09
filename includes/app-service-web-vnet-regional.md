---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: a4eb22320a15cc76a7543c25583003d57ea4e538
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473819"
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

Az alkalmazás alapértelmezés szerint csak a RFC1918-forgalmat irányítja át a VNet. Ha az összes kimenő forgalmat át szeretné irányítani a VNet, alkalmazza az alkalmazás beállítását WEBSITE_VNET_ROUTE_ALL az alkalmazásra. Az alkalmazás beállításának konfigurálása:

1. Nyissa meg a **konfigurációs** felhasználói felületet az alkalmazás-portálon. Válassza az **Új alkalmazás beállítása** lehetőséget.
1. Írja  be a WEBSITE_VNET_ROUTE_ALL **nevet a név** mezőbe, és írja be az **1** **értéket az érték** mezőbe.

   ![Alkalmazásbeállítás megadása][4]

1. Kattintson az **OK** gombra.
1. Kattintson a **Mentés** gombra.

> [!NOTE]
> Ha az összes kimenő forgalmat átirányítja a VNet, a rendszer az integrációs alhálózatra alkalmazott NSG és UDR vonatkozik. Ha az összes kimenő forgalmat átirányítja a VNet, a kimenő címek továbbra is az alkalmazás tulajdonságaiban felsorolt kimenő címek lesznek, kivéve, ha olyan útvonalakat ad meg, amelyek máshová nem küldik a forgalmat.
> 
> A regionális VNet-integráció nem tudja használni a 25-ös portot.

Bizonyos korlátozások vonatkoznak a VNet-integrációnak az azonos régióban található virtuális hálózatok való használatára:

* A globális társ-összekapcsolási kapcsolatok erőforrásai nem érhetők el.
* A szolgáltatás a Premium v2 és a Premium v3 összes App Service skálázási egységében érhető el. Standard szintű, de csak az újabb App Service skálázási egységek esetében érhető el. Ha régebbi méretezési egységet használ, a funkciót csak prémium v2 App Service csomaggal lehet használni. Ha azt szeretné, hogy a szolgáltatás a standard App Service csomagban is használható legyen, hozzon létre egy prémium szintű v3 App Service-csomagot. Ezek a csomagok csak a legújabb méretezési egységeken támogatottak. Ha ezt követően szeretné, lekicsinyítheti a méretezést.  
* Az integrációs alhálózatot csak egy App Service csomag használhatja.
* A funkciót nem lehet használni a App Service Environmentban található elkülönített csomagbeli alkalmazások.
* A szolgáltatáshoz egy Azure Resource Manager VNet egy/28 vagy nagyobb, nem használt alhálózatra van szükség.
* Az alkalmazásnak és a VNet ugyanabban a régióban kell lennie.
* A VNet nem törölhető integrált alkalmazással. A VNet törlése előtt távolítsa el az integrációt.
* App Service-csomag esetében csak egy regionális VNet-integráció lehet. Ugyanazon a App Service csomagon belül több alkalmazás is használhatja ugyanazt a VNet.
* Egy alkalmazás vagy csomag előfizetése nem módosítható, amíg van olyan alkalmazás, amely regionális VNet-integrációt használ.
* Az alkalmazás a konfiguráció módosítása nélkül nem tudja feloldani a Azure DNS Private Zones címeit

A VNet-integráció egy dedikált alhálózat használatával függ.  Amikor kiépít egy alhálózatot, az Azure-alhálózat elveszíti az 5 IP-címet az elejétől. Az egyes csomagokhoz tartozó integrációs alhálózaton egy-egy címnek kell lennie. Ha négy példányra méretezi az alkalmazást, akkor a rendszer négy címet használ. Az alhálózat méretétől számított 5 cím terhelés azt jelenti, hogy a maximálisan elérhető címek száma CIDR-blokk:

- /28 rendelkezik 11 címmel
- /27 27 címnek van
- /26 rendelkezik 59-címmel

Ha a méret fel-vagy leskálázását választja, a címnek rövid ideig kell megdupláznia. A méret mérete azt jelenti, hogy az alhálózati méretekben a valós rendelkezésre álló támogatott példányok száma az, ha az alhálózat a következő:

- /28, a maximális vízszintes skála 5 példány
- /27, a maximális vízszintes skála 13 példány
- /26, a maximális vízszintes skála 29 példány

A maximális horizontális skálán megjelenő korlátok feltételezik, hogy a méretekben vagy az SKU-ban legalább egy ponton fel kell skálázást. 

Mivel az alhálózat mérete nem módosítható a hozzárendelés után, olyan alhálózatot használjon, amely elég nagy ahhoz, hogy megfeleljen az alkalmazásnak. Az alhálózati kapacitással kapcsolatos problémák elkerülése érdekében a/26 64-as címmel az ajánlott méret.  

Ha azt szeretné, hogy egy másik csomagban lévő alkalmazásai olyan VNet érjenek el, amely már kapcsolódik egy másik csomagban lévő alkalmazásokhoz, válasszon egy másik alhálózatot, mint amelyet a meglévő VNet-integráció használ.

A szolgáltatás teljes mértékben támogatott Windows-és Linux-alkalmazásokhoz, beleértve az [Egyéni tárolókat](../articles/app-service/quickstart-custom-container.md)is. Az összes viselkedés ugyanaz, mint a Windows-alkalmazások és a Linux-alkalmazások között.

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A regionális VNet-integráció lehetővé teszi a szolgáltatási végpontok használatát. Ha szolgáltatási végpontokat szeretne használni az alkalmazással, a regionális VNet-integráció segítségével csatlakozhat egy kiválasztott VNet, majd a szolgáltatási végpontokat az integrációhoz használt alhálózaton a célként megadott szolgáltatással konfigurálhatja. Ha ezt követően egy szolgáltatáshoz szeretne hozzáférni a szolgáltatási végpontokon keresztül:

1. a regionális VNet-integráció konfigurálása a webalkalmazással
1. Lépjen a célhely szolgáltatáshoz, és konfigurálja a szolgáltatási végpontokat az integrációhoz használt alhálózattal.

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

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


Ezek a beállítások elküldik az alkalmazásból érkező összes kimenő hívást a VNet, továbbá lehetővé teszi, hogy az alkalmazás Azure DNS privát zónákat használjon.   Ezek a beállítások az alkalmazásból érkező összes kimenő hívást elküldik a VNet. Emellett lehetővé teszi, hogy az alkalmazás a Azure DNS használja a saját DNS zóna munkavégző szinten történő lekérdezésével. Ezt a funkciót akkor kell használni, ha egy futó alkalmazás egy saját DNS zónához fér hozzá.

> [!NOTE]
>Egyéni tartomány saját DNS zónát használó webalkalmazáshoz való hozzáadása nem lehetséges a VNET-integráció. Az egyéni tartomány érvényesítése a vezérlő szintjén, nem pedig a munkavégző szinten történik, ami megakadályozza a DNS-rekordok észlelését. Ha saját DNS zónából szeretne egyéni tartományt használni, az érvényesítést Application Gateway vagy ILB App Service Environment használatával kell kihagyni.

### <a name="private-endpoints"></a>Privát végpontok

Ha [privát végpontokra][privateendpoints]szeretne hívásokat kezdeményezni, győződjön meg arról, hogy a DNS-keresések fel lesznek oldva a privát végpontra. Annak biztosítása érdekében, hogy az alkalmazásból érkező DNS-keresések a privát végpontokra mutassanak, a következőket teheti:

* integráció a Azure DNS Private Zonessal. Ha a VNet nem rendelkezik egyéni DNS-kiszolgálóval, akkor ez automatikusan
* felügyelje az alkalmazás által használt DNS-kiszolgáló privát végpontját. Ehhez ismernie kell a privát végpontok címeit, majd azt a végpontot, amelyet egy rekord használatával szeretne elérni az adott címen.
* saját DNS-kiszolgáló konfigurálása a Azure DNS privát zónákhoz való továbbításhoz

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
