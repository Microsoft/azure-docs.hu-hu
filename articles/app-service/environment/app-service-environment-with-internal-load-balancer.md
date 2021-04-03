---
title: ILB-beli bemutató v1 létrehozása
description: ILB-vel rendelkező bekészítés létrehozása és használata. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c4e5dedf2075a2e13cc91c5eed2c0f03ba498b97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021520"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Belső Load Balancer használata App Service Environment

> [!NOTE] 
> Ez a cikk a App Service Environment v1-es verzióról szól. A App Service Environment újabb verziója könnyebben használható, és nagyobb teljesítményű infrastruktúrán fut. Ha többet szeretne megtudni az új verzióról, kezdje a [app Service Environment bevezetésével](intro.md).
>

A App Service Environment (bevezetési) funkció egy olyan prémium szintű Azure App Service, amely olyan továbbfejlesztett konfigurációs képességet biztosít, amely nem érhető el a több-bérlős bélyegzők szolgáltatásban. A bevezetési funkció lényegében üzembe helyezi a Azure App Servicet az Azure-Virtual Networkban (VNet). A App Service környezetek által kínált képességek jobb megismeréséhez olvassa el a [Mi az a app Service Environment][WhatisASE] dokumentációt. Ha nem tudja, milyen előnyökkel jár a VNet való működés, olvassa el az [Azure Virtual Network – gyakori kérdések][virtualnetwork]című részt. 

## <a name="overview"></a>Áttekintés
A bevezetést egy internetről elérhető végponttal vagy egy IP-címmel lehet üzembe helyezni a VNet. Ahhoz, hogy az IP-címet egy VNet-címhez szeretné beállítani, belső Load Balancer (ILB) kell központilag telepítenie a bevezetést. Ha a kisegítő ILB konfigurálva van, az alábbiakat biztosítja:

* saját tartománya vagy altartománya. Az egyszerűvé tétel érdekében ez a dokumentum feltételezi az altartományt, de mindkét irányban konfigurálható. 
* a HTTPS-hez használt tanúsítvány
* DNS-kezelés az altartományhoz. 

Cserébe többek között az alábbiakat teheti meg:

* intranetes alkalmazások, például üzletági alkalmazások üzemeltetése biztonságosan a felhőben, amely egy helyen vagy ExpressRoute VPN-en keresztül érhető el
* a nyilvános DNS-kiszolgálókon nem szereplő alkalmazások a felhőben
* internetes elkülönített háttérbeli alkalmazások létrehozása, amelyekkel az előtér-alkalmazások biztonságosan integrálhatók

#### <a name="disabled-functionality"></a>Letiltott funkciók
Bizonyos dolgok nem hajthatók végre, ha ILB-beadást használ. Ezek a következők:

* a IPSSL használata
* IP-címek kiosztása adott alkalmazásokhoz
* tanúsítvány vásárlása és használata egy alkalmazással a portálon keresztül. Természetesen a tanúsítványokat továbbra is közvetlenül a hitelesítésszolgáltatóval szerezheti be, és használhatja az alkalmazásokkal, de nem a Azure Portalon keresztül.

## <a name="creating-an-ilb-ase"></a>ILB-bekészítés létrehozása
Egy ILB-bekészítés létrehozása nem sokban különbözik a hagyományos központú adatforrások létrehozásával. A kiegészítő információk létrehozásával kapcsolatos részletes információkért lásd: [app Service Environment létrehozása][HowtoCreateASE]. A ILB beadásának folyamata megegyezik a VNet létrehozása és a már meglévő VNet kiválasztása között. Az ILB ASE létrehozása: 

1. A Azure Portal válassza az **erőforrás létrehozása-> web és mobil – > app Service Environment** lehetőséget.
2. Válassza ki előfizetését.
3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
4. Válasszon ki vagy hozzon létre egy virtuális hálózatot.
5. Hozzon létre egy alhálózatot, ha kiválaszt egy VNet.
6. Válassza ki **Virtual Network/Location-> VNet konfigurációját** , és állítsa a VIP-típust belső értékre.
7. Adja meg az altartomány nevét (ez a jelen útmutatóban létrehozott alkalmazásokhoz használt altartomány).
8. Válassza **az OK** , majd a **Létrehozás** lehetőséget.

![Megjeleníti a ILB-előkészítés létrehozásához használt képernyőket.][1]

A Virtual Network ablaktáblán található egy VNet konfigurációs beállítás, amely lehetővé teszi a külső VIP-vagy belső VIP-címek közötti választást. Az alapértelmezett érték a Külső. Ha a külső értékre van állítva, a beadási szolgáltatás egy internetről elérhető VIP-t használ. Ha a Belső lehetőséget választja, az ASE ILB-vel vagy virtuális hálózati IP-címmel lesz konfigurálva. 

A belső lehetőség kiválasztása után a rendszer eltávolítja a kiegészítő IP-címeket, és ehelyett a beadási altartományt kell megadnia. Külső virtuális IP-cím használatával a benyújtó által létrehozott alkalmazások altartományában a kiegészítő csomag neve szerepel. Ha a központot a (z) ***contosotest** _ névvel ellátott, és a kiegészítőben lévő alkalmazás neve _*_mytest_*_, az altartomány formátuma _*_contosotest.p.azurewebsites.net_*_ , az alkalmazás URL-címe pedig _* _mytest.contosotest.p.azurewebsites.net_* *. Ha a VIP-típust belső értékre állítja, a beadási név nem szerepel a központhoz tartozó altartományban. Explicit módon megadja az altartományt. Ha az altartomány ***contoso.Corp.net**_ , és a _*_timereporting_*_ nevű szakterületen hozta létre az alkalmazást, akkor az alkalmazás URL-címe a következő: _ *_timereporting.contoso.Corp.net_* *.

## <a name="apps-in-an-ilb-ase"></a>Alkalmazások egy ILB-ben
Az alkalmazások ILB-ben történő létrehozása megegyeznek az alkalmazások szokásos módon történő létrehozásával. 

1. A Azure Portal válassza az **erőforrás létrehozása-> web és mobil-> webes** vagy **mobil** vagy API- **alkalmazás** elemet.
2. Adja meg az alkalmazás nevét.
3. Válassza ki előfizetését.
4. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
5. Válasszon ki vagy hozzon létre egy App Service tervet (ASP). Ha új ASP hoz létre, válassza ki a beadási helyet, és válassza ki azt a munkavégző készletet, amelyben létre szeretné hozni az ASP-t. Az ASP létrehozásakor kiválaszthatja a beadási helyet és a munkavégző készletet. Az alkalmazás nevének megadásakor látni fogja, hogy az alkalmazás neve alá tartozó altartomány helyébe a szolgáltató altartománya kerül. 
6. Válassza a **Létrehozás** lehetőséget. Jelölje be a rögzítés az **irányítópulton** jelölőnégyzetet, ha azt szeretné, hogy az alkalmazás megjelenjen az irányítópulton. 

![Bemutatja, hogyan hozhat létre alkalmazást egy ILB-előAzure Portalban.][2]

Az alkalmazás neve alatt a aldomain neve frissül, hogy tükrözze a központjának altartományát. 

## <a name="post-ilb-ase-creation-validation"></a>ILB-létrehozási ellenőrzés utáni érvényesítés
Az ILB ASE kissé különbözik az ILB nélküli ASE környezettől. Ahogy már említettük, a saját DNS-t kell kezelnie, és a HTTPS-kapcsolatokhoz is meg kell adnia a saját tanúsítványát. 

Miután létrehozta a központot, megfigyelheti, hogy az altartomány megjeleníti a megadott altartományt, és van egy új elem a **ILB-tanúsítvány** nevű **beállítási** menüben. A kiegészítő szolgáltatás önaláírt tanúsítvánnyal jön létre, amely megkönnyíti a HTTPS tesztelését. A portálon megtudhatja, hogy meg kell adnia a saját tanúsítványát a HTTPS-hez, de ez azt javasolja, hogy rendelkezzen egy olyan tanúsítvánnyal, amely a saját altartománnyal rendelkezik. 

![Megjeleníti a beadási pont létrehozásakor megadott altartományt.][3]

Ha egyszerűen próbálkozik a dolgokkal, és nem tudja, hogyan hozhat létre egy tanúsítványt, az IIS MMC konzol alkalmazásával hozhat létre önaláírt tanúsítványt. A létrehozása után exportálhatja. pfx-fájlként, majd feltöltheti azt a ILB-tanúsítvány felhasználói felületén. Ha egy önaláírt tanúsítvánnyal védett helyet ad meg, a böngésző figyelmeztetést küld arról, hogy az elérni kívánt hely nem biztonságos, mert nem tudja érvényesíteni a tanúsítványt. Ha el szeretné kerülni ezt a figyelmeztetést, szüksége lesz egy megfelelően aláírt tanúsítványra, amely megfelel az altartománynak, és a böngésző által felismert megbízhatósági lánctal rendelkezik.

![Bemutatja, hogyan hozhat létre önaláírt tanúsítványt az IIS MMC konzol alkalmazásával.][6]

Ha saját tanúsítványokkal szeretné kipróbálni a folyamatot, és a HTTP-és HTTPS-hozzáférést is tesztelni kívánja a saját előállítók számára:

1. Ugrás a beadási felhasználói felületre, miután a beadási szolgáltatás létrejött a **be>i beállítások-> ILB tanúsítványokat**.
2. Állítsa be a ILB tanúsítványát a tanúsítvány PFX-fájljának kiválasztásával és a jelszó megadásával. Ez a lépés eltarthat egy kis ideig a folyamat során, és megjelenik egy, a skálázási művelet által folyamatban lévő üzenet.
3. Szerezze be a ILB-címet a központhoz (**be>i tulajdonságok-> virtuális IP-cím**).
4. Hozzon létre egy webes alkalmazást a létrehozás után. 
5. Hozzon létre egy virtuális gépet, ha még nem rendelkezik ilyennel a VNET (nem ugyanabban az alhálózatban, ahol a központot vagy a betörést).
6. Állítsa be a DNS-t az altartományhoz. Használhat helyettesítő karaktert a DNS-beli altartományhoz, vagy ha néhány egyszerű tesztet szeretne végezni, szerkessze a virtuális gépen található gazdagépek fájlt a webalkalmazás nevének VIP IP-címként való beállításához. Ha a szolgáltató altartománynévvel rendelkezik. ilbase.com, és elvégezte a webalkalmazás-mytestapp, hogy az a mytestapp.ilbase.com-ben legyen felhasználva, állítsa be, hogy az a Hosts fájlban legyen. (Windows rendszeren a gazdagépek fájlja a következő címen található: C:\Windows\System32\drivers\etc\)
7. Használjon böngészőt a virtuális gépen, és nyissa meg a `https://mytestapp.ilbase.com` (z) (vagy bármilyen webalkalmazás nevét a saját altartománnyal együtt).
8. A virtuális gép böngészőjével keresse fel a következő oldalt: `https://mytestapp.ilbase.com`. Önaláírt tanúsítvány használata esetén el kell fogadnia a biztonság hiányát. 

A ILB IP-címe a tulajdonságok között a virtuális IP-cím mezőben jelenik meg.

![Azt mutatja, hogy a ILB tartozó IP-cím a tulajdonságok között a virtuális IP-cím mezőben szerepel.][4]

## <a name="using-an-ilb-ase"></a>ILB-kiegészítő szolgáltatás használata
#### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
Az ILB-beadási szolgáltatás lehetővé teszi az alkalmazások hálózati elkülönítését. Az alkalmazások nem érhetők el, vagy az Internet sem ismeri. Ez a megközelítés kiválóan használható az intranetes webhelyek, például az üzletági alkalmazások üzemeltetéséhez. Ha továbbra is korlátozni szeretné a hozzáférést, továbbra is használhat hálózati biztonsági csoportokat (NSG) a hozzáférés vezérléséhez a hálózati szinten. 

Ha a NSG-t szeretné a hozzáférés további korlátozására használni, meg kell győződnie arról, hogy nem kell megszüntetnie a szolgáltató működéséhez szükséges kommunikációt. Annak ellenére, hogy a HTTP/HTTPS-hozzáférés csak a szolgáltató által használt ILB keresztül történik, a szolgáltató továbbra is a VNet kívüli erőforrásokból függ. Ha szeretné megtekinteni, hogy milyen hálózati hozzáférésre van szükség, tekintse meg a [Bejövő forgalom vezérlése egy app Service Environment][ControlInbound] és  [hálózati konfiguráció részleteit app Service környezetek ExpressRoute][ExpressRoute]. 

A NSG konfigurálásához ismernie kell az Azure által a szolgáltatói felügyelethez használt IP-címet. Ez az IP-cím is a kifelé irányuló kimenő IP-cím, ha internetes kérelmeket tesz elérhetővé. A beadáshoz tartozó kimenő IP-cím statikus marad a szolgáltatói életében. Ha törli és újból létrehozza a beadást, akkor egy új IP-címet fog kapni. Az IP-cím megkereséséhez lépjen a **Beállítások-> tulajdonságok** elemre, és keresse meg a **kimenő IP-címet**. 

![Itt látható, hogy hol található a bemenő IP-cím.][5]

#### <a name="general-ilb-ase-management"></a>Általános ILB-felügyelet
Egy ILB-bevezetési szolgáltatás kezelése nagyjából azonos a közüzemi közüzemek kezelésével. A munkavégző készletek több ASP-példány üzemeltetéséhez és az előtér-kiszolgálók vertikális felskálázásához nagyobb mennyiségű HTTP/HTTPS-forgalom kezelésére van szükség. A bevezetési adatok konfigurálásával kapcsolatos általános információkért lásd: [app Service Environment konfigurálása][ASEConfig]. 

A további felügyeleti elemek a Tanúsítványkezelő és a DNS-kezelés. Be kell szereznie és fel kell töltenie a HTTPS-hez használt tanúsítványt, miután az ILB-t létrehozta, és lecseréli az érvényesség lejárta előtt. Mivel az Azure tulajdonosa az alaptartomány, az informatikai részleg tanúsítványokat biztosít a külső VIP-ASE. Mivel a ILB-előállítók által használt altartomány bármi lehet, meg kell adnia a saját tanúsítványát a HTTPS-hez. 

#### <a name="dns-configuration"></a>DNS-konfiguráció
Külső VIP használata esetén a DNS-t az Azure felügyeli. Az ASE környezetben létrehozott összes alkalmazás automatikusan hozzáadódik Azure DNS-hez, amely egy nyilvános DNS. Az ILB ASE környezetben Önnek kell kezelnie a saját DNS-ét. Egy adott altartományhoz, például a contoso.corp.net-hoz létre kell hoznia egy DNS-rekordot, amely a ILB-címere mutat:

- \*
- *. SCM
- ftp
- publish

## <a name="getting-started"></a>Első lépések
App Service környezetek használatának megkezdéséhez tekintse [meg a app Service környezetek bemutatása][WhatisASE] című témakört.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md