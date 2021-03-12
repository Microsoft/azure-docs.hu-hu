---
title: Az Azure Service Fabric-fürt beállításainak módosítása
description: Ez a cikk a háló beállításait és a testre szabható háló-frissítési szabályzatokat ismerteti.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: 78d83faea802862d3cd6d1b1a9cf9f1016245065
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232052"
---
# <a name="customize-service-fabric-cluster-settings"></a>Service Fabric-fürt beállításainak testreszabása
Ez a cikk a Service Fabric-fürthöz testreszabható különböző háló-beállításokat ismerteti. Az Azure-ban üzemeltetett fürtök esetében a beállításokat a [Azure Portal](https://portal.azure.com) vagy egy Azure Resource Manager sablon segítségével szabhatja testre. További információ: Azure- [fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-azure.md). Önálló fürtök esetén testreszabhatja a beállításokat, ha frissíti a *ClusterConfig.js* fájlt, és végrehajtja a fürtön a konfiguráció frissítését. További információ: [önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md).

Három különböző frissítési szabályzat létezik:

- **Dinamikus** – a dinamikus konfiguráció módosításai nem okozzák a Service Fabric folyamatok vagy a szolgáltatás-gazdagép folyamatainak újraindítását. 
- **Statikus** – a statikus konfiguráció módosításai a Service Fabric csomópont újraindítását eredményezik a módosítás felhasználása érdekében. A csomópontok szolgáltatásai újra lesznek indítva.
- Nincs feldolgozva **– ezek** a beállítások nem módosíthatók. Ezeknek a beállításoknak a módosításához a fürtöt meg kell semmisíteni, és létre kell hozni egy új fürtöt. 

Az alábbi lista a testre szabható, a szakasz alapján rendszerezhető háló-beállításokat sorolja fel.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/http

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|karakterlánc, az alapértelmezett érték a "None"|Statikus| Ez nem ellenőrzi a kiszolgáló tanúsítványát; a kérelem sikeres. A fordított proxy által megbízhatónak tartott távoli tanúsítványok ujjlenyomatai megfelelnek vesszővel tagolt listáját a config ServiceCertificateThumbprints találja. A fordított proxy által megbízhatónak ítélt távoli tanúsítványok tulajdonos neve és kiállítói ujjlenyomata ServiceCommonNameAndIssuer tekintse meg a következőt:. További információért lásd a [biztonságos kapcsolatok fordított proxyval](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services)kapcsolatos témakört. |
|BodyChunkSize |Uint, alapértelmezett érték 16384 |Dinamikus| Megadja a méretet a törzs olvasásához használt bájtokban. |
|CrlCheckingFlag|uint, alapértelmezett érték a 0x40000000 |Dinamikus| Az alkalmazás/szolgáltatás-tanúsítványlánc érvényesítésének jelzői; például: CRL-ellenőrzés 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY beállítás értéke 0 letiltja a CRL-ellenőrzés a támogatott értékek teljes listáját a dwFlags CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Az idő másodpercben. alapértelmezett értéke 120 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan.  Megadja az alapértelmezett kérelem időtúllépését a http-alkalmazás átjárójában feldolgozott HTTP-kérelmek esetében. |
|ForwardClientCertificate|bool, az alapértelmezett érték FALSE|Dinamikus|Ha false értékre van állítva, akkor a fordított proxy nem kéri az ügyféltanúsítványt. Ha igaz értékre van állítva, a fordított proxy a TLS-kézfogás során kérni fogja az ügyféltanúsítványt, majd továbbítja a Base64 kódolású PEM formátumú karakterláncot a szolgáltatásnak egy X-Client-Certificate nevű fejlécben. a szolgáltatás a tanúsítvány adatainak vizsgálata után a megfelelő állapotkód miatt sikertelen lehet a kérést. Ha ez igaz, és az ügyfél nem tartalmaz tanúsítványt, a fordított proxy egy üres fejlécet továbbít, és lehetővé teszi, hogy a szolgáltatás kezelje az esetet. A fordított proxy transzparens rétegként fog működni. További információt az ügyféltanúsítvány- [alapú hitelesítés beállítása](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy)című témakörben talál. |
|GatewayAuthCredentialType |karakterlánc, az alapértelmezett érték a "None" |Statikus| Azt jelzi, hogy milyen típusú biztonsági hitelesítő adatokat kell használni a http app Gateway-végpont érvényes értékei: none/X509. |
|GatewayX509CertificateFindType |string (alapértelmezett érték: "FindByThumbprint") |Dinamikus| Azt jelzi, hogyan lehet a GatewayX509CertificateStoreName által megadott tárolóban megkeresni a tanúsítványokat a következő támogatott érték szerint: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | karakterlánc, az alapértelmezett érték: "" |Dinamikus| Keresési szűrő értéke a http app Gateway-tanúsítvány megkereséséhez. Ez a tanúsítvány a https-végponton van konfigurálva, és az alkalmazás identitásának ellenőrzésére is használható, ha a szolgáltatások szükségesek. A FindValue először megkeresve; és ha ez nem létezik, A FindValueSecondary megkeresve. |
|GatewayX509CertificateFindValueSecondary | karakterlánc, az alapértelmezett érték: "" |Dinamikus|Keresési szűrő értéke a http app Gateway-tanúsítvány megkereséséhez. Ez a tanúsítvány a https-végponton van konfigurálva, és az alkalmazás identitásának ellenőrzésére is használható, ha a szolgáltatások szükségesek. A FindValue először megkeresve; és ha ez nem létezik, A FindValueSecondary megkeresve.|
|GatewayX509CertificateStoreName |karakterlánc, az alapértelmezett érték a "My" |Dinamikus| A http-alkalmazás átjárójának tanúsítványát tartalmazó X. 509 tanúsítványtároló neve. |
|HttpRequestConnectTimeout|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (5)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan.  Megadja a http-alkalmazás átjáróján küldött HTTP-kérések csatlakozási időtúllépését.  |
|IgnoreCrlOfflineError|bool, az alapértelmezett érték TRUE (igaz)|Dinamikus|Azt jelzi, hogy a CRL-t figyelmen kívül hagyják-e az alkalmazás/szolgáltatás tanúsítványának ellenőrzésekor. |
|IsEnabled |Bool, az alapértelmezett érték false |Statikus| Engedélyezi vagy letiltja a HttpApplicationGateway. A HttpApplicationGateway alapértelmezés szerint le van tiltva, és ezt a konfigurációt be kell állítani az engedélyezéshez. |
|NumberOfParallelOperations | Uint, alapértelmezett érték 5000 |Statikus|A HTTP-kiszolgáló várólistáján közzétenni kívánt olvasási műveletek száma. Ezzel a beállítással megadható, hogy a HttpGateway milyen egyidejű kérelmeket tud kielégíteni. |
|RemoveServiceResponseHeaders|karakterlánc, az alapértelmezett érték a "date; Server|Statikus|Pontosvesszővel/vesszővel elválasztott lista a válasz fejlécekről, amelyek el lesznek távolítva a szolgáltatás válaszáról; az ügyfélnek való továbbítás előtt. Ha a beállítás üres karakterláncra van beállítva; a szolgáltatás által visszaadott összes fejléc továbbítása. azaz ne írja felül a dátumot és a kiszolgálót |
|ResolveServiceBackoffInterval |Az idő másodpercben, az alapértelmezett érték 5 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan.  Az alapértelmezett visszatartási időközt adja meg, mielőtt újrapróbálkozik a nem sikerült megoldani a szolgáltatási műveletet. |
|SecureOnlyMode|bool, az alapértelmezett érték FALSE|Dinamikus| SecureOnlyMode: True: a fordított proxy csak a biztonságos végpontokat közzétevő szolgáltatásokra fog továbbítani. hamis: a fordított proxy továbbítja a kéréseket a biztonságos/nem biztonságos végpontoknak. További információ: [fordított proxy végpont kiválasztási logikája](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|karakterlánc, az alapértelmezett érték: ""|Dinamikus|A fordított proxy által megbízhatónak tartott távoli tanúsítványok ujjlenyomatai megfelelnek vesszővel tagolt listája. További információért lásd a [biztonságos kapcsolatok fordított proxyval](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services)kapcsolatos témakört. |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/http/ServiceCommonNameAndIssuer

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezés szerint nincs|Dinamikus| A fordított proxy által megbízhatónak ítélt távoli tanúsítványok tulajdonosának neve és kiállítói ujjlenyomata. További információért lásd a [biztonságos kapcsolatok fordított proxyval](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services)kapcsolatos témakört. |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, az alapértelmezett érték 0|Statikus|A BackupRestoreService MinReplicaSetSize |
|PlacementConstraints|karakterlánc, az alapértelmezett érték: ""|Statikus|    A BackupRestore szolgáltatás PlacementConstraints |
|SecretEncryptionCertThumbprint|karakterlánc, az alapértelmezett érték: ""|Dinamikus|A titkos titkosítási X509 tanúsítványának ujjlenyomata |
|SecretEncryptionCertX509StoreName|karakterlánc, javasolt érték: "My" (nincs alapértelmezett) |    Dinamikus|    Ez azt a tanúsítványt jelzi, amelyet a rendszer a Backup Restore Service által használt hitelesítő adatok visszafejtéséhez használt X. 509 tanúsítványtároló titkosításához és visszafejtéséhez használ. |
|TargetReplicaSetSize|int, az alapértelmezett érték 0|Statikus| A BackupRestoreService TargetReplicaSetSize |

## <a name="clustermanager"></a>ClusterManager

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | Bool, az alapértelmezett érték false |Dinamikus|Azt határozza meg, hogy engedélyezett-e az egyéni frissítési rendezési házirendek használata. Ezt a funkciót a kétfázisú verziófrissítés végrehajtásához használhatja. A Service Fabric 6,5 a fürt vagy az alkalmazások frissítése során a frissítési tartományokra vonatkozó rendezési házirend megadásának támogatását adja meg. A támogatott házirendek a következők: numerikus, Lexicographical, ReverseNumeric és ReverseLexicographical. Az alapértelmezett érték a numerikus. Ahhoz, hogy használhassa ezt a funkciót, a ClusterManager/AllowCustomUpgradeSortPolicies nevű fürt jegyzékfájlját igaz értékre kell állítani, ha az SF 6,5-kód frissítését követően egy második konfigurációs verziófrissítési lépésre van szükség. Fontos, hogy ez két fázison történjen, ellenkező esetben a kód frissítése megzavarhatja a frissítési sorrendet az első frissítés során.|
|EnableDefaultServicesUpgrade | Bool, az alapértelmezett érték false |Dinamikus|Az alapértelmezett szolgáltatások verziófrissítésének engedélyezése az alkalmazás frissítése során. A frissítés után felülírják az alapértelmezett szolgáltatások leírását. |
|FabricUpgradeHealthCheckInterval |Az idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Az állapot-ellenőrzés gyakorisága a figyelt háló frissítése közben |
|FabricUpgradeStatusPollInterval |Az idő másodpercben, az alapértelmezett érték 60 |Dinamikus|A háló frissítési állapotának lekérdezési gyakorisága. Ez az érték határozza meg az egyes GetFabricUpgradeProgress-hívások frissítési sebességét |
|ImageBuilderTimeoutBuffer |Az idő másodpercben, alapértelmezés szerint 3 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az az időtartam, ameddig a rendszerkép-készítő adott időtúllépési hibája visszatérhet az ügyfélnek. Ha a puffer túl kicsi; Ezután az ügyfél időtúllépést okoz a kiszolgáló előtt, és általános időtúllépési hibát jelez. |
|InfrastructureTaskHealthCheckRetryTimeout | Az idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az újrapróbálkozások sikertelen állapotának ellenőrzése az infrastruktúra feladatainak feldolgozása közben. Az átadott állapot-ellenőrzés betartásával az időzítő alaphelyzetbe áll. |
|InfrastructureTaskHealthCheckStableDuration | Az idő másodpercben, az alapértelmezett érték: 0|Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Az egymást követő átadott állapot-ellenőrzéseknek az infrastruktúra-feladatok utólagos feldolgozását megelőzően bekövetkező idő elteltével sikeresen befejeződik. A sikertelen állapot-ellenőrzések megfigyelése visszaállítja ezt az időzítőt. |
|InfrastructureTaskHealthCheckWaitDuration |Az idő másodpercben, az alapértelmezett érték: 0|Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Az az időtartam, ameddig az állapot-ellenőrzések megkezdése előtt meg kell várni az infrastruktúra-feladat utólagos feldolgozását. |
|InfrastructureTaskProcessingInterval | Az idő másodpercben, az alapértelmezett érték 10 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az infrastrukturális feladatok feldolgozásának állapotára szolgáló számítógép által használt feldolgozási időköz. |
|MaxCommunicationTimeout |Az idő másodpercben, az alapértelmezett érték 600 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A ClusterManager és más rendszerszolgáltatások közötti belső kommunikáció maximális időtúllépése (pl.: Elnevezési szolgáltatás; Feladatátvételi felügyelő és stb.). Ennek az időkorlátnak kisebbnek kell lennie, mint a globális MaxOperationTimeout (mivel előfordulhat, hogy több kommunikációra van szükség az egyes ügyfél-műveletek rendszerösszetevői között). |
|MaxDataMigrationTimeout |Az idő másodpercben, az alapértelmezett érték 600 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A háló frissítése után a rendszer az adatáttelepítés helyreállítási műveleteinek maximális időtúllépését hajtja végre. |
|MaxOperationRetryDelay |Az idő másodpercben, az alapértelmezett érték 5|Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. A belső újrapróbálkozások maximális késleltetése, ha a rendszer hibákat észlel. |
|MaxOperationTimeout |Az idő másodpercben, az alapértelmezett érték a MaxValue |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. A ClusterManager belüli belső feldolgozási műveletek maximális globális időtúllépése. |
|MaxTimeoutRetryBuffer | Az idő másodpercben, az alapértelmezett érték 600 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Időtúllépések miatti belső újrapróbálkozás esetén a művelet maximális időtúllépése `<Original Time out> + <MaxTimeoutRetryBuffer>` . A MinOperationTimeout-növekmények további időtúllépést adnak hozzá. |
|MinOperationTimeout | Az idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A ClusterManager belüli belső feldolgozási műveletek minimális globális időtúllépése. |
|MinReplicaSetSize |Int, az alapértelmezett érték 3 |Nem engedélyezett|A ClusterManager MinReplicaSetSize. |
|PlacementConstraints | karakterlánc, az alapértelmezett érték: "" |Nem engedélyezett|A ClusterManager PlacementConstraints. |
|QuorumLossWaitDuration |Az idő másodpercben, az alapértelmezett érték a MaxValue |Nem engedélyezett| Másodpercek alatt meg kell adni a TimeSpan. A ClusterManager QuorumLossWaitDuration. |
|ReplicaRestartWaitDuration |Idő másodpercben, alapértelmezett érték (60,0 \* 30)|Nem engedélyezett|Másodpercek alatt meg kell adni a TimeSpan. A ClusterManager ReplicaRestartWaitDuration. |
|ReplicaSetCheckTimeoutRollbackOverride |Az idő másodpercben, az alapértelmezett érték 1200 |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Ha a ReplicaSetCheckTimeout a DWORD maximális értékre van beállítva; ezt követően felülbírálja a konfiguráció értékét a visszaállítás céljára. A rendszer soha nem bírálja felül a továbbításhoz használt értéket. |
|SkipRollbackUpdateDefaultService | Bool, az alapértelmezett érték false |Dinamikus|A CM kihagyja a frissített alapértelmezett szolgáltatások visszaállítását az alkalmazás verziófrissítésének visszaállítása során. |
|StandByReplicaKeepDuration | Idő másodpercben, alapértelmezett érték (3600,0 \* 2)|Nem engedélyezett|Másodpercek alatt meg kell adni a TimeSpan. A ClusterManager StandByReplicaKeepDuration. |
|TargetReplicaSetSize |Int, az alapértelmezett érték 7 |Nem engedélyezett|A ClusterManager TargetReplicaSetSize. |
|UpgradeHealthCheckInterval |Az idő másodpercben, az alapértelmezett érték 60 |Dinamikus|A figyelt alkalmazások frissítésének gyakorisága az állapot ellenőrzésekor |
|UpgradeStatusPollInterval |Az idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Az alkalmazás frissítési állapotának lekérdezési gyakorisága. Ez az érték határozza meg az egyes GetApplicationUpgradeProgress-hívások frissítési sebességét |
|CompleteClientRequest | Bool, az alapértelmezett érték false |Dinamikus| Az ügyfél kérelmének befejezése, ha a CM fogadja el. |

## <a name="common"></a>Közös

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Az idő másodpercben, az alapértelmezett érték 1 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Teljesítmény figyelési időköze A 0 vagy negatív értékre való beállítás letiltja a figyelést. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Paraméter** | **Megengedett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, alapértelmezés szerint nincs|Dinamikus|Meghatározza, hogy a házirend-Töredezettségmentesítés a csomópontok kiürítése után következik-e. Az adott metrikus 0 érték azt jelzi, hogy az SF a frissítési és a tartalék között egyenletesen kell próbálkoznia a csomópontok töredezettségmentesítésével. 1 azt jelzi, hogy a csomópontok töredezettségmentesítése szükséges |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Paraméter** | **Megengedett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, alapértelmezés szerint nincs|Dinamikus|Meghatározza azt a metrikát, amelyet a töredezettségmentesítéshez kell használni, nem pedig a terheléselosztáshoz. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Paraméter** | **Megengedett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, alapértelmezés szerint nincs|Dinamikus|Meghatározza, hogy hány szabad csomópontra van szükség a fürt detöredezettségének megadásához, ha a százalékot a [0,0-1,0] tartományban vagy üres csomópontok száma mezőben adja meg, >= 1,0 |

## <a name="diagnostics"></a>Diagnosztika

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Bool, az alapértelmezett érték TRUE (igaz) | Dinamikus | Zárja ki azokat a HTTP-kérelmeket, amelyek nem befolyásolják a fürt állapotát a naplózásból. Jelenleg a rendszer csak a "GET" típusú kérelmeket zárja ki; Ez azonban változhat. |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, az alapértelmezett érték TRUE (igaz) | Dinamikus |Azt határozza meg, hogy szükséges-e megszemélyesítés, ha a diagnosztikai tárolókat az alkalmazás nevében éri el. |
|AppEtwTraceDeletionAgeInDays |Int, az alapértelmezett érték 3 | Dinamikus |Ennyi nap elteltével töröljük az Application ETW-nyomkövetést tartalmazó régi ETL-fájlokat. |
|ApplicationLogsFormatVersion |Int, az alapértelmezett érték 0 | Dinamikus |Az Application logs formátumának verziója. A támogatott értékek: 0 és 1. Az 1. verzió több mezőt is tartalmaz a ETW-esemény rekordjából, mint a 0. verzió. |
|AuditHttpRequests |Bool, az alapértelmezett érték false | Dinamikus | A HTTP-naplózás be-és kikapcsolása. A naplózás célja, hogy megtekintse a fürtön végrehajtott tevékenységeket. beleértve a kérelmet kezdeményező személyeket is. Vegye figyelembe, hogy ez a legjobb kísérlet a naplózásra; és a nyomkövetés elvesztése is előfordulhat. A "user" hitelesítéssel rendelkező HTTP-kérelmek nincsenek rögzítve. |
|CaptureHttpTelemetry|Bool, az alapértelmezett érték TRUE (igaz) | Dinamikus | A HTTP-telemetria be-vagy kikapcsolása. A Service Fabric telemetria célja, hogy a telemetria-adat rögzíthető legyen a jövőbeli munka megtervezése és a problémás területek azonosítása érdekében. A telemetria nem rögzíti a személyes adatok vagy a kérés törzsét. A telemetria csak akkor rögzíti az összes HTTP-kérelmet, ha másként van konfigurálva. |
|ClusterId |Sztring | Dinamikus |A fürt egyedi azonosítója. Ez a fürt létrehozásakor jön létre. |
|ConsumerInstances |Sztring | Dinamikus |A DCA-fogyasztói példányok listája. |
|DiskFullSafetySpaceInMB |Int, alapértelmezett érték 1024 | Dinamikus |A fennmaradó lemezterület MB-ban, a DCA általi használat elleni védelem érdekében. |
|EnableCircularTraceSession |Bool, az alapértelmezett érték false | Statikus |A jelző jelzi, hogy a körkörös nyomkövetési munkameneteket kell-e használni. |
|EnablePlatformEventsFileSink |Bool, az alapértelmezett érték false | Statikus |A lemezre írt platform eseményeinek engedélyezése/letiltása |
|EnableTelemetry |Bool, az alapértelmezett érték TRUE (igaz) | Dinamikus |A telemetria engedélyezése vagy letiltása folyamatban van. |
|FailuresOnlyHttpTelemetry | Bool, az alapértelmezett érték false | Dinamikus | Ha engedélyezve van a HTTP-telemetria rögzítése; csak a sikertelen kérelmek rögzítése. Ez segít csökkenteni a telemetria által generált események számát. |
|HttpTelemetryCapturePercentage | int, alapértelmezett érték 50 | Dinamikus | Ha engedélyezve van a HTTP-telemetria rögzítése; csak a kérések véletlenszerű százalékos arányának rögzítése. Ez segít csökkenteni a telemetria által generált események számát. |
|MaxDiskQuotaInMB |Int, alapértelmezett érték 65536 | Dinamikus |Windows-és linuxos háló-naplófájlok esetén a lemezkvóta MB-ban. |
|ProducerInstances |Sztring | Dinamikus |A DCA-előállító példányainak listája. |

## <a name="dnsservice"></a>DnsService
| **Paraméter** | **Megengedett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|bool, az alapértelmezett érték FALSE|Statikus|A particionált szolgáltatásokra vonatkozó DNS-lekérdezések támogatásának engedélyezésére szolgáló jelző. A szolgáltatás alapértelmezés szerint ki van kapcsolva. További információ: [Service FABRIC DNS szolgáltatás.](service-fabric-dnsservice.md)|
|InstanceCount|int, az alapértelmezett érték-1|Statikus|Az alapértelmezett érték a-1, ami azt jelenti, hogy a DnsService minden csomóponton fut. Ahhoz, hogy a DnsService jól ismert 53-as portot használjon, a beépített-nek 1-re kell állítania, így nem lehet több példánya ugyanazon a gépen.|
|IsEnabled|bool, az alapértelmezett érték FALSE|Statikus|Engedélyezi vagy letiltja a DnsService. A DnsService alapértelmezés szerint le van tiltva, és ezt a konfigurációt be kell állítani az engedélyezéshez. |
|PartitionPrefix|karakterlánc, az alapértelmezett érték a "--"|Statikus|A particionált szolgáltatásokhoz tartozó DNS-lekérdezésekben megadja a partíció-előtag karakterláncának értékét. Az érték: <ul><li>RFC-kompatibilisnek kell lennie, mert egy DNS-lekérdezés része lesz.</li><li>A nem tartalmazhat pontot ("."), mert a pont nem zavarja a DNS-utótag viselkedését.</li><li>Nem lehet hosszabb 5 karakternél.</li><li>Nem lehet üres karakterlánc.</li><li>Ha a PartitionPrefix beállítás felülbírálva van, akkor a PartitionSuffix felülbírálva kell lennie, és fordítva.</li></ul>További információ: [Service Fabric DNS-szolgáltatás.](service-fabric-dnsservice.md).|
|PartitionSuffix|karakterlánc, az alapértelmezett érték: ""|Statikus|A particionált szolgáltatások DNS-lekérdezései esetében szabályozza a partíció utótagjának karakterlánc-értékét. Az érték: <ul><li>RFC-kompatibilisnek kell lennie, mert egy DNS-lekérdezés része lesz.</li><li>A nem tartalmazhat pontot ("."), mert a pont nem zavarja a DNS-utótag viselkedését.</li><li>Nem lehet hosszabb 5 karakternél.</li><li>Ha a PartitionPrefix beállítás felülbírálva van, akkor a PartitionSuffix felülbírálva kell lennie, és fordítva.</li></ul>További információ: [Service Fabric DNS-szolgáltatás.](service-fabric-dnsservice.md). |
|RetryTransientFabricErrors|Bool, az alapértelmezett érték TRUE (igaz)|Statikus|A beállítás szabályozza az újrapróbálkozási képességeket Service Fabric API-k DnsService való meghívásakor. Ha ez a beállítás engedélyezve van, az újrapróbálkozások akár 3 alkalommal is újrapróbálkoznak, ha átmeneti hiba történik.|

## <a name="eventstoreservice"></a>EventStoreService

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, az alapértelmezett érték 0|Statikus|A EventStore szolgáltatás MinReplicaSetSize |
|PlacementConstraints|karakterlánc, az alapértelmezett érték: ""|Statikus|    A EventStore szolgáltatás PlacementConstraints |
|TargetReplicaSetSize|int, az alapértelmezett érték 0|Statikus| A EventStore szolgáltatás TargetReplicaSetSize |

## <a name="fabricclient"></a>FabricClient

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Az idő másodpercben, az alapértelmezett érték 2 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A kapcsolat időtúllépési intervalluma minden alkalommal, amikor az ügyfél megpróbál megnyitni egy kapcsolódást az átjáróhoz.|
|HealthOperationTimeout |Az idő másodpercben, az alapértelmezett érték 120 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az Health Managernek elküldött jelentési üzenet időtúllépése. |
|HealthReportRetrySendInterval |Az idő másodpercben, az alapértelmezett érték 30, minimum 1 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az az időtartam, amikor a jelentéskészítési összetevő újraküldi a halmozott állapot-jelentéseket az Health Managernek. |
|HealthReportSendInterval |Az idő másodpercben, az alapértelmezett érték 30 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az az időköz, amelyen a jelentéskészítő összetevő halmozott állapotú jelentéseket küld az Health Managernek. |
|KeepAliveIntervalInSeconds |Int, az alapértelmezett érték 20 |Statikus|Az a időköz, amikor a FabricClient-átvitel életben tartási üzeneteket küld az átjárónak. 0; esetén a életben tartás le van tiltva. Pozitív értéknek kell lennie. |
|MaxFileSenderThreads |Uint, az alapértelmezett érték 10 |Statikus|A párhuzamosan továbbított fájlok maximális száma. |
|NodeAddresses |karakterlánc, az alapértelmezett érték: "" |Statikus|Különböző csomópontokon található címek (kapcsolódási karakterláncok) gyűjteménye, amelyek a elnevezési szolgáltatás való kommunikációhoz használhatók. Az ügyfél kezdetben véletlenszerűen választja ki az egyik címet. Ha egynél több kapcsolati sztring van megadva, és a kapcsolat meghiúsul egy kommunikációs vagy időtúllépési hiba miatt; az ügyfél a következő címnek egymás utáni használatára vált. Az újrapróbálkozási szemantika részleteiért tekintse meg a elnevezési szolgáltatás-újrapróbálkozási szakaszt. |
|PartitionLocationCacheLimit |Int, alapértelmezett érték 100000 |Statikus|A szolgáltatás-feloldáshoz gyorsítótárazott partíciók száma (legfeljebb 0 értékre van állítva). |
|RetryBackoffInterval |Az idő másodpercben, alapértelmezés szerint 3 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A művelet újbóli megkísérlése előtt a visszatartási időköz. |
|ServiceChangePollInterval |Az idő másodpercben, az alapértelmezett érték 120 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az egymást követő lekérdezésekben az ügyfél és az átjáró közötti adatváltozások közötti időköz a regisztrált szolgáltatások változási értesítéseinek visszahívása esetén. |

## <a name="fabrichost"></a>Hálóbeli

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, az alapértelmezett érték 10 |Dinamikus|Ez a maximális szám, amely után a rendszer újrapróbálkozik a sikertelen aktiválással. |
|ActivationMaxRetryInterval |Az idő másodpercben, az alapértelmezett érték 300 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az aktiválás maximális újrapróbálkozási időköze. Minden folyamatos hiba esetén az újrapróbálkozási időköz kiszámítása percben (ActivationMaxRetryInterval; Folyamatos meghibásodások száma * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Az idő másodpercben, az alapértelmezett érték 5 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Leállítási időköz minden aktiválási hiba esetén; minden folyamatos aktiválási hiba esetén a rendszer újra megkísérli az aktiválást a MaxActivationFailureCount. Az újrapróbálkozási időköz minden próbálkozáskor a folyamatos aktiválási hiba és az aktiválási visszatartási időköz szorzata. |
|EnableRestartManagement |Bool, az alapértelmezett érték false |Dinamikus|Ez a kiszolgáló újraindításának engedélyezése. |
|EnableServiceFabricAutomaticUpdates |Bool, az alapértelmezett érték false |Dinamikus|A háló automatikus frissítésének engedélyezése Windows Updateon keresztül. |
|EnableServiceFabricBaseUpgrade |Bool, az alapértelmezett érték false |Dinamikus|Ez a kiszolgáló alapszintű frissítésének engedélyezése. |
|FailureReportingExpeditedReportingIntervalEnabled | Bool, az alapértelmezett érték TRUE (igaz) | Statikus | Gyorsabb feltöltési sebességet tesz lehetővé a DCA, ha a Hálóbeli nem jelentési módban van. |
|FailureReportingTimeout | TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (60) | Statikus |Másodpercek alatt meg kell adni a TimeSpan. A DCA sikertelen jelentéskészítésének időtúllépése az esetben, ha a Hálóbeli korai fázisú indítási hibát észlel. | 
|RunDCAOnStartupFailure | Bool, az alapértelmezett érték TRUE (igaz) | Statikus |Meghatározza, hogy el kell-e indítani a DCA a naplók feltöltéséhez, amikor a Hálóbeli indításakor problémák léptek fel. | 
|StartTimeout |Az idő másodpercben, az alapértelmezett érték 300 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Időtúllépés a fabricactivationmanager indításakor. |
|StopTimeout |Az idő másodpercben, az alapértelmezett érték 300 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az üzemeltetett szolgáltatás aktiválásának időtúllépése; inaktiválás és frissítés. |

## <a name="fabricnode"></a>FabricNode

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |string (alapértelmezett érték: "FindByThumbprint") |Dinamikus|Azt jelzi, hogyan lehet a ClientAuthX509StoreName által megadott tárolóban megkeresni a tanúsítványokat a következő támogatott érték szerint: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |karakterlánc, az alapértelmezett érték: "" | Dinamikus|Keresési szűrő értéke, amely az alapértelmezett rendszergazdai szerepkör FabricClient tartozó tanúsítvány megkeresésére szolgál. |
|ClientAuthX509FindValueSecondary |karakterlánc, az alapértelmezett érték: "" |Dinamikus|Keresési szűrő értéke, amely az alapértelmezett rendszergazdai szerepkör FabricClient tartozó tanúsítvány megkeresésére szolgál. |
|ClientAuthX509StoreName |karakterlánc, az alapértelmezett érték a "My" |Dinamikus|Annak az X. 509 tanúsítványtárolónak a neve, amely az alapértelmezett rendszergazdai szerepkör FabricClient tanúsítványát tartalmazza. |
|ClusterX509FindType |string (alapértelmezett érték: "FindByThumbprint") |Dinamikus|Azt jelzi, hogyan lehet a ClusterX509StoreName által megadott tárolóban megkeresni a fürt tanúsítványát: "FindByThumbprint"; "FindBySubjectName" és "FindBySubjectName"; Ha több egyezés van; a rendszer a legtávolabbi lejáratú egyet használja. |
|ClusterX509FindValue |karakterlánc, az alapértelmezett érték: "" |Dinamikus|A fürt tanúsítványának megkereséséhez használt keresési szűrő értéke. |
|ClusterX509FindValueSecondary |karakterlánc, az alapértelmezett érték: "" |Dinamikus|A fürt tanúsítványának megkereséséhez használt keresési szűrő értéke. |
|ClusterX509StoreName |karakterlánc, az alapértelmezett érték a "My" |Dinamikus|Az X. 509 tanúsítványtároló neve, amely a fürtön belüli kommunikáció biztonságossá tételére szolgáló fürtözött tanúsítványt tartalmaz. |
|EndApplicationPortRange |Int, az alapértelmezett érték 0 |Statikus|Az üzemeltetési alrendszer által kezelt alkalmazás-portok végpontja (nincs befogadó). Kötelező, ha a EndpointFilteringEnabled a gazdagépen igaz. |
|ServerAuthX509FindType |string (alapértelmezett érték: "FindByThumbprint") |Dinamikus|Azt jelzi, hogyan kereshet meg a kiszolgálói tanúsítvány a ServerAuthX509StoreName által megadott tárolóban, a következő támogatott értékkel: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |karakterlánc, az alapértelmezett érték: "" |Dinamikus|A kiszolgálói tanúsítvány megkereséséhez használt keresési szűrő értéke. |
|ServerAuthX509FindValueSecondary |karakterlánc, az alapértelmezett érték: "" |Dinamikus|A kiszolgálói tanúsítvány megkereséséhez használt keresési szűrő értéke. |
|ServerAuthX509StoreName |karakterlánc, az alapértelmezett érték a "My" |Dinamikus|Annak az X. 509 tanúsítványtárolónak a neve, amely a főétel szolgáltatás kiszolgálói tanúsítványát tartalmazza. |
|StartApplicationPortRange |Int, az alapértelmezett érték 0 |Statikus|Az üzemeltetési alrendszer által kezelt alkalmazás-portok kezdete. Kötelező, ha a EndpointFilteringEnabled a gazdagépen igaz. |
|StateTraceInterval |Az idő másodpercben, az alapértelmezett érték 300 |Statikus|Másodpercek alatt meg kell adni a TimeSpan. A csomópontok állapotának nyomkövetési időköze az egyes csomópontokon és az FM/FMM csomópontokon. |
|UserRoleClientX509FindType |string (alapértelmezett érték: "FindByThumbprint") |Dinamikus|Azt jelzi, hogyan lehet a UserRoleClientX509StoreName által megadott tárolóban megkeresni a tanúsítványokat a következő támogatott érték szerint: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |karakterlánc, az alapértelmezett érték: "" |Dinamikus|Keresési szűrő értéke, amely az alapértelmezett felhasználói szerepkör FabricClient tartozó tanúsítvány megkeresésére szolgál. |
|UserRoleClientX509FindValueSecondary |karakterlánc, az alapértelmezett érték: "" |Dinamikus|Keresési szűrő értéke, amely az alapértelmezett felhasználói szerepkör FabricClient tartozó tanúsítvány megkeresésére szolgál. |
|UserRoleClientX509StoreName |karakterlánc, az alapértelmezett érték a "My" |Dinamikus|Annak az X. 509 tanúsítványtárolónak a neve, amely az alapértelmezett felhasználói szerepkör FabricClient tanúsítványát tartalmazza. |

## <a name="failovermanager"></a>FailoverManager

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, az alapértelmezett érték FALSE |Dinamikus|Annak jelzése, hogy engedélyezett-e a csomópont állapotának eltávolítása a magok csomópontja számára |
|BuildReplicaTimeLimit|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (3600)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Állapot-nyilvántartó replika létrehozásának határideje; a figyelmeztetési állapot jelentésének elindítását követően |
|ClusterPauseThreshold|int, az alapértelmezett érték 1|Dinamikus|Ha a rendszer csomópontjainak száma az alábbi érték alá kerül, akkor az elhelyezés; terheléselosztás; és a feladatátvétel le van állítva. |
|CreateInstanceTimeLimit|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (300)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Állapot nélküli példány létrehozásának határideje; a figyelmeztetési állapot jelentésének elindítását követően |
|ExpectedClusterSize|int, az alapértelmezett érték 1|Dinamikus|A fürt első indításakor; az FM megvárja, amíg a több csomópont jelentést készít a többi szolgáltatás megkezdése előtt. például a rendszerszolgáltatások, például a Névadás. Ennek az értéknek a növelésével megnövekszik a fürt elindításához szükséges idő. azonban megakadályozza, hogy a korai csomópontok túlterhelve legyenek, és a további átlépések is szükségesek lesznek, ahogy a további csomópontok online állapotba kerülnek. Ezt az értéket általában úgy kell beállítani, hogy a fürt kezdeti méretének néhány kis hányada legyen. |
|ExpectedNodeDeactivationDuration|TimeSpan, az alapértelmezett érték gyakori:: TimeSpan:: FromSeconds (60.0 \* 30)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Ez az a várható időtartam, amikor egy csomópont inaktiválást végez a ben. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, az alapértelmezett érték gyakori:: TimeSpan:: FromSeconds (60.0 \* 30)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A csomópontnak a Windows Fabric frissítése során történő frissítésének várható időtartama. |
|ExpectedReplicaUpgradeDuration|TimeSpan, az alapértelmezett érték gyakori:: TimeSpan:: FromSeconds (60.0 \* 30)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Ez az az időtartam, ameddig az összes replikát frissíteni kell egy csomóponton az alkalmazás frissítése során. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, az alapértelmezett érték TRUE (igaz)|Dinamikus|Ha True értékre van állítva; a cél másodpéldány-készlet 1. méretével rendelkező replikák a frissítés közben is áthelyezhetők. |
|MinReplicaSetSize|int, az alapértelmezett érték 3|Nem engedélyezett|Ez a replikakészlet minimális mérete az FM számára. Ha az aktív FM-replikák száma ezen érték alá csökken; az FM elutasítja a fürt változásait egészen addig, amíg a replikák minimális száma helyre nem kerül. |
|PlacementConstraints|karakterlánc, az alapértelmezett érték: ""|Nem engedélyezett|A feladatátvételi kezelő replikáinak elhelyezésére vonatkozó korlátozások |
|PlacementTimeLimit|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (600)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A cél replikák számának elérésére vonatkozó időkorlát; a figyelmeztetési állapot jelentésének elindítását követően |
|QuorumLossWaitDuration |Az idő másodpercben, az alapértelmezett érték a MaxValue |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Ez a maximális időtartam, amellyel a partíciók kvórum elvesztésének állapotában engedélyezhető. Ha a partíció továbbra is a kvórum elvesztése után következik be az időtartam után; a partíció helyreállítása a kvórum elvesztésével történik azáltal, hogy a leállított replikák elvesznek. Vegye figyelembe, hogy ez adatvesztést eredményezhet. |
|ReconfigurationTimeLimit|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (300)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az újrakonfigurálás határideje; a figyelmeztetési állapot jelentésének elindítását követően |
|ReplicaRestartWaitDuration|TimeSpan, az alapértelmezett érték gyakori:: TimeSpan:: FromSeconds (60.0 \* 30)|Nem engedélyezett|Másodpercek alatt meg kell adni a TimeSpan. Ez a FMService ReplicaRestartWaitDuration |
| SeedNodeQuorumAdditionalBufferNodes | int, az alapértelmezett érték 0 | Dinamikus | A felvenni kívánt mag-csomópontok puffere (a vetőmag-csomópontok kvórumával együtt) az FM-nek lehetővé kell tennie, hogy legfeljebb (totalNumSeedNodes-(seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) vetőmag-csomópont álljon le. |
|StandByReplicaKeepDuration|TimeSpan, az alapértelmezett érték gyakori:: TimeSpan:: FromSeconds (3600.0 \* 24 \* 7)|Nem engedélyezett|Másodpercek alatt meg kell adni a TimeSpan. Ez a FMService StandByReplicaKeepDuration |
|TargetReplicaSetSize|int, az alapértelmezett érték 7|Nem engedélyezett|A Windows Fabric által karbantartott FM-replikák száma. A nagyobb szám nagyobb megbízhatóságot eredményez az FM-adatmennyiségnél; kis teljesítményű kompromisszummal. |
|UserMaxStandByReplicaCount |Int, az alapértelmezett érték 1 |Dinamikus|A rendszer által a felhasználói szolgáltatások számára megmaradó készenléti replikák alapértelmezett maximális száma. |
|UserReplicaRestartWaitDuration |Az idő másodpercben, az alapértelmezett érték 60,0 \* 30 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Ha egy megőrzött replika leáll; Windows Fabric vár erre az időtartamra a replika számára, hogy új helyettesítő replikák létrehozása előtt készítsen biztonsági mentést (ami az állapot másolatát igényli). |
|UserStandByReplicaKeepDuration |Az idő másodpercben, az alapértelmezett érték 3600,0 \* 24 \* 7 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Ha egy megőrzött replika vissza fog térni a leállási állapotból; lehetséges, hogy már lecserélték. Ez az időzítő azt határozza meg, hogy az FM mennyi ideig tart a készenléti replika számára az eldobás előtt. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, alapértelmezett érték 604800 |Statikus| Ez körülbelül ennyi ideig tart a terminál-állapotú műveletek megőrzése. Ez a StoredActionCleanupIntervalInSeconds is függ; mivel a tisztítási munkák csak ezen az intervallumon hajthatók végre. 604800 7 nap. |
|DataLossCheckPollIntervalInSeconds|int, az alapértelmezett érték 5|Statikus|Ez az idő az ellenőrzések között, amelyet a rendszer végrehajt, miközben adatvesztésre vár. Az adatvesztési számnak a belső iteráción belüli ellenőrzése DataLossCheckWaitDurationInSeconds/ez. |
|DataLossCheckWaitDurationInSeconds|int, az alapértelmezett érték 25|Statikus|A teljes időtartam; másodpercek alatt; a rendszer megvárja, amíg az adatvesztés történik. Ez belsőleg használatos a StartPartitionDataLossAsync () API meghívásakor. |
|MinReplicaSetSize |Int, az alapértelmezett érték 0 |Statikus|A FaultAnalysisService MinReplicaSetSize. |
|PlacementConstraints | karakterlánc, az alapértelmezett érték: ""|Statikus| A FaultAnalysisService PlacementConstraints. |
|QuorumLossWaitDuration | Az idő másodpercben, az alapértelmezett érték a MaxValue |Statikus|Másodpercek alatt meg kell adni a TimeSpan. A FaultAnalysisService QuorumLossWaitDuration. |
|ReplicaDropWaitDurationInSeconds|int, alapértelmezett érték 600|Statikus|Ezt a paramétert az adatvesztési API meghívásakor használja a rendszer. Azt határozza meg, hogy a rendszer mennyi ideig várjon, amíg egy replika el fog dobni az eltávolítás után a replika belső meghívása után. |
|ReplicaRestartWaitDuration |Az idő másodpercben, az alapértelmezett érték 60 perc|Statikus|Másodpercek alatt meg kell adni a TimeSpan. A FaultAnalysisService ReplicaRestartWaitDuration. |
|StandByReplicaKeepDuration| Az idő másodpercben, az alapértelmezett érték (60 *24* 7) perc |Statikus|Másodpercek alatt meg kell adni a TimeSpan. A FaultAnalysisService StandByReplicaKeepDuration. |
|StoredActionCleanupIntervalInSeconds | Int, alapértelmezett érték 3600 |Statikus|Ez azt mutatja, hogy a tároló milyen gyakran lesz törölve. Csak a terminál állapotában lévő műveletek; és ez legalább CompletedActionKeepDurationInSeconds ezelőtt elvégezhető. |
|StoredChaosEventCleanupIntervalInSeconds | Int, alapértelmezett érték 3600 |Statikus|Milyen gyakran naplózzák a rendszer a tárolót a tisztításhoz. Ha az események száma meghaladja a 30000-et; a tisztítás beindul. |
|TargetReplicaSetSize |Int, az alapértelmezett érték 0 |Statikus|NOT_PLATFORM_UNIX_START a FaultAnalysisService TargetReplicaSetSize. |

## <a name="federation"></a>Összevonás

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|LeaseDuration |Az idő másodpercben, az alapértelmezett érték 30 |Dinamikus|Az időtartam, ameddig egy bérlet egy csomópont és a szomszédai között tart. |
|LeaseDurationAcrossFaultDomain |Az idő másodpercben, az alapértelmezett érték 30 |Dinamikus|Az az időtartam, ameddig egy bérlet a csomópontok és szomszédaik között a tartalék tartományok között tart. |

## <a name="filestoreservice"></a>FileStoreService

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Bool, az alapértelmezett érték TRUE (igaz)|Dinamikus|A konfigurációs beállítással meghatározhatja, hogy a file Store szolgáltatás elfogadja-e a darabolásos fájl feltöltését, vagy nem a másolási alkalmazáscsomag során. |
|AnonymousAccessEnabled | Bool, az alapértelmezett érték TRUE (igaz) |Statikus|A FileStoreService-megosztások névtelen hozzáférésének engedélyezése/letiltása. |
|CommonName1Ntlmx509CommonName|karakterlánc, az alapértelmezett érték: ""|Statikus| Az NTLM-hitelesítés használatakor a CommonName1NtlmPasswordSecret HMAC létrehozásához használt X509-tanúsítvány köznapi neve |
|CommonName1Ntlmx509StoreLocation|string (alapértelmezett érték: "LocalMachine")|Statikus|A CommonName1NtlmPasswordSecret HMAC létrehozásához használt X509-tanúsítvány tárolási helye az NTLM-hitelesítés használatakor |
|CommonName1Ntlmx509StoreName|karakterlánc, az alapértelmezett érték a "MY"| Statikus|Az NTLM-hitelesítés használatakor a CommonName1NtlmPasswordSecret HMAC létrehozásához használt X509-tanúsítvány tárolójának neve |
|CommonName2Ntlmx509CommonName|karakterlánc, az alapértelmezett érték: ""|Statikus|Az NTLM-hitelesítés használatakor a CommonName2NtlmPasswordSecret HMAC létrehozásához használt X509-tanúsítvány köznapi neve |
|CommonName2Ntlmx509StoreLocation|string (alapértelmezett érték: "LocalMachine")| Statikus|A CommonName2NtlmPasswordSecret HMAC létrehozásához használt X509-tanúsítvány tárolási helye az NTLM-hitelesítés használatakor |
|CommonName2Ntlmx509StoreName|karakterlánc, az alapértelmezett érték a "MY"|Statikus| Az NTLM-hitelesítés használatakor a CommonName2NtlmPasswordSecret HMAC létrehozásához használt X509-tanúsítvány tárolójának neve |
|CommonNameNtlmPasswordSecret|SecureString, az alapértelmezett érték a common:: SecureString ("")| Statikus|Az NTLM-hitelesítés használatakor ugyanazt a jelszót használó, a mag által létrehozott jelszó titkos kulcsa |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromMinutes (5)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A lemezterület ellenőrzésének időintervalluma a jelentéskészítési állapot eseményeihez, ha a lemez nincs elég szabad terület. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, az alapértelmezett érték gyakori:: TimeSpan:: FromMinutes (15)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A lemezterület ellenőrzésének időintervalluma a jelentéskészítési állapot eseményeihez, ha elegendő lemezterület van a lemezen. |
|EnableImageStoreHealthReporting |bool, az alapértelmezett érték TRUE (igaz)    |Statikus|Annak megállapítása, hogy a file Store szolgáltatásnak jelentenie kell-e az állapotát. |
|FreeDiskSpaceNotificationSizeInKB|Int64, alapértelmezett értéke 25 \* 1024 |Dinamikus|Annak a szabad lemezterületnek a mérete, amely alatt az állapot figyelmeztetése megjelenhet. A konfiguráció és a FreeDiskSpaceNotificationThresholdPercentage-konfiguráció minimális értéke az állapot-figyelmeztetés küldésének meghatározására szolgál. |
|FreeDiskSpaceNotificationThresholdPercentage|dupla, alapértelmezett érték 0,02 |Dinamikus|A szabad lemezterület százalékos aránya, amely alatt az állapotra vonatkozó figyelmeztetés jelenhet meg. A konfiguráció és a FreeDiskSpaceNotificationInMB konfiguráció minimális értéke az állapot-figyelmeztetés küldésének meghatározására szolgál. |
|GenerateV1CommonNameAccount| bool, az alapértelmezett érték TRUE (igaz)|Statikus|Megadja, hogy a rendszer létrehozzon-e egy fiókot a Felhasználónév v1 generálási algoritmussal. A Service Fabric 6,1-es verziótól kezdődően; mindig létrejön egy v2-generációval rendelkező fiók. A v1-fiók a v2-generációt nem támogató verziókról/verzióról történő frissítéshez szükséges (6,1 előtt).|
|MaxCopyOperationThreads | Uint, az alapértelmezett érték 0 |Dinamikus| A másodlagos által az elsődlegestől másolható párhuzamos fájlok maximális száma. "0" = = magok száma. |
|MaxFileOperationThreads | Uint, alapértelmezett érték 100 |Statikus| Az elsődlegesen a FileOperations (másolás/áthelyezés) végrehajtásához engedélyezett párhuzamos szálak maximális száma. "0" = = magok száma. |
|MaxRequestProcessingThreads | Uint, alapértelmezett érték 200 |Statikus|Az elsődlegesen a kérelmek feldolgozására jogosult párhuzamos szálak maximális száma. "0" = = magok száma. |
|MaxSecondaryFileCopyFailureThreshold | Uint, az alapértelmezett érték 25|Dinamikus|A másoláskor a rendszer a másodpéldány-újrapróbálkozások maximális számát adja meg a másodlagos feladás előtt. |
|MaxStoreOperations | Uint, alapértelmezett érték 4096 |Statikus|Az elsődlegesen engedélyezett párhuzamos tárolási tranzakciós műveletek maximális száma. "0" = = magok száma. |
|NamingOperationTimeout |Az idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az elnevezési művelet végrehajtása időtúllépéssel. |
|PrimaryAccountNTLMPasswordSecret | SecureString, az alapértelmezett érték üres |Statikus| Az NTLM-hitelesítés használatakor a magot használó, azonos jelszót generáló jelszó titkos kulcsa. |
|PrimaryAccountNTLMX509StoreLocation | string (alapértelmezett érték: "LocalMachine")|Statikus| Az X509-tanúsítvány tárolási helye, amely az NTLM-hitelesítés használatakor a PrimaryAccountNTLMPasswordSecret HMAC létrehozásához használatos. |
|PrimaryAccountNTLMX509StoreName | karakterlánc, az alapértelmezett érték a "MY"|Statikus| Az X509-tanúsítvány tárolójának neve, amely az NTLM-hitelesítés használatakor a PrimaryAccountNTLMPasswordSecret HMAC létrehozásához használatos. |
|PrimaryAccountNTLMX509Thumbprint | karakterlánc, az alapértelmezett érték: ""|Statikus|Az X509-Tanúsítvány ujjlenyomata, amely az NTLM-hitelesítés használatakor a PrimaryAccountNTLMPasswordSecret HMAC létrehozásához használatos. |
|PrimaryAccountType | karakterlánc, az alapértelmezett érték: "" |Statikus|A rendszerbiztonsági tag elsődleges AccountType az FileStoreService-megosztások ACL-jéhez. |
|PrimaryAccountUserName | karakterlánc, az alapértelmezett érték: "" |Statikus|Az FileStoreService-megosztások ACL-jéhez tartozó elsődleges fiók felhasználóneve. |
|PrimaryAccountUserPassword | SecureString, az alapértelmezett érték üres |Statikus|Az FileStoreService-megosztások ACL-címe a rendszerbiztonsági tag elsődleges fiókjának jelszava. |
|QueryOperationTimeout | Az idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A lekérdezési művelet végrehajtása közbeni időtúllépés. |
|SecondaryAccountNTLMPasswordSecret | SecureString, az alapértelmezett érték üres |Statikus| Az NTLM-hitelesítés használatakor a magot használó, azonos jelszót generáló jelszó titkos kulcsa. |
|SecondaryAccountNTLMX509StoreLocation | string (alapértelmezett érték: "LocalMachine") |Statikus|Az X509-tanúsítvány tárolási helye, amely az NTLM-hitelesítés használatakor a SecondaryAccountNTLMPasswordSecret HMAC létrehozásához használatos. |
|SecondaryAccountNTLMX509StoreName | karakterlánc, az alapértelmezett érték a "MY" |Statikus|Az X509-tanúsítvány tárolójának neve, amely az NTLM-hitelesítés használatakor a SecondaryAccountNTLMPasswordSecret HMAC létrehozásához használatos. |
|SecondaryAccountNTLMX509Thumbprint | karakterlánc, az alapértelmezett érték: ""| Statikus|Az X509-Tanúsítvány ujjlenyomata, amely az NTLM-hitelesítés használatakor a SecondaryAccountNTLMPasswordSecret HMAC létrehozásához használatos. |
|SecondaryAccountType | karakterlánc, az alapértelmezett érték: ""|Statikus| A rendszerbiztonsági tag másodlagos AccountType az FileStoreService-megosztások ACL-jéhez. |
|SecondaryAccountUserName | karakterlánc, az alapértelmezett érték: ""| Statikus|A FileStoreService-megosztások ACL-címe a rendszerbiztonsági tag másodlagos fiókjának felhasználóneve. |
|SecondaryAccountUserPassword | SecureString, az alapértelmezett érték üres |Statikus|A FileStoreService-megosztások ACL-címe a rendszerbiztonsági tag másodlagos fiókjának jelszava. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, alapértelmezett érték 500|Dinamikus|A fájlmásolás újrapróbálkozási késleltetése (ezredmásodpercben).|
|UseChunkContentInTransportMessage|bool, az alapértelmezett érték TRUE (igaz)|Dinamikus|A v 6.4-ben bevezetett, a feltöltési protokoll új verziójának használatára vonatkozó jelző. Ez a protokoll-verzió a Service Fabric Transport használatával fájlokat tölt fel a rendszerkép-tárolóba, amely jobb teljesítményt nyújt, mint a korábbi verziókban használt SMB protokoll. |

## <a name="healthmanager"></a>HealthManager

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Bool, az alapértelmezett érték false |Statikus|A fürt állapotának kiértékelésére szolgáló házirend: az alkalmazás típusának kiértékelésének engedélyezése. |
|MaxSuggestedNumberOfEntityHealthReports|Int, alapértelmezett érték 100 |Dinamikus|Azon állapot-jelentések maximális száma, amelyekkel az entitások felvehetik a figyelmet a watchdog állapot-jelentési logikája miatt. Minden egyes Health-entitásnak viszonylag kis számú állapotjelentést kell megtörténnie. Ha a jelentések száma túllépi ezt a számot, problémák merülhetnek fel a watchdog megvalósításával kapcsolatban. Az entitás kiértékelése során a túl sok jelentést tartalmazó entitás figyelmeztetési állapotú jelentéssel van megjelölve. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, az alapértelmezett érték false |Statikus|A fürt állapotának kiértékelésére szolgáló házirend: a figyelmeztetések hibákként vannak kezelve. |
|MaxPercentUnhealthyApplications | Int, az alapértelmezett érték 0 |Statikus|A fürt állapotának kiértékelésére szolgáló házirend: a fürt kifogástalan állapotának maximális százaléka. |
|MaxPercentUnhealthyNodes | Int, az alapértelmezett érték 0 |Statikus|A fürt állapotának kiértékelésére szolgáló házirend: a fürt kifogástalan állapotának maximális százaléka. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, az alapértelmezett érték 10|Statikus|A fürt frissítése állapotának kiértékelésére szolgáló házirend: a fürt kifogástalan állapotának megfelelő különbözeti csomópontok maximális százaléka |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, az alapértelmezett érték 15|Statikus|A fürt frissítése állapotának kiértékelésére szolgáló házirend: a nem kifogástalan állapotú csomópontok különbözetének maximális százaléka egy frissítési tartományban, amely lehetővé teszi, hogy a fürt állapota Kifogástalan legyen. |

## <a name="hosting"></a>Üzemeltetés

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Egész szám, alapértelmezés szerint 10 |Dinamikus|Ennyi alkalommal próbálkozik újra a sikertelen aktiválás a rendszeren, mielőtt feladja |
|ActivationMaxRetryInterval |Az idő másodpercben, az alapértelmezett érték 300 |Dinamikus|A rendszer minden folyamatos aktiválási hiba esetén újrapróbálkozik az aktiválással akár ActivationMaxFailureCount. A ActivationMaxRetryInterval a várakozási idő intervallumát adja meg az újrapróbálkozás előtt, az összes aktiválási hiba után |
|ActivationRetryBackoffInterval |Az idő másodpercben, az alapértelmezett érték 5 |Dinamikus|Leállítási időköz minden aktiválási hiba esetén; A rendszer minden folyamatos aktiválási hiba esetén újra megkísérli az aktiválást a MaxActivationFailureCount. Az újrapróbálkozási időköz minden próbálkozáskor a folyamatos aktiválási hiba és az aktiválási visszatartási időköz szorzata. |
|ActivationTimeout| TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (180)|Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Az alkalmazás aktiválásának időtúllépése; inaktiválás és frissítés. |
|ApplicationHostCloseTimeout| TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (120)|Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Ha a háló kilépését egy önaktivált folyamat észleli, A FabricRuntime lezárja a felhasználó gazdagépének (applicationHost) folyamatában lévő összes replikát. Ez a bezárási művelet időtúllépése. |
| CnsNetworkPluginCnmUrlPort | wstring, alapértelmezett érték: L "48080" | Statikus | Azure CNM API URL-port |
| CnsNetworkPluginCnsUrlPort | wstring, alapértelmezett érték: L "10090" | Statikus | Azure CNS URL-port |
|ContainerServiceArguments|karakterlánc, az alapértelmezett érték a "-H localhost: 2375-H npipe://"|Statikus|Service Fabric (SF) a Docker-démont (kivéve a Windows-ügyfélszámítógépeken, például a Win10) kezeli. Ez a konfiguráció lehetővé teszi, hogy a felhasználó olyan egyéni argumentumokat határozzon meg, amelyeket a Docker-démonnak át kell adni a indításakor. Ha egyéni argumentumok vannak megadva, Service Fabric ne adjon át más argumentumot a Docker-motornak a "--Pidfile" argumentum kivételével. Ezért a felhasználóknak nem szabad a "--Pidfile" argumentumot megadniuk az ügyfél argumentumai részeként. Emellett az egyéni argumentumoknak biztosítaniuk kell, hogy a Docker-démon figyelje az alapértelmezett Name pipe-ot a Windowsban (vagy Linux-alapú tartomány socket on Linux rendszeren) ahhoz, hogy a Service Fabric kommunikálni tudjon.|
|ContainerServiceLogFileMaxSizeInKb|int, alapértelmezett érték 32768|Statikus|A Docker-tárolók által generált naplófájl maximális fájlmérete.  Csak Windows.|
|Containerimagedownloadtimeout attribútummal állítható|int, másodpercek száma, alapértelmezett érték 1200 (20 perc)|Dinamikus|A rendszerkép letöltésének időkorlátja másodpercben.|
|ContainerImagesToSkip|karakterlánc, a képek neve függőleges vonallal elválasztva, alapértelmezett érték: ""|Statikus|Egy vagy több olyan tároló-rendszerkép neve, amely nem törölhető.  A PruneContainerImages paraméterrel együtt használható.|
|ContainerServiceLogFileNamePrefix|string (alapértelmezett érték: "sfcontainerlogs")|Statikus|A Docker-tárolók által létrehozott naplófájlok fájlnevének előtagja.  Csak Windows.|
|ContainerServiceLogFileRetentionCount|int, az alapértelmezett érték 10|Statikus|A Docker-tárolók által generált naplófájlok száma a naplófájlok felülírása előtt.  Csak Windows.|
|CreateFabricRuntimeTimeout|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (120)|Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. A szinkronizálási FabricCreateRuntime hívásának időtúllépési értéke |
|DefaultContainerRepositoryAccountName|karakterlánc, az alapértelmezett érték: ""|Statikus|A ApplicationManifest.xmlban megadott hitelesítő adatok helyett használt alapértelmezett hitelesítő adatok |
|DefaultContainerRepositoryPassword|karakterlánc, az alapértelmezett érték: ""|Statikus|Az ApplicationManifest.xmlban megadott hitelesítő adatok helyett használt alapértelmezett jelszavas hitelesítő adatok|
|DefaultContainerRepositoryPasswordType|karakterlánc, az alapértelmezett érték: ""|Statikus|Ha nem üres karakterláncot használ, az érték "titkosított" vagy "SecretsStoreRef" lehet.|
|DefaultDnsSearchSuffixEmpty|bool, az alapértelmezett érték FALSE|Statikus|Alapértelmezés szerint a szolgáltatás nevét a rendszer a Container Services SF DNS-neveként fűzi hozzá. Ez a funkció leállítja ezt a viselkedést, így a megoldási útvonalon alapértelmezés szerint semmi sem lesz hozzáfűzve az SF DNS-névhez.|
|DeploymentMaxFailureCount|int, az alapértelmezett érték 20| Dinamikus|Az alkalmazás központi telepítése újra próbálkozik a DeploymentMaxFailureCount, mielőtt az alkalmazás telepítése a csomóponton meghiúsul.| 
|DeploymentMaxRetryInterval| TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (3600)|Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Az üzemelő példány maximális újrapróbálkozási időköze. Minden folyamatos hiba esetén az újrapróbálkozási időköz kiszámítása percben (DeploymentMaxRetryInterval; Folyamatos meghibásodások száma * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (10)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az üzembe helyezési hiba időkorlátja. A rendszer minden folyamatos üzembe helyezési hiba esetén újra megkísérli az üzembe helyezést a MaxDeploymentFailureCount. Az újrapróbálkozási időköz a folyamatos üzembe helyezési hiba és a központi telepítési leállítási intervallumának szorzata. |
|DisableContainers|bool, az alapértelmezett érték FALSE|Statikus|A tárolók letiltására szolgáló konfiguráció a DisableContainerServiceStartOnContainerActivatorOpen helyett, amely elavult konfiguráció |
|DisableDockerRequestRetry|bool, az alapértelmezett érték FALSE |Dinamikus| Alapértelmezés szerint az SF a DD-vel (Docker Dameon) és a (z) "DockerRequestTimeout" időtúllépéssel kommunikál a hozzájuk küldött összes http-kérelemnél. Ha a DD nem válaszol az adott időszakon belül; Az SF újraküldi a kérést, ha a legfelső szintű művelet továbbra is hátralévő ideig tart.  HyperV-tárolóval; A DD időnként sokkal több időt vesz igénybe, hogy felvegye a tárolót, vagy inaktiválja azt. Ilyen esetekben a DD-kérelmek időtúllépése az SF perspektívából, az SF pedig újrapróbálkozik a művelettel. Néha úgy tűnik, hogy a DD-ra nagyobb nyomást okoz. Ez a konfiguráció lehetővé teszi az újrapróbálkozások letiltását, és várjon, amíg a DD válaszol. |
|DnsServerListTwoIps | Bool, az alapértelmezett érték FALSE | Statikus | Ez a jelző kétszer hozzáadja a helyi DNS-kiszolgálót az időszakos megoldási problémák enyhítése érdekében. |
| DockerTerminateOnLastHandleClosed | bool, az alapértelmezett érték FALSE | Statikus | Alapértelmezés szerint, ha a Hálóbeli a "dockerd" kezeli (a következő alapján: SkipDockerProcessManagement = = false), ez a beállítás azt konfigurálja, hogy mi történik a Hálóbeli vagy a dockerd összeomlása esetén. Ha a beállítás értéke `true` esetén a rendszer az összes futó tárolót kényszeríti, a HCS frissítőügynök megszakítja a műveletet. Ha a tárolók értékre van állítva `false` , továbbra is futni fog. Megjegyzés: az előzőtől a 8,0-ig ez a viselkedés akaratlanul egyenértékű volt `false` . Az alapértelmezett beállítás az, `true` amit várhatóan az alapértelmezett lépés, hogy a karbantartási logikánk a folyamat újraindításakor érvénybe lép. |
| DoNotInjectLocalDnsServer | bool, az alapértelmezett érték FALSE | Statikus | Megakadályozza, hogy a futtatókörnyezet a helyi IP-címet a tárolók DNS-kiszolgálójának beinjektálja. |
|EnableActivateNoWindow| bool, az alapértelmezett érték FALSE|Dinamikus| Az aktivált folyamat a háttérben, konzol nélkül jön létre. |
|EnableContainerServiceDebugMode|bool, az alapértelmezett érték TRUE (igaz)|Statikus|A Docker-tárolók naplózásának engedélyezése/letiltása.  Csak Windows.|
|EnableDockerHealthCheckIntegration|bool, az alapértelmezett érték TRUE (igaz)|Statikus|Lehetővé teszi a Docker HEALTHCHECK-események integrálását Service Fabric rendszerállapot-jelentéssel |
|EnableProcessDebugging|bool, az alapértelmezett érték FALSE|Dinamikus| Lehetővé teszi az alkalmazás-gazdagépek indítását a hibakeresőben |
|EndpointProviderEnabled| bool, az alapértelmezett érték FALSE|Statikus| Lehetővé teszi a végponti erőforrások hálón keresztüli kezelését. A FabricNode-ben a kezdő és a záró alkalmazás-porttartomány meghatározását igényli. |
|FabricContainerAppsEnabled| bool, az alapértelmezett érték FALSE|Statikus| |
|FirewallPolicyEnabled|bool, az alapértelmezett érték FALSE|Statikus| Engedélyezi a tűzfal portjainak megnyitását a végponti erőforrásokhoz a ServiceManifest megadott explicit portokkal. |
|GetCodePackageActivationContextTimeout|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (120)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A CodePackageActivationContext-hívások időtúllépési értéke. Ez nem alkalmazható az ad hoc szolgáltatásokra. |
|GovernOnlyMainMemoryForProcesses|bool, az alapértelmezett érték FALSE|Statikus|Az erőforrás-szabályozás alapértelmezett viselkedése az, hogy a MemoryInMB megadott korlátot a folyamat által használt teljes memória (RAM + swap) mennyisége határozza meg. Ha túllépi a korlátot; a folyamat OutOfMemory-kivételt fog kapni. Ha a paraméter értéke TRUE (igaz). a korlát csak a folyamat által használt RAM memória mennyiségére lesz alkalmazva. Ha túllépi a korlátot; Ha a beállítás értéke TRUE (igaz); Ezután az operációs rendszer a fő memóriát a lemezre fogja cserélni. |
|IPProviderEnabled|bool, az alapértelmezett érték FALSE|Statikus|Engedélyezi az IP-címek kezelését. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, az alapértelmezett érték FALSE|Statikus|Azt jelzi, hogy a DefaultContainerRepositoryPassword titkosítva van-e.|
|LinuxExternalExecutablePath|string (alapértelmezett érték: "/usr/bin/") |Statikus|A csomóponton található külső végrehajtható parancsok elsődleges könyvtára.|
|NTLMAuthenticationEnabled|bool, az alapértelmezett érték FALSE|Statikus| Lehetővé teszi az NTLM használatát a más felhasználóként futó kód-csomagok esetében, hogy a számítógépek folyamatai biztonságosan kommunikálhassanak. |
|NTLMAuthenticationPasswordSecret|SecureString, az alapértelmezett érték a common:: SecureString ("")|Statikus|A titkosított, amely az NTLM-felhasználók jelszavának előállítására szolgál. Ha a NTLMAuthenticationEnabled értéke TRUE (igaz), be kell állítani. A telepítő érvényesíti. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromMinutes (3)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Környezet-specifikus beállítások azt az időszakot, amikor a gazdagép a FileStoreService NTLM-konfigurációhoz használt új tanúsítványokat keres. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromMinutes (4)|Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. A tanúsítvány köznapi neveit használó NTLM-felhasználók konfigurálásának időtúllépése. Az NTLM-felhasználók a FileStoreService-megosztásokhoz szükségesek. |
|PruneContainerImages|bool, az alapértelmezett érték FALSE|Dinamikus| Távolítsa el a nem használt alkalmazás-tároló lemezképeit a csomópontokból. Ha egy alkalmazásban (törölnek a Service Fabric-fürtről, az alkalmazás által használt tároló-lemezképek el lesznek távolítva azokon a csomópontokon, amelyeken a Service Fabric letöltötte. A metszés óránként fut, így akár egy órát is igénybe vehet, amíg a lemezképek el lesznek távolítva a fürtből.<br>Service Fabric soha nem tölti le és nem távolítja el az alkalmazáshoz nem kapcsolódó képeket.  A manuálisan letöltött, nem kapcsolódó rendszerképeket explicit módon el kell távolítani.<br>A ContainerImagesToSkip paraméterben meg lehet adni azokat a lemezképeket, amelyeket nem szabad törölni.| 
|RegisterCodePackageHostTimeout|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (120)|Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. A FabricRegisterCodePackageHost-szinkronizálási hívás időtúllépési értéke. Ez csak a többcsomagos csomagok alkalmazás-gazdagépekre vonatkozik, például a FWP |
|RequestTimeout|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (30)|Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Ez a felhasználó alkalmazás-gazdagépe és a háló folyamata közötti, különböző üzemeltetési műveletekhez, például a gyári regisztrációhoz való kommunikáció időtúllépését jelenti. futtatókörnyezet regisztrálása. |
|RunAsPolicyEnabled| bool, az alapértelmezett érték FALSE|Statikus| Lehetővé teszi a programkódok futtatását a felhasználótól eltérő helyi felhasználóként, amely a háló folyamatát futtatja. Ennek a házirend-hálónak az engedélyezéséhez RENDSZERként vagy SeAssignPrimaryTokenPrivilege rendelkező felhasználóként kell futnia. |
|ServiceFactoryRegistrationTimeout| TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (120)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A szinkronizálási regiszter időtúllépési értéke (állapot nélküli/állapot-nyilvántartó) ServiceFactory hívása |
|ServiceTypeDisableFailureThreshold |Egész szám, alapértelmezés szerint 1 |Dinamikus|Ez a küszöbérték, amely után a rendszer értesíti a FailoverManager (FM), hogy letiltsa a szolgáltatás típusát a csomóponton, és egy másik csomópontot próbál meg elhelyezésre. |
|ServiceTypeDisableGraceInterval|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (30)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az az időtartam, amely után a szolgáltatástípus le lehet tiltani |
|ServiceTypeRegistrationTimeout |Az idő másodpercben, az alapértelmezett érték 300 |Dinamikus|A ServiceType számára engedélyezett maximális idő a hálóban |
|UseContainerServiceArguments|bool, az alapértelmezett érték TRUE (igaz)|Statikus|Ez a konfiguráció azt adja meg, hogy a gazdagép a Docker Daemon-hoz tartozó argumentumokat (a config ContainerServiceArguments-ben megadva) hagyja ki.|

## <a name="httpgateway"></a>HttpGateway

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, alapértelmezett érték 50 |Statikus| A HTTP-kiszolgáló várólistáján közzétenni kívánt olvasási műveletek száma. Ezzel a beállítással megadható, hogy a HttpGateway milyen egyidejű kérelmeket tud kielégíteni. |
|HttpGatewayHealthReportSendInterval |Az idő másodpercben, az alapértelmezett érték 30 |Statikus|Másodpercek alatt meg kell adni a TimeSpan. Az az időtartam, amikor a http-átjáró halmozott állapotú jelentéseket küld az állapotfigyelő kezelőnek. |
|HttpStrictTransportSecurityHeader|karakterlánc, az alapértelmezett érték: ""|Dinamikus| A HttpGateway által küldött összes válaszban szerepeltetni kívánt HTTP-alapú szigorú átviteli biztonsági fejléc értékének megadása. Ha üres sztringre van beállítva; Ez a fejléc nem fog szerepelni az átjáró válaszában.|
|IsEnabled|Bool, az alapértelmezett érték false |Statikus| Engedélyezi vagy letiltja a HttpGateway. A HttpGateway alapértelmezés szerint le van tiltva. |
|MaxEntityBodySize |Uint, alapértelmezett érték 4194304 |Dinamikus|A http-kérésből várható törzs maximális méretét adja meg. Az alapértelmezett érték a 4MB nál. A Httpgateway sikertelen lesz, ha a mérete > ez az érték. A minimális olvasási méret 4096 bájt. Ezért ennek >= 4096-nek kell lennie. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Engedélyezve |Bool, az alapértelmezett érték false |Statikus|A ImageStoreService engedélyezett jelzője. Alapértelmezett: false |
|MinReplicaSetSize | Int, az alapértelmezett érték 3 |Statikus|A ImageStoreService MinReplicaSetSize. |
|PlacementConstraints | karakterlánc, az alapértelmezett érték: "" |Statikus| A ImageStoreService PlacementConstraints. |
|QuorumLossWaitDuration | Az idő másodpercben, az alapértelmezett érték a MaxValue |Statikus| Másodpercek alatt meg kell adni a TimeSpan. A ImageStoreService QuorumLossWaitDuration. |
|ReplicaRestartWaitDuration | Az idő másodpercben, az alapértelmezett érték 60,0 \* 30 |Statikus|Másodpercek alatt meg kell adni a TimeSpan. A ImageStoreService ReplicaRestartWaitDuration. |
|StandByReplicaKeepDuration | Az idő másodpercben, alapértelmezett értéke 3600,0 \* 2 |Statikus| Másodpercek alatt meg kell adni a TimeSpan. A ImageStoreService StandByReplicaKeepDuration. |
|TargetReplicaSetSize | Int, az alapértelmezett érték 7 |Statikus|A ImageStoreService TargetReplicaSetSize. |

## <a name="ktllogger"></a>KtlLogger

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, az alapértelmezett érték 1 |Dinamikus|Jelző, amely azt jelzi, hogy a memória beállításait automatikusan és dinamikusan kell-e konfigurálni. Ha a nulla értéket adja meg, a rendszer a memória konfigurációs beállításait közvetlenül használja, és a rendszerfeltételek alapján nem változik. Ha az egyiket, a rendszer automatikusan konfigurálja a memória beállításait, és a rendszerfeltételek alapján változhat. |
|MaximumDestagingWriteOutstandingInKB | Int, az alapértelmezett érték 0 |Dinamikus|Azon KB-os szám, amely lehetővé teszi, hogy a megosztott napló előre jelezze a dedikált naplót. A 0 értékkel jelezze a nem korlátot.
|SharedLogId |karakterlánc, az alapértelmezett érték: "" |Statikus|A megosztott napló tárolójának egyedi GUID azonosítója. Használja a "" értéket, ha az alapértelmezett elérési utat használja a háló adatai gyökerében. |
|SharedLogPath |karakterlánc, az alapértelmezett érték: "" |Statikus|A megosztott naplózási tároló helyének elérési útja és fájlneve. Használja a "" lehetőséget az alapértelmezett elérési út használatára a háló adatai gyökerében. |
|SharedLogSizeInMB |Int, alapértelmezett érték 8192 |Statikus|A megosztott napló tárolójában lefoglalható MB-ban megadott szám. |
|SharedLogThrottleLimitInPercentUsed|int, az alapértelmezett érték 0 | Statikus | A sávszélesség-szabályozást kiváltó megosztott napló használatának százalékos aránya. Az értéknek 0 és 100 között kell lennie. A 0 érték azt jelenti, hogy az alapértelmezett százalékos értéket használja. A 100 érték azt jelenti, hogy nincs szabályozás. 1 és 99 közötti érték határozza meg, hogy a naplózás hány százalékban fog történni. Ha például a megosztott napló 10 GB-os, és az értéke 90, akkor a szabályozás a NAPLÓZÁSI KÖTETNEK használata után fog történni. Az alapértelmezett érték használata ajánlott.|
|WriteBufferMemoryPoolMaximumInKB | Int, az alapértelmezett érték 0 |Dinamikus|A KB-ban megadott szám, amely lehetővé teszi az írási puffer memória-készletének növelését. A 0 értékkel jelezze a nem korlátot. |
|WriteBufferMemoryPoolMinimumInKB |Int, alapértelmezett érték 8388608 |Dinamikus|Az írási pufferbeli memória-készlethez először lefoglalni kívánt KB-os szám. A 0 érték megadásával jelezheti, hogy az alapértelmezett korlátnak nem kell konzisztensnek lennie az alábbi SharedLogSizeInMB. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|IsEnabled|bool, az alapértelmezett érték FALSE|Statikus|A felügyelt identitási jogkivonat szolgáltatás jelenlétének és állapotának szabályozása a fürtben; ez az előfeltétel a Service Fabric alkalmazások felügyelt identitási funkciójának használatához.|

## <a name="management"></a>Kezelés

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromMinutes (5)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az alkalmazás típusának törléséhez engedélyezett törlési időköz az alkalmazás automatikus kitakarítása során.|
|AzureStorageMaxConnections | Int, alapértelmezett érték 5000 |Dinamikus|Az Azure Storage-hoz való egyidejű kapcsolatok maximális száma. |
|AzureStorageMaxWorkerThreads | Int, az alapértelmezett érték 25 |Dinamikus|A feldolgozói szálak maximális száma párhuzamosan. |
|AzureStorageOperationTimeout | Az idő másodpercben, az alapértelmezett érték 6000 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Időtúllépés a xstore művelet befejezéséhez. |
|CleanupApplicationPackageOnProvisionSuccess|bool, az alapértelmezett érték TRUE (igaz) |Dinamikus|Engedélyezheti vagy letilthatja az alkalmazáscsomag automatikus törlését a sikeres üzembe helyezéshez.
|CleanupUnusedApplicationTypes|Bool, az alapértelmezett érték FALSE |Dinamikus|Ez a konfiguráció, ha engedélyezve van, lehetővé teszi a nem használt alkalmazások típusának regisztrációját a legújabb három nem használt verzió kihagyása mellett, így a rendszerkép-tárolóban foglalt lemezterület kivágása is megtörténik. Az automatikus tisztítás az adott alkalmazás típusának sikeres kiépítését követően aktiválódik, és az összes alkalmazás típusának naponta egyszer rendszeresen fut. A kihagyható verziók száma a "MaxUnusedAppTypeVersionsToKeep" paraméter használatával konfigurálható. <br/> *Ajánlott eljárás a használata `true` .*
|DisableChecksumValidation | Bool, az alapértelmezett érték false |Statikus| Ezzel a konfigurációval engedélyezheti vagy letilthatja az ellenőrzőösszeg-érvényesítést az alkalmazás üzembe helyezése során. |
|DisableServerSideCopy | Bool, az alapértelmezett érték false |Statikus|Ezzel a konfigurációval engedélyezheti vagy letilthatja az alkalmazáscsomag kiszolgálóoldali példányát a Lemezképtárolóba az alkalmazás üzembe helyezése során. |
|ImageCachingEnabled | Bool, az alapértelmezett érték TRUE (igaz) |Statikus|Ez a konfiguráció lehetővé teszi a gyorsítótárazás engedélyezését vagy letiltását. |
|ImageStoreConnectionString |SecureString |Statikus|A Lemezképtárolóba gyökeréhez tartozó kapcsolódási karakterlánc. |
|ImageStoreMinimumTransferBPS | Int, alapértelmezett érték 1024 |Dinamikus|A fürt és a Lemezképtárolóba közötti minimális adatátviteli sebesség. Ez az érték határozza meg az időtúllépést a külső Lemezképtárolóba elérésekor. Csak akkor módosítsa ezt az értéket, ha a fürt és a Lemezképtárolóba közötti késés magas, hogy több idő legyen a fürt külső Lemezképtárolóba való letöltésére. |
|MaxUnusedAppTypeVersionsToKeep | Int, az alapértelmezett érték 3 |Dinamikus|Ez a konfiguráció határozza meg a tisztításhoz kihagyható, nem használt alkalmazás-verziók számát. Ez a paraméter csak akkor alkalmazható, ha a CleanupUnusedApplicationTypes paraméter engedélyezve van. <br/>*Az általános ajánlott eljárás az alapértelmezett () használata `3` . Az 1 értéknél kisebb értékek nem érvényesek.*|


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Paraméter** | **Megengedett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, alapértelmezés szerint nincs|Dinamikus|Meghatározza a metrikák MetricActivityThresholds készletét a fürtben. A kiegyenlítés akkor működik, ha a maxNodeLoad nagyobb, mint a MetricActivityThresholds. A Defrag-metrikák esetében meghatározza a terhelési mennyiségét, amely egyenlő vagy alacsonyabb, amelynek Service Fabric a csomópontot üresen kell tekinteni |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Paraméter** | **Megengedett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, alapértelmezés szerint nincs|Dinamikus|Meghatározza a metrikák MetricBalancingThresholds készletét a fürtben. A kiegyenlítés akkor működik, ha a maxNodeLoad/minNodeLoad nagyobb, mint a MetricBalancingThresholds. A töredezettségmentesítés akkor működik, ha legalább egy FD-vagy UD-maxNodeLoad/minNodeLoad kisebb, mint a MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Paraméter** | **Megengedett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, alapértelmezés szerint nincs|Dinamikus|Meghatározza azt a terhelést, amely a replikával együtt tapad, és 0 (Betöltés nem tapad a replikához) és 1 (a replika-alapértelmezett Load Sticks) közötti értéket vesz igénybe. |

## <a name="namingservice"></a>NamingService

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, az alapértelmezett érték 0 |Statikus|A LRU szolgáltatás Description cache-gyorsítótárában fenntartott bejegyzések maximális száma az elnevezési átjárón (legfeljebb 0 értékre van állítva). |
|MaxClientConnections |Int, alapértelmezett érték 1000 |Dinamikus|A maximálisan engedélyezett számú ügyfélkapcsolat az átjárón. |
|MaxFileOperationTimeout |Az idő másodpercben, az alapértelmezett érték 30 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A file Store szolgáltatás műveletéhez engedélyezett maximális időkorlát. A nagyobb időtúllépést megadó kérések el lesznek utasítva. |
|MaxIndexedEmptyPartitions |Int, alapértelmezett érték 1000 |Dinamikus|Azon üres partíciók maximális száma, amelyek indexelve maradnak az értesítési gyorsítótárban az ügyfelek újrakapcsolatának szinkronizálásához. A rendszer a számot meghaladó üres partíciókat eltávolítja az indexből a keresési verziók növekvő sorrendjében. Az ügyfelek újrakapcsolódása továbbra is képes szinkronizálni és fogadni a hiányzó üres partíciós frissítéseket; a szinkronizálási protokoll azonban drágább lesz. |
|MaxMessageSize |Int, az alapértelmezett érték 4 \* 1024 \* 1024 |Statikus|Az ügyfél-csomópontok közötti kommunikáció maximális mérete az elnevezés használatakor. DOS-támadások enyhítése; az alapértelmezett érték a 4MB nál. |
|MaxNamingServiceHealthReports | Int, az alapértelmezett érték 10 |Dinamikus|Az adattárolási szolgáltatás által jelentett lassú műveletek maximális száma egyszerre. Ha 0; minden lassú művelet elküldése. |
|MaxOperationTimeout |Az idő másodpercben, az alapértelmezett érték 600 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az ügyfél műveleteihez engedélyezett maximális időkorlát. A nagyobb időtúllépést megadó kérések el lesznek utasítva. |
|MaxOutstandingNotificationsPerClient |Int, alapértelmezett érték 1000 |Dinamikus|A függőben lévő értesítések maximális száma, mielőtt az átjáró kényszeríti az ügyfél regisztrálását. |
|MinReplicaSetSize | Int, az alapértelmezett érték 3 |Nem engedélyezett| A frissítés befejezéséhez szükséges elnevezési szolgáltatás-replikák minimális száma. Ha kevesebb replika van a rendszeren, a megbízhatósági rendszer megtagadja a elnevezési szolgáltatás-tároló frissítését, amíg a replikák vissza nem állnak. Ez az érték soha nem lehet nagyobb, mint a TargetReplicaSetSize. |
|PartitionCount |Int, az alapértelmezett érték 3 |Nem engedélyezett|A létrehozandó elnevezési szolgáltatás tároló partícióinak száma. Minden partíció rendelkezik egy olyan partíciós kulccsal, amely megfelel az indexének. tehát a partíció kulcsai [0; PartitionCount] létezik. A elnevezési szolgáltatás partíciók számának növelésével megnövelhető a elnevezési szolgáltatás által elvégezhető skála, amely csökkenti a biztonsági másolati készletben tárolt adatok átlagos mennyiségét. az erőforrások megnövekedett kihasználtságának díja (mivel a PartitionCount * ReplicaSetSize szolgáltatás replikáit fenn kell tartani).|
|PlacementConstraints | karakterlánc, az alapértelmezett érték: "" |Nem engedélyezett| A elnevezési szolgáltatás elhelyezési megkötése. |
|QuorumLossWaitDuration | Az idő másodpercben, az alapértelmezett érték a MaxValue |Nem engedélyezett| Másodpercek alatt meg kell adni a TimeSpan. Ha egy elnevezési szolgáltatás kvórum elvesztése válik elérhetővé; Ez az időzítő elindul. Az FM lejáratakor a rendszer a lefelé irányuló replikákat elveszettnek tekinti. és próbálja meg helyreállítani a kvórumot. Ez adatvesztéshez vezethet. |
|RepairInterval | Az idő másodpercben, az alapértelmezett érték 5 |Statikus| Másodpercek alatt meg kell adni a TimeSpan. Az az intervallum, amelyben a hatóság tulajdonosának és nevének tulajdonosának neve inkonzisztencia-javítása megkezdődik. |
|ReplicaRestartWaitDuration | Idő másodpercben, alapértelmezett érték (60,0 * 30)|Nem engedélyezett| Másodpercek alatt meg kell adni a TimeSpan. Ha egy elnevezési szolgáltatás replika leáll; Ez az időzítő elindul. Amikor lejár, az FM megkezdi a leállt replikák cseréjét (ez még nem veszi figyelembe az elveszetteket). |
|ServiceDescriptionCacheLimit | Int, az alapértelmezett érték 0 |Statikus| A LRU szolgáltatás Description cache-ben fenntartott bejegyzéseinek maximális száma az elnevezési tároló szolgáltatásban (legfeljebb 0 értékre van állítva). |
|ServiceNotificationTimeout |Az idő másodpercben, az alapértelmezett érték 30 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A szolgáltatás értesítéseinek az ügyfélnek történő kézbesítéséhez használt időkorlát. |
|StandByReplicaKeepDuration | Az idő másodpercben, az alapértelmezett érték 3600,0 * 2 |Nem engedélyezett| Másodpercek alatt meg kell adni a TimeSpan. Amikor egy elnevezési szolgáltatás replika vissza fog térni a leállási állapotból; lehetséges, hogy már lecserélték. Ez az időzítő azt határozza meg, hogy az FM mennyi ideig tart a készenléti replika számára az eldobás előtt. |
|TargetReplicaSetSize |Int, az alapértelmezett érték 7 |Nem engedélyezett|A elnevezési szolgáltatás tároló egyes partícióinak replikáinak száma. A replikakészlet számának növelése növeli a elnevezési szolgáltatás tárolóban található információk megbízhatósági szintjét. a csomópontok meghibásodása miatt elveszik az információ elvesztésének változását; a Windows Fabric terhelésének és az elnevezési adatok frissítéseinek elvégzéséhez szükséges idő növelésének díja.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Paraméter** | **Megengedett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, alapértelmezés szerint nincs|Dinamikus|Csomópont kapacitása százalék/metrika neve; pufferként használatos, hogy a feladatátvételi esethez egy csomóponton legyen egy szabad hely. |

## <a name="nodecapacities"></a>NodeCapacities

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Statikus|Csomópont-kapacitások gyűjteménye különböző mérőszámokhoz. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Statikus|Ismerteti azokat a tartalék tartományokat, amelyek a csomóponthoz tartoznak. A tartalék tartomány egy URI-n keresztül van definiálva, amely leírja a csomópont helyét az adatközpontban.  A tartalék tartomány URI-ja az FD:/FD, majd egy URI-elérésiút-szegmens.|
|UpgradeDomainId |karakterlánc, az alapértelmezett érték: "" |Statikus|A csomóponthoz tartozó frissítési tartományt ismerteti. |

## <a name="nodeproperties"></a>NodeProperties

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Statikus|Karakterlánc kulcs-érték párok gyűjteménye a csomópont tulajdonságaihoz. |

## <a name="paas"></a>PaaS

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ClusterId |karakterlánc, az alapértelmezett érték: "" |Nem engedélyezett|A Fabric által a konfiguráció védelme érdekében használt X509-tanúsítványtároló. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Számlálók |Sztring | Dinamikus |A gyűjteni kívánt teljesítményszámlálók vesszővel tagolt listája. |
|IsEnabled |Bool, az alapértelmezett érték TRUE (igaz) | Dinamikus |A jelző jelzi, hogy engedélyezve van-e a teljesítményszámláló gyűjteménye a helyi csomóponton. |
|MaxCounterBinaryFileSizeInMB |Int, az alapértelmezett érték 1 | Dinamikus |A teljesítményszámláló bináris fájljainak maximális mérete (MB). |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, az alapértelmezett érték 10 | Dinamikus |Az új teljesítményszámláló bináris fájljának létrehozása utáni maximális időköz (másodpercben). |
|SamplingIntervalInSeconds |Int, alapértelmezett érték 60 | Dinamikus |A gyűjtött teljesítményszámlálók mintavételi időköze. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, az alapértelmezett érték 0 | Dinamikus|Meghatározza az affinitási megkötés prioritását: 0: Hard; 1: lágy; negatív: figyelmen kívül hagyás. |
|ApplicationCapacityConstraintPriority | Int, az alapértelmezett érték 0 | Dinamikus|Meghatározza a kapacitási megkötés prioritását: 0: nehéz; 1: lágy; negatív: figyelmen kívül hagyás. |
|AutoDetectAvailableResources|bool, az alapértelmezett érték TRUE (igaz)|Statikus|Ez a konfiguráció elindítja a rendelkezésre álló erőforrások automatikus észlelését a csomóponton (CPU és memória), ha ez a konfiguráció igaz értékre van állítva. a valós kapacitások beolvasása után kijavítani fogjuk, ha a felhasználó hibás csomópont-kapacitást adott meg, vagy egyáltalán nem definiálta őket, ha a konfiguráció hamis értékre van állítva, a felhasználó által megadott hibás csomópont-kapacitások de nem fogjuk kijavítani őket; azt jelenti, hogy a felhasználó szeretné, hogy a csomópontok >ként legyenek meghatározva, mint a csomópont, vagy ha a kapacitás nincs meghatározva; a rendszer korlátlan kapacitást feltételez |
|BalancingDelayAfterNewNode | Az idő másodpercben, az alapértelmezett érték 120 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az új csomópont hozzáadása után ne kezdjen el kiegyenlíteni tevékenységeket ezen az időszakon belül. |
|BalancingDelayAfterNodeDown | Az idő másodpercben, az alapértelmezett érték 120 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az adott időszakon belül ne kezdjen el kiegyenlíteni tevékenységeket egy csomópont leállási eseménye után. |
|BlockNodeInUpgradeConstraintPriority | Int, az alapértelmezett érték-1 |Dinamikus|Meghatározza a kapacitási megkötés prioritását: 0: nehéz; 1: lágy; negatív: figyelmen kívül hagyás  |
|CapacityConstraintPriority | Int, az alapértelmezett érték 0 | Dinamikus|Meghatározza a kapacitási megkötés prioritását: 0: nehéz; 1: lágy; negatív: figyelmen kívül hagyás. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, az alapértelmezett érték 20 | Dinamikus|Meghatározza, hogy a rendszer hány alkalommal ResourceBalancer el a kiállított mozgásokat a diagnosztika végrehajtása előtt, és az állapottal kapcsolatos figyelmeztetések kibocsátása megtörténjen. Negatív: nincsenek kibocsátva figyelmeztetések ebben a feltételben. |
|ConstraintFixPartialDelayAfterNewNode | Az idő másodpercben, az alapértelmezett érték 120 |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Az új csomópont hozzáadása után a DDo nem oldja meg a FaultDomain és a UpgradeDomain korlátozás megsértését ezen az időtartamon belül. |
|ConstraintFixPartialDelayAfterNodeDown | Az idő másodpercben, az alapértelmezett érték 120 |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Ne javítsa ki a FaultDomain és a UpgradeDomain korlátozás megsértését ezen az időtartamon belül egy csomópont-leállási esemény után. |
|ConstraintViolationHealthReportLimit | Int, alapértelmezett érték 50 |Dinamikus| Meghatározza, hogy a rendszer hányszor megsértse a replikát a diagnosztika végrehajtása előtt, és az állapotadatok kibocsátása előtt ne legyenek kijavítva. |
|DetailedConstraintViolationHealthReportLimit | Int, alapértelmezett érték 200 |Dinamikus| Meghatározza, hogy a rendszer hányszor megsértse a replikát a diagnosztika végrehajtása előtt, és részletes állapot-jelentéseket bocsát ki. |
|DetailedDiagnosticsInfoListLimit | Int, az alapértelmezett érték 15 |Dinamikus| Meghatározza a diagnosztikai bejegyzések (részletes információkkal) számát a diagnosztika csonkítása előtt.|
|DetailedNodeListLimit | Int, az alapértelmezett érték 15 |Dinamikus| Meghatározza, hogy hány csomópont/korlátozás szerepeljen a nem elhelyezni kívánt replika-jelentésekben a csonkítás előtt. |
|DetailedPartitionListLimit | Int, az alapértelmezett érték 15 |Dinamikus| Meghatározza, hogy a rendszer a diagnosztikai bejegyzésben szereplő partíciók számát adja meg a diagnosztika előtti csonkítás előtt. |
|DetailedVerboseHealthReportLimit | Int, alapértelmezett érték 200 | Dinamikus|Azt határozza meg, hogy a rendszer hányszor adja meg a nem helyezett replikát a részletes állapotadatok kibocsátása előtt. |
|EnforceUserServiceMetricCapacities|bool, az alapértelmezett érték FALSE | Statikus |Lehetővé teszi a háló szolgáltatások védelmét. Minden felhasználói szolgáltatás egy feladatütemezés/CGROUP alatt található, és a megadott mennyiségű erőforrásra korlátozódik. Ennek statikusnak kell lennie (a Hálóbeli újraindítását igényli) a felhasználói feladatok objektumának létrehozásakor/eltávolításakor, valamint a rögzített korlátoknak a háló-gazdagép megnyitásakor végzett beállításának megfelelően. |
|FaultDomainConstraintPriority | Int, az alapértelmezett érték 0 |Dinamikus| Meghatározza a tartalék tartomány korlátozásának prioritását: 0: Hard; 1: lágy; negatív: figyelmen kívül hagyás. |
|GlobalMovementThrottleCountingInterval | Az idő másodpercben, az alapértelmezett érték 600 |Statikus| Másodpercek alatt meg kell adni a TimeSpan. Adja meg az előző intervallum hosszát, amelynek nyomon követésére a tartományi replika-mozgások (a GlobalMovementThrottleThreshold együtt használatos). 0 értékre állítható, így figyelmen kívül hagyható a globális szabályozás. |
|GlobalMovementThrottleThreshold | Uint, alapértelmezett érték 1000 |Dinamikus| A GlobalMovementThrottleCountingInterval által jelzett korábbi intervallumban az elosztási fázisban engedélyezett mozgások maximális száma. |
|GlobalMovementThrottleThresholdForBalancing | Uint, az alapértelmezett érték 0 | Dinamikus|A GlobalMovementThrottleCountingInterval által jelzett korábbi időszakban a terheléselosztási fázisban engedélyezett mozgások maximális száma. a 0 érték nem korlátozza a korlátot. |
|GlobalMovementThrottleThresholdForPlacement | Uint, az alapértelmezett érték 0 |Dinamikus| A GlobalMovementThrottleCountingInterval által jelzett korábbi intervallumban az elhelyezési fázisban engedélyezett mozgások maximális száma. 0 a korlátot jelzi.|
|GlobalMovementThrottleThresholdPercentage|dupla, az alapértelmezett érték 0|Dinamikus|A GlobalMovementThrottleCountingInterval által jelzett korábbi intervallumban a terheléselosztási és elhelyezési fázisokban megengedett teljes mozgások maximális száma (a fürt replikáinak teljes száma százalékban kifejezve). a 0 érték nem korlátozza a korlátot. Ha ez és a GlobalMovementThrottleThreshold is meg van adva, ezt követően a rendszer több konzervatív korlátot használ.|
|GlobalMovementThrottleThresholdPercentageForBalancing|dupla, az alapértelmezett érték 0|Dinamikus|A terheléselosztási fázisban engedélyezett mozgások maximális száma (a PLB replikáinak teljes száma százalékban kifejezve) a GlobalMovementThrottleCountingInterval által jelzett korábbi intervallumban. a 0 érték nem korlátozza a korlátot. Ha ez és a GlobalMovementThrottleThresholdForBalancing is meg van adva, ezt követően a rendszer több konzervatív korlátot használ.|
|InBuildThrottlingAssociatedMetric | karakterlánc, az alapértelmezett érték: "" |Statikus| A szabályozáshoz társított metrika neve. |
|InBuildThrottlingEnabled | Bool, az alapértelmezett érték false |Dinamikus| Döntse el, hogy engedélyezve van-e a beépített szabályozás. |
|InBuildThrottlingGlobalMaxValue | Int, az alapértelmezett érték 0 |Dinamikus|A Build-replikák maximális száma globálisan engedélyezett. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, az alapértelmezett érték false | Dinamikus|Meghatározza, hogy a feladatátvételi egységek bármelyikének frissítése megszakítsa-e a gyors vagy lassú terheléselosztási futtatást. Ha a (Z: létrehozása/törlése megszakad, a rendszer megszakítja a megadott "hamis" kiegyenlítési futtatást. hiányzó replikákkal rendelkezik; módosította az elsődleges replika helyét, vagy megváltoztatta a replikák számát. A rendszer nem szakítja meg az egyensúlyi futtatást más esetekben – ha (Z: további replikákkal rendelkezik; módosította bármelyik replika jelölőjét; csak a partíciós verzió vagy bármely más eset módosítva. |
|MinConstraintCheckInterval | Az idő másodpercben, az alapértelmezett érték 1 |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Meghatározza azt a minimális időtartamot, ameddig a két egymást követő megkötés-ellenőrzési kör előtt el kell telnie. |
|MinLoadBalancingInterval | Az idő másodpercben, az alapértelmezett érték 5 |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Meghatározza azt a minimális időtartamot, amelynek el kell telnie két egymást követő kiegyenlítő kör előtt. |
|MinPlacementInterval | Az idő másodpercben, az alapértelmezett érték 1 |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Meghatározza azt a minimális időtartamot, amelynek a két egymást követő elhelyezési forduló előtt el kell telnie. |
|MoveExistingReplicaForPlacement | Bool, az alapértelmezett érték TRUE (igaz) |Dinamikus|A beállítás, amely meghatározza, hogy a meglévő replika áthelyezhető-e az elhelyezés során. |
|MovementPerPartitionThrottleCountingInterval | Az idő másodpercben, az alapértelmezett érték 600 |Statikus| Másodpercek alatt meg kell adni a TimeSpan. Adja meg az előző intervallum hosszát, amelynél nyomon követhető az egyes partíciók replikáinak mozgatása (a MovementPerPartitionThrottleThreshold együtt használva). |
|MovementPerPartitionThrottleThreshold | Uint, alapértelmezett érték 50 |Dinamikus| Egy partíció esetében nem történik terheléselosztással kapcsolatos mozgás, ha az adott partíció replikáinak kiegyensúlyozásával kapcsolatos mozgások száma elérte vagy túllépte a MovementPerFailoverUnitThrottleThreshold a MovementPerPartitionThrottleCountingInterval által jelzett korábbi intervallumban. |
|MoveParentToFixAffinityViolation | Bool, az alapértelmezett érték false |Dinamikus| Ez a beállítás határozza meg, hogy a szülő replikák áthelyezhetők-e az affinitási megkötések kijavítására.|
|PartiallyPlaceServices | Bool, az alapértelmezett érték TRUE (igaz) |Dinamikus| Meghatározza, hogy a fürt összes szolgáltatás-replikája "All vagy Nothing" értékűre kerüljön-e a számukra korlátozottan megfelelő csomópontok számára.|
|PlaceChildWithoutParent | Bool, az alapértelmezett érték TRUE (igaz) | Dinamikus|Ez a beállítás határozza meg, hogy a gyermek-szolgáltatás replikája hol helyezhető el, ha nincs fölérendelt replika. |
|PlacementConstraintPriority | Int, az alapértelmezett érték 0 | Dinamikus|Meghatározza az elhelyezési megkötés prioritását: 0: Hard; 1: lágy; negatív: figyelmen kívül hagyás. |
|PlacementConstraintValidationCacheSize | Int, alapértelmezett érték 10000 |Dinamikus| Korlátozza az elhelyezési korlátozási kifejezések gyors ellenőrzéséhez és gyorsítótárazásához használt tábla méretét. |
|PlacementSearchTimeout | Az idő másodpercben, az alapértelmezett érték 0,5 |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Szolgáltatások elhelyezésekor; a találatok visszaadását megelőzően legfeljebb ennyi ideig kereshet. |
|PLBRefreshGap | Az idő másodpercben, az alapértelmezett érték 1 |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Meghatározza azt a minimális időtartamot, ameddig a PLB újra frissítenie kell az állapotot. |
|PreferredLocationConstraintPriority | Int, az alapértelmezett érték 2| Dinamikus|Meghatározza az előnyben részesített hely megkötésének prioritását: 0: Hard; 1: lágy; 2: optimalizálás; negatív: figyelmen kívül hagyás |
|PreferredPrimaryDomainsConstraintPriority| Int, az alapértelmezett érték 1 | Dinamikus| Meghatározza az elsődleges tartomány elsődleges korlátozásának prioritását: 0: Hard; 1: lágy; negatív: figyelmen kívül hagyás |
|PreferUpgradedUDs|bool, az alapértelmezett érték FALSE|Dinamikus|Be-és kikapcsolja a logikát, amely a már frissített frissítési való áttérést részesíti előnyben. Az SF 7,0-től kezdődően a paraméter alapértelmezett értéke TRUE (hamis) értékre változik.|
|PreventTransientOvercommit | Bool, az alapértelmezett érték false | Dinamikus|Meghatározza, hogy a rendszer azonnal PLB-e a kezdeményezett lépések által felszabadított erőforrásokra. Alapértelmezés szerint; A PLB kezdeményezheti a kilépést, és áthelyezheti azt ugyanazon a csomóponton, amely átmeneti túllépést hozhat létre. Ha a paraméter igaz értékre van állítva, a rendszer letiltja az ilyen típusú túllépéseket és az igény szerinti Defrag (aka placementWithMove) letiltását. |
|ScaleoutCountConstraintPriority | Int, az alapértelmezett érték 0 |Dinamikus| Meghatározza a horizontális felskálázás száma megkötésének prioritását: 0: Hard; 1: lágy; negatív: figyelmen kívül hagyás. |
|SubclusteringEnabled|Bool, az alapértelmezett érték FALSE | Dinamikus |A kiegyenlítő szórás kiszámításához az alfürtözést kell nyugtázni |
|SubclusteringReportingPolicy| Int, az alapértelmezett érték 1 |Dinamikus|Meghatározza, hogy a rendszer hogyan és hogyan küldje el a alfürtözési állapotjelentést: 0: ne jelentse a jelentést; 1: figyelmeztetés; 2: OK |
|SwapPrimaryThrottlingAssociatedMetric | karakterlánc, az alapértelmezett érték: ""|Statikus| A szabályozáshoz társított metrika neve. |
|SwapPrimaryThrottlingEnabled | Bool, az alapértelmezett érték false|Dinamikus| Döntse el, hogy engedélyezve van-e a swap-primer szabályozás. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, az alapértelmezett érték 0 |Dinamikus| A swap-elsődleges replikák maximális száma globálisan engedélyezett. |
|TraceCRMReasons |Bool, az alapértelmezett érték TRUE (igaz) |Dinamikus|Megadja, hogy nyomon követhető-e a CRM által kiadott mozgások a működési események csatornáján. |
|UpgradeDomainConstraintPriority | Int, az alapértelmezett érték 1| Dinamikus|Meghatározza a frissítési tartomány korlátozásának prioritását: 0: Hard; 1: lágy; negatív: figyelmen kívül hagyás. |
|UseMoveCostReports | Bool, az alapértelmezett érték false | Dinamikus|Arra utasítja az LB-t, hogy figyelmen kívül hagyja a pontozási függvény Cost elemét; az így keletkező potenciálisan nagy számú lépés a jobb kiegyensúlyozott elhelyezés érdekében. |
|UseSeparateSecondaryLoad | Bool, az alapértelmezett érték TRUE (igaz) | Dinamikus|Ez a beállítás határozza meg, hogy a másodlagos replikák esetén a különálló terhelést kell-e használni. |
|UseSeparateSecondaryMoveCost | Bool, az alapértelmezett érték false | Dinamikus|Ez a beállítás határozza meg, hogy a másodlagos replikák esetén a különálló áthelyezési költségeket kell-e használni. |
|ValidatePlacementConstraint | Bool, az alapértelmezett érték TRUE (igaz) |Dinamikus| Megadja, hogy a szolgáltatás PlacementConstraint-kifejezése érvényesítve van-e a szolgáltatás ServiceDescription leírásban frissítésekor. |
|ValidatePrimaryPlacementConstraintOnPromote| Bool, az alapértelmezett érték TRUE (igaz) |Dinamikus|Megadja, hogy a rendszer kiértékeli-e a szolgáltatás PlacementConstraint-kifejezését a feladatátvétel elsődleges preferencia-értékeként. |
|VerboseHealthReportLimit | Int, az alapértelmezett érték 20 | Dinamikus|Azt határozza meg, hogy a replikák hányszor legyenek felhelyezve az állapotra vonatkozó figyelmeztetés megkezdése előtt (ha a részletes állapot jelentéskészítés engedélyezve van). |
|NodeLoadsOperationalTracingEnabled | Bool, az alapértelmezett érték TRUE (igaz) |Dinamikus|Olyan konfiguráció, amely lehetővé teszi a csomópontok működési szerkezeti nyomkövetését az esemény-tárolóban. |
|NodeLoadsOperationalTracingInterval | TimeSpan, az alapértelmezett érték gyakori:: TimeSpan:: FromSeconds (20) | Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az az időköz, amellyel nyomon követhető a csomópontok minden egyes szolgáltatási tartományhoz tartozó Event Store-ba. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Az idő másodpercben, az alapértelmezett érték 900 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az az időtartam, ameddig a rendszer várakozik, mielőtt leállítja azokat a szolgáltatás-gazdagépeket, amelyek az alkalmazás frissítése során lezárult replikákkal rendelkeznek.|
|FabricUpgradeMaxReplicaCloseDuration | Az idő másodpercben, az alapértelmezett érték 900 |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. Az az időtartam, ameddig a rendszer várakozik, mielőtt leállítja azokat a szolgáltatás-gazdagépeket, amelyeken a háló frissítése közben lezárul a replikák. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (120)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az az időtartam, ameddig a rendszer várni fogja, mielőtt leállítja azokat a gazdagépeket, amelyeken a replikák beragadva vannak. Ha ez az érték 0, a rendszer nem utasítja a replikákat a bezáráshoz.|
|NodeDeactivationMaxReplicaCloseDuration | Az idő másodpercben, az alapértelmezett érték 900 |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az az időtartam, ameddig a rendszer várakozik a csomópont inaktiválása során lezárt replikákkal rendelkező szolgáltatások leállítása előtt. |
|PeriodicApiSlowTraceInterval | Az idő másodpercben, az alapértelmezett érték 5 perc |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. A PeriodicApiSlowTraceInterval határozza meg, hogy az API-figyelő milyen időközönként fogja követni a lassú API-hívásokat. |
|ReplicaChangeRoleFailureRestartThreshold|int, az alapértelmezett érték 10|Dinamikus| Egész. Itt adhatja meg, hogy az elsődleges előléptetés során hány API-hiba legyen alkalmazva az automatikus kockázatcsökkentő művelet (replika-újraindítás) után. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, alapértelmezett érték 2147483647|Dinamikus| Egész. Itt adhatja meg, hogy az elsődleges előléptetés során hány API-hiba után történjen a figyelmeztetési állapot jelentésének kiemelése.|
|ServiceApiHealthDuration | Az idő másodpercben, az alapértelmezett érték 30 perc |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. A ServiceApiHealthDuration határozza meg, hogy mennyi ideig kell várni a Service API futtatására, mielőtt bejelentjük, hogy az állapota nem kifogástalan. |
|ServiceReconfigurationApiHealthDuration | Az idő másodpercben, az alapértelmezett érték 30 |Dinamikus| Másodpercek alatt meg kell adni a TimeSpan. A ServiceReconfigurationApiHealthDuration határozza meg, hogy mennyi ideig várjon a Service API futtatása a nem megfelelő állapot jelentése előtt. Ez a rendelkezésre állást befolyásoló API-hívásokra vonatkozik.|

## <a name="replication"></a>Replikáció
| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, az alapértelmezett érték gyakori:: TimeSpan:: FromMilliseconds (15)|Statikus|Másodpercek alatt meg kell adni a TimeSpan. Meghatározza azt az időtartamot, ameddig a replikátor a művelet fogadása után várakozik a visszaigazolás visszaküldése előtt. Az ebben az időszakban fogadott egyéb műveletekhez a rendszer visszaküldi a visszaigazolásokat egy üzenetben – > csökkenti a hálózati forgalmat, de potenciálisan csökkenti a replikátor átviteli sebességét.|
|MaxCopyQueueSize|uint, alapértelmezett érték 1024|Statikus|Ez a maximális érték határozza meg a várólista kezdeti méretét, amely megőrzi a replikációs műveleteket. Vegye figyelembe, hogy a teljesítményének 2-nek kell lennie. Ha a futás közben a várólista erre a méretre nő, a rendszer az elsődleges és a másodlagos replikálók között szabályozza a műveletet.|
|MaxPrimaryReplicationQueueMemorySize|uint, az alapértelmezett érték 0|Statikus|Az elsődleges replikációs várólista maximális értéke bájtban megadva.|
|MaxPrimaryReplicationQueueSize|uint, alapértelmezett érték 1024|Statikus|Az elsődleges replikációs várólistában található műveletek maximális száma. Vegye figyelembe, hogy a teljesítményének 2-nek kell lennie.|
|MaxReplicationMessageSize|uint, alapértelmezett érték 52428800|Statikus|A replikációs műveletek maximális mérete. Az alapértelmezett érték 50 MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, az alapértelmezett érték 0|Statikus|A másodlagos replikálási várólista maximális értéke bájtban megadva.|
|MaxSecondaryReplicationQueueSize|uint, alapértelmezett érték 2048|Statikus|A másodlagos replikálási várólistában található műveletek maximális száma. Vegye figyelembe, hogy a teljesítményének 2-nek kell lennie.|
|QueueHealthMonitoringInterval|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (30)|Statikus|Másodpercek alatt meg kell adni a TimeSpan. Ez az érték határozza meg azt az időtartamot, amelyet a replikátor használ a replikációs műveletek várólistáján lévő összes figyelmeztetési/hiba állapottal kapcsolatos esemény figyelésére. A "0" érték letiltja az állapot figyelését |
|QueueHealthWarningAtUsagePercent|uint, alapértelmezett érték 80|Statikus|Ez az érték határozza meg a replikációs várólista használatát (százalékban), amely után a figyelmeztetést a nagy üzenetsor-használatról küldjük. Ezt a QueueHealthMonitoringInterval türelmi intervalluma után tesszük. Ha a várólista használata a türelmi intervallumban a százalék alá esik|
|ReplicatorAddress|karakterlánc, az alapértelmezett érték "localhost: 0"|Statikus|A végpont egy "IP: Port" karakterlánc formájában jelenik meg, amelyet az Windows Fabric-replikátor használ a más replikákkal való kapcsolat létesítéséhez a küldési/fogadási műveletek érdekében.|
|ReplicatorListenAddress|karakterlánc, az alapértelmezett érték "localhost: 0"|Statikus|A végpont egy "IP: Port" karakterlánc formájában, amelyet a Windows Fabric replikátor használ a más replikák műveleteinek fogadására.|
|ReplicatorPublishAddress|karakterlánc, az alapértelmezett érték "localhost: 0"|Statikus|A végpont egy "IP: Port" karakterlánc formájában, amelyet a Windows Fabric replikátor használ a műveletek más replikák számára történő elküldéséhez.|
|RetryInterval|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (5)|Statikus|Másodpercek alatt meg kell adni a TimeSpan. Egy művelet elvesztése vagy elutasítása esetén az időzítő meghatározza, hogy a replikátor milyen gyakran próbálkozzon újra a művelet elküldésével.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|IsEnabled|bool, az alapértelmezett érték FALSE |Statikus|Azt szabályozza, hogy a szolgáltatás engedélyezve van-e a fürtön. |

## <a name="runas"></a>Futtató

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|RunAsAccountName |karakterlánc, az alapértelmezett érték: "" |Dinamikus|A futtató fiók nevét jelzi. Erre csak a "DomainUser" vagy a "ManagedServiceAccount" típusú fiók esetében van szükség. Az érvényes értékek: "tartomány \ felhasználó" vagy " user@domain ". |
|RunAsAccountType|karakterlánc, az alapértelmezett érték: "" |Dinamikus|A futtató fiók típusát jelzi. Erre azért van szükség, mert a RunAs szakasz érvényes értékei a következők: "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|karakterlánc, az alapértelmezett érték: "" |Dinamikus|Azt jelzi, hogy a futtató fiók jelszava. Erre csak a "DomainUser" fióktípus esetében van szükség. |

## <a name="runas_dca"></a>RunAs_DCA

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|RunAsAccountName |karakterlánc, az alapértelmezett érték: "" |Dinamikus|A futtató fiók nevét jelzi. Erre csak a "DomainUser" vagy a "ManagedServiceAccount" típusú fiók esetében van szükség. Az érvényes értékek: "tartomány \ felhasználó" vagy " user@domain ". |
|RunAsAccountType|karakterlánc, az alapértelmezett érték: "" |Dinamikus|A futtató fiók típusát jelzi. Erre azért van szükség, mert a RunAs szakasz érvényes értékei a következők: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|karakterlánc, az alapértelmezett érték: "" |Dinamikus|Azt jelzi, hogy a futtató fiók jelszava. Erre csak a "DomainUser" fióktípus esetében van szükség. |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|RunAsAccountName |karakterlánc, az alapértelmezett érték: "" |Dinamikus|A futtató fiók nevét jelzi. Erre csak a "DomainUser" vagy a "ManagedServiceAccount" típusú fiók esetében van szükség. Az érvényes értékek: "tartomány \ felhasználó" vagy " user@domain ". |
|RunAsAccountType|karakterlánc, az alapértelmezett érték: "" |Dinamikus|A futtató fiók típusát jelzi. Erre azért van szükség, mert a RunAs szakasz érvényes értékei a következők: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|karakterlánc, az alapértelmezett érték: "" |Dinamikus|Azt jelzi, hogy a futtató fiók jelszava. Erre csak a "DomainUser" fióktípus esetében van szükség. |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|RunAsAccountName |karakterlánc, az alapértelmezett érték: "" |Dinamikus|A futtató fiók nevét jelzi. Erre csak a "DomainUser" vagy a "ManagedServiceAccount" típusú fiók esetében van szükség. Az érvényes értékek: "tartomány \ felhasználó" vagy " user@domain ". |
|RunAsAccountType|karakterlánc, az alapértelmezett érték: "" |Dinamikus|A futtató fiók típusát jelzi. Erre azért van szükség, mert a RunAs szakasz érvényes értékei a következők: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|karakterlánc, az alapértelmezett érték: "" |Dinamikus|Azt jelzi, hogy a futtató fiók jelszava. Erre csak a "DomainUser" fióktípus esetében van szükség. |

## <a name="security"></a>Biztonság
| **Paraméter** | **Megengedett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|karakterlánc, az alapértelmezett érték: ""|Statikus|HRE tanúsítvány-végpont formátuma, alapértelmezett Azure-beli kereskedelmi értéke, amely nem alapértelmezett környezetekhez van megadva, például Azure Government "https: \/ /login.microsoftonline.us/ {0} /federationmetadata/2007-06/federationmetadata.xml" |
|AADClientApplication|karakterlánc, az alapértelmezett érték: ""|Statikus|A natív ügyfélalkalmazás neve vagy azonosítója, amely a Fabric-ügyfeleket jelképezi |
|AADClusterApplication|karakterlánc, az alapértelmezett érték: ""|Statikus|A fürtöt jelölő webes API-alkalmazás neve vagy azonosítója |
|AADLoginEndpoint|karakterlánc, az alapértelmezett érték: ""|Statikus|HRE bejelentkezési végpont, alapértelmezett Azure Commercial, nem alapértelmezett környezethez van megadva, például Azure Government "https: \/ /login.microsoftonline.us" |
|AADTenantId|karakterlánc, az alapértelmezett érték: ""|Statikus|Bérlő azonosítója (GUID) |
|AcceptExpiredPinnedClusterCertificate|bool, az alapértelmezett érték FALSE|Dinamikus|Jelző, amely azt jelzi, hogy az ujjlenyomattal deklarált lejárt fürtözött tanúsítványok csak a fürt tanúsítványainak esetében érvényesek-e. a fürt életben tartásához. |
|AdminClientCertThumbprints|karakterlánc, az alapértelmezett érték: ""|Dinamikus|Az ügyfelek által a rendszergazdai szerepkörben használt tanúsítványok ujjlenyomatai megfelelnek. Vesszővel tagolt nevek listája. |
|AADTokenEndpointFormat|karakterlánc, az alapértelmezett érték: ""|Statikus|HRE jogkivonat-végpont, alapértelmezett Azure Commercial, nem alapértelmezett környezethez megadva, például Azure Government "https: \/ /login.microsoftonline.us/ {0} " |
|AdminClientClaims|karakterlánc, az alapértelmezett érték: ""|Dinamikus|A rendszergazdai ügyfelektől várt összes lehetséges jogcím; a ClientClaims megegyező formátum; Ez a lista belsőleg bekerül a ClientClaims; így nem kell ugyanazt a bejegyzést hozzáadnia a ClientClaims. |
|AdminClientIdentities|karakterlánc, az alapértelmezett érték: ""|Dinamikus|A Fabric-ügyfelek Windows-identitásai a rendszergazdai szerepkörben; a Kiemelt háló műveleteinek engedélyezésére szolgál. Vesszővel tagolt lista; Minden bejegyzés egy tartományi fiók neve vagy egy csoportnév. A kényelem érdekében a fabric.exe futtató fiók automatikusan hozzá van rendelve a rendszergazdai szerepkörhöz; tehát a csoport ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|karakterlánc, az alapértelmezett érték a/Home/sfuser/sfusercerts |Statikus|Az a mappa, ahol a AppRunAsAccountGroup X509 tanúsítványai és titkos kulcsai találhatók |
|CertificateExpirySafetyMargin|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromMinutes (43200)|Statikus|Másodpercek alatt meg kell adni a TimeSpan. A tanúsítvány lejáratának biztonsági margója; a tanúsítvány állapotáról szóló jelentés állapota az OK értékről figyelmeztetésre változik, ha a lejárat ennél közelebb van. Az alapértelmezett érték 30 nap. |
|CertificateHealthReportingInterval|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (3600 * 8)|Statikus|Másodpercek alatt meg kell adni a TimeSpan. A tanúsítvány állapotának jelentéskészítési intervallumának megadása; alapértelmezett érték: 8 óra; 0-ra való beállítás letiltja a tanúsítványok állapotának jelentését |
|ClientCertThumbprints|karakterlánc, az alapértelmezett érték: ""|Dinamikus|Az ügyfelek által a fürttel folytatott kommunikációhoz használt tanúsítványok ujjlenyomatai megfelelnek; a fürt ezt az engedélyt a bejövő kapcsolatok engedélyezésére használja. Vesszővel tagolt nevek listája. |
|ClientClaimAuthEnabled|bool, az alapértelmezett érték FALSE|Statikus|Azt jelzi, hogy engedélyezve van-e a jogcím-alapú hitelesítés az ügyfeleken; a True érték beállítása implicit módon beállítja a ClientRoleEnabled. |
|ClientClaims|karakterlánc, az alapértelmezett érték: ""|Dinamikus|Az ügyfelektől az átjáróhoz való csatlakozásra várt összes lehetséges jogcím. Ez egy "vagy" lista: ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry... minden ClaimsEntry egy "és" lista: ClaimType = ClaimValue && ClaimType = ClaimValue && ClaimType = ClaimValue... |
|ClientIdentities|karakterlánc, az alapértelmezett érték: ""|Dinamikus|A FabricClient Windows-identitásai; az elnevezési átjáró ezt használja a bejövő kapcsolatok engedélyezéséhez. Vesszővel tagolt lista; Minden bejegyzés egy tartományi fiók neve vagy egy csoportnév. A kényelem érdekében a fabric.exet futtató fiók automatikusan engedélyezve van; tehát a Group ServiceFabricAllowedUsers és a ServiceFabricAdministrators. |
|ClientRoleEnabled|bool, az alapértelmezett érték FALSE|Statikus|Azt jelzi, hogy engedélyezve van-e az ügyfél szerepköre; Ha igaz értékre van állítva, az ügyfelek az identitásuk alapján rendelnek hozzá szerepköröket. V2 esetén; ennek engedélyezése azt jelenti, hogy az ügyfél nem a AdminClientCommonNames/AdminClientIdentities csak olvasási műveleteket hajt végre. |
|ClusterCertThumbprints|karakterlánc, az alapértelmezett érték: ""|Dinamikus|A fürthöz való csatlakozásra engedélyezett tanúsítványok ujjlenyomatai megfelelnek; vesszővel tagolt nevek listája |
|ClusterCredentialType|karakterlánc, az alapértelmezett érték a "None"|Nem engedélyezett|A fürt biztonságossá tételéhez használandó biztonsági hitelesítő adatok típusát adja meg. Az érvényes értékek: "None/X509/Windows" |
|ClusterIdentities|karakterlánc, az alapértelmezett érték: ""|Dinamikus|Fürtcsomópontok Windows-identitásai; a fürt tagságának engedélyezéséhez használatos. Vesszővel tagolt lista; Minden bejegyzés egy tartományi fiók neve vagy egy csoport neve |
|ClusterSpn|karakterlánc, az alapértelmezett érték: ""|Nem engedélyezett|A fürt egyszerű neve; Ha a háló egyetlen tartományi felhasználóként fut (gMSA/tartományi felhasználói fiók). A bérlet-figyelők SPN-je fabric.exe: összevonási figyelők; belső replikációs figyelők; Runtime Service-figyelő és elnevezési átjáró figyelője. Ezt üresen kell hagyni, ha a háló gépi fiókként fut; Ebben az esetben az oldalsó számítási figyelő SPN-t a figyelő szállítási címe alapján kapcsolhatja össze. |
|CrlCheckingFlag|uint, alapértelmezett érték a 0x40000000|Dinamikus|Alapértelmezett tanúsítványlánc érvényesítésének jelzője; az összetevő-specifikus jelző felülbírálható; Példa: összevonás/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY beállítás értéke 0 letiltja a CRL-ellenőrzés a támogatott értékek teljes listáját a dwFlags CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, az alapértelmezett érték gyakori:: TimeSpan:: FromMinutes (15)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. A CRL-ellenőrzés letiltása egy adott tanúsítvány esetében az offline hiba miatt. Ha a CRL offline hibája figyelmen kívül hagyható. |
|CrlOfflineHealthReportTtl|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromMinutes (1440)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. |
|DisableFirewallRuleForDomainProfile| bool, az alapértelmezett érték TRUE (igaz) |Statikus| Azt jelzi, hogy a tűzfalszabály nem engedélyezhető-e a tartományi profilhoz |
|DisableFirewallRuleForPrivateProfile| bool, az alapértelmezett érték TRUE (igaz) |Statikus| Azt jelzi, hogy nincs-e engedélyezve a tűzfalszabály a magánhálózati profilhoz | 
|DisableFirewallRuleForPublicProfile| bool, az alapértelmezett érték TRUE (igaz) | Statikus|Azt jelzi, hogy nincs-e engedélyezve a tűzfalszabály a nyilvános profilhoz |
| EnforceLinuxMinTlsVersion | bool, az alapértelmezett érték FALSE | Statikus | Ha True értékre van állítva; csak a TLS 1.2-es és újabb verziója támogatott.  Ha hamis; a korábbi TLS-verziók támogatása. Csak a Linux rendszerre vonatkozik |
| EnforcePrevalidationOnSecurityChanges | bool, az alapértelmezett érték FALSE| Dinamikus | A fürt frissítésének viselkedését vezérlő jelző a biztonsági beállítások módosításainak észlelése esetén. Ha a értéke "true" (igaz), a fürt frissítése megkísérli annak biztosítását, hogy legalább az egyik megjelenítési szabálynak megfelelő tanúsítvány is átadhat egy megfelelő érvényesítési szabályt. Az előérvényesítést az új beállítások bármely csomópontra való alkalmazása előtt hajtja végre a rendszer, de a frissítés megkezdésének időpontjában csak a Fürtfelügyelő szolgáltatás elsődleges replikáját futtató csomóponton fut. Az alapértelmezett érték a "false" (hamis) értékre van állítva. az 7,1-es verziótól kezdődően a beállítás értéke "true" lesz az új Azure Service Fabric-fürtök esetében.|
|FabricHostSpn| karakterlánc, az alapértelmezett érték: "" |Statikus| A Hálóbeli egyszerű szolgáltatásnév neve; Ha a háló egyetlen tartományi felhasználóként (gMSA/tartományi felhasználói fiók) fut, és a Hálóbeli a számítógépfiók alatt fut. Ez a Hálóbeli IPC-figyelő SPN-je; Alapértelmezés szerint üresen kell hagyni, mivel a Hálóbeli a számítógépfiók alatt fut |
|IgnoreCrlOfflineError|bool, az alapértelmezett érték FALSE|Dinamikus|Azt jelzi, hogy a CRL offline hibát figyelmen kívül hagy-e, amikor a kiszolgálóoldali hitelesíti a bejövő ügyféltanúsítványt |
|IgnoreSvrCrlOfflineError|bool, az alapértelmezett érték TRUE (igaz)|Dinamikus|Azt jelzi, hogy a CRL offline hibát figyelmen kívül hagy-e, amikor az ügyféloldali ellenőrzi a bejövő kiszolgálói tanúsítványokat; az alapértelmezett érték TRUE (igaz). A visszavont kiszolgálói tanúsítványokkal való támadásoknak meg kell felelnie a DNS-nek; nehezebb, mint a visszavont Ügyféltanúsítványok. |
|ServerAuthCredentialType|karakterlánc, az alapértelmezett érték a "None"|Statikus|A FabricClient és a fürt közötti kommunikáció biztonságossá tételéhez használandó biztonsági hitelesítő adatok típusát jelzi. Az érvényes értékek: "None/X509/Windows" |
|ServerCertThumbprints|karakterlánc, az alapértelmezett érték: ""|Dinamikus|A fürt által az ügyfelekkel való kommunikációhoz használt ujjlenyomatai megfelelnek az ügyfelek ezt használják a fürt hitelesítéséhez. Vesszővel tagolt nevek listája. |
|SettingsX509StoreName| karakterlánc, az alapértelmezett érték a "MY"| Dinamikus|A Fabric által a konfiguráció védelme érdekében használt X509-tanúsítványtároló |
|UseClusterCertForIpcServerTlsSecurity|bool, az alapértelmezett érték FALSE|Statikus|Azt határozza meg, hogy a fürt tanúsítványa használható-e az IPC-kiszolgáló TLS átviteli egységének védelméhez |
|X509Folder|karakterlánc, az alapértelmezett érték a/var/lib/waagent|Statikus|Az a mappa, ahol a X509 tanúsítványok és titkos kulcsok találhatók |
|TLS1_2_CipherList| sztring| Statikus|Ha nem üres sztringre van beállítva; felülbírálja a TLS 1.2 és az alatt támogatott Cipher-listát. Tekintse meg az "OpenSSL-Rejtjelek" dokumentációját a támogatott Rejtjelek listájának beolvasásához, valamint a következő, a TLS 1.2-es verzióra vonatkozó erős titkosítási lista formátumának megjelenítéséhez: "ECDHE-ECDSA-AES256-GCM-SHA384: ECDHE-ECDSA-AES128-GCM-SHA256: ECDHE-RSA-AES256-GCM-SHA384: ECDHE-RSA-AES-128-GCM-SHA256: ECDHE-ECDSA-AES256-CBC-SHA384: ECDHE-ECDSA-AES128-CBC-sha256: ECDHE-RSA-AES256-CBC-SHA384: ECDHE-AES128-sha256-CBC-. a csak Linux rendszerre vonatkozik. |

## <a name="securityadminclientx509names"></a>Biztonság/AdminClientX509Names

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezés szerint nincs|Dinamikus|Ez a "Name" és a "value" pár listája. Minden "név" a tulajdonos köznapi neve vagy DnsName, amely rendszergazdai X509 hitelesítve van. A "név" értéknél a "value" a kiállítói ujjlenyomatai megfelelnek tartozó tanúsítvány vesszővel elválasztott listája, ha nem üres, a rendszergazdai Ügyféltanúsítványok közvetlen kiállítójának szerepelnie kell a listán. |

## <a name="securityclientaccess"></a>Biztonság/ClientAccess

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ActivateNode |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| A csomópont aktiválásához szükséges biztonsági konfiguráció. |
|CancelTestCommand |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Egy adott TestCommand megszakítása – ha az a járaton van. |
|CodePackageControl |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| A kódok újraindításához szükséges biztonsági konfiguráció. |
|Létrehozás |karakterlánc, az alapértelmezett érték a "rendszergazda" | Dinamikus|Biztonsági konfiguráció az alkalmazások létrehozásához. |
|CreateComposeDeployment|karakterlánc, az alapértelmezett érték a "rendszergazda"| Dinamikus|Létrehoz egy összeállítási központi telepítést, amelyet az összeállítási fájlok ismertetnek. |
|CreateGatewayResource|karakterlánc, az alapértelmezett érték a "rendszergazda"| Dinamikus|Átjáró erőforrásának létrehozása |
|CreateName |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus|Biztonsági konfiguráció a névadási URI létrehozásához. |
|CreateNetwork|karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus|Létrehoz egy tároló hálózatot |
|CreateService |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| A szolgáltatás létrehozásának biztonsági beállításai. |
|CreateServiceFromTemplate |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus|Biztonsági konfiguráció a szolgáltatás létrehozásához sablonból. |
|CreateVolume|karakterlánc, az alapértelmezett érték a "rendszergazda"|Dinamikus|Kötet létrehozása |
|Deactivatenode művelet ( |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció a csomópontok inaktiválásához. |
|DeactivateNodesBatch |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció több csomópont inaktiválásához. |
|Törlés |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| A rendszerkép-tároló ügyfél-törlési műveletének biztonsági beállításai. |
|Alkalmazástörlés |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Az alkalmazás törlésének biztonsági beállításai. |
|DeleteComposeDeployment|karakterlánc, az alapértelmezett érték a "rendszergazda"| Dinamikus|Az összeállítási központi telepítés törlése |
|DeleteGatewayResource|karakterlánc, az alapértelmezett érték a "rendszergazda"| Dinamikus|Átjáró erőforrásának törlése |
|DeleteName |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus|Biztonsági konfiguráció az URI-azonosító törléséhez. |
|DeleteNetwork|karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus|Tároló hálózat törlése |
|DeleteService művelet |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus|Biztonsági konfiguráció a szolgáltatás törléséhez. |
|DeleteVolume|karakterlánc, az alapértelmezett érték a "rendszergazda"|Dinamikus|Töröl egy kötetet.| 
|EnumerateProperties |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" | Dinamikus|Biztonsági konfiguráció az elnevezési tulajdonság enumerálásához. |
|EnumerateSubnames |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Az URI-enumerálások elnevezésének biztonsági konfigurációja. |
|FileContent |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| A rendszerkép-tároló ügyfél fájlátviteli szolgáltatásának biztonsági konfigurációja (a fürtön kívül). |
|FileDownload |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció a rendszerkép-tároló ügyféloldali fájljának letöltésének kezdeményezéséhez (a fürtön kívül). |
|FinishInfrastructureTask |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Az infrastrukturális feladatok befejezésének biztonsági beállításai. |
|GetChaosReport | karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| A káosz állapotának beolvasása egy adott időtartományon belül. |
|GetClusterConfiguration | karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" | Dinamikus|Egy partíció GetClusterConfiguration okoz. |
|GetClusterConfigurationUpgradeStatus | karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Egy partíció GetClusterConfigurationUpgradeStatus okoz. |
|GetFabricUpgradeStatus |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Biztonsági konfiguráció a fürt frissítési állapotának lekérdezéséhez. |
|GetFolderSize |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus|A FileStoreService-mappa méretének lekérése biztonsági konfiguráció |
|GetNodeDeactivationStatus |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció az inaktiválási állapot ellenőrzéséhez. |
|GetNodeTransitionProgress | karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Biztonsági konfiguráció a csomópont-átváltási parancs előrehaladásának megkezdéséhez. |
|GetPartitionDataLossProgress | karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" | Dinamikus|Beolvassa egy meghívás-adatvesztés API-hívásának állapotát. |
|GetPartitionQuorumLossProgress | karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Beolvassa az egy meghívás Kvórumának elvesztése API-hívás állapotát. |
|GetPartitionRestartProgress | karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Beolvassa az újraindítási partíció API-hívásának folyamatát. |
|GetSecrets|karakterlánc, az alapértelmezett érték a "rendszergazda"|Dinamikus|Titkos értékek beolvasása |
|GetServiceDescription |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Biztonsági konfiguráció a hosszú lekérdezési szolgáltatás értesítéseihez és a szolgáltatás leírásának olvasásához. |
|GetStagingLocation |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció a rendszerkép-tároló ügyfél átmeneti helyének lekéréséhez. |
|GetStoreLocation |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció a rendszerkép-tárolóban lévő ügyfél-tároló helyének lekéréséhez. |
|GetUpgradeOrchestrationServiceState|karakterlánc, az alapértelmezett érték a "rendszergazda"| Dinamikus|Egy partíción GetUpgradeOrchestrationServiceState indukál |
|GetUpgradesPendingApproval |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Egy partíció GetUpgradesPendingApproval okoz. |
|GetUpgradeStatus |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Biztonsági konfiguráció az alkalmazás frissítési állapotának lekérdezéséhez. |
|InternalList |karakterlánc, az alapértelmezett érték a "rendszergazda" | Dinamikus|Biztonsági konfiguráció a rendszerkép-tároló ügyfél-fájlok listázási műveletéhez (belső). |
|InvokeContainerApi|karakterlánc, az alapértelmezett érték a "rendszergazda"|Dinamikus|Tároló API meghívása |
|InvokeInfrastructureCommand |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Az infrastruktúra-feladatok felügyeleti parancsainak biztonsági konfigurációja. |
|InvokeInfrastructureQuery |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" | Dinamikus|Az infrastruktúra-feladatok lekérdezésének biztonsági beállításai. |
|Lista |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" | Dinamikus|Biztonsági konfiguráció a rendszerkép-tárolói ügyfél-fájllista művelethez. |
|MoveNextFabricUpgradeDomain |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció a fürt frissítéseinek egy explicit frissítési tartománnyal való folytatásához. |
|MoveNextUpgradeDomain |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció egy explicit frissítési tartománnyal folytatott alkalmazások frissítéseinek folytatásához. |
|MoveReplicaControl |karakterlánc, az alapértelmezett érték a "rendszergazda" | Dinamikus|Replika áthelyezése. |
|NameExists |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" | Dinamikus|Az URI-azonosítók meglétét ellenőrző biztonsági konfiguráció. |
|NodeControl |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| A kezdéshez szükséges biztonsági konfiguráció; leállítása és a csomópontok újraindítása. |
|Nodestateremoved művelet |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| A jelentési csomópont állapotának biztonsági konfigurációja el lett távolítva. |
|Ping |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Az ügyfél pingelésének biztonsági konfigurációja. |
|PredeployPackageToNode |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Előtelepítési API. |
|PrefixResolveService |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Biztonsági konfiguráció a panasztételi szolgáltatás előtagjának feloldásához. |
|PropertyReadBatch |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Biztonsági konfiguráció az elnevezési tulajdonság olvasási műveleteihez. |
|PropertyWriteBatch |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus|Az elnevezési tulajdonság írási műveleteinek biztonsági beállításai. |
|ProvisionApplicationType |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Az alkalmazás típusú kiépítés biztonsági konfigurációja. |
|ProvisionFabric |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció az MSI és/vagy a cluster manifest kiépítés esetén. |
|Lekérdezés |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Biztonsági konfiguráció lekérdezéseknél. |
|Recoverpartition művelet fejeződött |karakterlánc, az alapértelmezett érték a "rendszergazda" | Dinamikus|A partíciók helyreállításának biztonsági beállításai. |
|RecoverPartitions |karakterlánc, az alapértelmezett érték a "rendszergazda" | Dinamikus|A partíciók helyreállításának biztonsági beállításai. |
|Recoverservicepartitions művelet fejeződött |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| A szolgáltatási partíciók helyreállításának biztonsági beállításai. |
|Recoversystempartitions művelet |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció a rendszerszolgáltatási partíciók helyreállításához. |
|RemoveNodeDeactivations |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció az Inaktiválás több csomóponton történő visszafordításához. |
|ReportFabricUpgradeHealth |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció a fürt verziófrissítésének folytatásához a jelenlegi verziófrissítési folyamattal. |
|ReportFault |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció jelentési hiba esetén. |
|ReportHealth |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció a jelentéskészítési állapothoz. |
|ReportUpgradeHealth |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció az alkalmazások verziófrissítésének folytatásához a jelenlegi verziófrissítési folyamattal. |
|ResetPartitionLoad |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Biztonsági konfiguráció a (Z alaphelyzetbe állításához. |
|ResolveNameOwner |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" | Dinamikus|A névhasználati URI tulajdonosának feloldásához szükséges biztonsági konfiguráció. |
|ResolvePartition |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" | Dinamikus|A rendszerszolgáltatások feloldásának biztonsági beállításai. |
|ResolveService |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Biztonsági konfiguráció a panasztételi szolgáltatás feloldásához. |
|ResolveSystemService|karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó"|Dinamikus| Rendszerszolgáltatások feloldásának biztonsági beállításai |
|RollbackApplicationUpgrade |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció az alkalmazások verziófrissítésének visszaállításához. |
|RollbackFabricUpgrade |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Biztonsági konfiguráció a fürt frissítéseinek visszaállításához. |
|ServiceNotifications |karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| Az eseményvezérelt szolgáltatás értesítéseinek biztonsági beállításai. |
|SetUpgradeOrchestrationServiceState|karakterlánc, az alapértelmezett érték a "rendszergazda"| Dinamikus|Egy partíción SetUpgradeOrchestrationServiceState indukál |
|StartApprovedUpgrades |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Egy partíció StartApprovedUpgrades okoz. |
|StartChaos |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Elindítja a káoszt – ha még nem indult el. |
|StartClusterConfigurationUpgrade |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Egy partíció StartClusterConfigurationUpgrade okoz. |
|StartInfrastructureTask |karakterlánc, az alapértelmezett érték a "rendszergazda" | Dinamikus|Az infrastruktúra-feladatok indításának biztonsági beállításai. |
|StartNodeTransition |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Csomópont-átmenet indításának biztonsági beállításai. |
|StartPartitionDataLoss |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Kiváltja az adatvesztést egy partíción. |
|StartPartitionQuorumLoss |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Egy partíció Kvórumának elvesztését idézi elő. |
|StartPartitionRestart |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Egyidejűleg újraindítja egy partíció egy részét vagy az összes replikáját. |
|StopChaos |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Leállítja a káoszt – ha már elindult. |
|ToggleVerboseServicePlacementHealthReporting | karakterlánc, az alapértelmezett érték a "rendszergazda \| \| felhasználó" |Dinamikus| A biztonsági konfiguráció a részletes ServicePlacement HealthReporting. |
|UnprovisionApplicationType |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Az alkalmazás típusának kiépítéséhez szükséges biztonsági konfiguráció. |
|UnprovisionFabric |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Az MSI-hez és/vagy a fürthöz tartozó jegyzékfájl biztonsági konfigurációja nem építhető ki. |
|UnreliableTransportControl |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Nem megbízható átvitel a viselkedések hozzáadásához és eltávolításához. |
|Updateservice művelet |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus|A szolgáltatás frissítéseinek biztonsági konfigurációja. |
|UpgradeApplication |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| Az alkalmazások frissítéseinek megkezdéséhez vagy megszakításához szükséges biztonsági konfiguráció. |
|UpgradeComposeDeployment|karakterlánc, az alapértelmezett érték a "rendszergazda"| Dinamikus|Az összeállítás központi telepítésének frissítése |
|UpgradeFabric |karakterlánc, az alapértelmezett érték a "rendszergazda" |Dinamikus| A fürt frissítéseinek indítására szolgáló biztonsági konfiguráció. |
|Feltöltés |karakterlánc, az alapértelmezett érték a "rendszergazda" | Dinamikus|A rendszerkép-tároló ügyfél-feltöltési műveletének biztonsági konfigurációja. |

## <a name="securityclientcertificateissuerstores"></a>Biztonság/ClientCertificateIssuerStores

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, alapértelmezés szerint nincs |Dinamikus|X509 kiállítói tanúsítványok tárolói az Ügyféltanúsítványok számára; Név = clientIssuerCN; Value = a tárolók vesszővel tagolt listája |

## <a name="securityclientx509names"></a>Biztonság/ClientX509Names

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezés szerint nincs|Dinamikus|Ez a "Name" és a "value" pár listája. Minden "név" a tulajdonos köznapi neve vagy DnsName, amely az X509 számára van engedélyezve. Az adott "név" értéknél a "value" a kiállítói ujjlenyomatai megfelelnek tartozó tanúsítvány vesszővel elválasztott listája, ha nem üres, az Ügyféltanúsítványok közvetlen kiállítójának szerepelnie kell a listán.|

## <a name="securityclustercertificateissuerstores"></a>Biztonság/ClusterCertificateIssuerStores

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, alapértelmezés szerint nincs |Dinamikus|X509 kiállítói tanúsítványok tárolói; Név = clusterIssuerCN; Value = a tárolók vesszővel tagolt listája |

## <a name="securityclusterx509names"></a>Biztonság/ClusterX509Names

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezés szerint nincs|Dinamikus|Ez a "Name" és a "value" pár listája. Minden "név" a fürt műveleteihez jóváhagyott X509-tanúsítványok tulajdonos köznapi neve vagy DnsName. Az adott "név" értéknél a "value" a kiállítói ujjlenyomatai megfelelnek tartozó tanúsítványok vesszővel elválasztott listája, ha nem üres, a fürt tanúsítványainak közvetlen kiállítójának szerepelnie kell a listán.|

## <a name="securityservercertificateissuerstores"></a>Biztonság/ServerCertificateIssuerStores

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, alapértelmezés szerint nincs |Dinamikus|X509 kiállítói tanúsítványok tárolói; Név = serverIssuerCN; Value = a tárolók vesszővel tagolt listája |

## <a name="securityserverx509names"></a>Biztonság/ServerX509Names

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezés szerint nincs|Dinamikus|Ez a "Name" és a "value" pár listája. Az egyes "nevek" a X509-tanúsítványok tulajdonos köznapi neve vagy DnsName. Az adott "név" értéknél a "value" a kiállítói ujjlenyomatai megfelelnek tartozó tanúsítvány vesszővel elválasztott listája, ha nem üres, a kiszolgálói tanúsítványok közvetlen kiállítójának szerepelnie kell a listán.|

## <a name="setup"></a>Telepítés

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ContainerNetworkName|karakterlánc, az alapértelmezett érték: ""| Statikus |A hálózati név, amelyet a Container Network beállításakor kell használni.|
|ContainerNetworkSetup|bool, az alapértelmezett érték a FALSE (Linux) és az alapértelmezett érték TRUE (Windows)| Statikus |Azt határozza meg, hogy a tároló hálózatot kell-e beállítani.|
|FabricDataRoot |Sztring | Nem engedélyezett |Service Fabric az adatgyökérkönyvtárat. Az Azure alapértelmezett értéke d:\svcfab |
|FabricLogRoot |Sztring | Nem engedélyezett |Service Fabric-napló gyökérkönyvtára. Itt vannak elhelyezve az SF-naplók és-Nyomkövetések. |
|NodesToBeRemoved|karakterlánc, az alapértelmezett érték: ""| Dinamikus |Azok a csomópontok, amelyeket el kell távolítani a konfiguráció frissítésének részeként. (Csak önálló telepítés esetén)|
|ServiceRunAsAccountName |Sztring | Nem engedélyezett |Annak a fióknak a neve, amelyben a Fabric Host szolgáltatás futtatására kerül sor. |
|SkipContainerNetworkResetOnReboot|bool, az alapértelmezett érték FALSE|Nem lefaggyúzott|Megadhatja, hogy kihagyja-e a Container Network újraindítását.|
|SkipFirewallConfiguration |Bool, az alapértelmezett érték false | Nem engedélyezett |Megadja, hogy a tűzfal beállításait a rendszeren kell-e beállítani. Ez csak akkor érvényes, ha a Windows tűzfalat használja. Ha harmadik féltől származó tűzfalakat használ, akkor meg kell nyitnia a rendszer és a használni kívánt alkalmazások portját. |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Szolgáltatók |string (alapértelmezett érték: "DSTS") |Statikus|Az engedélyezni kívánt jogkivonat-ellenőrző szolgáltatók vesszővel tagolt listája (érvényes szolgáltatók a következők: DSTS; HRE). Jelenleg csak egy szolgáltató engedélyezhető bármikor. |

## <a name="traceetw"></a>Nyomkövetés/ETW

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Level |Int, az alapértelmezett érték 4 | Dinamikus |A nyomkövetési ETW szintje 1, 2, 3, 4 értéket vehet igénybe. A támogatáshoz meg kell őriznie a nyomkövetési szintet 4 |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Az idő másodpercben, az alapértelmezett érték 0,015 | Statikus | Másodpercek alatt meg kell adni a TimeSpan. Meghatározza azt az időtartamot, ameddig a replikátor a művelet fogadása után várakozik a visszaigazolás visszaküldése előtt. Az ebben az időszakban fogadott egyéb műveletekhez a rendszer visszaküldi a visszaigazolásokat egy üzenetben – > csökkenti a hálózati forgalmat, de potenciálisan csökkenti a replikátor átviteli sebességét. |
|MaxCopyQueueSize |Uint, alapértelmezett érték 16384 | Statikus |Ez a maximális érték határozza meg a várólista kezdeti méretét, amely megőrzi a replikációs műveleteket. Vegye figyelembe, hogy a teljesítményének 2-nek kell lennie. Ha a futás közben a várólista erre a méretre nő, a rendszer az elsődleges és a másodlagos replikálók között szabályozza a műveletet. |
|MaxPrimaryReplicationQueueMemorySize |Uint, az alapértelmezett érték 0 | Statikus |Az elsődleges replikációs várólista maximális értéke bájtban megadva. |
|MaxPrimaryReplicationQueueSize |Uint, alapértelmezett érték 8192 | Statikus |Az elsődleges replikációs várólistában található műveletek maximális száma. Vegye figyelembe, hogy a teljesítményének 2-nek kell lennie. |
|MaxReplicationMessageSize |Uint, alapértelmezett érték 52428800 | Statikus | A replikációs műveletek maximális mérete. Az alapértelmezett érték 50 MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, az alapértelmezett érték 0 | Statikus |A másodlagos replikálási várólista maximális értéke bájtban megadva. |
|MaxSecondaryReplicationQueueSize |Uint, alapértelmezett érték 16384 | Statikus |A másodlagos replikálási várólistában található műveletek maximális száma. Vegye figyelembe, hogy a teljesítményének 2-nek kell lennie. |
|ReplicatorAddress |karakterlánc, az alapértelmezett érték "localhost: 0" | Statikus | A végpont egy "IP: Port" karakterlánc formájában jelenik meg, amelyet az Windows Fabric-replikátor használ a más replikákkal való kapcsolat létesítéséhez a küldési/fogadási műveletek érdekében. |

## <a name="transport"></a>Átvitel
| **Paraméter** | **Megengedett értékek** |**Frissítési szabályzat** |**Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (60)|Statikus|Másodpercek alatt meg kell adni a TimeSpan. Időtúllépés a bejövő és a fogadó oldalon (beleértve a biztonsági egyeztetést biztonságos módban) a kapcsolatok beállításához |
|FrameHeaderErrorCheckingEnabled|bool, az alapértelmezett érték TRUE (igaz)|Statikus|Alapértelmezett beállítás a frame fejlécének hibaellenőrzés nem biztonságos módban; az összetevő-beállítás felülbírálja ezt a beállítást. |
|MessageErrorCheckingEnabled|bool, az alapértelmezett érték TRUE (igaz)|Statikus|Alapértelmezett beállítás az üzenet fejlécének és törzsének hibaellenőrzés nem biztonságos módban; az összetevő-beállítás felülbírálja ezt a beállítást. |
|ResolveOption|karakterlánc, az alapértelmezett érték a "meghatározatlan"|Statikus|Meghatározza a teljes tartománynév feloldásának módját.  Az érvényes értékek: "meghatározatlan/IPv4/IPv6". |
|SendTimeout|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (300)|Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Küldési időtúllépés a beragadt kapcsolat észleléséhez. A TCP-hibajelentések nem megbízhatók bizonyos környezetekben. Ezt a rendelkezésre álló hálózati sávszélesség és a kimenő adatforgalom mérete ( \* MaxMessageSize \/ \* SendQueueSizeLimit) alapján kell módosítani. |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool, az alapértelmezett érték TRUE (igaz) |Statikus| Az automatikus lekérdezési és frissítési művelet egy cél-állapotú fájl alapján. |
|AutoupgradeInstallEnabled|Bool, az alapértelmezett érték FALSE|Statikus|A kód frissítési műveletének automatikus lekérdezése, üzembe helyezése és telepítése a cél-állapot fájl alapján.|
|GoalStateExpirationReminderInDays|int, az alapértelmezett érték 30|Statikus|Megadja, hogy hány nap elteltével jelenjen meg a cél állapotának emlékeztetője.|
|MinReplicaSetSize |Int, az alapértelmezett érték 0 |Statikus |A UpgradeOrchestrationService MinReplicaSetSize.|
|PlacementConstraints | karakterlánc, az alapértelmezett érték: "" |Statikus| A UpgradeOrchestrationService PlacementConstraints. |
|QuorumLossWaitDuration | Az idő másodpercben, az alapértelmezett érték a MaxValue |Statikus| Másodpercek alatt meg kell adni a TimeSpan. A UpgradeOrchestrationService QuorumLossWaitDuration. |
|ReplicaRestartWaitDuration | Az idő másodpercben, az alapértelmezett érték 60 perc|Statikus| Másodpercek alatt meg kell adni a TimeSpan. A UpgradeOrchestrationService ReplicaRestartWaitDuration. |
|StandByReplicaKeepDuration | Idő (másodperc), az alapértelmezett érték 60 *24* 7 perc |Statikus| Másodpercek alatt meg kell adni a TimeSpan. A UpgradeOrchestrationService StandByReplicaKeepDuration. |
|TargetReplicaSetSize |Int, az alapértelmezett érték 0 |Statikus |A UpgradeOrchestrationService TargetReplicaSetSize. |
|UpgradeApprovalRequired | Bool, az alapértelmezett érték false | Statikus|A kód frissítésének beállításához rendszergazdai jóváhagyásra van szükség a továbblépés előtt. |

## <a name="upgradeservice"></a>UpgradeService

| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|BaseUrl | karakterlánc, az alapértelmezett érték: "" |Statikus|A UpgradeService BaseUrl. |
|ClusterId | karakterlánc, az alapértelmezett érték: "" |Statikus|A UpgradeService ClusterId. |
|CoordinatorType | string (alapértelmezett érték: "WUTest")|Nem engedélyezett|A UpgradeService CoordinatorType. |
|MinReplicaSetSize | Int, az alapértelmezett érték 2 |Nem engedélyezett| A UpgradeService MinReplicaSetSize. |
|OnlyBaseUpgrade | Bool, az alapértelmezett érték false |Dinamikus|A UpgradeService OnlyBaseUpgrade. |
|PlacementConstraints |karakterlánc, az alapértelmezett érték: "" |Nem engedélyezett|A frissítési szolgáltatás PlacementConstraints. |
|PollIntervalInSeconds|TimeSpan, alapértelmezés szerint gyakori:: TimeSpan:: FromSeconds (60) |Dinamikus|Másodpercek alatt meg kell adni a TimeSpan. Az ARM-felügyeleti műveletek UpgradeService-lekérdezési időköze. |
|TargetReplicaSetSize | Int, az alapértelmezett érték 3 |Nem engedélyezett| A UpgradeService TargetReplicaSetSize. |
|TestCabFolder | karakterlánc, az alapértelmezett érték: "" |Statikus| A UpgradeService TestCabFolder. |
|X509FindType | karakterlánc, az alapértelmezett érték: ""|Dinamikus| A UpgradeService X509FindType. |
|X509FindValue | karakterlánc, az alapértelmezett érték: "" |Dinamikus| A UpgradeService X509FindValue. |
|X509SecondaryFindValue | karakterlánc, az alapértelmezett érték: "" |Dinamikus| A UpgradeService X509SecondaryFindValue. |
|X509StoreLocation | karakterlánc, az alapértelmezett érték: "" |Dinamikus| A UpgradeService X509StoreLocation. |
|X509StoreName | karakterlánc, az alapértelmezett érték a "My"|Dinamikus|A UpgradeService X509StoreName. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities
| **Paraméter** | **Megengedett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PropertyGroup| UserServiceMetricCapacitiesMap, alapértelmezés szerint nincs | Statikus | A felhasználói szolgáltatások erőforrás-irányítási határértékeinek gyűjteményének statikusnak kell lennie, mivel az automatikus észlelési logikát érint |

## <a name="next-steps"></a>Következő lépések
További információ: Azure- [fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-azure.md) és [önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md).
