---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 53d0352448ca6ad231ecc285cc7bf795bf74d9e7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497310"
---
## <a name="azure-security-benchmark"></a>Azure-biztonsági teljesítményteszt

Az [Azure biztonsági teljesítményteszt](../../../../articles/security/benchmarks/overview.md) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja felhőalapú megoldásait az Azure-ban. A szolgáltatás azure-biztonsági teljesítményteszthez való teljes leképezését az Azure-biztonsági teljesítményteszt leképezési [fájljait lásd:](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatás elérhető beépített beépítettei hogyan érhetők el erre a megfelelőségi szabványra, tekintse meg a Azure Policy való megfelelésről – [Azure-biztonsági teljesítményteszt](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Hálózati biztonság |NS-2 |Magánhálózatok összekapcsolása |[Azure Cache for Redis virtuális hálózaton belül kell lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|Adatvédelem |DP-4 |Bizalmas adatok titkosítása az átvitel során |[Csak a saját Azure Cache for Redis kell engedélyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure biztonsági teljesítményteszt v1

Az [Azure biztonsági teljesítményteszt](../../../../articles/security/benchmarks/overview.md) javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja felhőalapú megoldásait az Azure-ban. A szolgáltatás azure-biztonsági teljesítményteszthez való teljes leképezését az Azure-biztonsági teljesítményteszt leképezési [fájljait lásd:](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Annak áttekintését, hogy az Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el erre a megfelelőségi szabványra, tekintse meg a Azure Policy való megfelelésről – [Azure biztonsági teljesítményteszt](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Adatvédelem |4.4 |Az átvitel során bizalmas adatok titkosítása |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC 3. szint

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, lásd: Azure Policy jogszabályi megfelelőség – [CMMC 3. szintje.](../../../../articles/governance/policy/samples/cmmc-l3.md)
További információ erről a megfelelőségi szabványról: [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Hozzáférés-vezérlés |AC.1.002 |Korlátozza az információs rendszerek hozzáférését az arra jogosult felhasználók által végrehajtható tranzakciókra és funkciókra. |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Rendszer- és kommunikációvédelem |SC.1.175 |A kommunikáció (azaz a szervezeti rendszerek által továbbított vagy fogadott információk) figyelése, szabályozása és védelme a szervezeti rendszerek külső határainál és kulcsfontosságú belső határainál. |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Rendszer- és kommunikációvédelem |SC.3.185 |Olyan kriptográfiai mechanizmusokat alkalmaz, amelyek megakadályozzák a CUI jogosulatlan nyilvánosságra hozatalát az átvitel során, kivéve, ha más fizikai védelem védi őket. |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Annak áttekintését, hogy az Azure Policy összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el erre a megfelelőségi szabványra, lásd: [Azure Policy regulatory compliance - HIPAA HITRUST 9.2 (A HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)szabványnak megfelelő szabályozásnak való megfelelőség).
További információ erről a megfelelőségi szabványról: [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Hálózati kapcsolat vezérlése |0809.01n2Organizational.1234 - 01.n |A hálózati forgalom szabályozása a szervezet hozzáférés-vezérlési szabályzatának megfelelően történik a tűzfalon és más, a hálózattal kapcsolatos korlátozásokon keresztül az egyes hálózati hozzáférési pontokkal vagy külső távközlési szolgáltatás felügyelt adapterével. |[Csak a saját Azure Cache for Redis kell engedélyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Hálózati kapcsolat vezérlése |0810.01n2Organizational.5 - 01.n |Az átvitt adatok biztonságosak, és legalább nyílt, nyilvános hálózatokon titkosítva vannak. |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Hálózati kapcsolat vezérlése |0811.01n2Organizational.6 - 01.n |A forgalmi szabályzat alóli kivételek dokumentálva vannak egy támogató küldetési/üzleti okkal, a kivétel időtartamával, és legalább évente felülvizsgálva vannak; A forgalomirányítási szabályzat kivételei el vannak távolítva, ha már nem támogatja egy explicit küldetés/üzleti szükség. |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Hálózati kapcsolat vezérlése |0812.01n2Organizational.8 - 01.n |A nem távoli kapcsolatot létesítő távoli eszközök nem kommunikálhatnak külső (távoli) erőforrásokkal. |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Hálózati kapcsolat vezérlése |0814.01n1Szervezeti.12 – 01.n |A felhasználók a hozzáférés-vezérlési szabályzatnak, valamint a klinikai és üzleti alkalmazások követelményeinek megfelelően korlátozva vannak a felhasználók számára a belső hálózathoz való csatlakozásra vonatkozó alapértelmezett és kivételi tiltási szabályzattal a felügyelt felületeken. |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Külső felekkel kapcsolatos kockázatok azonosítása |1451.05iCSPSzervezeti.2 – 05.i |A felhőszolgáltatók olyan vezérlőket terveznek és valósítanak meg, amelyek mérsékelik és tartalmazzák az adatbiztonsági kockázatokat a feladatok, a szerepköralapú hozzáférés és a legkevesebb jogosultsággal rendelkező hozzáférés megfelelő elkülönítésével az ellátási láncban dolgozó összes személy számára. |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Online tranzakciók |0946.09y2Organizational.14 - 09.y |A szervezetnek a tranzakcióban érintett összes félnek titkosítást kell használnia, és elektronikus aláírásokat kell használnia. |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

Annak áttekintését, Azure Policy az összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el erre a megfelelőségi szabványra, lásd: Azure Policy jogszabályi megfelelőség [– ISO 27001:2013.](../../../../articles/governance/policy/samples/iso-27001.md)
További információ erről a megfelelőségi szabványról: [ISO 27001:2013.](https://www.iso.org/isoiec-27001-information-security.html)

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Titkosítás |10.1.1 |Titkosítási vezérlők használatára vonatkozó szabályzat |[Csak a saját Azure Cache for Redis kell engedélyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Kommunikációs biztonság |13.2.1 |Információátviteli házirendek és eljárások |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="new-zealand-ism-restricted"></a>Új-Zéland isM korlátozott

Annak áttekintését, hogy Azure Policy összes Azure-szolgáltatás elérhető beépített beépítettei hogyan érhetők el erre a megfelelőségi szabványra, tekintse meg a Azure Policy jogszabályi megfelelőség [– Új-Zéland ISM korlátozott](../../../../articles/governance/policy/samples/new-zealand-ism.md).
További információ erről a megfelelőségi szabványról: [Új-Zéland ISM korlátozott](https://www.nzism.gcsb.govt.nz/).

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Szoftverbiztonság |SS-8 |14.5.8-as webalkalmazások |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Adatkezelés |DM-6 |20.4.4 Adatbázisfájlok |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Ha meg Azure Policy, hogy az összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, tekintse meg a [következőt: Azure Policy Regulatory Compliance - NIST SP 800-171 R2 ( Szabályozási megfelelőség – NIST SP 800-171 R2).](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md)
További információ erről a megfelelőségi szabványról: [NIST SP 800-171 R2.](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzat verziója<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Rendszer- és kommunikációvédelem |3.13.1 |A kommunikáció (azaz a szervezeti rendszerek által továbbított vagy fogadott információk) figyelése, szabályozása és védelme a szervezeti rendszerek külső határainál és kulcsfontosságú belső határainál. |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Rendszer- és kommunikációvédelem |3.13.8 |Olyan kriptográfiai mechanizmusokat alkalmaz, amelyek megakadályozzák a CUI jogosulatlan nyilvánosságra hozatalát az átvitel során, kivéve, ha más fizikai védelem védi őket. |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Annak áttekintését, Azure Policy az összes Azure-szolgáltatáshoz elérhető beépített funkciók hogyan érhetők el ehhez a megfelelőségi szabványhoz, tekintse meg a [Azure Policy Regulatory Compliance - NIST SP 800-53 R4 (Szabályozási megfelelőség – NIST SP 800-53 R4) cikkeket.](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md)
További információ erről a megfelelőségi szabványról: [NIST SP 800-53 R4.](https://nvd.nist.gov/800-53)

|Tartomány |Vezérlőazonosító |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Szabályzatverzió<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Rendszer- és kommunikációvédelem |SC-8 (1) |Átvitel titkosságának és integritásának \| titkosítása vagy alternatív fizikai védelem |[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

