---
title: Gyakran ismételt kérdések az Azure Spring Cloud-ról | Microsoft Docs
description: Ez a cikk az Azure Spring Cloud szolgáltatással kapcsolatos gyakori kérdésekre ad választ.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 50c8a36b943abb24f2c31bfc2bc33e646a74524a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596626"
---
# <a name="azure-spring-cloud-faq"></a>Azure Spring Cloud – gyakori kérdések

Ez a cikk az Azure Spring Cloud szolgáltatással kapcsolatos gyakori kérdésekre ad választ.

## <a name="general"></a>Általános

### <a name="why-azure-spring-cloud"></a>Az Azure Spring Cloud bemutatása

Az Azure Spring Cloud a tavaszi felhőalapú fejlesztők számára biztosít egy szolgáltatásként elérhető platformot (Péter). Az Azure Spring Cloud felügyeli az alkalmazás-infrastruktúráját, így az alkalmazás kódjára és az üzleti logikára koncentrálhat. Az Azure Spring Cloud beépített alapfunkciói közé tartozik az Eureka, a konfigurációs kiszolgáló, a szolgáltatás beállításjegyzék-kiszolgálója, a Pivotal Build szolgáltatás, a Blue-Green üzembe helyezése stb. Ez a szolgáltatás azt is lehetővé teszi, hogy a fejlesztők az alkalmazásaikat más Azure-szolgáltatásokkal, például a Azure Cosmos DB, a Azure Database for MySQL és az Azure cache Redis-hez kössenek.

Az Azure Spring Cloud a fejlesztők és a kezelők számára a Azure Monitor, Application Insights és Log Analytics integrálásával fokozza az Application Diagnostics-élményt.

### <a name="how-secure-is-azure-spring-cloud"></a>Mennyire biztonságos az Azure Spring Cloud?

A biztonság és az adatvédelem az Azure és az Azure Spring Cloud ügyfeleinek legfontosabb prioritásai közé tartozik. Az Azure segítségével biztosíthatja, hogy csak az ügyfelek férhessenek hozzá az alkalmazásadatokhöz, a naplókhoz és a konfigurációhoz az összes információ biztonságos titkosításával. 

* Az Azure Spring Cloud szolgáltatási példányai egymástól elszigeteltek.
* Az Azure Spring Cloud teljes TLS/SSL-és Tanúsítványkezelőt biztosít.
* A OpenJDK és a Spring Cloud Runtime kritikus biztonsági javításait a lehető leghamarabb alkalmazza az Azure Spring Cloud-ra.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Mely régiókban érhető el az Azure Spring Cloud?

USA keleti régiója, USA 2. keleti régiója, az USA középső régiója, az USA déli középső régiója, az USA északi középső régiója, az USA nyugati régiója, az USA nyugati régiója, Nyugat-Európa, Észak-Európa, Egyesült Királyság déli régiója, Délkelet-Ázsia, Kelet-Ausztrália, Kanada középső régiója, Egyesült Arab Emírségek, Közép-India, Kelet-Kína Kelet-Ázsia Korea [További információ](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud)

### <a name="is-any-customer-data-stored-outside-of-the-specified-region"></a>A megadott régión kívül tárolt ügyféladatok?

Az Azure Spring Cloud egy regionális szolgáltatás. Az Azure Spring Cloud-beli ügyféladatokat a rendszer a megadott régión belül több régióban tárolja a redundancia érdekében. A földrajzi és a régióval kapcsolatos további tudnivalókért tekintse meg az [adattárolás az Azure-ban](https://azure.microsoft.com/global-infrastructure/data-residency/)című témakört.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Mik az Azure Spring Cloud ismert korlátai?

Az Azure Spring Cloud a következő ismert korlátozásokkal rendelkezik:
    
* `spring.application.name` a rendszer felülbírálja az egyes alkalmazások létrehozásához használt alkalmazás nevét.
* `server.port` az alapértelmezett érték a 1025-es port. Ha más értéket alkalmaz, a rendszer felülbírálja. Vegye figyelembe ezt a beállítást is, és ne adja meg a kiszolgáló portját a kódban.
* A Azure Portal és Azure Resource Manager sablonok nem támogatják az alkalmazáscsomag feltöltését. Az alkalmazás csomagjait csak az Azure CLI-n keresztüli üzembe helyezésével töltheti fel.

### <a name="what-pricing-tiers-are-available"></a>Milyen díjszabási szintek érhetők el? 
Melyiket érdemes használni, és mi a határértékek az egyes rétegeken belül?
* Az Azure Spring Cloud két díjszabási szintet kínál: alapszintű és standard. Az alapszintű csomag fejlesztési és tesztelési célokra, valamint az Azure Spring Cloud kipróbálására szolgál. A standard szint az általános célú éles adatforgalom futtatására van optimalizálva. Tekintse meg az [Azure Spring Cloud díjszabását](https://azure.microsoft.com/pricing/details/spring-cloud/) a korlátozások és a funkciók szintjének összehasonlításával kapcsolatban.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hogyan adhatok visszajelzést és jelenthetem a jelentéssel kapcsolatos problémákat?

Ha problémák merülnek fel az Azure Spring Cloudtel kapcsolatban, hozzon létre egy [Azure-támogatási kérelmet](../azure-portal/supportability/how-to-create-azure-support-request.md). Ha beküld egy szolgáltatási kérelmet, vagy visszajelzést szeretne küldeni, ugorjon az [Azure-visszajelzések](https://feedback.azure.com/forums/34192--general-feedback)lehetőségre.

## <a name="development"></a>Fejlesztés

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Tavaszi Felhőbeli fejlesztő vagyok, de az Azure új. Mi a leggyorsabb módszer arra, hogy megtudjam, hogyan fejleszthet egy Azure Spring Cloud-alkalmazást?

Az Azure Spring Cloud első lépéseinek megkezdéséhez kövesse a rövid útmutató [: Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával](spring-cloud-quickstart.md)című témakör utasításait.

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Milyen Java-futtatókörnyezetet támogat az Azure Spring Cloud?

Az Azure Spring Cloud támogatja a Java 8 és a 11 platformot. Lásd: [Java-futtatókörnyezet és operációsrendszer-verziók](#java-runtime-and-os-versions)

### <a name="is-spring-boot-24x-supported"></a>A Spring boot 2.4. x támogatott?
A Spring boot 2,4-as számú, és jelenleg a Spring-Közösséggel dolgozik a megoldásban. Addig is vegye fel ezt a két függőséget, hogy engedélyezze a TLS-hitelesítést az alkalmazások és az Eureka között.

```xml
<dependency> 
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.19.4</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey.contribs</groupId>
    <artifactId>jersey-apache-client4</artifactId>
    <version>1.19.4</version>
</dependency>
```

::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Hol tekinthetem meg a Spring Cloud Application-naplókat és-metrikákat?

A metrikák kereséséhez az alkalmazás áttekintése lapon és a [Azure monitor](../azure-monitor/essentials/data-platform-metrics.md#metrics-explorer) lapon.

Az Azure Spring Cloud támogatja a Spring Cloud Application-naplók és-mérőszámok exportálását az Azure Storage, a EventHub és a [log Analyticsba](../azure-monitor/logs/data-platform-logs.md). A Log Analytics *AppPlatformLogsforSpring* a táblázat neve. Ha szeretné megtudni, hogyan engedélyezheti, tekintse meg a [diagnosztikai szolgáltatásokat](diagnostic-services.md)ismertető témakört.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Támogatja az Azure Spring Cloud az elosztott nyomkövetést?

Igen. További információ: [oktatóanyag: elosztott nyomkövetés használata az Azure Spring Cloud használatával](spring-cloud-tutorial-distributed-tracing.md).

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>Milyen típusú erőforrásokat támogat a szolgáltatás-kötés?

Jelenleg három szolgáltatás támogatott:
* Azure Cosmos DB
* Azure Database for MySQL
* Azure cache a Redis-hez.
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Megtekinthetem, hozzáadhatom vagy áthelyezhetem az állandó köteteket az alkalmazásaim belsejében?

Igen.

### <a name="how-many-outbound-public-ip-addresses-does-an-azure-spring-cloud-instance-have"></a>Hány kimenő nyilvános IP-cím van az Azure Spring Cloud-példányon?

A kimenő nyilvános IP-címek száma a rétegektől és más tényezőktől függően változhat. 

| Azure Spring Cloud-példány típusa | Kimenő nyilvános IP-címek alapértelmezett száma |
| -------------------------------- | ---------------------------------------------- |
| Alapszintű példányok             | 1                                              |
| Standard szintű példányok          | 2                                              |
| VNet injekciós példányok         | 1                                              |


### <a name="can-i-increase-the-number-of-outbound-public-ip-addresses"></a>Megnövelhető a kimenő nyilvános IP-címek száma?

Igen, megnyithat egy [támogatási jegyet](https://azure.microsoft.com/support/faq/)  , amely további kimenő nyilvános IP-címeket kér le.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Ha törölek vagy Áthelyezek egy Azure Spring Cloud Service-példányt, akkor a bővítmény erőforrásai is törlődnek vagy áthelyezhetők?

A bővítmény erőforrásainak tulajdonosaként használt erőforrás-szolgáltatók logikáján múlik. Egy példány bővítmény-erőforrásai `Microsoft.AppPlatform` nem ugyanahhoz a névtérhez tartoznak, így a viselkedés az erőforrás-szolgáltatótól függ. A DELETE/Move művelet például nem fog lépcsőzetesen a **diagnosztikai beállítások** erőforrásaihoz csatlakozni. Ha egy új Azure Spring Cloud-példány ugyanazzal az erőforrás-AZONOSÍTÓval lett kiépítve, mint a törölt, vagy ha az előző Azure Spring Cloud-példányt visszahelyezik, a korábbi **diagnosztikai beállítások** erőforrásai tovább bővítik azt.

A Spring Cloud diagnosztikai beállításait az Azure CLI használatával törölheti:

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>Java-futtatókörnyezet és operációsrendszer-verziók

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>A Java Runtime mely verzióit támogatja az Azure Spring Cloud?

Az Azure Spring Cloud a legújabb buildekkel támogatja a Java LTS-verziókat, amely jelenleg június 2020, a Java 8 és a Java 11 támogatott. Lásd: [Az Azure-hoz készült JDK telepítése és Azure stack](/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>Kik építették ezeket a Java-futtatókörnyezeteket?

Azul rendszerek. Az Azul Zulu for Azure – Enterprise Edition buildjei a JDK ingyenes, többplatformos, éles környezetben való használatra kész és a Microsoft, illetve az Azul Systems által támogatott disztribúciói az Azure-hoz és az Azure Stackhez. A Java SE-alkalmazások létrehozásához és futtatásához szükséges összes összetevőt tartalmazzák.

### <a name="how-often-will-java-runtimes-get-updated"></a>Milyen gyakran frissülnek a Java-futtatókörnyezetek?

Az LTS és az MTS JDK-kiadásai negyedéves biztonsági frissítéseket, hibajavításokat és kritikus sávon kívüli frissítéseket és javításokat is igénybe vesznek. Ez a támogatás magában foglalja a Java 7 és 8 backports, valamint a Java újabb verzióiban, például a Java 11-ben jelentett hibajavításokat.

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Mennyi ideig lesz támogatott a Java 8 és a Java 11 LTS verzió?

Lásd: [a Java hosszú távú támogatása az Azure-ban és a Azure stack](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* A Java 8 LTS 2030. decembertől lesz támogatott.
* A Java 11 LTS 2027 szeptemberéig lesz támogatott.

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>Hogyan tölthetők le támogatott Java-futtatókörnyezet a helyi fejlesztéshez?

Lásd: [Az Azure-hoz készült JDK telepítése és Azure stack](/azure/developer/java/fundamentals/java-jdk-install).

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>Mi a régebbi Java-futtatókörnyezetek kivonási szabályzata?

A nyilvános hirdetményt 12 hónappal a régi futtatókörnyezet-verzió kivonása előtt küldi el a rendszer. Egy újabb verzióra való Migrálás 12 hónapig fog rendelkezni.

* Az előfizetés-adminisztrátorok e-mailben értesítést kapnak, amikor kivonják a Java-verziót.
* A kivonási adatok a dokumentációban lesznek közzétéve.

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Hogyan Kérhetek támogatást a Java futtatókörnyezeti szinten felmerülő problémákhoz?

A támogatási jegyet Azure-támogatással is megnyithatja.  Lásd: [Azure-támogatási kérelem létrehozása](../azure-portal/supportability/how-to-create-azure-support-request.md).

### <a name="what-is-the-operation-system-to-run-my-apps"></a>Mi az az operációs rendszer az alkalmazások futtatásához?

A legújabb Ubuntu LTS-verziót használjuk, a jelenleg [ubuntu 20,04 LTS (gyújtóponti Fossa)](https://releases.ubuntu.com/focal/) az alapértelmezett operációs rendszer.

### <a name="how-often-are-os-security-patches-applied"></a>Milyen gyakran alkalmazza az operációs rendszer biztonsági javításait?

Az Azure Spring Cloud-ra vonatkozó biztonsági javítások havi rendszerességgel kerülnek a termelésbe.
Az Azure Spring Cloud-ra vonatkozó kritikus biztonsági javítások (CVE-pontszám >= 9) a lehető leghamarabb bekerülnek.
::: zone-end

## <a name="deployment"></a>Üzembe helyezés

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Támogatja az Azure Spring Cloud a Blue-Green üzembe helyezést?
Igen. További információ: [átmeneti környezet beállítása](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Hozzáférhetek a Kubernetes az alkalmazás-tárolók kezeléséhez?

Nem.  Az Azure Spring Cloud elvonta a fejlesztőt a mögöttes architektúrából, így az alkalmazás kódjára és az üzleti logikára koncentrálhat.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Támogatja az Azure Spring Cloud a tárolók létrehozását a forrástól?

Igen. További információ: [a Spring Cloud-alkalmazás elindítása forráskódból](spring-cloud-quickstart.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Támogatja az Azure Spring Cloud az automatikus skálázást az alkalmazás példányaiban?

Igen.  További információ: az [autoscale beállítása](spring-cloud-tutorial-setup-autoscale.md).

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Mik az ajánlott eljárások a meglévő Spring Cloud-szolgáltatások Azure Spring Cloud-ba való áttelepítéséhez?

A meglévő Spring Cloud-szolgáltatásoknak az Azure Spring Cloudba való áttelepítése során érdemes megfigyelni az alábbi ajánlott eljárásokat:
* Minden alkalmazás-függőséget fel kell oldani.
* Készítse elő a konfigurációs bejegyzéseket, a környezeti változókat és a JVM paramétereket, hogy össze lehessen hasonlítani őket az Azure Spring Cloud üzembe helyezésével.
* Ha szolgáltatási kötést szeretne használni, ugorjon az Azure-szolgáltatásaihoz, és győződjön meg arról, hogy a megfelelő hozzáférési engedélyeket adta meg.
* Javasoljuk, hogy távolítsa el vagy tiltsa le azokat a beágyazott szolgáltatásokat, amelyek ütközhetnek az Azure Spring Cloud által felügyelt szolgáltatásokkal, például a Service Discovery szolgáltatással, a konfigurációs kiszolgálóval és így tovább.
* Javasoljuk, hogy a hivatalos és a stabil Pivotal rugós kódtárakat használja. A Pivotal Spring-kódtárak nem hivatalos, bétaverziós vagy elágazó verziói nem rendelkeznek szolgáltatói szerződés (SLA) támogatással.

Az áttelepítés után figyelje a CPU-/RAM-mérőszámokat és a hálózati forgalmat annak érdekében, hogy az alkalmazás példányai megfelelően méretezhetők legyenek.
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>.NET Core-verziók

### <a name="which-net-core-versions-are-supported"></a>Mely .NET Core verziók támogatottak?

.NET Core 3,1 és újabb verziók.

### <a name="how-long-will-net-core-31-be-supported"></a>Mennyi ideig lesz támogatott a .NET Core 3,1?

2022. december 3. között. Lásd: [.net Core támogatási szabályzat](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).
::: zone-end


## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>Milyen hatással van a szolgáltatás-beállításjegyzék ritkán nem érhető el?

Bizonyos ritkán előforduló forgatókönyvek esetében előfordulhat, hogy bizonyos hibákat tapasztal, például: 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
az alkalmazások naplóiból. Ezt a problémát a rugós keretrendszer a hálózati instabil vagy más hálózati problémák miatt nagyon alacsony díjszabással vezette be. 

Nincs hatással a felhasználói élményre, az Eureka-ügyfél szívverési és újrapróbálkozási szabályzattal is rendelkezik. Érdemes lehet egy átmeneti hibának tekinteni, és biztonságosan kihagyni.

Ennek a résznek a tökéletesítését fogjuk kijavítani, és ezt a hibát a felhasználói alkalmazások rövid időn belül el kell kerülniük.


## <a name="next-steps"></a>Következő lépések

Ha további kérdései vannak, tekintse meg az [Azure Spring Cloud hibaelhárítási útmutatóját](spring-cloud-troubleshoot.md).
