---
title: Azure Private Link – gyakori kérdések (GYIK)
description: További információ az Azure Private linkről.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: d06e90a691389b99d8f439364203b921f49b2305
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496473"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure Private Link – gyakori kérdések (GYIK)

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Mi az Azure Private Endpoint és az Azure Private link Service?

- **[Azure Private-végpont](private-endpoint-overview.md)**: az Azure Private Endpoint egy olyan hálózati adapter, amely az Azure Private-kapcsolaton keresztüli szolgáltatáshoz kapcsolódóan és biztonságosan csatlakozik az Ön számára. A privát végpontok használatával kapcsolódhat egy olyan Azure Pásti szolgáltatáshoz, amely támogatja a privát vagy a saját privát kapcsolati szolgáltatását.
- **[Azure Private link Service](private-link-service-overview.md)**: az Azure Private link Service szolgáltató által létrehozott szolgáltatás. Jelenleg egy magánhálózati kapcsolati szolgáltatás csatlakoztatható egy standard Load Balancer előtérbeli IP-konfigurációjához. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Hogyan történik a forgalom küldése a privát hivatkozás használatakor?
A forgalmat a Microsoft gerincen keresztül küldjük el. Nem halad át az interneten. Az Azure Private link nem tárolja az ügyféladatokat.
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>Mi a különbség a szolgáltatási végpontok és a privát végpontok között?
- A privát végpontok egy adott szolgáltatás mögötti, részletes szegmentálást biztosító erőforrásokhoz biztosítanak hálózati hozzáférést. A forgalom nyilvános végpontok használata nélkül érheti el a szolgáltatási erőforrást a helyszínen.
- A szolgáltatási végpontok egy nyilvánosan irányítható IP-címen maradnak.  A privát végpont a magánhálózati IP-cím azon virtuális hálózat címterület területén, ahol a magánhálózati végpont konfigurálva van.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Mi a kapcsolat a Private link Service és a privát végpont között?
Több magánhálózati kapcsolati erőforrástípus támogatja a hozzáférést a privát végponton keresztül. Az erőforrások közé tartozik az Azure Pásti-szolgáltatások és a saját privát kapcsolati szolgáltatás. Ez egy-a-többhöz kapcsolat. 

A privát kapcsolati szolgáltatás több privát végponttól érkező kapcsolatokat fogad. Egy privát végpont egy privát kapcsolati szolgáltatáshoz csatlakozik.    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>Le kell tiltani a privát kapcsolathoz tartozó hálózati házirendeket
Igen. A privát végpont és a magánhálózati kapcsolat szolgáltatásnak is le kell tiltania a hálózati házirendeket a megfelelő működéshez. Mindkettő tulajdonságai egymástól függetlenek.

## <a name="private-endpoint"></a>Privát végpont 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Létrehozhatok több privát végpontot is ugyanabban a VNet? Csatlakozhatnak különböző szolgáltatásokhoz? 
Igen. Több privát végpont is szerepelhet ugyanabban a VNet vagy alhálózatban. Más szolgáltatásokhoz is csatlakozhatnak.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Szükség van dedikált alhálózatra a privát végpontokhoz? 
Nem. Nincs szükség dedikált alhálózatra a privát végpontokhoz. A privát végponti IP-címet bármely alhálózatból kiválaszthatja abban a VNet, ahol a szolgáltatás telepítve van.  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>Csatlakozhatnak privát végpontok a privát kapcsolati szolgáltatásokhoz Azure Active Directory bérlők között? 
Igen. A privát végpontok kapcsolódhatnak a privát kapcsolati szolgáltatásokhoz vagy az Azure-beli Azure Active Directory-bérlők között. A bérlők közötti kapcsolattal rendelkező privát végpontok manuális kérelem-jóváhagyást igényelnek. 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Csatlakozhatnak a privát végpontok az Azure Pásti-erőforrásokhoz az Azure-régiók között?
Igen. A privát végpontok Azure Pásti-erőforrásokhoz is csatlakozhatnak az Azure-régiók között.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>Módosíthatom a privát végpont hálózati adapterét (NIC)?
Privát végpont létrehozásakor a rendszer egy írásvédett hálózati adaptert rendel hozzá. Ez nem módosítható, és továbbra is a privát végpont életciklusa alatt marad.

### <a name="how-do-i-achieve-availability-while-using-private-endpoints-in-case-of-regional-failures-"></a>Hogyan a rendelkezésre állást a saját végpontok használatakor a regionális hibák esetén?

A privát végpontok a 99,99%-os SLA-val rendelkező, magasan rendelkezésre álló erőforrások, amelyek [Az Azure Private-kapcsolatra vonatkozó SLA](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/)-t Mivel azonban regionális erőforrások, az Azure-régiók kiesése hatással lehet a rendelkezésre állásra. Ha regionális meghibásodás esetén szeretné elérni a rendelkezésre állást, különböző régiókban is üzembe helyezhető több PEs-kapcsolat ugyanahhoz a cél erőforráshoz. Így ha az egyik régió leáll, továbbra is átirányíthatja a helyreállítási forgatókönyvek forgalmát a különböző régiókban lévő PE-n keresztül a cél erőforrás eléréséhez. A regionális hibák a rendeltetési szolgáltatási oldalon való kezelésével kapcsolatos információkért tekintse át a szolgáltatás dokumentációját a feladatátvételről és a helyreállításról. A magánhálózati kapcsolati forgalom a cél végpont Azure DNS feloldását követi. 


## <a name="private-link-service"></a>Privát kapcsolati szolgáltatás
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Mik a Private link Service létrehozásához szükséges előfeltételek? 
A szolgáltatás hátterének virtuális hálózatban kell lennie, és egy standard Load Balancer mögött kell lennie.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Hogyan méretezhetim a privát link Service-t? 
A privát kapcsolati szolgáltatást többféleképpen is méretezheti: 
- Háttérbeli virtuális gépek hozzáadása a standard Load Balancer mögötti készlethez 
- Adjon hozzá egy IP-címet a privát kapcsolati szolgáltatáshoz. Privát kapcsolati szolgáltatásként legfeljebb 8 IP-címet engedélyezünk.  
- Adja hozzá a standard Load Balancerhoz az új Private link Service-t. A Load Balancer legfeljebb nyolc magánhálózati kapcsolati szolgáltatást engedélyez.   

### <a name="what-is-natnetwork-address-translation-ip-configuration-used-in-private-link-service-how-can-i-scale-in-terms-of-available-ports-and-connections"></a>Mi a NAT (hálózati címfordítás) IP-konfigurációja a Private link Service-ben? Hogyan méretezhetők a rendelkezésre álló portok és kapcsolatok? 

A NAT IP-konfiguráció biztosítja, hogy a forrás (fogyasztói oldal) és a célként megadott (szolgáltatói) címtartomány között ne legyenek IP-ütközések, ha forrás NAT-t biztosít a privát kapcsolati forgalomhoz a célhely oldalon (szolgáltató oldalon). A NAT IP-címe forrás IP-címként jelenik meg a szolgáltatás által fogadott összes csomaghoz és a cél IP-címéhez a szolgáltatás által küldött összes csomag esetében.  A NAT IP-címet a szolgáltató virtuális hálózatában található bármely alhálózatból lehet kiválasztani. 

Mindegyik NAT-IP 64 GB/s TCP-kapcsolatot (64 kilobájt portot) biztosít a standard Load Balancer mögötti virtuális gépeken. További kapcsolatok méretezéséhez és hozzáadásához hozzáadhat új NAT IP-címeket, vagy további virtuális gépeket adhat hozzá a standard Load Balancerhoz. Így a portok rendelkezésre állását és a további kapcsolatok engedélyezését is lehetővé teszi. A kapcsolatok elosztása a NAT-IP-címek és a standard Load Balancer mögötti virtuális gépek között történik.

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>A szolgáltatáshoz több privát végpont is csatlakoztatható?
Igen. Egy privát kapcsolati szolgáltatás több privát végpontról is fogadhat kapcsolatokat. Egy privát végpont azonban csak egy privát kapcsolati szolgáltatáshoz tud csatlakozni.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Hogyan szabályozható a privát kapcsolati szolgáltatás expozíciója?
Az expozíciót a magánjellegű kapcsolat szolgáltatás láthatósági konfigurációjának használatával szabályozhatja. A láthatóság három beállítást támogat:

- A nem csak az Azure **RBAC-hozzáféréssel** rendelkező előfizetések megkereshetik a szolgáltatást. 
-  A csak a jóváhagyott és az Azure RBAC hozzáféréssel rendelkező előfizetések megkereshetik a szolgáltatást. 
- **Minden** – mindenki megtalálja a szolgáltatást. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Létrehozhatok egy alapszintű Load balancert tartalmazó privát hivatkozási szolgáltatást? 
Nem. Az alapszintű Load balanceren keresztüli privát kapcsolati szolgáltatás nem támogatott.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Dedikált alhálózat szükséges a Private link Service-hez? 
Nem. A Private link Service nem igényel dedikált alhálózatot. Bármelyik alhálózatot kiválaszthatja a VNet, ahol a szolgáltatás telepítve van.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Azure Private-hivatkozást használó szolgáltató vagyok. Meg kell győződni arról, hogy minden ügyfélnek egyedi IP-címmel kell rendelkeznie, és ne legyen átfedésben az IP-tárhelytel? 
Nem. Az Azure Private link ezt a funkciót biztosítja Önnek. Nem szükséges, hogy ne legyen átfedésben a címtartomány az ügyfél címterület-területével. 

##  <a name="next-steps"></a>Következő lépések

- További információ az [Azure Private linkről](private-link-overview.md)
