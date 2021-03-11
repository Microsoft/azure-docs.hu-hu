---
title: A Cloud Foundry integrálása az Azure-ba | Microsoft Docs
description: Leírja, hogy a Cloud Foundry hogyan használhatja az Azure-szolgáltatásokat a nagyvállalati élmény fokozása érdekében
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: ff2a6618b60ff2cfa5faa74c905e140466a14359
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563319"
---
# <a name="integrate-cloud-foundry-with-azure"></a>A Cloud Foundry Azure-ral való integrálása

[Cloud Foundry](https://docs.cloudfoundry.org/) egy, a Cloud Providers IaaS platformon futó Pásti-platform. A Felhőbeli szolgáltatók konzisztens alkalmazás-telepítési élményt biztosítanak. Emellett számos Azure-szolgáltatással integrálható, nagyvállalati szintű, méretezhetőséggel és költségmegtakarítással.
[A Cloud Foundry 6 alrendszerből](https://docs.cloudfoundry.org/concepts/architecture/)áll, amelyek rugalmasan méretezhetők online állapotba, többek között a következőket: Útválasztás, hitelesítés, alkalmazások életciklusának kezelése, szolgáltatások kezelése, üzenetkezelés és figyelés. Az egyes alrendszerek esetében Cloud Foundry konfigurálhatja a levelező Azure-szolgáltatás használatát. 

![Cloud Foundry az Azure integrációs architektúráján](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. magas rendelkezésre állás és méretezhetőség
### <a name="managed-disk"></a>Felügyelt lemez
A Bosh Az Azure CPI (Cloud Provider Interface) szolgáltatást használja a rutinok létrehozásához és törléséhez. Alapértelmezés szerint a rendszer nem felügyelt lemezeket használ. Az ügyfélnek manuálisan kell létrehoznia a Storage-fiókokat, majd konfigurálnia kell a fiókokat a CF manifest-fájlokban. Ez azért van, mert a lemezek száma a Storage-fiókban.
A most [felügyelt lemez](https://azure.microsoft.com/services/managed-disks/) elérhető, és felügyelt biztonságos és megbízható lemezes tárolást biztosít a virtuális gépek számára. Az ügyfélnek már nem kell foglalkoznia a Storage-fiókkal a skálázás és a HA esetében. Az Azure automatikusan rendezi a lemezeket. Akár új, akár meglévő üzemelő példány, az Azure CPI a felügyelt lemez létrehozását vagy áttelepítését fogja kezelni a CF üzembe helyezése során. Ezt a PCF 1,11 támogatja. A nyílt forráskódú Cloud Foundry [felügyelt lemezekkel kapcsolatos útmutatót](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) is megismerheti. 
### <a name="availability-zone-"></a>Rendelkezésre állási zóna *
A Felhőbeli natív alkalmazás platformként a Cloud Foundry [négy magas szintű rendelkezésre állást](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html)biztosít. Míg a szoftverek első három szintjét a CF-rendszer is képes kezelni, a platform hibatűrését a felhőalapú szolgáltatók biztosítják. A Key CF-összetevőket a Cloud Provider platform HA megoldással kell védeni. Ide tartozik a GoRouters, a Diego Brains, a CF Database és a Service csempék. Alapértelmezés szerint az [Azure rendelkezésre állási készlete](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) az adatközpontban lévő fürtök hibatűrésére szolgál.
A jó új, az [Azure rendelkezésre állási zónája](../availability-zones/az-overview.md) most már elérhető, így a hibatűrés a következő szintre kerül, és az adatközpontok kis késleltetésű redundancia.
Az Azure rendelkezésre állási zónája a virtuális gépek egy csoportját 2 + adatközpontba helyezi, és a virtuális gépek minden készlete redundánsan más készletekbe kerül. Ha az egyik zóna nem működik, a többi készlet továbbra is élő, a katasztrófa elkülönítve.
> [!NOTE] 
> Az Azure rendelkezésre állási zónája még nem érhető el az összes régióban, tekintse meg a [támogatott régiók listájának legújabb bejelentését](../availability-zones/az-overview.md). A nyílt forráskódú Cloud Foundry esetében tekintse [meg az Azure-beli rendelkezésre állási zónát a nyílt forráskódú Cloud Foundry útmutatóban](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. hálózati Útválasztás
Alapértelmezés szerint az Azure alapszintű Load Balancer a bejövő CF API/apps kérelmekhez használatos, és továbbítja őket a Gorouters. A CF-összetevők, például a Diego Brain, a MySQL, az ERT a Load balancert is használhatják a forgalom elosztására. Az Azure teljes körűen felügyelt terheléselosztási megoldásokat is biztosít. Ha a TLS/SSL-lezárást ("SSL-kiszervezést") vagy HTTP/HTTPS-kérelem alkalmazási rétegének feldolgozását keresi, érdemes megfontolnia Application Gateway. A 4. rétegben a magas rendelkezésre állás és a méretezhetőség terheléselosztásához a standard Load balancert érdemes figyelembe venni.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
Az [Azure Application Gateway](../application-gateway/overview.md) különböző 7. rétegbeli terheléselosztási funkciókat kínál, beleértve az SSL-kiszervezést, a végpontok közötti TLS-t, a webalkalmazási tűzfalat, a cookie-alapú munkamenet-affinitást és egyebeket [Application Gateway a nyílt forráskódú Cloud Foundryban is konfigurálható](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). A PCF esetében ellenőrizze a  [PCF 2,1 kibocsátási megjegyzéseit](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) a POC-teszthez.

### <a name="azure-standard-load-balancer-"></a>Azure standard Load Balancer *
A Azure Load Balancer egy 4. rétegbeli terheléselosztó. A szolgáltatás egy elosztott terhelésű készletben lévő szolgáltatások példányai között osztja el a forgalmat. A standard verzió [speciális funkciókat](../load-balancer/load-balancer-overview.md) biztosít az alapszintű verzióhoz. Például 1. A háttér-készlet maximális korlátja 100 és 1000 virtuális gép között van kiemelve.  2. A végpontok mostantól több rendelkezésre állási csoportot is támogatnak egyetlen rendelkezésre állási csoport helyett.  3. További funkciók, például HA portok, gazdagabb monitorozási információk stb. Ha áthelyezi az Azure rendelkezésre állási zónáját, a standard Load Balancer szükséges. Az új központi telepítéshez javasoljuk, hogy kezdjen el az Azure standard Load Balancer. 

## <a name="3-authentication"></a>3. hitelesítés 
[Cloud Foundry a felhasználói fiók és a hitelesítés](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) a CF központi Identitáskezelés szolgáltatás, és annak különböző összetevői. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a Microsoft több-bérlős, felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. Alapértelmezés szerint az UAA Cloud Foundry hitelesítéshez használatos. Az UAA speciális lehetőségként az Azure AD-t is támogatja külső felhasználói tárolóként. Az Azure AD-felhasználók Cloud Foundry-fiók nélkül férhetnek hozzá Cloud Foundry az LDAP-identitással. Az alábbi lépéseket követve [konfigurálhatja az UAA-hez készült Azure ad-t a PCF-ben](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Cloud Foundry futtatókörnyezeti rendszer adattárolója
A Cloud Foundry kiváló bővíthetőséget kínál az Azure blobtárhely vagy az Azure MySQL/PostgreSQL szolgáltatások használatára az alkalmazás-futtatókörnyezet rendszertárolásához.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure-Blobtárhely Cloud Foundry Cloud Controller-blobtárhely
A Cloud Controller blobtárhely egy kritikus adattár a buildpacks, a feldolgozók, a csomagok és az erőforrások készletei számára. Alapértelmezés szerint az NFS-kiszolgáló a Cloud Controller blobtárhely használatos. Az egypontos meghibásodás elkerülése érdekében használja az Azure Blob Storaget külső tárolóként. Tekintse meg a háttér [Cloud Foundry dokumentációját](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) , valamint a [Pivotal Cloud Foundry lehetőségeit](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL Cloud Foundry rugalmas futási idő adatbázis *
VÖ. a rugalmas futtatókörnyezethez két fő rendszeradatbázis szükséges:
#### <a name="ccdb"></a>CCDB 
A Cloud Controller adatbázisa.  A Cloud Controller REST API végpontokat biztosít az ügyfelek számára a rendszer eléréséhez. A CCDB táblákat tárol a szervezethez, a szóközöket, a szolgáltatásokat, a felhasználói szerepköröket és egyebeket a felhőalapú vezérlőhöz.
#### <a name="uaadb"></a>UAADB 
A felhasználói fiók és a hitelesítés adatbázisa. Tárolja a felhasználó hitelesítéssel kapcsolatos adatait, például a titkosított felhasználóneveket és jelszavakat.

Alapértelmezés szerint a rendszer egy helyi rendszeradatbázist (MySQL) is használhat. Az Azure Managed MySQL-t és a PostgreSQL-szolgáltatásokat igény szerint kihasználhatja. Az alábbi útmutatást követve [engedélyezheti az Azure MySQL/PostgreSQL-t az CCDB, a UAADB és más rendszeradatbázisok számára nyílt forráskódú Cloud Foundryokkal](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Nyissa meg Service Broker
Az Azure Service Broker konzisztens felületet biztosít az alkalmazások Azure-szolgáltatásokhoz való hozzáférésének kezeléséhez. Az [Azure Project új nyílt Service Brokerja](https://github.com/Azure/open-service-broker-azure) egyetlen és egyszerű módszert kínál a szolgáltatások alkalmazására Cloud Foundry, OpenShift és Kubernetes között. A PCF-on üzembe helyezési utasításokért tekintse meg az [Azure Open Service Broker for PCF csempét](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) .

## <a name="6-metrics-and-logging"></a>6. mérőszámok és naplózás
Az Azure Log Analytics fúvóka egy Cloud Foundry-összetevő, amely a [Cloud Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) [Azure monitor naplókba](https://azure.microsoft.com/services/log-analytics/)továbbítja a metrikákat. A fúvókával összegyűjtheti, megtekintheti és elemezheti a CF rendszerállapot-és teljesítmény-mérőszámait több üzemelő példány között.
Ide [kattintva](./cloudfoundry-oms-nozzle.md) megtudhatja, hogyan helyezheti üzembe az Azure log Analytics fúvókát a nyílt forráskódú és a pivotal Cloud Foundry-környezetbe, majd Hogyan férhet hozzá az adatokhoz a Azure monitor naplók konzolján. 
> [!NOTE]
> A PCF 2,0-es verziójában a virtuális gépekre vonatkozó BOSH-Loggregator alapértelmezés szerint továbbítva lesznek a-firehose, és integrálva vannak Azure Monitor naplók konzolba.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. költségmegtakarítás
### <a name="cost-saving-for-devtest-environments"></a>A fejlesztési és tesztelési környezetek költségmegtakarítása
#### <a name="b-series-"></a>B sorozat: *
Habár az F és a D virtuálisgép-sorozatot általában a Pivotal Cloud Foundry éles környezetekhez ajánlották, az új "feltört" [B sorozat](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) új lehetőségeket kínál. A B sorozatú feltört virtuális gépek ideálisak olyan számítási feladatokhoz, amelyeknek nincs szükségük a CPU teljes teljesítményére, például webkiszolgálók, kis adatbázisok, fejlesztési és tesztelési környezetek. Ezek a számítási feladatok általában feltört teljesítménnyel kapcsolatos követelményekkel rendelkeznek. Ez a $0.012/Hour (B1) és a 0,05/óra (F1) összehasonlítása, a virtuálisgép- [méretek](../virtual-machines/sizes-general.md) teljes listáját és a részletek [árát](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) tartalmazza. 
#### <a name="managed-standard-disk"></a>Felügyelt standard lemez: 
A prémium szintű lemezek használata ajánlott a megbízható teljesítmény érdekében.  A [felügyelt lemezekkel](https://azure.microsoft.com/services/managed-disks/)a standard szintű tárolás hasonló megbízhatóságot is biztosíthat, eltérő teljesítménnyel. Az olyan számítási feladatok esetében, amelyek nem érzékenyek, mint a fejlesztési/tesztelési vagy a nem kritikus környezet, a felügyelt standard lemezek egy alternatív lehetőséget kínálnak alacsonyabb díjszabással.  
### <a name="cost-saving-in-general"></a>Általános költségmegtakarítás 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Jelentős virtuálisgép-költségmegtakarítás az Azure-foglalásokkal: 
Napjainkban az összes CF virtuális gép számlázása "igény szerinti" díjszabással történik, még akkor is, ha a környezetek általában határozatlan ideig maradnak. Most már 1 vagy 3 éves időszakra is lefoglalhatja a VM-kapacitást, és 45-65%-os kedvezményt nyerhet. A kedvezményeket a rendszer a számlázási rendszeren alkalmazza, a környezet módosítása nélkül. Részletekért lásd: az [Azure-foglalások működése](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Felügyelt prémium lemez kisebb méretekkel: 
A felügyelt lemezek támogatják a kisebb méretű lemezeket, például a P4 (32 GB) és a P6 (64 GB) a prémium és a standard szintű lemezek esetében. Ha kis számítási feladatokkal rendelkezik, megtakaríthatja a költségeket, ha standard prémium szintű lemezekről felügyelt prémium lemezekre végez áttelepítést.
#### <a name="use-azure-first-party-services"></a>Az Azure első féltől származó szolgáltatásainak használata: 
Az Azure első féltől származó szolgáltatásának kihasználása csökkenti a hosszú távú adminisztrációs költségeket, továbbá a fenti részekben említett és megbízhatóságot is. 

A Pivotal a PCF-ügyfelek számára egy [kis helyigényű ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) -t indított el, az összetevők pedig mindössze 4 virtuális gépen helyezkednek el, amely akár 2500 alkalmazás-példányt is tartalmaz. A próbaverzió már elérhető az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Következő lépések
Az Azure-integrációs funkciók először [nyílt forráskódú Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/)érhetők el, mielőtt a pivotal Cloud Foundry elérhetővé válnak. A *-val jelölt szolgáltatások továbbra sem érhetők el a PCF-on keresztül. A Azure Stack Cloud Foundry integrációját nem fedi le ebben a dokumentumban sem.
Ha a PCF támogatja a * vagy a Cloud Foundry integrációt a Azure Stackval, lépjen kapcsolatba a Pivotal és a Microsoft-fiók Managerrel a legújabb állapothoz.