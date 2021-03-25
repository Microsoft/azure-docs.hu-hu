---
title: Azure-API Management használata az Azure Kubernetes Service-ben üzembe helyezett Service-szolgáltatásokkal | Microsoft Docs
description: Ez a cikk a API Management az AK-val való üzembe helyezésének lehetőségeit ismerteti
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 36dfc8c906c52c6822e583db3a08c891306f7e78
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047932"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Az Azure API Management használata az Azure Kubernetes Service-ben üzembe helyezett Service-szolgáltatásokkal

A szolgáltatás ideális az API-k létrehozásához. Az [Azure Kubernetes szolgáltatással](https://azure.microsoft.com/services/kubernetes-service/) (ak) gyorsan üzembe helyezheti és üzemeltetheti a felhőben a [Service-alapú architektúrát](/azure/architecture/guide/architecture-styles/microservices) . Ezután kihasználhatja az [Azure API Management](https://aka.ms/apimrocks) (API Management) szolgáltatást belső és külső felhasználásra szolgáló API-kként való közzétételhez. Ez a cikk a API Management az AK-val való üzembe helyezésének lehetőségeit ismerteti. A Kubernetes, a API Management és az Azure hálózatkezelésének alapszintű ismeretét feltételezi. 

## <a name="background"></a>Háttér

Ha a Service-t API-ként teszi közzé a használat során, akkor kihívást jelenthet a szolgáltatások és az azokat használó ügyfelek közötti kommunikáció kezelése. Számos különböző, többek között a hitelesítést, az engedélyezést, a szabályozást, a gyorsítótárazást, az átalakítást és a figyelést érintő probléma áll fenn. Ezek az érintettek attól függetlenül érvényesek, hogy a rendszer belső vagy külső ügyfelek számára teszi elérhetővé a szolgáltatást. 

Az [API-átjáró](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) mintája ezeket a problémákat tárgyalja. Az API-átjárók a szolgáltatásba bevezető ajtóként szolgálnak, leválasztják az ügyfeleket a szolgáltatásokból, felvesz egy további biztonsági réteget, és csökkenti a szolgáltatások összetettségét azáltal, hogy megszünteti a több területet érintő szempontok kezelésére vonatkozó terheket. 

Az [Azure API Management](https://aka.ms/apimrocks) egy kulcsrakész megoldás az API-átjárók igényeinek megoldásához. Gyorsan létrehozhat egy konzisztens és modern átjárót a szolgáltatásokhoz, és API-ként teheti közzé őket. A teljes életciklusú API Management megoldásként további képességeket is biztosít, beleértve az API-felderítéshez, az API-életciklus-felügyelethez és az API-elemzésekhez használható önkiszolgáló fejlesztői portált is.

Együttes használatakor az AK és a API Management platformot biztosít a Service-alapú API-k üzembe helyezéséhez, közzétételéhez, biztonságossá tételéhez, figyeléséhez és kezeléséhez. Ebben a cikkben áthaladunk néhány lehetőséget az ak-nak a API Managementsal együtt történő üzembe helyezésére. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes Services és API-k

Egy Kubernetes-fürtben a tárolók olyan [hüvelyekben](https://kubernetes.io/docs/concepts/workloads/pods/pod/)vannak üzembe helyezhetők, amelyek elmúlóak, és életciklussal rendelkeznek. A munkavégző csomópont elpusztulása esetén a csomóponton futó hüvely elveszik. Ezért a pod IP-címe bármikor megváltoztatható. Nem hivatkozhatunk arra, hogy kommunikáljon a pod-mel. 

A probléma megoldásához a Kubernetes bevezette a [szolgáltatások](https://kubernetes.io/docs/concepts/services-networking/service/)fogalmát. A Kubernetes szolgáltatás egy absztrakt réteg, amely a hüvelyek logikai csoportját határozza meg, és lehetővé teszi a külső adatforgalom, a terheléselosztás és a szolgáltatások felderítését a hüvelyek számára. 

Ha készen áll arra, hogy a API Management API-jai segítségével elérhetővé tegye a szolgáltatásait API-kkal, a API Management-ban található API-kra Kubernetes kell. Nincsenek beállított szabályok. Ez attól függ, hogy az üzleti képességek vagy tartományok hogyan lettek kialakítva és particionálva a szolgáltatás elején. Ha például egy szolgáltatás mögött található hüvelyek felelősek egy adott erőforráson (például az ügyfélen) lévő összes műveletért, akkor a szolgáltatás egy API-ra képezhető le. Ha egy erőforráson végrehajtott műveletek több szolgáltatásba vannak particionálva (pl. GetOrder, PlaceOrder), akkor több szolgáltatás is logikailag összesíthető egyetlen API-ban az API managementben (lásd az 1. ábrát). 

A leképezések is fejlődnek. Mivel a API Management létrehoz egy homlokzatot a-szolgáltatások előtt, lehetővé teszi, hogy az idő múlásával újraértékelje a szolgáltatásait és a megfelelő méretet. 

![Szolgáltatások leképezése API-kra](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>API Management üzembe helyezése az AK előtt

A API Management egy AK-fürt előtt történő üzembe helyezésének néhány lehetősége van. 

Amíg egy AK-fürtöt mindig telepítenek egy virtuális hálózaton (VNet), nem szükséges API Management példányt telepíteni a VNet. Ha API Management nem a fürt VNet belül található, az AK-fürtnek közzé kell tennie a API Management nyilvános végpontját, hogy csatlakozhasson a szolgáltatáshoz. Ebben az esetben szükség van a API Management és az AK közötti kapcsolat védelmére. Más szóval biztosítani kell, hogy a fürt kizárólag API Managementon keresztül legyen elérhető. Nézzük végig a beállításokat. 

### <a name="option-1-expose-services-publicly"></a>1. lehetőség: a szolgáltatások nyilvánosan elérhetővé tétele

Az AK-fürtökben lévő szolgáltatások nyilvánosan elérhetők a NodePort, a terheléselosztó vagy a ExternalName [szolgáltatás típusaival](../aks/concepts-network.md) . Ebben az esetben a szolgáltatások közvetlenül a nyilvános internetről érhetők el. A API Management a fürt előtt történő üzembe helyezése után biztosítani kell, hogy az összes bejövő forgalom a API Managementon haladjon át a szolgáltatáson keresztüli hitelesítés alkalmazásával. Például a API Management tartalmazhatnak egy hozzáférési jogkivonatot a fürtre irányuló minden kérelemben. Az egyes szolgáltatások feladata a jogkivonat ellenőrzése a kérelem feldolgozása előtt. 


Lehet, hogy ez a legegyszerűbb lehetőség a API Management az AK-ban való üzembe helyezésére, különösen akkor, ha már van megvalósítva hitelesítési logika a Service-szolgáltatásokban. 

![Szolgáltatások közvetlen közzététele](./media/api-management-aks/direct.png)

Szakemberek
* Egyszerű konfiguráció a API Management oldalon, mert nem kell befecskendezni a fürt VNet
* Nincs változás az AK oldalán, ha a szolgáltatások már nyilvánosan elérhetők, és a hitelesítési logika már létezik a Service-ben.

Hátránya
* Lehetséges biztonsági kockázat a szolgáltatási végpontok nyilvános láthatósága miatt
* Nincs egyszeri belépési pont a bejövő fürt forgalmához
* Ismétlődő hitelesítési logikával bonyolítja a szolgáltatásait

### <a name="option-2-install-an-ingress-controller"></a>2. lehetőség: bejövő forgalomú vezérlő telepítése

Bár az 1. lehetőség egyszerűbb lehet, a fentiekben említett jelentős hátrányok vannak. Ha egy API Management-példány nem a fürt VNet található, a kölcsönös TLS-hitelesítés (mTLS) robusztus módja annak, hogy a forgalom biztonságos és megbízható legyen a API Management-példány és az AK-fürt közötti mindkét irányban. 

A kölcsönös TLS-hitelesítést a API Management [natív módon támogatja](./api-management-howto-mutual-certificates.md) , és a Kubernetes-ben engedélyezhető a bejövő vezérlő (3. ábra) [telepítésével](../aks/ingress-own-tls.md) . Ennek eredményeképpen a hitelesítés a beáramló vezérlőben történik, ami leegyszerűsíti a szolgáltatásait. Emellett a API Management IP-címeit is hozzáadhatja az engedélyezett listához a bejövő forgalom lehetőséggel, így biztosíthatja, hogy csak API Management férhessen hozzá a fürthöz.  

 
![Közzététel bejövő vezérlőn keresztül](./media/api-management-aks/ingress-controller.png)


Szakemberek
* Egyszerű konfiguráció a API Management oldalon, mert nem kell befecskendezni a fürt VNet, és a mTLS natív módon támogatott
* Központosítja a bejövő fürt forgalmának védelmét a beáramló vezérlő rétegében
* Csökkenti a biztonsági kockázatokat a nyilvánosan látható fürt végpontok minimalizálásával

Hátránya
* A fürt konfigurációjának összetettségét növeli a bejövő vezérlő telepítése, konfigurálása és karbantartása, valamint a mTLS használt tanúsítványok kezelése miatt.
* Biztonsági kockázat a bejövő hozzáférés-vezérlési végpont (ok) nyilvános láthatósága miatt


Ha API Managementon keresztül tesz közzé API-kat, egyszerűen és közösen biztosíthatja az API-k elérését az előfizetési kulcsok használatával. A közzétett API-kat használó fejlesztőknek érvényes előfizetési kulcsot kell tartalmazniuk a HTTP-kérelmekben, amikor hívásokat kezdeményeznek az API-khoz. Ellenkező esetben a rendszer azonnal visszautasítja a hívásokat a API Management átjáró. Nincsenek továbbítva a háttér-szolgáltatásoknak.

Az API-k eléréséhez szükséges előfizetési kulcs beszerzéséhez előfizetésre van szükség. Az előfizetések lényegében egy megnevezett tárolók egy pár előfizetési kulcshoz. A közzétett API-kat használó fejlesztők előfizetéseket kaphatnak. És nincs szükségük az API-közzétevők jóváhagyására. Az API-közzétevők közvetlenül az API-felhasználók számára is létrehozhatnak előfizetéseket.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>3. lehetőség: a APIM üzembe helyezése a fürt VNet belül

Bizonyos esetekben a jogszabályi korlátozásokkal vagy szigorú biztonsági követelményekkel rendelkező ügyfelek az 1. és a 2. lehetőséget a nyilvánosan elérhető végpontok miatt nem életképes megoldásoknak tekinthetik meg. Másokban előfordulhat, hogy az AK-fürt és a-szolgáltatásokat használó alkalmazások ugyanabban a VNet belül vannak, ezért nincs ok arra, hogy nyilvánosan elérhetővé tegye a fürtöt, mivel az összes API-forgalom a VNet belül marad. Ezekben a forgatókönyvekben a API Management üzembe helyezését a fürt VNet végezheti el. [API Management fejlesztői és prémium szintű](https://aka.ms/apimpricing) csomagok támogatják a VNet-telepítést. 

A [API Management kétféleképpen helyezhetők üzembe VNet](./api-management-using-with-vnet.md) – külső és belső. 

Ha az API-felhasználók nem a fürt VNet találhatók, akkor a külső üzemmódot (4. ábra) kell használni. Ebben a módban a API Management átjáró bekerül a fürt VNet, de a nyilvános internetről elérhető külső terheléselosztó használatával. Segít a fürt teljes elrejtésében, miközben továbbra is lehetővé teszi, hogy a külső ügyfelek használják a szolgáltatásait. Emellett az Azure hálózati funkcióit, például a hálózati biztonsági csoportokat (NSG) is használhatja a hálózati forgalom korlátozására.

![Külső VNet mód](./media/api-management-aks/vnet-external.png)

Ha az összes API-felhasználó a fürt VNet belül található, akkor a belső mód (5. ábra) használható. Ebben a módban a API Management átjáró bekerül a fürt VNET, és a belső terheléselosztó használatával csak ezen a VNet belülről érhető el. A API Management átjáró vagy az AK-fürt nem érhető el a nyilvános internetről. 

![Belső VNet mód](./media/api-management-aks/vnet-internal.png)

 Az AK-fürt mindkét esetben nyilvánosan nem látható. A 2. lehetőséghez képest előfordulhat, hogy a bejövő adatkezelő nem szükséges. A forgatókönyvtől és a konfigurációtól függően előfordulhat, hogy a hitelesítés API Management és a szolgáltatások között továbbra is szükséges. Ha például egy szolgáltatás hálóját fogadja el, az mindig kölcsönös TLS-hitelesítést igényel. 

Szakemberek
* A legbiztonságosabb lehetőség, mert az AK-fürt nem rendelkezik nyilvános végponttal
* Leegyszerűsíti a fürtkonfiguráció konfigurálását, mivel nem rendelkezik nyilvános végponttal
* A VNet belüli API Management és ak-nak a belső mód használatával való elrejtésének képessége
* A hálózati forgalom szabályozása az Azure hálózati képességei, például a hálózati biztonsági csoportok (NSG-EK) használatával

Hátránya
* A VNet-ben való munkavégzéshez szükséges API Management üzembe helyezésének és konfigurálásának bonyolultsága

## <a name="next-steps"></a>Következő lépések

* További információ a [hálózati fogalmakról az AK-beli alkalmazásokhoz](../aks/concepts-network.md)
* További információ a [API Management virtuális hálózatokkal való használatáról](./api-management-using-with-vnet.md)
