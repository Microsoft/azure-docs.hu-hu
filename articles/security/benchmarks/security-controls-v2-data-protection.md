---
title: Azure Security teljesítményteszt v2 – adatvédelem
description: Az Azure Security teljesítményteszt v2 adatvédelme
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: c8d907062835f18393946b04f1f1e9d5ec345411
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735760"
---
# <a name="security-control-v2-data-protection"></a>Biztonság-vezérlés v2: Adatvédelem

Az adatvédelem magában foglalja az adatvédelem inaktív állapotban, továbbításkor és a jogosult hozzáférési mechanizmusok útján történő felügyeletét. Ez magában foglalja a bizalmas adategységek felderítését, osztályozását, védelmének és figyelését a hozzáférés-vezérlés, a titkosítás és az Azure-beli naplózás használatával.

A megfelelő beépített Azure Policy megjelenítéséhez tekintse meg [Az Azure Security benchmark szabályozási megfelelőségének beépített kezdeményezését](../../governance/policy/samples/azure-security-benchmark#data-protection) ismertető témakört: Adatvédelem

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Bizalmas adatok felderítése, besorolása és címkézése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| DP-1 | 13,1, 14,5, 14,7 | SC-28 |

Felderítheti, osztályozhatja és címkézheti a bizalmas adatokat, így megtervezheti a megfelelő vezérlőket, hogy biztosítsa a bizalmas adatok tárolását, feldolgozását és biztonságos továbbítását a szervezet technológiai rendszereinek segítségével.

Az Azure-ban, a helyszínen, az Office 365-ben és máshol lévő Office-dokumentumokban található bizalmas információkhoz használja az Azure Information Protectiont (és a hozzá tartozó vizsgálati eszközt).

Az Azure SQL Information Protection segítséget nyújthat az Azure SQL-adatbázisokban tárolt adatok besorolásában és címkézésében.

- [Bizalmas információk címkézése az Azure Information Protection használatával](/azure/information-protection/what-is-information-protection) 

- [Az Azure SQL-adatfelderítés implementálása](../../azure-sql/database/data-discovery-and-classification-overview.md)

**Felelősség**: Megosztott

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Adatbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: A bizalmas adatok védelme

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| DP-2 | 13,2, 2,10 | SC-7, AC-4 |

A bizalmas adatok védelme az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC), a hálózat-alapú hozzáférés-vezérlés és az Azure-szolgáltatások adott vezérlői (például az SQL és más adatbázisok titkosítása) használatával. 

A következetes hozzáférés-vezérlés biztosításához a hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani. A vállalati szegmentálási stratégiát a bizalmas vagy üzleti szempontból kritikus fontosságú adatok és rendszerek helyének tudatában kell kialakítani.

A Microsoft által felügyelt mögöttes platformon a Microsoft az összes ügyféltartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatainak elvesztése és mások általi hozzáférése ellen. Ahhoz, hogy az ügyféladatok az Azure-on belül biztonságban maradjanak, a Microsoft implementált néhány alapértelmezett adatvédelmi vezérlőt és képességet.

- [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Adatbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Bizalmas adatok jogosulatlan átvitelének monitorozása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| DP-3 | 13,3 | AC-4, SI-4 |

Az adatok jogosulatlan átvitelének figyelése a nagyvállalati láthatóságon és a vezérlésen kívüli helyszíneken. Ez általában az olyan rendellenes tevékenységek (nagy méretű vagy szokatlan átvitelek) monitorozását jelenti, amelyek jogosulatlan adatkiszivárogtatást jelezhetnek. 

Az Azure Storage Advanced Threat Protection (ATP) és az Azure SQL ATP riasztást küldhet olyan rendellenes információátvitelekről, amelyek bizalmas adatok jogosulatlan átvitelét jelezhetik. 

Az Azure Information Protection (AIP) a besorolt és címkézett információk monitorozását teszi lehetővé. 

Ha az adatveszteség-megelőzési (data loss prevention, DLP) megfelelőséghez szükséges, egy gazdagépalapú DLP-megoldással észlelési és/vagy megelőzési célú vezérlőket kényszeríthet ki az adatok kiszivárogtatásának megakadályozása érdekében.

- [Azure Defender SQL-hez](../../azure-sql/database/azure-defender-for-sql.md)

- [Azure Defender tároláshoz](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Felelősség**: Megosztott

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Bizalmas információk átvitel közbeni titkosítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

A hozzáférés-vezérlés kiegészítéseként a továbbításban lévő adatokat a titkosítás használatával kell védeni a "sávon kívüli" támadások (például a forgalom rögzítése) ellen, így biztosítva, hogy a támadók ne tudják egyszerűen olvasni vagy módosítani az adatokat.

Habár ez nem kötelező a magánhálózaton lévő forgalom esetében, ez kritikus fontosságú a külső és a nyilvános hálózatok forgalmában. HTTP-forgalom esetén győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó összes ügyfél egyeztetheti a TLS v 1.2-es vagy újabb verzióját. A Távoli felügyelet érdekében titkosítatlan protokoll helyett használja az SSH-t (Linuxon) vagy RDP/TLS (Windows rendszeren). Az elavult SSL-, TLS-és SSH-verziók és protokollok, valamint a gyenge titkosítások le lesznek tiltva.

Alapértelmezés szerint az Azure titkosítást biztosít az Azure-adatközpontok közötti adatátvitelhez.

- [A titkosítás ismertetése az Azure-ban](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Információk a TLS biztonságáról](/security/engineering/solving-tls1-problem)

- [Kettős titkosítás az Azure-beli adatforgalomban](../fundamentals/double-encryption.md#data-in-transit)

**Felelősség**: Megosztott

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Adatbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Inaktív bizalmas adatok titkosítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| DP-5 | 14,8 | SC-28, SC-12 |

A hozzáférés-vezérlés kiegészítéseként a REST-adatok védelme a "sávon kívüli" támadásoktól (például a mögöttes tárolóhoz való hozzáférés) a titkosítás használatával történik. Ezzel biztosíthatja, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatforrást. 

Az Azure alapértelmezés szerint az inaktív adatok titkosítását biztosítja. A szigorúan bizalmas adatok esetében lehetősége van arra, hogy az összes Azure-erőforráson további titkosítást hajtsanak végre, ahol elérhetők. Az Azure alapértelmezés szerint kezeli a titkosítási kulcsokat, az Azure azonban lehetőséget biztosít a saját kulcsok (az ügyfél által felügyelt kulcsok) kezelésére bizonyos Azure-szolgáltatásokhoz.

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Az ügyfél által felügyelt titkosítási kulcsok konfigurálása](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [Titkosítási modell és kulcskezelő tábla](../fundamentals/encryption-models.md)

- [Inaktív adatok az Azure-ban – Rest kettős titkosítás](../fundamentals/double-encryption.md#data-at-rest)

**Felelősség**: Megosztott

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Adatbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-data-security)