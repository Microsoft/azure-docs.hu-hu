---
title: Kényszerített bújtatás konfigurálása
description: Megtudhatja, hogyan engedélyezheti a App Service Environment működését, ha a kimenő forgalom kényszerített bújtatást végez a virtuális hálózaton.
author: ccompy
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 95a4d00a27a0da363561f469b4c5e9e2ad16463c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97510498"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Az App Service-környezet konfigurálása kényszerített bújtatással

Az App Service-környezet (ASE) az Azure App Service üzemelő példánya az ügyfelek Azure Virtual Network-hálózatában. Számos ügyfél konfigurálja az Azure-beli virtuális hálózatait VPN-ek vagy Azure ExpressRoute-kapcsolatok segítségével úgy, hogy azok a helyszíni hálózatok kiterjesztéseként működjenek. A kényszerített bújtatás átirányítja az internetre irányuló forgalmat a VPN-re vagy egy virtuális berendezésre. A virtuális készülékeket gyakran a kimenő hálózati forgalom vizsgálatára és naplózására használják. 

Az ASE több külső függőséggel rendelkezik, amelyek leírását megtalálhatja az [App Service-környezet hálózati architektúráját][network] ismertető dokumentumban. Általában az ASE minden kimenő függőségének az ASE virtuális IP-címén kell keresztülmennie. Ha az alábbi információk felhasználása nélkül módosítja a forgalom útválasztását az ASE-re vagy az ASE-ről, az ASE működése leáll.

Egy Azure-beli virtuális hálózatban az útválasztás a leghosszabb előtag-megfeleltetés (LPM) alapján történik. Ha egynél több útvonal rendelkezik ugyanazzal az LPM megfeleltetéssel, akkor a rendszer az útvonalat a kiindulás alapján választja ki, az alábbi sorrendben:

* Felhasználó által meghatározott útvonal (UDR)
* BGP-útvonal (ExpressRoute használatánál)
* Rendszerútvonal

Ha többet szeretne megtudni a virtuális hálózatokban történő útválasztásról, olvassa el a [felhasználó által megadott útvonalakat és IP-továbbítást][routes] ismertető cikket. 

Ha nem közvetlenül az internetre szeretné átirányítani a kimenő ASE-forgalmat, az alábbi lehetőségei vannak:

* Közvetlen internetelérés engedélyezése az ASE számára
* ASE-alhálózat konfigurálása a BGP-útvonalak figyelmen kívül hagyására
* Az ASE alhálózat konfigurálása szolgáltatásvégpontok használatára az Azure SQL-hez és az Azure Storage szolgáltatáshoz
* Saját IP-címek hozzáadása az ASE Azure SQL-tűzfalhoz

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Közvetlen internetelérés engedélyezése az App Service-környezet számára

Ha engedélyezni szeretné, hogy az ASE közvetlenül az internethez csatlakozzon akkor is, ha az Azure-beli virtuális hálózat ExpressRoute-tal van konfigurálva, a következőket teheti:

* Konfigurálja az ExpressRoute-ot a 0.0.0.0/0 tartomány meghirdetésére. Ez alapértelmezés szerint átirányít minden kimenő forgalmat a helyszíni hálózatba.
* Hozzon létre egy UDR-t a 0.0.0.0/0 címelőtaggal és következő ugrási típusú internettel, és alkalmazza azt az ASE-alhálózatra.

Ha végrehajtja ezt a két módosítást, az App Service-környezet alhálózatából származó, internetre küldött forgalom nem lesz rákényszerítve az ExpressRoute-kapcsolatra.

Ha a hálózat már irányít forgalmat a helyszínen, létre kell hozni egy alhálózatot az ASE üzemeltetéséhez és az UDR konfigurálásához az ASE számára, mielőtt megkísérelni üzembe helyezni az ASE-t.  

> [!IMPORTANT]
> Az UDR-ben meghatározott útvonalaknak annyira pontosnak kell lenniük, hogy prioritást kapjanak az ExpressRoute-konfiguráció által meghirdetett bármely útvonallal szemben. Az előző példa a széles 0.0.0.0/0 címtartományt használja. Ezt véletlenül felülírhatják olyan útvonalhirdetések, amelyek pontosabb címtartományokat használnak.
>
> Az App Service-környezetek nem támogatottak olyan ExpressRoute-konfigurációk esetén, amelyek keresztbe hirdetnek útvonalakat a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonalra. A konfigurált nyilvános társviszonyt létesítő ExpressRoute-konfigurációk útvonalhirdetéseket kapnak a Microsofttól. A meghirdetések Microsoft Azure-címtartományok nagy készletét tartalmazzák. Ha a címtartományokat keresztbe hirdetik a privát társviszony-létesítési útvonalon, az összes, az App Service-környezet alhálózatából származó kimenő hálózati csomag átirányítással kerül az ügyfél helyszíni hálózati infrastruktúrájába. Az App Service-környezetek alapértelmezés szerint nem támogatják ezt a hálózati kialakítást. Egy megoldás erre a problémára az, ha leállítja az útvonalak keresztbe hirdetését a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonalra. Egy másik megoldás az, ha lehetővé teszi az App Service-környezet működését kényszerített bújtatásos konfigurációban.

![Közvetlen internet-hozzáférés][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>ASE-alhálózat konfigurálása a BGP-útvonalak figyelmen kívül hagyására ## 

Az ASE-alhálózatot konfigurálhatja a BGP-útvonalak figyelmen kívül hagyására.  Ha a BGP-útvonalak figyelmen kívül hagyásához vannak konfigurálva, a kisegítő lehetőség problémák nélkül hozzáférhet a függőségeihez.  Ahhoz azonban, hogy az alkalmazásai hozzá tudjanak férni a helyszíni erőforrásokhoz, UDR-eket is létre kell hoznia.

ASE-alhálózat konfigurálása a BGP-útvonalak figyelmen kívül hagyására:

* Hozzon létre egy UDR-t, és rendelje hozzá az ASE-alhálózathoz, ha ez még nem történt meg.
* Az Azure Portalon nyissa meg az ASE-alhálózathoz rendelt útválasztási táblázat felhasználói felületét.  Válassza a Konfiguráció lehetőséget.  A virtuális hálózati átjáró útvonal-propagálásának beállítása letiltva értékre.  Kattintson a Mentés gombra. A beállítás kikapcsolására vonatkozó információkat az [útválasztási táblázat létrehozásával][routetable] foglalkozó témakörben találja meg.

Miután konfigurálta a bevezető alhálózatot az összes BGP-útvonal figyelmen kívül hagyásához, az alkalmazások többé nem lesznek elérhetők a helyszínen. Annak engedélyezéséhez, hogy az alkalmazások hozzáférjenek a helyszíni erőforrásokhoz, szerkessze a UDR rendelt alhálózatot, és adjon hozzá útvonalakat a helyszíni címtartományok számára. A Következő ugrási típus értéke legyen Virtuális hálózati átjáró. 


## <a name="configure-your-ase-with-service-endpoints"></a>Az ASE konfigurálása szolgáltatásvégpontokkal ##

Ha az Azure SQL és az Azure Storage felé irányuló forgalmon kívül az ASE összes kimenő forgalmát át szeretné irányítani, végezze el a következő lépéseket:

1. Hozzon létre egy útválasztási táblázatot, és rendelje hozzá az ASE-alhálózathoz. Keresse meg a régiójának megfelelő címeket az [App Service-környezet kezelési címeit][management] ismertető szakaszban. Hozzon létre útvonalakat ezekhez a címekhez egy következő ugrási típusú internettel. Ezekre az útvonalakra azért van szükség, mert az App Service Environment bejövő felügyeleti forgalmának ugyanarra a címre kell válaszolnia, amelyet elküldtek.   

2. Engedélyezze a szolgáltatásvégpontokat az Azure SQL-hez és az Azure Storage-hoz az ASE-alhálózattal.  Miután ezt a lépést elvégezte, konfigurálhatja virtuális hálózatát kényszerített bújtatással.

A bevezetési sablon használatával történő telepítésével kapcsolatos részletekért olvassa el a [app Service Environment sablon][template]alapján történő létrehozását ismertető cikkből.

A szolgáltatásvégpontokkal Azure-beli virtuális hálózatok és alhálózatok készletére korlátozhatja a több-bérlős szolgáltatásokhoz való hozzáférést. A szolgáltatásvégpontokról bővebben a [virtuális hálózatok szolgáltatásvégpontjaival][serviceendpoints] kapcsolatos dokumentációban olvashat. 

Amikor engedélyezi a szolgáltatásvégpontokat egy erőforráson, az összes többi útvonalhoz képest nagyobb prioritású útvonalak jönnek létre. Ha kényszerítetten bújtatott ASE-vel használ szolgáltatásvégpontokat, az Azure SQL és az Azure Storage felügyeleti forgalma nem kényszerítetten bújtatott. Az ASE többi függőségi forgalma kényszerítetten bújtatott, és nem veszhet el, különben az ASE nem működik megfelelően.

Amikor a szolgáltatásvégpontok engedélyezettek egy Azure SQL-példánnyal rendelkező alhálózaton, akkor az erről az alhálózatról elért összes Azure SQL-példányhoz engedélyezve kell lennie a szolgáltatásvégpontoknak. Ha több Azure SQL-példányt szeretne elérni ugyanarról az alhálózatról, nem engedélyezheti a szolgáltatásvégpontokat csak az egyik Azure SQL-példányon, egy másikon pedig nem.  Az Azure Storage nem úgy viselkedik, mint az Azure SQL.  Amikor az Azure Storage szolgáltatáshoz engedélyezi a szolgáltatásvégpontokat, azzal zárolja az erőforráshoz való hozzáférést az alhálózatról, de továbbra is elérhet más Azure Storage-fiókokat, még akkor is, ha azokon nincsenek engedélyezve a szolgáltatásvégpontok.  

Ha kényszerített bújtatást konfigurál egy hálózatszűrő berendezéssel, akkor ne feledje, hogy az ASE az Azure SQL és az Azure Storage mellett más függőségekkel is rendelkezik. Ha a forgalom le van tiltva a függőségek számára, a bevezetés nem fog megfelelően működni.

![Kényszerített bújtatás szolgáltatásvégpontokkal][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>Saját IP-címek hozzáadása az ASE Azure SQL-tűzfalhoz ##

Ha az Azure Storage felé irányuló forgalmon kívül bújtatni szeretné az ASE összes kimenő forgalmát, végezze el a következő lépéseket:

1. Hozzon létre egy útválasztási táblázatot, és rendelje hozzá az ASE-alhálózathoz. Keresse meg a régiójának megfelelő címeket az [App Service-környezet kezelési címeit][management] ismertető szakaszban. Hozzon létre útvonalakat ezekhez a címekhez egy következő ugrási típusú internettel. Ezekre az útvonalakra azért van szükség, mert az App Service Environment bejövő felügyeleti forgalmának ugyanarra a címre kell válaszolnia, amelyet elküldtek. 

2. Szolgáltatásvégpontok engedélyezése az Azure Storage szolgáltatáshoz az ASE alhálózattal

3. Szerezze be az App Service-környezetből az internetre irányuló összes kimenő forgalomhoz használt címeket. Ha a helyszínen irányítja át a forgalmat, ezek a címek a NAT-ok és az átjárók IP-címei. Ha az App Service-környezet kimenő forgalmát egy hálózati virtuális berendezésen keresztül szeretné vezetni, a kimenő forgalom címe a hálózati virtuális berendezés nyilvános IP-címe lesz.

4. _A kimenő címek címeinek beállítása meglévő app Service Environmentban:_ Nyissa meg a resources.azure.com, és lépjen az előfizetés/ \<subscription id> /ResourceGroups/ \<ase resource group> /providers/Microsoft.Web/hostingEnvironments/ \<ase name> . Itt láthatja az App Service-környezetet leíró JSON-t. Győződjön meg arról, hogy a lap tetején ott az **olvasás/írás** felirat. Válassza a **Szerkesztés** elemet. Görgessen le a lap aljáig. Módosítsa a **userWhitelistedIpRanges** beállítást **null** értékről az alábbihoz hasonlóra. Használja azokat a címeket, amelyeket a kimenő forgalom címtartományának kíván beállítani. 

    ```json
    "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"]
    ```

   A lap tetején kattintson a **PUT** elemre. Ez a lehetőség aktivál egy méretezési műveletet az App Service-környezetben, és beállítja a tűzfalat.

_Az ASE létrehozása a kimenő forgalmi címekkel_: Kövesse az [App Service-környezet sablonnal történő létrehozását][template] ismertető cikk utasításait, és kérje le a megfelelő sablont.  Szerkessze az azuredeploy.json fájl „resources” (erőforrások) szakaszát, de a „properties” (tulajdonságok) blokkot ne, és a **userWhitelistedIpRanges** értékhez adjon hozzá egy sort a saját értékeivel.

```json
"resources": [
    {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
            "name": "[parameters('aseName')]",
            "location": "[parameters('aseLocation')]",
            "ipSslAddressCount": 0,
            "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
            "dnsSuffix" : "[parameters('dnsSuffix')]",
            "virtualNetwork": {
                "Id": "[parameters('existingVnetResourceId')]",
                "Subnet": "[parameters('subnetName')]"
            },
            "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
    }
]
```

Ezek a változások közvetlenül az Azure Storage felé küldik a forgalmat az ASE-ből, továbbá az ASE virtuális IP-címétől eltérő további címekről is engedélyezik az Azure SQL elérését.

   ![Kényszerített bújtatás SQL-engedélyezési][3]

## <a name="preventing-issues"></a>Hibák megelőzése ##

Ha megszakad a kommunikáció az ASE és annak függőségeit között, az ASE sérült állapotba kerül.  Ha túl sokáig marad sérült, akkor az ASE fel lesz függesztve. Az ASE felfüggesztésének megszüntetéséhez kövesse az ASE portál utasításait.

A kommunikáció egyszerű megszakítása mellett a túl sok késés bevezetésével kedvezőtlen hatással lehet az ASE-re. Túl sok késés léphet fel, ha az ASE túl messze van a helyszíni hálózattól.  Túl messze van például, ha át kell szelni egy óceánt vagy egy kontinenst a helyszíni hálózat eléréséhez. Késés az intranetes torlódás vagy a kimenő sávszélesség korlátozásai miatt is előfordulhat.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
