---
title: A HIPAA HITRUST 9,2-re vonatkozó szabályozási megfelelőségi részletek
description: A HIPAA HITRUST 9,2 szabályozási megfelelőség beépített kezdeményezésének részletei. Minden vezérlő egy vagy több olyan Azure Policy-definícióra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 03/31/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: db8f3c887a6ed7beb2febb6910bec32e7b0ea218
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096393"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>A HIPAA HITRUST 9,2 szabályozási megfelelőségi beépített kezdeményezés részletei

A következő cikk azt ismerteti, hogyan jelennek meg a Azure Policy szabályozási megfelelőségének beépített kezdeményezési definíciója a **megfelelőségi tartományokra** és a HIPAA HITRUST 9,2-es **vezérlőre** .
További információ erről a megfelelőségi szabványról: [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html). A _tulajdonjog_ megismeréséhez tekintse meg a [Azure Policy házirend-definíció](../concepts/definition-structure.md#type) és [a megosztott felelősség a felhőben](../../../security/fundamentals/shared-responsibility.md)című témakört.

A következő leképezések a **HIPAA HITRUST 9,2** vezérlőkre vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy adott **megfelelőségi tartományra** ugorhat. Számos vezérlő [Azure Policy](../overview.md) kezdeményezési definícióval van implementálva. A teljes kezdeményezési definíció áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot.
Ezután keresse meg és válassza ki a **HITRUST/HIPAA** szabályozási megfelelőség beépített kezdeményezési definícióját.

Ez a beépített kezdeményezés a [HIPAA HITRUST 9,2 Blueprint minta](../../blueprints/samples/hipaa-hitrust-9-2.md)részeként van üzembe helyezve.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../overview.md) -definícióhoz vannak társítva.
> Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../how-to/get-compliance-data.md) ; azonban gyakran nem egy-az-egyhez vagy egy teljes egyezés egy vezérlőelem és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a szabályzat-definícióknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tartományok, a vezérlők és a Azure Policy-definíciók közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json).

## <a name="privilege-management"></a>Jogosultságok kezelése

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>A virtualizált rendszereket működtető rendszerek felügyeleti funkcióihoz vagy rendszergazdai konzolokhoz való hozzáférése a legalacsonyabb jogosultsági szint elve alapján, a technikai szabályozások által támogatottak szerint korlátozódik a személyzetre.

**Azonosító**: 11180.01 c3system. 6-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehető legrövidebb időn belül (JIT) való hozzáférést a Azure Security Center a javaslatok szerint figyeli |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>A jogosultságokat hivatalosan engedélyezték és szabályozzák, a felhasználók számára a funkcionális szerepkörük (például a felhasználó vagy a rendszergazda) számára szükséges és eseményvezérelt alapon, valamint minden rendszertermék/elem esetében dokumentálva vannak.

**Azonosító**: 1143.01 c1system. 123-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A felügyeleti portokat be kell zárni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |A távoli felügyeleti portok megnyitásakor a virtuális gép nagy mértékben veszélyezteti az internetes támadások kockázatát. Ezek a támadások kipróbálták a hitelesítő adatokat, hogy rendszergazdai hozzáférést kapjanak a géphez. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>A szervezet explicit módon engedélyezi a hozzáférést az adott biztonsági funkciókhoz (hardver, szoftver és belső vezérlőprogram) és a biztonsággal kapcsolatos információkhoz.

**Azonosító**: 1144.01 c1system. 4-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Javasoljuk, hogy legfeljebb 3 előfizetés-tulajdonost jelöljön ki, hogy csökkentse a feltört tulajdonos általi illetéktelen behatolás lehetőségét. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>A szerepköralapú hozzáférés-vezérlés megvalósítható, és az egyes felhasználók egy vagy több szerepkörhöz, illetve egy vagy több rendszerfunkcióhoz való hozzárendelésére alkalmasak.

**Azonosító**: 1145.01 c2system. 1-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az előfizetéshez egynél több tulajdonos rendelhető hozzá](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Ajánlott egynél több előfizetés-tulajdonost kijelölni a rendszergazdai hozzáférés redundancia érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>A szervezet olyan programok fejlesztését és használatát segíti elő, amelyek nem szükségesek az emelt szintű jogosultságok és rendszerrutinok futtatásához, hogy ne kelljen jogosultságokat biztosítani a felhasználóknak.

**Azonosító**: 1146.01 c2system. 23-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |A nem figyelt hozzáférés megakadályozása érdekében el kell távolítani a tulajdonosi engedélyekkel rendelkező külső fiókokat az előfizetésből. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>A emelt szintű jogosultságok egy másik felhasználói AZONOSÍTÓhoz vannak rendelve, amely a normál üzleti használatra szolgál, és az összes felhasználó egyetlen szerepkörben fér hozzá a Kiemelt szolgáltatásokhoz, és az ilyen privilegizált hozzáférés a lehető legkisebbre van beállítva.

**Azonosító**: 1147.01 c2system. 456-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Az elavult, tulajdonosi engedélyekkel rendelkező fiókokat el kell távolítani az előfizetésből.  Az elavult fiókok olyan fiókok, amelyek nem tudnak bejelentkezni. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>A szervezet korlátozza a hozzáférést a Kiemelt funkciókhoz és az összes biztonsági adathoz.

**Azonosító**: 1148.01 c2system. 78-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Egyéni RBAC-szabályok használatának naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Olyan beépített szerepköröket naplózhat, mint például a "tulajdonos, közreműködő, olvasó", az egyéni RBAC-szerepkörök helyett, amelyek hajlamosak a hibákra. Az egyéni szerepkörök használata Kivételként kezelendő, és szigorú felülvizsgálati és veszélyforrás-modellezést igényel |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[A Windows rendszerű gépeknek meg kell felelniük a "biztonsági beállítások – fiókok" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |A Windows rendszerű számítógépeknek a "biztonsági beállítások – fiókok" kategóriában megadott Csoportházirend beállításokkal kell rendelkezniük, hogy korlátozzák a helyi fiókok használatát az üres jelszavak és a vendég fiók állapotának korlátozására. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>A szervezet megkönnyíti az információk megosztását azáltal, hogy lehetővé teszi a jogosult felhasználók számára az üzleti partner hozzáférésének meghatározását, ha a szervezet által meghatározott mérlegelési jogosultsággal rendelkezik, és manuális eljárásokkal vagy automatizált mechanizmusokkal segíti a felhasználókat abban, hogy információkat osszon meg vagy Együttműködési döntéseket hozhassanak.

**Azonosító**: 1149.01 c2system. 9-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Szerepköralapú Access Control (RBAC) használata szükséges a Kubernetes-szolgáltatásokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Ha részletes szűrést szeretne biztosítani a felhasználók által végrehajtható műveletekhez, használja a Role-Based Access Control (RBAC) a Kubernetes Service-fürtök engedélyeinek kezeléséhez és a vonatkozó engedélyezési házirendek konfigurálásához. |Naplózás, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>A kezelt információk tárolására, feldolgozására vagy továbbítására szolgáló rendszerösszetevők hozzáférés-vezérlési rendszerét alapértelmezett megtagadás értékre állítja be a rendszer &quot; &quot; .

**Azonosító**: 1150.01 c2system. 10-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A felügyeleti portokat be kell zárni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |A távoli felügyeleti portok megnyitásakor a virtuális gép nagy mértékben veszélyezteti az internetes támadások kockázatát. Ezek a támadások kipróbálták a hitelesítő adatokat, hogy rendszergazdai hozzáférést kapjanak a géphez. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>A szervezet a Kiemelt jogosultsági szintű fiókokra korlátozza az információs rendszereken a felhasználók előre meghatározott részhalmazát.

**Azonosító**: 1151.01 c3system. 1-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Javasoljuk, hogy legfeljebb 3 előfizetés-tulajdonost jelöljön ki, hogy csökkentse a feltört tulajdonos általi illetéktelen behatolás lehetőségét. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>A szervezet ellenőrzi a privilegizált függvények végrehajtását az információs rendszereken, és biztosítja, hogy az információs rendszerek megakadályozzák a nem privilegizált felhasználók számára a Kiemelt funkciók végrehajtását.

**Azonosító**: 1152.01 c3system. 2-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az előfizetéshez egynél több tulajdonos rendelhető hozzá](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Ajánlott egynél több előfizetés-tulajdonost kijelölni a rendszergazdai hozzáférés redundancia érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>Az összes, nincs explicit módon szükséges fájlrendszer-hozzáférés le van tiltva, és csak a jogosult felhasználók férhetnek hozzá kizárólag a felhasználók feladat-feladatai teljesítményéhez kifejezetten szükséges engedélyekhez.

**Azonosító**: 1153.01 c3system. 35-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Szerepköralapú Access Control (RBAC) használata szükséges a Kubernetes-szolgáltatásokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Ha részletes szűrést szeretne biztosítani a felhasználók által végrehajtható műveletekhez, használja a Role-Based Access Control (RBAC) a Kubernetes Service-fürtök engedélyeinek kezeléséhez és a vonatkozó engedélyezési házirendek konfigurálásához. |Naplózás, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>A vállalkozók minimális rendszer-és fizikai hozzáféréssel rendelkeznek, csak azt követően, hogy a szervezet felméri az ajánlattevő azon képességét, hogy megfeleljen a biztonsági követelményeinek, és az ajánlattevő beleegyezik abba.

**Azonosító**: 1154.01 c3system. 4-01. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Javasoljuk, hogy legfeljebb 3 előfizetés-tulajdonost jelöljön ki, hogy csökkentse a feltört tulajdonos általi illetéktelen behatolás lehetőségét. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>A külső kapcsolatok felhasználói hitelesítése

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>Az erős hitelesítési módszerek, például a többtényezős, a RADIUS vagy a Kerberos (privilegizált hozzáférés esetén) és a CHAP (a telefonos módszerekhez tartozó hitelesítő adatok titkosításához) implementálva vannak a szervezetek hálózatával létesített összes külső kapcsolat esetében.

**Azonosító**: 1116.01 j1organizational. 145-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Az Multi-Factor Authentication (MFA) minden tulajdonosi engedélyekkel rendelkező előfizetési fiók esetében engedélyezni kell a fiókok vagy erőforrások megszegésének megakadályozására. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>A szállítók és az üzleti partnerek (például a távoli karbantartás esetében) távelérése le van tiltva/inaktiválva van, ha nincs használatban.

**Azonosító**: 1117.01 j1organizational. 23-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A fiókok vagy erőforrások megszegésének megelőzése érdekében minden írási jogosultsággal rendelkező előfizetési fiók esetében engedélyezni kell a Multi-Factor Authentication (MFA) használatát. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>A szervezetek a titkosítást (például &nbsp; VPN-megoldásokat vagy saját vonalakat) implementálják, és a szervezet hálózata alkalmazottai, alvállalkozói vagy harmadik fél (például szállítók) általi távoli hozzáférését naplózzák.

**Azonosító**: 1118.01 j2organizational. 124-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Az olvasási jogosultsággal rendelkező összes előfizetés-fiókhoz engedélyezni kell Multi-Factor Authentication (MFA) használatát a fiókok vagy erőforrások megszegésének megelőzése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>A hálózati berendezések nem várt telefonos funkciókra vannak bejelölve.

**Azonosító**: 1119.01 j2organizational. 3-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehető legrövidebb időn belül (JIT) való hozzáférést a Azure Security Center a javaslatok szerint figyeli |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>A távfelügyeleti munkamenetek engedélyezve vannak, titkosítottak, és fokozott biztonsági mértékeket alkalmaznak.

**Azonosító**: 1121.01 j3organizational. 2-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Az Multi-Factor Authentication (MFA) minden tulajdonosi engedélyekkel rendelkező előfizetési fiók esetében engedélyezni kell a fiókok vagy erőforrások megszegésének megakadályozására. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>Ha a rendszer nem használ titkosítást a telefonos kapcsolatokhoz, az CIO vagy a kijelölt képviselője konkrét írásos engedélyt ad.

**Azonosító**: 1173.01 j1organizational. 6-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A fiókok vagy erőforrások megszegésének megelőzése érdekében minden írási jogosultsággal rendelkező előfizetési fiók esetében engedélyezni kell a Multi-Factor Authentication (MFA) használatát. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>A szervezet védi a vezeték nélküli hozzáférést a bizalmas adatokat tartalmazó rendszerekhez a felhasználók és eszközök hitelesítésével.

**Azonosító**: 1174.01 j1organizational. 7-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Az olvasási jogosultsággal rendelkező összes előfizetés-fiókhoz engedélyezni kell Multi-Factor Authentication (MFA) használatát a fiókok vagy erőforrások megszegésének megelőzése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>A nyilvános hálózatokon keresztüli távoli hozzáférés csak a sikeres azonosítás és hitelesítés után zajlik.

**Azonosító**: 1175.01 j1organizational. 8-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehető legrövidebb időn belül (JIT) való hozzáférést a Azure Security Center a javaslatok szerint figyeli |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>A szervezetnek visszahívási képességre van szüksége az ismételt hitelesítéssel, hogy ellenőrizze a telefonos kapcsolatokat az illetékes helyekről.

**Azonosító**: 1176.01 j2organizational. 5-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Az Multi-Factor Authentication (MFA) minden tulajdonosi engedélyekkel rendelkező előfizetési fiók esetében engedélyezni kell a fiókok vagy erőforrások megszegésének megakadályozására. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>A szállítókhoz rendelt felhasználói azonosítókat a szervezet hozzáférési felülvizsgálati szabályzatával összhangban kell megtekinteni legalább évente.

**Azonosító**: 1177.01 j2organizational. 6-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A fiókok vagy erőforrások megszegésének megelőzése érdekében minden írási jogosultsággal rendelkező előfizetési fiók esetében engedélyezni kell a Multi-Factor Authentication (MFA) használatát. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>A csomópont-hitelesítés, beleértve a titkosítási technikákat (például a számítógép tanúsítványait), alternatív módszert kínál a távoli felhasználók csoportjainak hitelesítésére, ha azok biztonságos, közös számítógépes létesítményhez csatlakoznak.

**Azonosító**: 1178.01 j2organizational. 7-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Az olvasási jogosultsággal rendelkező összes előfizetés-fiókhoz engedélyezni kell Multi-Factor Authentication (MFA) használatát a fiókok vagy erőforrások megszegésének megelőzése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>Az információs rendszer figyeli és szabályozza a távelérési módszereket.

**Azonosító**: 1179.01 j3organizational. 1-01. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehető legrövidebb időn belül (JIT) való hozzáférést a Azure Security Center a javaslatok szerint figyeli |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Távoli diagnosztikai és konfigurációs portok védelme

### <a name="access-to-network-equipment-is-physically-protected"></a>A hálózati eszközökhöz való hozzáférés fizikailag védett.

**Azonosító**: 1192.01 l1organizational. 1-01. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehető legrövidebb időn belül (JIT) való hozzáférést a Azure Security Center a javaslatok szerint figyeli |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>A diagnosztikai és konfigurációs portok eléréséhez szükséges vezérlők közé tartozik a kulcs zárolásának használata, valamint a porthoz való fizikai hozzáférés vezérlését támogató eljárások megvalósítása.

**Azonosító**: 1193.01 l2organizational. 13-01. l **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A felügyeleti portokat be kell zárni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |A távoli felügyeleti portok megnyitásakor a virtuális gép nagy mértékben veszélyezteti az internetes támadások kockázatát. Ezek a támadások kipróbálták a hitelesítő adatokat, hogy rendszergazdai hozzáférést kapjanak a géphez. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>A számítógépekre vagy hálózati rendszerekre telepített portok, szolgáltatások és hasonló alkalmazások le vannak tiltva vagy eltávolíthatók.

**Azonosító**: 1194.01 l2organizational. 2-01. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A távoli hibakeresést ki kell kapcsolni a webalkalmazásokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |A távoli hibakereséshez a bejövő portok megnyitására van szükség egy webalkalmazáson. A távoli hibakeresést ki kell kapcsolni. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>A szervezet minden 365 (365) napon belül áttekinti az információs rendszerét a szükségtelen és nem biztonságos függvények, portok, protokollok és/vagy szolgáltatások azonosítására és letiltására.

**Azonosító**: 1195.01 l3organizational. 1-01. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A távoli hibakeresést ki kell kapcsolni a Function apps szolgáltatásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A távoli hibakereséshez bejövő portokat kell megnyitni a Function apps szolgáltatásban. A távoli hibakeresést ki kell kapcsolni. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>A szervezet az információs rendszeren nem engedélyezett (feketelistán lévő) szoftvereket azonosít, megelőzi a programok végrehajtását a jogosulatlan (feketelistán lévő) szoftverek listájának megfelelően, és az engedélyezett (feketelistán lévő) szoftverek használatának megtiltását, valamint a jogosulatlan (feketelistán lévő) szoftverek listáját ellenőrzi és frissíti.

**Azonosító**: 1196.01 l3organizational. 24-01. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A távoli hibakeresést ki kell kapcsolni API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |A távoli hibakereséshez a bejövő portok megnyitására van szükség az API-alkalmazásokban. A távoli hibakeresést ki kell kapcsolni. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>A szervezet letiltja a Bluetooth-és társközi hálózati protokollokat az információs rendszeren belül, amely szükségtelen vagy nem biztonságos.

**Azonosító**: 1197.01 l3organizational. 3-01. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazás-vezérlőelemek lehetővé teszik a gépen futó ismert, biztonságos alkalmazások listájának meghatározását, és riasztást küld, ha más alkalmazások futnak. Ez segít megerősíteni a gépeket a kártevők ellen. A szabályok konfigurálásának és karbantartásának egyszerűbbé tétele érdekében Security Center a gépi tanulás segítségével elemzi az egyes gépeken futó alkalmazásokat, és javaslatot tesz az ismert alkalmazások listájára. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>Elkülönítés a hálózatokban

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>A szervezet biztonsági átjárói (például tűzfalak) kikényszerítik a biztonsági házirendeket, és úgy vannak konfigurálva, hogy szűrik a tartományok közötti forgalmat, blokkolják a jogosulatlan hozzáférést, és a belső vezetékes, belső vezeték nélküli és külső hálózati szegmensek (például az Internet) közötti elkülönítést használják, beleértve a DMZ és az egyes tartományok hozzáférés-vezérlési szabályzatának érvényesítését.

**Azonosító**: 0805.01 m1organizational. 12-01. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ez a házirend minden olyan App Service naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a házirend minden olyan Container Registry naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ez a házirend minden olyan Cosmos DB naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ez a házirend minden olyan eseményt naplóz, amely nem virtuális hálózati szolgáltatás-végpont használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Az átjáró-alhálózatokat nem szabad hálózati biztonsági csoporttal konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Ez a szabályzat megtagadja, hogy az átjáró-alhálózat hálózati biztonsági csoporttal van-e konfigurálva. Egy hálózati biztonsági csoport átjáró-alhálózathoz való hozzárendelésével az átjáró működése leáll. |tagadja |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG) való hozzáférés korlátozásával védi a virtuális gépeket a lehetséges fenyegetésekkel szemben. További információ a forgalom szabályozásáról a NSG-on [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ez a házirend minden olyan Key Vault naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ez a házirend minden olyan Service Bus naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ez a házirend minden olyan SQL Server naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[A Storage-fiókoknak virtuális hálózati szolgáltatási végpontot kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ez a házirend minden olyan Storage-fiókot naplóz, amely nem virtuális hálózati szolgáltatás-végpont használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Az alhálózatokat hálózati biztonsági csoporttal kell társítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |A hálózati biztonsági csoporttal (NSG) való hozzáférés korlátozásával védi az alhálózatot az esetleges fenyegetésekkel szemben. A NSG olyan Access Control lista (ACL) szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják az alhálózat felé irányuló hálózati forgalmat. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a házirend a nem jóváhagyott virtuális hálózathoz csatlakoztatott virtuális gépeket naplózza. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>A szervezetek hálózata logikailag és fizikailag szegmentálva van egy meghatározott biztonsági kerületben és egy beosztott vezérlőkkel, beleértve a nyilvánosan elérhető rendszerösszetevők alhálózatait is, amelyek logikailag elkülönülnek a belső hálózattól a szervezeti követelmények alapján. a forgalom ellenőrzése a szükséges funkciókon és az adatok/rendszerek besorolásán alapul, kockázatértékelés és a rájuk vonatkozó biztonsági követelmények alapján.

**Azonosító**: 0806.01 m2organizational. 12356-01. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ez a házirend minden olyan App Service naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a házirend minden olyan Container Registry naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ez a házirend minden olyan Cosmos DB naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ez a házirend minden olyan eseményt naplóz, amely nem virtuális hálózati szolgáltatás-végpont használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Az átjáró-alhálózatokat nem szabad hálózati biztonsági csoporttal konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Ez a szabályzat megtagadja, hogy az átjáró-alhálózat hálózati biztonsági csoporttal van-e konfigurálva. Egy hálózati biztonsági csoport átjáró-alhálózathoz való hozzárendelésével az átjáró működése leáll. |tagadja |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG) való hozzáférés korlátozásával védi a virtuális gépeket a lehetséges fenyegetésekkel szemben. További információ a forgalom szabályozásáról a NSG-on [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ez a házirend minden olyan Key Vault naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ez a házirend minden olyan Service Bus naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ez a házirend minden olyan SQL Server naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[A Storage-fiókoknak virtuális hálózati szolgáltatási végpontot kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ez a házirend minden olyan Storage-fiókot naplóz, amely nem virtuális hálózati szolgáltatás-végpont használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Az alhálózatokat hálózati biztonsági csoporttal kell társítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |A hálózati biztonsági csoporttal (NSG) való hozzáférés korlátozásával védi az alhálózatot az esetleges fenyegetésekkel szemben. A NSG olyan Access Control lista (ACL) szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják az alhálózat felé irányuló hálózati forgalmat. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a házirend a nem jóváhagyott virtuális hálózathoz csatlakoztatott virtuális gépeket naplózza. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>A hálózatok a fizikai kiszolgálók, alkalmazások vagy adatok virtualizált kiszolgálókra való áttelepítése során elkülönítettek a termelési szintű hálózatokból.

**Azonosító**: 0894.01 m2organizational. 7-01. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ez a házirend minden olyan App Service naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a házirend minden olyan Container Registry naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ez a házirend minden olyan Cosmos DB naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[A Network Watcher üzembe helyezése virtuális hálózatok létrehozásakor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |Ez a házirend egy Network Watcher-erőforrást hoz létre a virtuális hálózatokkal rendelkező régiókban. Biztosítania kell egy networkWatcherRG nevű erőforráscsoport létezését, amelyet a Network Watcher-példányok üzembe helyezéséhez fog használni. |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ez a házirend minden olyan eseményt naplóz, amely nem virtuális hálózati szolgáltatás-végpont használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Az átjáró-alhálózatokat nem szabad hálózati biztonsági csoporttal konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Ez a szabályzat megtagadja, hogy az átjáró-alhálózat hálózati biztonsági csoporttal van-e konfigurálva. Egy hálózati biztonsági csoport átjáró-alhálózathoz való hozzárendelésével az átjáró működése leáll. |tagadja |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG) való hozzáférés korlátozásával védi a virtuális gépeket a lehetséges fenyegetésekkel szemben. További információ a forgalom szabályozásáról a NSG-on [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ez a házirend minden olyan Key Vault naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ez a házirend minden olyan Service Bus naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ez a házirend minden olyan SQL Server naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[A Storage-fiókoknak virtuális hálózati szolgáltatási végpontot kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ez a házirend minden olyan Storage-fiókot naplóz, amely nem virtuális hálózati szolgáltatás-végpont használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Az alhálózatokat hálózati biztonsági csoporttal kell társítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |A hálózati biztonsági csoporttal (NSG) való hozzáférés korlátozásával védi az alhálózatot az esetleges fenyegetésekkel szemben. A NSG olyan Access Control lista (ACL) szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják az alhálózat felé irányuló hálózati forgalmat. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a házirend a nem jóváhagyott virtuális hálózathoz csatlakoztatott virtuális gépeket naplózza. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>Hálózati kapcsolatok vezérlése

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>A hálózati forgalmat a szervezetek hozzáférés-vezérlési szabályzatának megfelelően, a hálózati hozzáférési pontokhoz vagy a külső távközlési szolgáltatás felügyelt felületéhez kapcsolódó egyéb korlátozásokkal összhangban kell szabályozni.

**Azonosító**: 0809.01 n2organizational. 1234-01. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a lehetséges támadási felület csökkentése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Az API-alkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG) való hozzáférés korlátozásával védi a virtuális gépeket a lehetséges fenyegetésekkel szemben. További információ a forgalom szabályozásáról a NSG-on [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A legújabb TLS-verziót kell használni az API-alkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a függvényalkalmazás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a webalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak az SSL-kapcsolaton keresztüli kapcsolatok engedélyezése az Azure cache-Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |A biztonságos átvitel naplózási követelménye a Storage-fiókban. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Az alhálózatokat hálózati biztonsági csoporttal kell társítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |A hálózati biztonsági csoporttal (NSG) való hozzáférés korlátozásával védi az alhálózatot az esetleges fenyegetésekkel szemben. A NSG olyan Access Control lista (ACL) szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják az alhálózat felé irányuló hálózati forgalmat. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a házirend a nem jóváhagyott virtuális hálózathoz csatlakoztatott virtuális gépeket naplózza. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A webalkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>Az átvitt adatok biztonságban vannak, és minimálisan titkosítva vannak nyitott, nyilvános hálózatokon.

**Azonosító**: 0810.01 n2organizational. 5-01. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a lehetséges támadási felület csökkentése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Az API-alkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG) való hozzáférés korlátozásával védi a virtuális gépeket a lehetséges fenyegetésekkel szemben. További információ a forgalom szabályozásáról a NSG-on [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A legújabb TLS-verziót kell használni az API-alkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a függvényalkalmazás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a webalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak az SSL-kapcsolaton keresztüli kapcsolatok engedélyezése az Azure cache-Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |A biztonságos átvitel naplózási követelménye a Storage-fiókban. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Az alhálózatokat hálózati biztonsági csoporttal kell társítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |A hálózati biztonsági csoporttal (NSG) való hozzáférés korlátozásával védi az alhálózatot az esetleges fenyegetésekkel szemben. A NSG olyan Access Control lista (ACL) szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják az alhálózat felé irányuló hálózati forgalmat. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a házirend a nem jóváhagyott virtuális hálózathoz csatlakoztatott virtuális gépeket naplózza. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A webalkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>A forgalomra vonatkozó házirend alóli kivételek dokumentálva vannak egy támogató feladat/üzleti igény, a kivétel időtartama, és legalább évente felülvizsgálva; a rendszer eltávolítja a forgalmi forgalomra vonatkozó házirend-kivételeket, ha a továbbiakban nem támogatja kifejezett üzleti igényeket.

**Azonosító**: 0811.01 n2organizational. 6-01. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a lehetséges támadási felület csökkentése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Az API-alkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG) való hozzáférés korlátozásával védi a virtuális gépeket a lehetséges fenyegetésekkel szemben. További információ a forgalom szabályozásáról a NSG-on [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A legújabb TLS-verziót kell használni az API-alkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a függvényalkalmazás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a webalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak az SSL-kapcsolaton keresztüli kapcsolatok engedélyezése az Azure cache-Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |A biztonságos átvitel naplózási követelménye a Storage-fiókban. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Az alhálózatokat hálózati biztonsági csoporttal kell társítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |A hálózati biztonsági csoporttal (NSG) való hozzáférés korlátozásával védi az alhálózatot az esetleges fenyegetésekkel szemben. A NSG olyan Access Control lista (ACL) szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják az alhálózat felé irányuló hálózati forgalmat. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a házirend a nem jóváhagyott virtuális hálózathoz csatlakoztatott virtuális gépeket naplózza. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A webalkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>A nem távoli kapcsolatot létrehozó távoli eszközök nem jogosultak külső (távoli) erőforrásokkal való kommunikációra.

**Azonosító**: 0812.01 n2organizational. 8-01. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a lehetséges támadási felület csökkentése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Az API-alkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG) való hozzáférés korlátozásával védi a virtuális gépeket a lehetséges fenyegetésekkel szemben. További információ a forgalom szabályozásáról a NSG-on [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A legújabb TLS-verziót kell használni az API-alkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a függvényalkalmazás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a webalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak az SSL-kapcsolaton keresztüli kapcsolatok engedélyezése az Azure cache-Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |A biztonságos átvitel naplózási követelménye a Storage-fiókban. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Az alhálózatokat hálózati biztonsági csoporttal kell társítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |A hálózati biztonsági csoporttal (NSG) való hozzáférés korlátozásával védi az alhálózatot az esetleges fenyegetésekkel szemben. A NSG olyan Access Control lista (ACL) szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják az alhálózat felé irányuló hálózati forgalmat. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a házirend a nem jóváhagyott virtuális hálózathoz csatlakoztatott virtuális gépeket naplózza. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A webalkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>A felhasználók a belső hálózathoz való kapcsolódás lehetőségét a hozzáférés-vezérlési házirend, valamint a klinikai és üzleti alkalmazások követelményeinek megfelelően a felügyelt felületeken a megtagadási és a megengedett kivételek alapján korlátozzák.

**Azonosító**: 0814.01 n1organizational. 12-01. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a lehetséges támadási felület csökkentése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Az API-alkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG) való hozzáférés korlátozásával védi a virtuális gépeket a lehetséges fenyegetésekkel szemben. További információ a forgalom szabályozásáról a NSG-on [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A legújabb TLS-verziót kell használni az API-alkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a függvényalkalmazás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a webalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak az SSL-kapcsolaton keresztüli kapcsolatok engedélyezése az Azure cache-Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |A biztonságos átvitel naplózási követelménye a Storage-fiókban. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Az alhálózatokat hálózati biztonsági csoporttal kell társítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |A hálózati biztonsági csoporttal (NSG) való hozzáférés korlátozásával védi az alhálózatot az esetleges fenyegetésekkel szemben. A NSG olyan Access Control lista (ACL) szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják az alhálózat felé irányuló hálózati forgalmat. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a házirend a nem jóváhagyott virtuális hálózathoz csatlakoztatott virtuális gépeket naplózza. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A webalkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>Felhasználói azonosító és hitelesítés

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>A szervezet gondoskodik arról, hogy a redundáns felhasználói azonosítók ne legyenek kiadva más felhasználók számára, és hogy minden felhasználó egyedi módon azonosítható és hitelesíthető legyen mind a helyi, mind a távoli hozzáféréshez az információs rendszerekhez.

**Azonosító**: 11109.01 q1organizational. 57-01. q **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Az Multi-Factor Authentication (MFA) minden tulajdonosi engedélyekkel rendelkező előfizetési fiók esetében engedélyezni kell a fiókok vagy erőforrások megszegésének megakadályozására. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>Nem szervezeti felhasználók (az összes, a szervezeten kívüli felhasználót, mint például a betegeket, az ügyfeleket, a vállalkozók vagy a külföldi állampolgárokat) vagy a nem szervezeti felhasználó nevében eljáró folyamatokat egyedileg azonosítják és hitelesítik.

**Azonosító**: 11110.01 q1organizational. 6-01. q **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A fiókok vagy erőforrások megszegésének megelőzése érdekében minden írási jogosultsággal rendelkező előfizetési fiók esetében engedélyezni kell a Multi-Factor Authentication (MFA) használatát. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>Ha PKI-alapú hitelesítést használ, az információs rendszer érvényesíti a tanúsítványokat egy elfogadott megbízhatósági kapcsolatra vonatkozó hitelesítési útvonal létrehozásával és ellenőrzésével, beleértve a tanúsítvány állapotára vonatkozó információk ellenőrzését is. kikényszeríti a hozzáférést a megfelelő titkos kulcshoz; leképezi az identitást az egyén vagy csoport megfelelő fiókjába; a helyi gyorsítótárba helyezi a visszavonási adatokat, hogy támogassa az elérési utak felderítését és érvényesítését abban az esetben, ha a hálózaton keresztül nem tud hozzáférni a visszavonási adatokhoz.

**Azonosító**: 11111.01 q2system. 4-01. q **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Az olvasási jogosultsággal rendelkező összes előfizetés-fiókhoz engedélyezni kell Multi-Factor Authentication (MFA) használatát a fiókok vagy erőforrások megszegésének megelőzése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>Az információs rendszer újrajátszás ellen védett hitelesítési mechanizmusokat alkalmaz, például az egyszer használatos, egyszeri jelszavakat vagy időbélyegeket a hálózati hozzáférés biztonságossá tételéhez a Kiemelt fiókok esetében; a hardveres jogkivonat-alapú hitelesítés esetében pedig olyan mechanizmusokat alkalmaz, amelyek megfelelnek a NIST SP 800-63-2-es, elektronikus hitelesítési útmutatóban tárgyalt minimális jogkivonat-követelményeknek.

**Azonosító**: 11112.01 q2organizational. 67-01. q **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Javasoljuk, hogy legfeljebb 3 előfizetés-tulajdonost jelöljön ki, hogy csökkentse a feltört tulajdonos általi illetéktelen behatolás lehetőségét. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>A szervezet megköveteli, hogy az elektronikus aláírások egyediek legyenek egyetlen személy számára, nem használhatók fel újra, vagy hozzá lettek rendelve másnak.

**Azonosító**: 11208.01 q1organizational. 8-01. q **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az előfizetéshez egynél több tulajdonos rendelhető hozzá](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Ajánlott egynél több előfizetés-tulajdonost kijelölni a rendszergazdai hozzáférés redundancia érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>Az elektronikus aláírások és a kézzel írt aláírások a saját elektronikus rekordokhoz kapcsolódnak.

**Azonosító**: 11210.01 q2organizational. 10-01. q **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows rendszerű gépek naplózása, amelyeknek a megadott tagjai vannak a rendszergazdák csoportban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Megköveteli, hogy az előfeltételek telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport egy vagy több, a Policy paraméterben felsorolt tagot tartalmaz. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>Az aláírt elektronikus rekordok tartalmazzák az ember által olvasható aláírással kapcsolatos adatokat.

**Azonosító**: 11211.01 q2organizational. 11-01. q **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A rendszergazdák csoport megadott tagjaiból hiányzó Windows-gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Megköveteli, hogy az előfeltételek telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport nem tartalmaz egy vagy több olyan tagot, amely szerepel a házirend-paraméterben. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>A Kiemelt funkciókat (például a rendszerfelügyeletet) végző felhasználók külön fiókokat használnak az emelt szintű függvények végrehajtásakor.

**Azonosító**: 1123.01 q1system. 2-01. q **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A rendszergazdák csoportba tartozó további fiókkal rendelkező Windows-gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Megköveteli, hogy az előfeltételek telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport olyan tagokat tartalmaz, amelyek nem szerepelnek a házirend-paraméterben. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>A többtényezős hitelesítési módszereket a szervezeti szabályzatoknak (például távoli hálózati hozzáférés esetén) megfelelően használják.

**Azonosító**: 1125.01 q2system. 1-01. q **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows rendszerű gépek naplózása, amelyeknek a megadott tagjai vannak a rendszergazdák csoportban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Megköveteli, hogy az előfeltételek telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport egy vagy több, a Policy paraméterben felsorolt tagot tartalmaz. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>Ha a többtényezős hitelesítéshez jogkivonatok vannak megadva, a hozzáférés megadását megelőzően a személyes ellenőrzés szükséges.

**Azonosító**: 1127.01 q2system. 3-01. q **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A rendszergazdák csoport megadott tagjaiból hiányzó Windows-gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Megköveteli, hogy az előfeltételek telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport nem tartalmaz egy vagy több olyan tagot, amely szerepel a házirend-paraméterben. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>Külső felekkel kapcsolatos kockázatok azonosítása

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>A szervezet adataihoz és rendszereihez külső felek általi hozzáférés nem engedélyezett, amíg meg nem történik az átvilágítás, a megfelelő ellenőrzéseket hajtották végre, és a biztonsági követelményeket tükröző szerződés/megállapodás aláírása tudomásul veszi, hogy megértik és elfogadják a rájuk vonatkozó kötelezettségeiket.

**Azonosító**: 1401.05 i1organizational. 1239-05. i **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |A biztonságos átvitel naplózási követelménye a Storage-fiókban. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>A szervezet és a külső felek közötti távelérési kapcsolatok titkosítva vannak.

**Azonosító**: 1402.05 i1organizational. 45-05. i **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>A külső feleknek biztosított hozzáférés a minimálisan szükséges minimumra korlátozódik, és csak a szükséges időtartamig adható meg.

**Azonosító**: 1403.05 i1organizational. 67-05. i **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A webalkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>A külső fél átvilágítása magában foglalja az interjúk, a dokumentumok áttekintése, a feladatlisták, a minősítési felülvizsgálatok (például a HITRUST) &nbsp; vagy más távoli eszközök betartását.

**Azonosító**: 1404.05 i2organizational. 1-05. i **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az API-alkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>A külső felek hozzáférésével kapcsolatos kockázatok azonosítása figyelembe veszi a konkrétan meghatározott problémák minimális készletét.

**Azonosító**: 1418.05 i1organizational. 8-05. i **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>A szervezet kielégítő garanciát szerez arra vonatkozóan, hogy az információ-ellátási láncon belül ésszerű biztonsági információk állnak rendelkezésre az adatszolgáltatói láncon belül.

**Azonosító**: 1450.05 i2organizational. 2-05. i **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>A Cloud Service Providers olyan vezérlőket tervez és valósít meg, amelyek az adatbiztonsági kockázatokat a feladatok, a szerepköralapú hozzáférés és a legalacsonyabb jogosultságú hozzáférés megfelelő elkülönítésével, valamint az ellátási láncban lévő összes munkatárs számára biztosítják.

**Azonosító**: 1451.05 icsporganizational. 2-05. i **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak az SSL-kapcsolaton keresztüli kapcsolatok engedélyezése az Azure cache-Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>Naplózás naplózása

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>Biztonságos naplózási rekord jön létre a rendszeren lévő összes tevékenységhez (létrehozás, olvasás, frissítés, törlés), amely a kezelt információkat tartalmazza.

**Azonosító**: 1202.09 aa1system. 1-09. AA **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Azure Data Lake Store erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Ellenőrizze, hogy vannak-e olyan hiányzó rendszerbiztonsági frissítések és kritikus frissítések, amelyeket telepíteni kell, hogy a Windows és a Linux rendszerű virtuálisgép-méretezési csoportok biztonságosak legyenek. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>A naplózási rekordok közé tartozik az egyedi felhasználói azonosító, az egyedi adattulajdonos azonosítója, az elvégezt függvény, valamint az esemény végrehajtásának dátuma és időpontja.

**Azonosító**: 1203.09 aa1system. 2-09. AA **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Logic Apps erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>Az emelt szintű jogosultsággal rendelkező felhasználók (rendszergazdák, operátorok stb.) tevékenységei közé tartozik az esemény sikeressége/meghibásodása, az esemény bekövetkezésének időpontja, az érintett fiók, az érintett folyamatok és az eseményre vonatkozó további információk.

**Azonosító**: 1204.09 aa1system. 3-09. AA **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A IoT Hub erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>A küldött és fogadott üzenetek naplói megmaradnak, beleértve az üzenet dátumát, időpontját, eredetét és célját, de nem annak tartalmát.

**Azonosító**: 1205.09 aa2system. 1-09. AA **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Batch-fiókokban lévő erőforrás-naplókat engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>A naplózás mindig elérhető, amíg a rendszer aktív, és nyomon követi a legfontosabb eseményeket, a sikeres/sikertelen adatelérést, a rendszerbiztonsági konfiguráció módosításait, A jogosultsági szintű vagy a segédprogram-használatot, az összes riasztást, a &nbsp; védelmi rendszerek (például A/V és az azonosítók) aktiválását és inaktiválását, valamint a rendszerszintű objektumok létrehozását és törlését.

**Azonosító**: 1206.09 aa2system. 23-09. AA **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Virtual Machine Scale Sets erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Javasoljuk, hogy engedélyezze a naplókat, hogy a tevékenység nyomvonala újra létre lehessen hozni, ha incidens vagy sérülés esetén szükséges a vizsgálatok. |AuditIfNotExists, letiltva |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>A naplózási rekordok megőrzése 90 nap, míg a régebbi naplózási rekordok egy évig archiválva lesznek.

**Azonosító**: 1207.09 aa2system. 4-09. AA **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Azure Stream Analytics erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Az Event hub erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>A naplókat a felügyeleti tevékenységek, a rendszer és az alkalmazások indítási/leállítási/hibák, a fájlok módosításai és a biztonsági házirendek változásai kezelik.

**Azonosító**: 1208.09 aa3system. 1-09. AA **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A keresési szolgáltatásokban engedélyezni kell az erőforrás-naplókat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[A Service Bus erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>Az információs rendszerek a következő részletes információkat tartalmazó naplózási rekordokat generálják: (i) a fájlnév hozzáfért; (II) az esemény kezdeményezéséhez használt program vagy parancs; és (III) forrás-és célcím.

**Azonosító**: 1209.09 aa3system. 2-09. AA **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A App Services lévő diagnosztikai naplókat engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Diagnosztikai naplók engedélyezésének naplózása az alkalmazásban. Ez lehetővé teszi, hogy újból létrehozza a tevékenységek nyomvonalait vizsgálat céljára, ha biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>A rendszer naplózza a szervezeten belüli vagy kívüli kezelt információk összes közzétételét, beleértve a közzététel típusát, az esemény, a címzett és a küldő dátumát/időpontját.

**Azonosító**: 1210.09 aa3system. 3-09. AA **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Diagnosztikai beállítás naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |A kiválasztott erőforrástípusok diagnosztikai beállításainak naplózása |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[A Data Lake Analytics erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>A szervezet minden 90 (90) nappal ellenőrzi, hogy a kezelt információk minden kivonata rendelkezik-e az adatok törlésével, vagy a használatuk továbbra is szükséges-e.

**Azonosító**: 1211.09 aa3system. 4-09. AA **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL Server naplózását engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A SQL Server naplózását engedélyezni kell ahhoz, hogy nyomon kövessék az adatbázis-tevékenységeket a kiszolgálón lévő összes adatbázisban, és azokat egy naplóba mentse. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Azure Key Vault felügyelt HSM erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |Ha egy biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága során szeretné újból létrehozni a tevékenységek nyomvonalait, érdemes lehet naplózni az erőforrás-naplóknak a felügyelt HSM való engedélyezésével. Kérjük, kövesse az alábbi utasításokat: [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging) . |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[A Key Vault erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újra létrehozza a vizsgálat céljára szolgáló tevékenység-nyomvonalat, amikor biztonsági incidens következik be, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>A rendszer használatának figyelése

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>Az információs rendszerekhez való jogosulatlan távoli kapcsolatok figyelése és felülvizsgálata legalább negyedévente történik, és a megfelelő lépéseket kell végrehajtani, ha jogosulatlan kapcsolatot derít fel.

**Azonosító**: 1120.09 ab3system. 9-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor az összes régióból gyűjti a tevékenység naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Ez a házirend naplózza a Azure Monitor log profilt, amely nem exportál tevékenységeket az összes Azure által támogatott régióból, beleértve a globálisat is. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>A szervezet figyeli az információs rendszer azon szabálytalanságok vagy rendellenességek azonosítására, amelyek a rendszermeghibásodások vagy a kompromisszumok jelei, valamint a rendszer optimális, rugalmas és biztonságos állapotának megerősítése.

**Azonosító**: 12100.09 ab2system. 15-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Log Analytics ügynöknek telepítve kell lennie a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Ez a házirend a Windows/Linux rendszerű virtuális gépeket naplózza, ha a Log Analytics-ügynök nincs telepítve. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>A szervezet határozza meg, hogy a rendszer milyen gyakran tekinti át a naplókat, hogyan dokumentálja a felülvizsgálatokat, és hogy az adott személy milyen szerepköröket és felelősségi köröket hajt végre, beleértve a szakmai minősítéseket vagy más szükséges minősítéseket.

**Azonosító**: 12101.09 ab1organizational. 3-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Log Analytics ügynöknek telepítve kell lennie a Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Ez a házirend minden Windows-vagy Linux-Virtual Machine Scale Sets naplóz, ha az Log Analytics ügynök nincs telepítve. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>A szervezet rendszeresen teszteli a megfigyelési és észlelési folyamatait, elhárítja a hiányosságokat, és javítja a folyamatait.

**Azonosító**: 12102.09 ab1organizational. 4-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azokat a Windows-gépeket naplózza, amelyeken a Log Analytics ügynök nem a várt módon van csatlakoztatva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Megköveteli, hogy az előfeltételek telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . A gépek nem megfelelőek, ha az ügynök nincs telepítve, vagy ha telepítve van, de a COM-objektum AgentConfigManager. MgmtSvcCfg azt adja vissza, hogy az a házirend-paraméterben megadott AZONOSÍTÓtól eltérő munkaterületre van regisztrálva. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>A rendszer az engedélyezett hozzáféréssel és a jogosulatlan hozzáférési kísérletekkel kapcsolatos összes vonatkozó jogi követelményt teljesíti.

**Azonosító**: 1212.09 ab1system. 1-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor log-profilnak össze kell gyűjtenie a naplókat a "Write", "Delete" és "Action" kategóriákba.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Ez a szabályzat biztosítja, hogy a log-profil a "Write", a "Delete" és a "Action" kategóriákba gyűjti a naplókat. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>A szervezet környezetében üzembe helyezett automatizált rendszerek a kulcsfontosságú események és a rendellenes tevékenységek figyelésére, valamint a rendszernaplók elemzésére szolgálnak, amelyek eredményei rendszeresen felül vannak értékelve.

**Azonosító**: 1213.09 ab2system. 128-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az Log Analytics ügynök automatikus üzembe helyezését engedélyezni kell az előfizetésen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |A biztonsági rések és fenyegetések figyeléséhez Azure Security Center adatokat gyűjt az Azure-beli virtuális gépekről. Az adatokat a korábban a Microsoft monitoring Agent (MMA) néven ismert Log Analytics ügynök gyűjti, amely különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat olvas be a gépről, és az adatokat az Log Analytics munkaterületre másolja az elemzéshez. Javasoljuk, hogy az automatikus kiépítés engedélyezésével automatikusan telepítse az ügynököt az összes támogatott Azure-beli virtuális gépre és a létrehozott újakra. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>A figyelés magában foglalja az emelt szintű műveleteket, a hitelesített hozzáférési vagy jogosulatlan hozzáférési kísérleteket, beleértve a deaktivált fiókok elérésére tett kísérleteket, valamint a rendszerriasztásokat és a hibákat.

**Azonosító**: 1214.09 ab2system. 3456-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor az összes régióból gyűjti a tevékenység naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Ez a házirend naplózza a Azure Monitor log profilt, amely nem exportál tevékenységeket az összes Azure által támogatott régióból, beleértve a globálisat is. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>A szervezet által alkalmazott naplózási és figyelési rendszerek támogatják a naplózás csökkentését és a jelentéskészítést.

**Azonosító**: 1215.09 ab2system. 7-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Log Analytics ügynöknek telepítve kell lennie a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Ez a házirend a Windows/Linux rendszerű virtuális gépeket naplózza, ha a Log Analytics-ügynök nincs telepítve. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>Az automatizált rendszerek a biztonsági rendszerek (például az IP-címek/AZONOSÍTÓk) és a rendszerrekordok figyelési tevékenységeinek napi áttekintésére szolgálnak, valamint azonosítják és dokumentálják a rendellenességeket.

**Azonosító**: 1216.09 ab3system. 12-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Log Analytics ügynöknek telepítve kell lennie a Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Ez a házirend minden Windows-vagy Linux-Virtual Machine Scale Sets naplóz, ha az Log Analytics ügynök nincs telepítve. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>A technikai személyzet riasztásokat hoz létre a gyanús tevékenységek elemzéséhez és kivizsgálásához, illetve a feltételezett szabálysértésekhez.

**Azonosító**: 1217.09 ab3system. 3-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azokat a Windows-gépeket naplózza, amelyeken a Log Analytics ügynök nem a várt módon van csatlakoztatva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Megköveteli, hogy az előfeltételek telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . A gépek nem megfelelőek, ha az ügynök nincs telepítve, vagy ha telepítve van, de a COM-objektum AgentConfigManager. MgmtSvcCfg azt adja vissza, hogy az a házirend-paraméterben megadott AZONOSÍTÓtól eltérő munkaterületre van regisztrálva. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>Az információs rendszer képes automatikusan feldolgozni a naplóbejegyzések naplózási rekordjait a választható feltételek alapján.

**Azonosító**: 1219.09 ab3system. 10-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor log-profilnak össze kell gyűjtenie a naplókat a "Write", "Delete" és "Action" kategóriákba.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Ez a szabályzat biztosítja, hogy a log-profil a "Write", a "Delete" és a "Action" kategóriákba gyűjti a naplókat. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>A figyelés magában foglalja a bejövő és a kimenő kommunikációt, valamint a fájlok integritásának figyelését.

**Azonosító**: 1220.09 ab3system. 56-09. AB **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az Log Analytics ügynök automatikus üzembe helyezését engedélyezni kell az előfizetésen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |A biztonsági rések és fenyegetések figyeléséhez Azure Security Center adatokat gyűjt az Azure-beli virtuális gépekről. Az adatokat a korábban a Microsoft monitoring Agent (MMA) néven ismert Log Analytics ügynök gyűjti, amely különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat olvas be a gépről, és az adatokat az Log Analytics munkaterületre másolja az elemzéshez. Javasoljuk, hogy az automatikus kiépítés engedélyezésével automatikusan telepítse az ügynököt az összes támogatott Azure-beli virtuális gépre és a létrehozott újakra. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>Rendszergazdai és operátori naplók

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>A szervezet biztosítja, hogy a megfelelő naplózás engedélyezve legyen a rendszergazdai tevékenységek naplózása érdekében; a rendszergazda és a kezelő rendszeresen ellenőrzi a rendszergazdákat és a naplókat.

**Azonosító**: 1270.09 ad1system. 12 – 09.ad **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A konkrét felügyeleti műveletekre vonatkozóan léteznie kell egy műveletnapló-riasztásnak.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a házirend olyan konkrét felügyeleti műveleteket naplóz, amelyeken nincs konfigurálva műveletnapló-riasztás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>A rendszer-és hálózati rendszergazdák felügyeletén kívül felügyelt behatolásjelző rendszer a rendszer-és hálózati adminisztrációs tevékenységek megfelelőségének figyelésére szolgál.

**Azonosító**: 1271.09 ad1system. 1 – 09.ad **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A konkrét felügyeleti műveletekre vonatkozóan léteznie kell egy műveletnapló-riasztásnak.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a házirend olyan konkrét felügyeleti műveleteket naplóz, amelyeken nincs konfigurálva műveletnapló-riasztás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>A feladatok elkülönítése

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>A feladatok elkülönítése arra szolgál, hogy korlátozza az információk és rendszerek jogosulatlan vagy véletlen módosításának kockázatát.

**Azonosító**: 1229.09 c1organizational. 1-09. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Szerepköralapú Access Control (RBAC) használata szükséges a Kubernetes-szolgáltatásokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Ha részletes szűrést szeretne biztosítani a felhasználók által végrehajtható műveletekhez, használja a Role-Based Access Control (RBAC) a Kubernetes Service-fürtök engedélyeinek kezeléséhez és a vonatkozó engedélyezési házirendek konfigurálásához. |Naplózás, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>Egyetlen személy sem férhet hozzá, nem módosíthatja és nem használhatja az információs rendszereket hitelesítés vagy észlelés nélkül.

**Azonosító**: 1230.09 c2organizational. 1-09. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Egyéni RBAC-szabályok használatának naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Olyan beépített szerepköröket naplózhat, mint például a "tulajdonos, közreműködő, olvasó", az egyéni RBAC-szerepkörök helyett, amelyek hajlamosak a hibákra. Az egyéni szerepkörök használata Kivételként kezelendő, és szigorú felülvizsgálati és veszélyforrás-modellezést igényel |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>A hozzáférés-vezérlés felügyeletéhez felelős személyek hozzáférése &nbsp; az egyes felhasználók szerepköre és feladatai alapján a minimálisan szükséges minimumra korlátozódik, és ezek a személyek nem férhetnek hozzá a vezérlőkhöz kapcsolódó naplózási funkciókhoz.

**Azonosító**: 1232.09 c3organizational. 12-09. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a "felhasználói jogok kiosztása" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |A Windows rendszerű számítógépeknek a "felhasználói jogok kiosztása" kategóriában megadott Csoportházirend beállításokkal kell rendelkezniük, amely lehetővé teszi a helyi bejelentkezést, az RDP-t, a hálózatról való hozzáférést és sok más felhasználói tevékenységet. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>A biztonsági naplózási tevékenységek függetlenek.

**Azonosító**: 1276.09 c2organizational. 2-09. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az egyéni előfizetés tulajdonosi szerepkörei nem létezhetnek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Ez a szabályzat biztosítja, hogy egyetlen egyéni előfizetés-tulajdonosi szerepkör sem létezik. |Naplózás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>Egy esemény kezdeményezése elkülönül az engedélyezéstől, hogy csökkentse az összejátszás lehetőségét.

**Azonosító**: 1277.09 c2organizational. 4-09. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a "biztonsági beállítások – felhasználói fiókok felügyelete" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |A Windows rendszerű gépeken a rendszergazdák, a jogosultságszint-emelési kérések, a fájl-és beállításjegyzék-írási hibák virtualizálása, a megadott Csoportházirend beállítások szerepelnek a "biztonsági beállítások – felhasználói fiókok felügyelete" kategóriában. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>A szervezet olyan feladatokat azonosít, amelyek elkülönítést igényelnek, és meghatározza az információs rendszer hozzáférési engedélyeit a feladatok elkülönítésének támogatásához; és a nem kompatibilis feladatokat több felhasználó is elkülöníti, hogy csökkentse a visszaélés vagy csalás lehetőségét.

**Azonosító**: 1278.09 c2organizational. 56-09. c **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az egyéni előfizetés tulajdonosi szerepkörei nem létezhetnek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Ez a szabályzat biztosítja, hogy egyetlen egyéni előfizetés-tulajdonosi szerepkör sem létezik. |Naplózás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>A kártékony kódok elleni védekezés

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>A víruskereső és kémprogram-elhárító telepítése, üzemeltetése és frissítése az összes végfelhasználói eszközön történik, hogy a rendszer rendszeres vizsgálatát végezzen a jogosulatlan szoftverek azonosításához és eltávolításához. Azok a kiszolgálói környezetek, amelyek esetében a kiszolgálói szoftverfejlesztők kifejezetten azt ajánlják, hogy a gazdagép-alapú víruskereső és kémprogram-elhárító szoftverek ne telepítsék a követelményt hálózati kártevő-észlelési (NBMD-) megoldáson keresztül.

**Azonosító**: 0201.09 j1organizational. 124-09. j **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazás-vezérlőelemek lehetővé teszik a gépen futó ismert, biztonságos alkalmazások listájának meghatározását, és riasztást küld, ha más alkalmazások futnak. Ez segít megerősíteni a gépeket a kártevők ellen. A szabályok konfigurálásának és karbantartásának egyszerűbbé tétele érdekében Security Center a gépi tanulás segítségével elemzi az egyes gépeken futó alkalmazásokat, és javaslatot tesz az ismert alkalmazások listájára. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[A Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware-bővítmény telepítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |Ezzel a szabályzattal egy alapértelmezett konfigurációval telepítheti a Microsoft IaaSAntimalware bővítményt, ha a virtuális gép nem a kártevő szoftverrel együtt van konfigurálva. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Az Endpoint Protection-megoldás létezésének és állapotának naplózása a virtuális gépek méretezési csoportjain, a fenyegetésektől és a sebezhetőségtől való védelem érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Az Azure-hoz készült Microsoft antimalware-t úgy kell konfigurálni, hogy automatikusan frissítse a védelmi aláírásokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |Ez a házirend minden olyan Windows rendszerű virtuális gépet naplóz, amely nincs konfigurálva a Microsoft antimalware Protection-aláírások automatikus frissítésével. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Hiányzó Endpoint Protection figyelése Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |A telepített Endpoint Protection ügynök nélküli kiszolgálókat javaslatokként Azure Security Center figyeli |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[A rendszerfrissítéseket telepíteni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A kiszolgálókon hiányzó biztonsági rendszerfrissítéseket a Azure Security Center a javaslatok szerint figyeli |AuditIfNotExists, letiltva |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>Biztonsági mentés

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>Az információk és szoftverek biztonsági másolatait, valamint az adathordozók és a visszaállítási eljárások rendszeresen végzett tesztelését a megfelelő időközönként hajtja végre.

**Azonosító**: 1616.09 l1organizational. 16-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A hosszú távú geo-redundáns biztonsági mentést engedélyezni kell az Azure SQL Database-adatbázisokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Ez a házirend minden olyan Azure SQL Database naplóz, amelynek hosszú távú, nem engedélyezett biztonsági másolata van. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>Az egyes rendszerekhez szükséges biztonsági mentési szint formális definícióját a rendszer definiálja és dokumentálja, beleértve az egyes rendszerek visszaállításának módját, a lemezképek hatókörét, a képfeldolgozás gyakoriságát és a megőrzés időtartamát a vonatkozó szerződéses, jogi, szabályozási és üzleti követelmények alapján.

**Azonosító**: 1617.09 l1organizational. 23-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL lehetővé teszi az adatbázis-kiszolgáló redundancia beállításának kiválasztását. Egy olyan geo-redundáns biztonsági mentési tárolóra állítható be, amelyben az adatai nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemel, de a rendszer egy párosított régióba is replikálja, hogy a helyreállítási lehetőség a régió meghibásodása esetén is biztosítható legyen. A Geo-redundáns tároló a biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozásakor engedélyezett. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>A biztonsági mentések egy fizikailag biztonságos távoli helyen tárolódnak, amely elegendő távolságot biztosít ahhoz, hogy az elsődleges helyen lévő adatok károsodása ésszerűen kihasználható legyen, és ésszerű fizikai és környezeti ellenőrzéseket biztosítson a távoli hely védelmének biztosítása érdekében.

**Azonosító**: 1618.09 l1organizational. 45-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL lehetővé teszi az adatbázis-kiszolgáló redundancia beállításának kiválasztását. Egy olyan geo-redundáns biztonsági mentési tárolóra állítható be, amelyben az adatai nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemel, de a rendszer egy párosított régióba is replikálja, hogy a helyreállítási lehetőség a régió meghibásodása esetén is biztosítható legyen. A Geo-redundáns tároló a biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozásakor engedélyezett. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>A rendszer karbantartja a biztonsági másolatok rekordjait, beleértve a tartalmat és az aktuális helyet is.

**Azonosító**: 1619.09 l1organizational. 7-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB lehetővé teszi az adatbázis-kiszolgáló redundancia beállításának kiválasztását. Egy olyan geo-redundáns biztonsági mentési tárolóra állítható be, amelyben az adatai nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemel, de a rendszer egy párosított régióba is replikálja, hogy a helyreállítási lehetőség a régió meghibásodása esetén is biztosítható legyen. A Geo-redundáns tároló a biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozásakor engedélyezett. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>Ha a biztonsági mentési szolgáltatást a harmadik fél továbbítja, a szolgáltatói szerződés tartalmazza a részletes védelmet a biztonsági mentési információk titkosságának, integritásának és rendelkezésre állásának szabályozásához.

**Azonosító**: 1620.09 l1organizational. 8-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Az Azure Backup engedélyezésével gondoskodjon az Azure-Virtual Machines védelméről. A Azure Backup biztonságos és költséghatékony adatvédelmi megoldás az Azure-hoz. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>Az összes biztonsági mentést az automatizált eszközök használatával követheti nyomon.

**Azonosító**: 1621.09 l2organizational. 1-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A hosszú távú geo-redundáns biztonsági mentést engedélyezni kell az Azure SQL Database-adatbázisokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Ez a házirend minden olyan Azure SQL Database naplóz, amelynek hosszú távú, nem engedélyezett biztonsági másolata van. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>A biztonsági másolatok integritásának és biztonságának fenntartása a jövőbeli rendelkezésre állás biztosítása érdekében, valamint a biztonsági másolatokkal kapcsolatos esetleges kisegítő problémák észlelése és enyhítése az egész területre kiterjedő katasztrófa esetén.

**Azonosító**: 1622.09 l2organizational. 23-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL lehetővé teszi az adatbázis-kiszolgáló redundancia beállításának kiválasztását. Egy olyan geo-redundáns biztonsági mentési tárolóra állítható be, amelyben az adatai nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemel, de a rendszer egy párosított régióba is replikálja, hogy a helyreállítási lehetőség a régió meghibásodása esetén is biztosítható legyen. A Geo-redundáns tároló a biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozásakor engedélyezett. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>A kezelt információkat a rendszer titkosított formában biztonsági mentést készít a titkosság biztosításához.

**Azonosító**: 1623.09 l2organizational. 4-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL lehetővé teszi az adatbázis-kiszolgáló redundancia beállításának kiválasztását. Egy olyan geo-redundáns biztonsági mentési tárolóra állítható be, amelyben az adatai nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemel, de a rendszer egy párosított régióba is replikálja, hogy a helyreállítási lehetőség a régió meghibásodása esetén is biztosítható legyen. A Geo-redundáns tároló a biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozásakor engedélyezett. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>A szervezet minden nap növekményes vagy különbözeti biztonsági mentést végez, a teljes biztonsági mentést hetente külön adathordozóra.

**Azonosító**: 1624.09 l3organizational. 12-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB lehetővé teszi az adatbázis-kiszolgáló redundancia beállításának kiválasztását. Egy olyan geo-redundáns biztonsági mentési tárolóra állítható be, amelyben az adatai nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemel, de a rendszer egy párosított régióba is replikálja, hogy a helyreállítási lehetőség a régió meghibásodása esetén is biztosítható legyen. A Geo-redundáns tároló a biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozásakor engedélyezett. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>A biztonsági másolatok három (3) generációját (teljes plusz az összes kapcsolódó növekményes vagy különbözeti biztonsági mentést) a rendszer kívül tárolja, és a helyszíni és a telephelyen kívüli biztonsági mentéseket a rendszer a név, a dátum, az idő és a művelet alapján naplózza.

**Azonosító**: 1625.09 l3organizational. 34-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Az Azure Backup engedélyezésével gondoskodjon az Azure-Virtual Machines védelméről. A Azure Backup biztonságos és költséghatékony adatvédelmi megoldás az Azure-hoz. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>A szervezet biztosítja a kezelt információk aktuális, lekérhető példányát a kiszolgálók áthelyezése előtt.

**Azonosító**: 1626.09 l3organizational. 5-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL lehetővé teszi az adatbázis-kiszolgáló redundancia beállításának kiválasztását. Egy olyan geo-redundáns biztonsági mentési tárolóra állítható be, amelyben az adatai nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemel, de a rendszer egy párosított régióba is replikálja, hogy a helyreállítási lehetőség a régió meghibásodása esetén is biztosítható legyen. A Geo-redundáns tároló a biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozásakor engedélyezett. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>A szervezet az egyes biztonsági másolatokat követő biztonsági mentési információkat teszteli a média megbízhatóságának és az információ integritásának ellenőrzéséhez, és legalább évente, ezt követően.

**Azonosító**: 1627.09 l3organizational. 6-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB lehetővé teszi az adatbázis-kiszolgáló redundancia beállításának kiválasztását. Egy olyan geo-redundáns biztonsági mentési tárolóra állítható be, amelyben az adatai nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemel, de a rendszer egy párosított régióba is replikálja, hogy a helyreállítási lehetőség a régió meghibásodása esetén is biztosítható legyen. A Geo-redundáns tároló a biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozásakor engedélyezett. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>Az adatbiztonsági mentési folyamat során felmerülő munkatársak szerepkörei és feladatai azonosítva vannak, és az adatokról tájékoztatnak a munkaerőről; a saját eszközökön (BYOD) lévő felhasználóknak a szervezeti és/vagy ügyféladatok biztonsági mentésére van szükségük az eszközön.

**Azonosító**: 1699.09 l1organizational. 10-09. l **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Az Azure Backup engedélyezésével gondoskodjon az Azure-Virtual Machines védelméről. A Azure Backup biztonságos és költséghatékony adatvédelmi megoldás az Azure-hoz. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>Hálózati vezérlők

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>A szervezet figyeli az információs rendszer számára engedélyezett és jogosulatlan vezeték nélküli hozzáférést, és tiltja a vezeték nélküli hozzáférési pontok (WAPs-EK) telepítését, kivéve, ha az informatikai részleg vagy a kijelölt képviselője írásban kifejezetten nem engedélyezte.

**Azonosító**: 0858.09 m1organizational. 4-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az összes hálózati portot korlátozni kell a virtuális géphez társított hálózati biztonsági csoportokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center azonosított néhány hálózati biztonsági csoport bejövő szabályát, hogy az túlságosan engedékeny legyen. A bejövő szabályok nem engedélyezhetik az "any" vagy az "Internet" tartományokból való hozzáférést. Ez potenciálisan lehetővé teheti a támadók számára az erőforrások célzását. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[A virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehető legrövidebb időn belül (JIT) való hozzáférést a Azure Security Center a javaslatok szerint figyeli |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[A Windows rendszerű gépeknek meg kell felelniük a "Windows tűzfal tulajdonságai" követelményeinek.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |A Windows rendszerű számítógépeken a tűzfal állapotához, a kapcsolatokhoz, a szabályok kezeléséhez és az értesítésekhez a "Windows tűzfal tulajdonságai" kategóriában megadott Csoportházirend beállítások szerepelnek. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>A szervezet biztosítja a hálózatban lévő információk biztonságát, a hálózati szolgáltatások és az információs szolgáltatások elérhetőségét a hálózaton keresztül, valamint a csatlakoztatott szolgáltatások illetéktelen hozzáférés elleni védelmét.

**Azonosító**: 0859.09 m1organizational. 78-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a lehetséges támadási felület csökkentése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>A szervezet hivatalosan felügyeli a hálózatban lévő berendezéseket, beleértve a felhasználói területeken lévő berendezéseket is.

**Azonosító**: 0860.09 m1organizational. 9-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |Ez a házirend automatikusan telepíti a diagnosztikai beállításokat a hálózati biztonsági csoportokra. A (z) {storagePrefixParameter} {NSGLocation} nevű Storage-fiók létrehozása automatikusan megtörténik. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[Service Bus virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ez a házirend minden olyan Service Bus naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>A helyi és/vagy nagy kiterjedésű hálózatokon, például a vezeték nélküli hálózatokon lévő eszközök azonosításához és hitelesítéséhez &nbsp; az információs rendszer a (i) megosztott ismert információs megoldást vagy (II) egy szervezeti hitelesítési megoldást használ, amelynek a pontos kiválasztása és erőssége az információs rendszer biztonsági kategorizálásának függvénye.

**Azonosító**: 0861.09 m2organizational. 67-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ez a házirend minden olyan App Service naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[A Windows rendszerű gépeknek meg kell felelniük a "biztonsági beállítások – hálózati hozzáférés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |A Windows rendszerű számítógépeknek a "biztonsági beállítások – hálózati hozzáférés" kategóriában megadott Csoportházirend beállításokkal kell rendelkezniük, beleértve a névtelen felhasználók hozzáférését, a helyi fiókokat és a beállításjegyzék távoli elérését. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>A szervezet biztosítja az információs rendszerek számára a továbbított információk titkosságának és integritásának védelmét, beleértve az átvitelhez és a fogadáshoz való felkészülés során.

**Azonosító**: 0862.09 m2organizational. 8-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL Server virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ez a házirend minden olyan SQL Server naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>A szervezet olyan tűzfal-konfigurációt hoz létre, amely korlátozza a nem megbízható hálózatok és a kezelt információs környezet bármely rendszerösszetevője közötti kapcsolatokat. a tűzfal konfigurációjának változásai a hálózati diagramon is frissülnek.

**Azonosító**: 0863.09 m2organizational. 910-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ez a házirend minden olyan eseményt naplóz, amely nem virtuális hálózati szolgáltatás-végpont használatára van konfigurálva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>A használati korlátozások és a megvalósítási útmutatások formálisan a VoIP-hez vannak meghatározva, beleértve a szolgáltatás engedélyezését és figyelését.

**Azonosító**: 0864.09 m2organizational. 12-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ez a házirend minden olyan Cosmos DB naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>A szervezet (i) az összekapcsolási biztonsági szerződések vagy más formális megállapodás használatával engedélyezi az információs rendszer és a szervezeten kívüli információs rendszerek közötti kapcsolatokat. (II) az egyes kapcsolatokra vonatkozó dokumentumok, az interfész jellemzői, a biztonsági követelmények és a közölt információk természete; (III) az összes megtagadását, a kivételek alóli szabályzatot alkalmazva lehetővé teszi az információs rendszer és a szervezeten kívüli információs rendszerek közötti kapcsolatok engedélyezését; a (z) és (IV) egy alapértelmezett megtagadás szabályt alkalmaz, amely a gazdagép-alapú tűzfalakon vagy a portok szűrési eszközein keresztül minden forgalmat elveszít a végpontokon (munkaállomásokon, kiszolgálókon stb.), kivéve a kifejezetten engedélyezett szolgáltatásokat és portokat.

**Azonosító**: 0865.09 m2organizational. 13-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ez a házirend minden olyan Key Vault naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>A szervezet leírja a hálózati összetevők logikai kezelésének csoportjait, szerepköreit és feladatait, valamint biztosítja a hálózati infrastruktúra elemeinek koordinálását és egységességét.

**Azonosító**: 0866.09 m3organizational. 1516-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Storage-fiókoknak korlátoznia kell a hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |A Storage-fiókok hálózati hozzáférését korlátozni kell. Konfigurálja a hálózati szabályokat úgy, hogy csak az engedélyezett hálózatokból származó alkalmazások férhessenek hozzá a Storage-fiókhoz. Az adott internetről vagy helyszíni ügyfelekről érkező kapcsolatok engedélyezéséhez az adott Azure-beli virtuális hálózatoktól vagy a nyilvános internetes IP-címtartományok felé irányuló adatforgalom megadható. |Naplózás, megtagadás, letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>A vezeték nélküli hozzáférési pontok biztonságos területeken vannak elhelyezve, és nem használatban vannak (például éjszaka, hétvégén).

**Azonosító**: 0867.09 m3organizational. 17-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Storage-fiókoknak virtuális hálózati szolgáltatási végpontot kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ez a házirend minden olyan Storage-fiókot naplóz, amely nem virtuális hálózati szolgáltatás-végpont használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>A szervezet létrehoz egy tűzfal-konfigurációt a bejövő és a kimenő forgalom korlátozására, amely a kezelt adatkörnyezethez szükséges.

**Azonosító**: 0868.09 m3organizational. 18-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a házirend minden olyan Container Registry naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>Az útválasztó konfigurációs fájljai biztonságosak és szinkronizálhatók.

**Azonosító**: 0869.09 m3organizational. 19-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a házirend minden olyan Container Registry naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>A rendszer megtagadja az összes proxy elérését, kivéve azokat a gazdagépeket, portokat és szolgáltatásokat, amelyekre kifejezetten szükség van.

**Azonosító**: 0870.09 m3organizational. 20-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a házirend minden olyan Container Registry naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>A mérvadó DNS-kiszolgálók belső és külső szerepkörökbe vannak különítve.

**Azonosító**: 0871.09 m3organizational. 22-09. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a házirend minden olyan Container Registry naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>Network Services biztonsága

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>A hálózati szolgáltató/kezelő által biztosított egyeztetett szolgáltatásokat a rendszer hivatalosan felügyeli és figyeli, hogy azok biztonságosan legyenek biztosítva.

**Azonosító**: 0835.09 n1organizational. 1-09. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A hálózati forgalom adatgyűjtési ügynökét telepíteni kell a Windows rendszerű virtuális gépekre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center a Microsoft függőségi ügynök használatával gyűjti össze az Azure-beli virtuális gépekről érkező hálózati adatforgalmi adatokat, így olyan speciális hálózatkezelési funkciókat tesz lehetővé, mint például a forgalom vizualizációja a hálózati térképen, a hálózatra vonatkozó ajánlásokat és az adott hálózati fenyegetéseket. |AuditIfNotExists, letiltva |[1.0.1 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |A virtuális gépek új Azure Resource Manager használatával olyan biztonsági fejlesztéseket biztosíthat, mint például a következők: erősebb hozzáférés-vezérlés (RBAC), jobb auditálás, Azure Resource Manager alapú üzembe helyezés és irányítás, hozzáférés a felügyelt identitásokhoz, hozzáférés a Key vaulthoz a titkokhoz, az Azure AD-alapú hitelesítéshez és a címkék és erőforráscsoportok támogatásához a biztonsági felügyelet megkönnyítéséhez |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>A szervezet hivatalosan hitelesíti és dokumentálja az egyes kapcsolatok jellemzőit egy információs rendszertől a szervezeten kívüli más információs rendszerig.

**Azonosító**: 0836.09. n2Organizational. 1-09. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A hálózati forgalom adatgyűjtési ügynökét Linux rendszerű virtuális gépekre kell telepíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center a Microsoft függőségi ügynök használatával gyűjti össze az Azure-beli virtuális gépekről érkező hálózati adatforgalmi adatokat, így olyan speciális hálózatkezelési funkciókat tesz lehetővé, mint például a forgalom vizualizációja a hálózati térképen, a hálózatra vonatkozó ajánlásokat és az adott hálózati fenyegetéseket. |AuditIfNotExists, letiltva |[1.0.1 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>A külső információs rendszerszolgáltatókhoz tartozó formális szerződések a biztonsággal és az adatvédelemmel kapcsolatos konkrét kötelezettségeket tartalmaznak.

**Azonosító**: 0837.09. n2Organizational. 2-09. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher egy regionális szolgáltatás, amely lehetővé teszi az Azure-ban, a-ben és az-ban lévő hálózati forgatókönyvek szintjének figyelését és diagnosztizálását. A forgatókönyvek szintjének figyelése lehetővé teszi a hibák diagnosztizálását a végpontok közötti hálózati szintű nézetben. A Network Watcher elérhető hálózati diagnosztikai és vizualizációs eszközök segítségével megismerheti, diagnosztizálhatja és elemezheti a hálózatát az Azure-ban. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>A szervezet folyamatosan felülvizsgálja és frissíti az összekapcsolási biztonsági megállapodásokat, és ellenőrzi a biztonsági követelmények betartatását.

**Azonosító**: 0885.09 n2organizational. 3-09. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A hálózati forgalom adatgyűjtési ügynökét Linux rendszerű virtuális gépekre kell telepíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center a Microsoft függőségi ügynök használatával gyűjti össze az Azure-beli virtuális gépekről érkező hálózati adatforgalmi adatokat, így olyan speciális hálózatkezelési funkciókat tesz lehetővé, mint például a forgalom vizualizációja a hálózati térképen, a hálózatra vonatkozó ajánlásokat és az adott hálózati fenyegetéseket. |AuditIfNotExists, letiltva |[1.0.1 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>A szervezet egy formális szerződésben vagy más dokumentumban, vagy i.) engedélyezheti az összes, megtagadási kivételt, vagy a II. megtagadást, az engedélyezés alóli kivételt (előnyben részesített), az adott információs rendszerek külső információs rendszerekhez való kapcsolódásának engedélyezésére vonatkozó szabályzatot.

**Azonosító**: 0886.09 n2organizational. 4-09. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher egy regionális szolgáltatás, amely lehetővé teszi az Azure-ban, a-ben és az-ban lévő hálózati forgatókönyvek szintjének figyelését és diagnosztizálását. A forgatókönyvek szintjének figyelése lehetővé teszi a hibák diagnosztizálását a végpontok közötti hálózati szintű nézetben. A Network Watcher elérhető hálózati diagnosztikai és vizualizációs eszközök segítségével megismerheti, diagnosztizálhatja és elemezheti a hálózatát az Azure-ban. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>A szervezet külső/kiszervezett szolgáltatók használatát igényli a külső/kiszervezett szolgáltatások kiépítésében használt konkrét funkciók, portok és protokollok azonosításához.

**Azonosító**: 0887.09 n2organizational. 5-09. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A hálózati forgalom adatgyűjtési ügynökét telepíteni kell a Windows rendszerű virtuális gépekre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center a Microsoft függőségi ügynök használatával gyűjti össze az Azure-beli virtuális gépekről érkező hálózati adatforgalmi adatokat, így olyan speciális hálózatkezelési funkciókat tesz lehetővé, mint például a forgalom vizualizációja a hálózati térképen, a hálózatra vonatkozó ajánlásokat és az adott hálózati fenyegetéseket. |AuditIfNotExists, letiltva |[1.0.1 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>A külső/kiszervezett szolgáltatóval kötött szerződés tartalmazza azt a specifikációt, amelyet a szolgáltató felelős a megosztott információk megosztásának védelméért.

**Azonosító**: 0888.09 n2organizational. 6-09. n **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher egy regionális szolgáltatás, amely lehetővé teszi az Azure-ban, a-ben és az-ban lévő hálózati forgatókönyvek szintjének figyelését és diagnosztizálását. A forgatókönyvek szintjének figyelése lehetővé teszi a hibák diagnosztizálását a végpontok közötti hálózati szintű nézetben. A Network Watcher elérhető hálózati diagnosztikai és vizualizációs eszközök segítségével megismerheti, diagnosztizálhatja és elemezheti a hálózatát az Azure-ban. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>Cserélhető adathordozók kezelése

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>Az adatbesorolási szint alapján a szervezet a használat előtt regisztrálja az adathordozót (beleértve a laptopokat is), megkeresi az adott adathordozó használatára vonatkozó ésszerű korlátozásokat, és megfelelő szintű fizikai és logikai védelmet biztosít (beleértve a titkosítást is) a kezelt adatokat tartalmazó adathordozók számára, a megfelelő megsemmisítés vagy fertőtlenítés céljából.

**Azonosító**: 0301.09 o1organizational. 123-09. o **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Az átlátható adattitkosítást engedélyezni kell a REST-alapú adatok védelme és a megfelelőségi követelmények teljesítése érdekében |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>A szervezet megvédi és szabályozza a bizalmas adatokat tartalmazó adathordozót az ellenőrzött területeken kívüli átvitel során.

**Azonosító**: 0302.09 o2organizational. 1-09. o **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A lemezes titkosítást a virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Az engedélyezett lemezes titkosítás nélküli virtuális gépeket javaslatokként Azure Security Center figyeli. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>A korlátozott használatot igénylő digitális és nem digitális adathordozók, valamint a használatuk korlátozására használt konkrét óvintézkedések azonosíthatók.

**Azonosító**: 0303.09 o2organizational. 2-09. o **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A nem csatolt lemezeket titkosítani kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Ez a házirend minden nem csatlakoztatott lemezt naplóz a titkosítás engedélyezése nélkül. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>A szervezet korlátozza az írható cserélhető adathordozók és a személyes tulajdonú cserélhető adathordozók használatát a szervezeti rendszerekben.

**Azonosító**: 0304.09 o3organizational. 1-09. o **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Titkosítás megkövetelése Data Lake Store fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Ez a szabályzat biztosítja, hogy a titkosítás minden Data Lake Store fiókon engedélyezve legyen |tagadja |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[Az SQL felügyelt példányainak az ügyfelek által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |A transzparens adattitkosítás (TDE) a saját kulccsal való implementálása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, nagyobb biztonságot nyújt egy HSM-alapú külső szolgáltatással, valamint a feladatok elkülönítését. Ez a javaslat a kapcsolódó megfelelőségi követelményeknek megfelelő szervezetekre vonatkozik. |AuditIfNotExists, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Az SQL-kiszolgálóknak az ügyfél által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |A transzparens adattitkosítás (TDE) a saját kulccsal való implementálása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, nagyobb biztonságot nyújt egy HSM-alapú külső szolgáltatással, valamint a feladatok elkülönítésének előléptetését. Ez a javaslat a kapcsolódó megfelelőségi követelményeknek megfelelő szervezetekre vonatkozik. |AuditIfNotExists, letiltva |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>Információcsere-házirendek és eljárások

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>A felhőalapú szolgáltatók egy iparág által elismert virtualizációs platformot és standard virtualizációs formátumokat (például Open Virtualization Format, OVF) használnak az együttműködési képesség biztosításához, és dokumentálták a használatban lévő hypervisorok egyéni módosításait, valamint az ügyfelek általi felülvizsgálathoz elérhető összes megoldás-specifikus virtualizálási hookot.

**Azonosító**: 0662.09 scsporganizational. 2-09. s **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a (z) "on" értékre van állítva a webalkalmazás "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" értéke](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt igényeljen a bejövő kérésekhez. Csak az érvényes tanúsítvánnyal rendelkező ügyfelek érhetik el az alkalmazást. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>A szervezet hivatalosan több védelmet is biztosít, mielőtt engedélyezné az információs rendszerek használatát az információcseréhez.

**Azonosító**: 0901.09 s1organizational. 1-09. s **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A CORS nem engedélyezheti minden erőforrásnak, hogy hozzáférjen a webalkalmazásokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé az összes tartomány számára a webalkalmazás elérését. Csak a szükséges tartományokat engedélyezze a webalkalmazással való kommunikációhoz. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>Távoli (külső) hozzáférés a szervezet információs eszközeihez és a külső információs eszközökhöz való hozzáférés (amelyekhez a szervezetnek nincs vezérlése) egyértelműen meghatározott feltételeken és kikötéseken alapul.

**Azonosító**: 0902.09 s2organizational. 13-09. s **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a függvény alkalmazásaihoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé az összes tartomány számára a Function app elérését. Csak a szükséges tartományokat engedélyezze a Function alkalmazással való kommunikációhoz. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>A szervezet olyan feltételeket és kikötéseket hoz létre, amelyek összhangban vannak a külső információs rendszerek tulajdonosával, üzemeltetésével és/vagy karbantartásával létrehozott megbízhatósági kapcsolattal, és lehetővé teszi, hogy a jogosult személyek hozzáférhessenek az információs rendszerhez a külső információs rendszerekből; és (II) a szervezet által vezérelt információk feldolgozása, tárolása vagy továbbítása külső információs rendszerekkel.

**Azonosító**: 0911.09 s1organizational. 2-09. s **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A CORS nem teszi lehetővé minden erőforrás számára az API-alkalmazás elérését](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé az összes tartomány számára az API-alkalmazás elérését. Csak a szükséges tartományokat engedélyezze az API-alkalmazással való kommunikációhoz. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>A titkosítás a távelérési munkamenetek titkosságának és integritásának a belső hálózatra és a külső rendszerekre való védelmére szolgál.

**Azonosító**: 0912.09 s1organizational. 4-09. s **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A távoli hibakeresést ki kell kapcsolni a webalkalmazásokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |A távoli hibakereséshez a bejövő portok megnyitására van szükség egy webalkalmazáson. A távoli hibakeresést ki kell kapcsolni. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>Az erős titkosítási protokollok használatával biztosítható a kezelt információk védelme a kevésbé megbízható/nyitott nyilvános hálózatokon való átvitel során.

**Azonosító**: 0913.09 s1organizational. 5-09. s **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A távoli hibakeresést ki kell kapcsolni a Function apps szolgáltatásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A távoli hibakereséshez bejövő portokat kell megnyitni a Function apps szolgáltatásban. A távoli hibakeresést ki kell kapcsolni. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>A szervezet biztosítja, hogy a kommunikációs védelmi követelmények, beleértve az információcsere biztonságát is, a szabályzatok fejlesztésének és megfelelőségének ellenőrzésének tárgya.

**Azonosító**: 0914.09 s1organizational. 6-09. s **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A távoli hibakeresést ki kell kapcsolni API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |A távoli hibakereséshez a bejövő portok megnyitására van szükség az API-alkalmazásokban. A távoli hibakeresést ki kell kapcsolni. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>A szervezet korlátozza a szervezet által vezérelt hordozható tárolóeszközök használatát a külső információs rendszereken lévő, felhatalmazott személyek által.

**Azonosító**: 0915.09 s2organizational. 2-09. s **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a (z) "on" értékre van állítva a webalkalmazás "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" értéke](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt igényeljen a bejövő kérésekhez. Csak az érvényes tanúsítvánnyal rendelkező ügyfelek érhetik el az alkalmazást. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>Az információs rendszer tiltja az együttműködésen alapuló számítástechnikai eszközök távoli aktiválását, és explicit módon jelzi, hogy a felhasználók fizikailag is jelen vannak az eszközökön.

**Azonosító**: 0916.09 s2organizational. 4-09. s **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A CORS nem engedélyezheti minden erőforrásnak, hogy hozzáférjen a webalkalmazásokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé az összes tartomány számára a webalkalmazás elérését. Csak a szükséges tartományokat engedélyezze a webalkalmazással való kommunikációhoz. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>A felhőalapú szolgáltatók biztonságos (például nem világos szöveges és hitelesített) szabványosított hálózati protokollokat használnak az adatok importálásához és exportálásához, valamint a szolgáltatás felügyeletéhez, valamint elérhetővé teszik a dokumentumot a fogyasztóknak (bérlőknek), amelyek részletesen ismertetik az érintett együttműködési és hordozhatósági szabványokat.

**Azonosító**: 0960.09 scsporganizational. 1-09. s **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a függvény alkalmazásaihoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé az összes tartomány számára a Function app elérését. Csak a szükséges tartományokat engedélyezze a Function alkalmazással való kommunikációhoz. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>A személyzet minden típusú információcsere (szóbeli, dokumentum-és elektronikus) esetében megfelelő képzést biztosít a vezető alapelvekről és gyakorlatokról.

**Azonosító**: 1325.09 s1organizational. 3-09. s **tulajdonlás**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A távoli hibakeresést ki kell kapcsolni a Function apps szolgáltatásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A távoli hibakereséshez bejövő portokat kell megnyitni a Function apps szolgáltatásban. A távoli hibakeresést ki kell kapcsolni. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>Online tranzakciók

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>A rendszer ellenőrzi, hogy az elektronikus kereskedelmi és az online tranzakcióban részt vevő adatok szerepelnek-e a kezelt információk alapján.

**Azonosító**: 0943.09 y1organizational. 1-09. y **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |A biztonságos átvitel naplózási követelménye a Storage-fiókban. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>Az összes érintett fél közötti kommunikációhoz használt protokollok titkosítási módszerekkel (például SSL) vannak védve.

**Azonosító**: 0945.09 y1organizational. 3-09. y **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A megadott tanúsítványokat nem tartalmazó Windows-gépek naplózása a megbízható gyökérben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |Megköveteli, hogy az előfeltételek telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . A gépek nem megfelelőek, ha a megbízható Főtanúsítvány-tároló (CERT: \ LocalMachine\Root) nem tartalmaz egy vagy több, a Policy paraméter által felsorolt tanúsítványt. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>A szervezetnek a tranzakcióban részt vevő felek mindegyike számára szükséges a titkosítás és az elektronikus aláírások használata.

**Azonosító**: 0946.09 y2organizational. 14-09. y **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak az SSL-kapcsolaton keresztüli kapcsolatok engedélyezése az Azure cache-Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>A szervezet gondoskodik arról, hogy a tranzakció részletei a nyilvánosan elérhető környezeteken kívül legyenek (például a szervezet intranetén lévő tárolási platformon), és az internetről közvetlenül elérhető adathordozón ne legyenek megőrizve és kitéve.

**Azonosító**: 0947.09 y2organizational. 2-09. y **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>Ha megbízható szolgáltatót használnak (például digitális aláírások és/vagy digitális tanúsítványok kiállítása és karbantartása céljából), a biztonság integrálva van és beágyazva van a teljes körű tanúsítvány-és aláírás-kezelési folyamat során.

**Azonosító**: 0948.09 y2organizational. 3-09. y **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>A kommunikációhoz használt protokollok az új biztonsági rések megoldásához lettek kibővítve, és a protokollok frissített verziói a lehető leghamarabb el lesznek fogadva.

**Azonosító**: 0949.09 y2organizational. 5-09. y **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az API-alkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[A legújabb TLS-verziót kell használni az API-alkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a függvényalkalmazás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a webalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[A webalkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>Az operatív szoftverek felügyelete

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>Csak a jogosult rendszergazdák jogosultak a szoftverek, alkalmazások és programok jóváhagyott frissítéseinek megvalósítására az üzleti követelmények és a kiadás biztonsági következményei alapján.

**Azonosító**: 0605.10 h1system. 12-10. h **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azokat a kiszolgálókat, amelyek nem felelnek meg a beállított alapkonfigurációnak, Azure Security Center a javaslatok szerint figyelik |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[A Windows rendszerű gépeknek meg kell felelniük a "biztonsági beállítások – naplózás" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |A Windows rendszerű számítógépeknek a naplózási házirend alkategóriájának kikényszerítéséhez és a biztonsági naplózások bekapcsolásához a "biztonsági beállítások – naplózás" kategóriában meg kell adni a megadott Csoportházirend beállításokat. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[A Windows rendszerű gépeknek meg kell felelniük a "rendszernaplózási házirendek – Fiókkezelés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |A Windows rendszerű számítógépeknek a "rendszernaplózási házirendek – Fiókkezelés" kategóriában meg kell adni a megadott Csoportházirend beállításokat az alkalmazás, a biztonság és a felhasználói csoport kezeléséhez, valamint más felügyeleti eseményekhez. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>Az alkalmazások és az operációs rendszerek tesztelésére a használhatóság, a biztonság és az éles környezet előtt van hatással.

**Azonosító**: 0606.10 h2system. 1 – 10. h **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |A biztonsági beállítások biztonsági beállításainak naplózása a Docker által telepített és a Azure Security Centerban javaslatokként megjelenő számítógépeken. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>A szervezet a Configuration Control program használatával tartja karban az összes megvalósított szoftver és a rendszer dokumentációját, és archiválja a megvalósított szoftver és a kapcsolódó rendszerdokumentáció korábbi verzióit.

**Azonosító**: 0607.10 h2system. 23-10. h **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazás-vezérlőelemek lehetővé teszik a gépen futó ismert, biztonságos alkalmazások listájának meghatározását, és riasztást küld, ha más alkalmazások futnak. Ez segít megerősíteni a gépeket a kártevők ellen. A szabályok konfigurálásának és karbantartásának egyszerűbbé tétele érdekében Security Center a gépi tanulás segítségével elemzi az egyes gépeken futó alkalmazásokat, és javaslatot tesz az ismert alkalmazások listájára. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |A támadások elleni védelem érdekében ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokban. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>Vezérlési eljárások módosítása

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>Az alkalmazásrendszer felelős kezelői szintén felelősek a projekt vagy a támogatási környezet szigorú ellenőrzésének (biztonságának), és gondoskodnak arról, hogy az összes javasolt rendszermódosítást ellenőrizze, hogy nem veszélyeztetik-e a rendszer vagy az operációs környezet biztonságát.

**Azonosító**: 0635.10 k1organizational. 12-10. k **tulajdonlása**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a rendszernaplózási házirendek részletes követési követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű számítógépeknek a "rendszernaplózási házirendek – részletes nyomon követés" kategóriában meg kell adni a megadott Csoportházirend beállításokat a naplózási DPAPI, a folyamat létrehozásának/megszakításának, az RPC-események és a PNP-tevékenységeknek. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>A szervezet hivatalosan kezeli a célt, a hatókört, a szerepköröket, a felelősségeket, a felügyeleti kötelezettségvállalást, a szervezeti entitások közötti koordinációt és a konfiguráció felügyeletének megfelelőségét (például szabályzatok, szabványok, folyamatok).

**Azonosító**: 0636.10 k2organizational. 1 – 10. k **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a rendszernaplózási házirendek részletes követési követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű számítógépeknek a "rendszernaplózási házirendek – részletes nyomon követés" kategóriában meg kell adni a megadott Csoportházirend beállításokat a naplózási DPAPI, a folyamat létrehozásának/megszakításának, az RPC-események és a PNP-tevékenységeknek. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>A szervezet kifejlesztett, dokumentált és implementált egy konfigurációs felügyeleti tervet az információs rendszer számára.

**Azonosító**: 0637.10 k2organizational. 2-10. k **tulajdonlása**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a rendszernaplózási házirendek részletes követési követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű számítógépeknek a "rendszernaplózási házirendek – részletes nyomon követés" kategóriában meg kell adni a megadott Csoportházirend beállításokat a naplózási DPAPI, a folyamat létrehozásának/megszakításának, az RPC-események és a PNP-tevékenységeknek. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>A módosításokat a rendszer hivatalosan szabályozza, dokumentálja és érvényesíti az információs rendszerek sérülésének csökkentése érdekében.

**Azonosító**: 0638.10 k2organizational. 34569-10. k **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a rendszernaplózási házirendek részletes követési követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű számítógépeknek a "rendszernaplózási házirendek – részletes nyomon követés" kategóriában meg kell adni a megadott Csoportházirend beállításokat a naplózási DPAPI, a folyamat létrehozásának/megszakításának, az RPC-események és a PNP-tevékenységeknek. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>A telepítési ellenőrzőlisták és a sebezhetőségi vizsgálatok a kiszolgálók, munkaállomások, eszközök és berendezések konfigurációjának ellenőrzésére szolgálnak, és biztosítják, hogy a konfiguráció megfeleljen a minimális követelményeknek.

**Azonosító**: 0639.10 k2organizational. 78-10. k **tulajdonlása**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a rendszernaplózási házirendek részletes követési követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű számítógépeknek a "rendszernaplózási házirendek – részletes nyomon követés" kategóriában meg kell adni a megadott Csoportházirend beállításokat a naplózási DPAPI, a folyamat létrehozásának/megszakításának, az RPC-események és a PNP-tevékenységeknek. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>Ahol a fejlesztés kiszervezett, a változásokra vonatkozó ellenőrzési eljárások a szerződés (ek) ben szerepelnek, és kifejezetten megkövetelik a fejlesztőtől, hogy nyomon kövesse a biztonsági hibákat és a hibák megoldását a rendszeren, az összetevőn vagy a szolgáltatáson belül, illetve a jelentés eredményeiben a szervezet által meghatározott munkatársak vagy szerepkörök számára.

**Azonosító**: 0640.10 k2organizational. 1012-10. k **tulajdonlása**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a rendszernaplózási házirendek részletes követési követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű számítógépeknek a "rendszernaplózási házirendek – részletes nyomon követés" kategóriában meg kell adni a megadott Csoportházirend beállításokat a naplózási DPAPI, a folyamat létrehozásának/megszakításának, az RPC-események és a PNP-tevékenységeknek. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>A szervezet nem használ automatikus frissítéseket a kritikus rendszereken.

**Azonosító**: 0641.10 k2organizational. 11-10. k **tulajdonlása**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a rendszernaplózási házirendek részletes követési követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű számítógépeknek a "rendszernaplózási házirendek – részletes nyomon követés" kategóriában meg kell adni a megadott Csoportházirend beállításokat a naplózási DPAPI, a folyamat létrehozásának/megszakításának, az RPC-események és a PNP-tevékenységeknek. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>A szervezet fejleszti, dokumentálja és karbantartja a Configuration Control alatt az információs rendszer aktuális alapkonfigurációját, és szükség szerint frissíti az alaptervet.

**Azonosító**: 0642.10 k3organizational. 12-10. k **tulajdonlása**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a rendszernaplózási házirendek részletes követési követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű számítógépeknek a "rendszernaplózási házirendek – részletes nyomon követés" kategóriában meg kell adni a megadott Csoportházirend beállításokat a naplózási DPAPI, a folyamat létrehozásának/megszakításának, az RPC-események és a PNP-tevékenységeknek. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>A szervezet (i) a legújabb biztonsági alapkonfigurációk használatával létrehozza és dokumentálja az információs rendszeren alkalmazott informatikai termékek kötelező konfigurációs beállításait; (II) azonosítja, dokumentálja és jóváhagyja az egyes összetevőkre vonatkozó kötelezően meghatározott konfigurációs beállítások alóli kivételeket az explicit működési követelmények alapján. a és a (III) a szervezeti házirendekkel és eljárásokkal összhangban figyeli és szabályozza a konfigurációs beállítások módosításait.

**Azonosító**: 0643.10 k3organizational. 3-10. k **tulajdonlása**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a rendszernaplózási házirendek részletes követési követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű számítógépeknek a "rendszernaplózási házirendek – részletes nyomon követés" kategóriában meg kell adni a megadott Csoportházirend beállításokat a naplózási DPAPI, a folyamat létrehozásának/megszakításának, az RPC-események és a PNP-tevékenységeknek. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>A szervezet automatikus mechanizmusokat alkalmaz (i) a konfigurációs beállítások központi kezelése, alkalmazása és ellenőrzése; (II) válaszol a hálózati és a rendszerbiztonsággal kapcsolatos konfigurációs beállítások jogosulatlan módosításaira; és (III) érvényesíti a hozzáférési korlátozásokat és naplózza a kényszerítési műveleteket.

**Azonosító**: 0644.10 k3organizational. 4-10. k **tulajdonlása**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a rendszernaplózási házirendek részletes követési követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű számítógépeknek a "rendszernaplózási házirendek – részletes nyomon követés" kategóriában meg kell adni a megadott Csoportházirend beállításokat a naplózási DPAPI, a folyamat létrehozásának/megszakításának, az RPC-események és a PNP-tevékenységeknek. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>A technikai sebezhetőségek szabályozása

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>A technikai sebezhetőségeket azonosítják, kiértékelik a kockázatot, és időben korrigálják azokat.

**Azonosító**: 0709.10 m1organizational. 1 – 10. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A biztonsági rések felmérésére szolgáló megoldást engedélyezni kell a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Naplózza a virtuális gépeket annak észlelésére, hogy támogatott sebezhetőségi felmérési megoldást futtatnak-e. A biztonsági rések azonosítása és elemzése minden számítógépes kockázati és biztonsági program alapvető összetevője. A Azure Security Center Standard díjszabási szintje a virtuális gépek sebezhetőségi vizsgálatát is magában foglalja, többletköltség nélkül. Emellett a Security Center automatikusan üzembe helyezheti ezt az eszközt. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |A biztonsági beállítások biztonsági beállításainak naplózása a Docker által telepített és a Azure Security Centerban javaslatokként megjelenő számítógépeken. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azokat a kiszolgálókat, amelyek nem felelnek meg a beállított alapkonfigurációnak, Azure Security Center a javaslatok szerint figyelik |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |A támadások elleni védelem érdekében ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokban. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Az SQL-adatbázisok biztonsági réseit szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |A sebezhetőségi felmérés eredményeinek figyelése, valamint az adatbázis-biztonsági rések szervizelésével kapcsolatos javaslatok. |AuditIfNotExists, letiltva |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[A biztonsági rések felmérését engedélyezni kell a felügyelt SQL-példányon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Minden olyan felügyelt SQL-példány naplózása, amelyen nincs engedélyezve az ismétlődő sebezhetőségi felmérések ellenőrzése. A sebezhetőségi felmérés felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Naplózza azokat az Azure SQL-kiszolgálókat, amelyeken engedélyezve van az ismétlődő sebezhetőségi felmérések ellenőrzése. A sebezhetőségi felmérés felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[A Windows rendszerű gépeknek meg kell felelniük a "biztonsági beállítások – Microsoft hálózati kiszolgáló" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |A Windows rendszerű számítógépeknek a "biztonsági beállítások – Microsoft hálózati kiszolgáló" kategóriában megadott Csoportházirend beállításokkal kell rendelkezniük az SMB v1-kiszolgáló letiltásához. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>Az összes rendszer-és hálózati összetevő megerősített konfigurációs szabványt tartalmaz.

**Azonosító**: 0710.10 m2organizational. 1 – 10. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A biztonsági rések felmérését engedélyezni kell a felügyelt SQL-példányon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Minden olyan felügyelt SQL-példány naplózása, amelyen nincs engedélyezve az ismétlődő sebezhetőségi felmérések ellenőrzése. A sebezhetőségi felmérés felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>A rendszerek által azonosított sebezhetőségek figyelésére, felmérésére, rangsorolására és szervizelésére a technikai sebezhetőségi kezelési program van érvényben.

**Azonosító**: 0711.10 m2organizational. 23-10. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A biztonsági rések felmérésére szolgáló megoldást engedélyezni kell a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Naplózza a virtuális gépeket annak észlelésére, hogy támogatott sebezhetőségi felmérési megoldást futtatnak-e. A biztonsági rések azonosítása és elemzése minden számítógépes kockázati és biztonsági program alapvető összetevője. A Azure Security Center Standard díjszabási szintje a virtuális gépek sebezhetőségi vizsgálatát is magában foglalja, többletköltség nélkül. Emellett a Security Center automatikusan üzembe helyezheti ezt az eszközt. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>A javítások tesztelése és értékelése a telepítésük előtt történik.

**Azonosító**: 0713.10 m2organizational. 5-10. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azokat a kiszolgálókat, amelyek nem felelnek meg a beállított alapkonfigurációnak, Azure Security Center a javaslatok szerint figyelik |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>A technikai sebezhetőségi kezelési programot negyedévente értékeljük.

**Azonosító**: 0714.10 m2organizational. 7 – 10. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |A támadások elleni védelem érdekében ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokban. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>A rendszerek megfelelő megerősítése (például csak a szükséges és biztonságos szolgáltatásokkal, portokkal és protokollokkal van konfigurálva).

**Azonosító**: 0715.10 m2organizational. 8 – 10. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |A biztonsági beállítások biztonsági beállításainak naplózása a Docker által telepített és a Azure Security Centerban javaslatokként megjelenő számítógépeken. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>A szervezet a vállalati biztonsági helyzetek felülvizsgálatát igény szerint végzi, de az összes 365 (365) napon belül egyszer, a szervezeti eljárásoknak megfelelően.

**Azonosító**: 0716.10 m3organizational. 1 – 10. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL-adatbázisok biztonsági réseit szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |A sebezhetőségi felmérés eredményeinek figyelése, valamint az adatbázis-biztonsági rések szervizelésével kapcsolatos javaslatok. |AuditIfNotExists, letiltva |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>A biztonsági rések keresési eszközei közé tartozik a beolvasott információs rendszerbiztonsági rések könnyű frissítése.

**Azonosító**: 0717.10 m3organizational. 2-10. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |A támadások elleni védelem érdekében ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokban. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>A szervezet megkeresi az információs rendszer és az üzemeltetett alkalmazások biztonsági réseit a hibák elhárításának (automatikusan) és újbóli (manuálisan vagy automatikusan) állapotának megállapítására, amikor a rendszer azonosított és jelentett új biztonsági réseket észlel és jelez.

**Azonosító**: 0718.10 m3organizational. 34-10. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azokat a kiszolgálókat, amelyek nem felelnek meg a beállított alapkonfigurációnak, Azure Security Center a javaslatok szerint figyelik |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>A szervezet frissíti az információrendszer-biztonsági rések listáját minden harminc (30) napon belül, vagy ha új biztonsági réseket azonosít és jelentett.

**Azonosító**: 0719.10 m3organizational. 5-10. m **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A biztonsági rések felmérését engedélyezni kell a felügyelt SQL-példányon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Minden olyan felügyelt SQL-példány naplózása, amelyen nincs engedélyezve az ismétlődő sebezhetőségi felmérések ellenőrzése. A sebezhetőségi felmérés felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>Üzletmenet-folytonosság és kockázatértékelés

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>A szervezet azonosítja az üzleti folytonosságot igénylő kritikus üzleti folyamatokat.

**Azonosító**: 1634.12 b1organizational. 1-12. b **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Olyan virtuális gépek naplózása, amelyek nem rendelkeznek a vész-helyreállítási beállításokkal. A vész-helyreállítással kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>Az üzletmenet folytonosságának biztonsági szempontjai (i) az események (vagy eseménysorozat) azonosításán alapulnak, amely megszakításokat okozhat a szervezet kritikus fontosságú üzleti folyamataiban (például a berendezések meghibásodása, az emberi hibák, a lopás, a tűz, a természeti katasztrófák pedig a terrorizmus); (II) az ilyen megszakítások valószínűségének és hatásának megállapítása, valamint az időbeli változás, valamint a helyreállítási időszak tekintetében a kockázatértékelés után. (III) a kockázatértékelés eredményei alapján egy üzletmenet-folytonossági stratégiát dolgozunk ki, amely az üzletmenet folytonosságának általános megközelítését határozza meg. és (IV) Miután létrejött a stratégia, a jóváhagyást a felügyelet adja meg, valamint egy, a stratégia megvalósításához létrehozott és jóváhagyott csomagot.

**Azonosító**: 1635.12 b1organizational. 2-12. b **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Key Vault felügyelt HSM-nek engedélyezve kell lennie a kiürítési védelemmel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Egy Azure Key Vault felügyelt HSM rosszindulatú törlése állandó adatvesztéshez vezethet. A szervezet rosszindulatú bennfentes a felügyelt HSM Azure Key Vault törlésére és törlésére is képes. A védelem törlésével megvédheti a bennfentes támadásoktól a Azure Key Vault felügyelt HSM-hez tartozó, nem kötelező megőrzési időtartam kikényszerítésével. A szervezeten belül a Microsoft nem tudja törölni a Azure Key Vault felügyelt HSM-et a helyreállítható törlés megőrzési ideje alatt. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[A kulcstárolóknak engedélyezve kell lennie a kiürítési védelemmel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A kulcstartó rosszindulatú törlése maradandó adatvesztést eredményezhet. A szervezet rosszindulatú bennfentes a kulcstartók törlésére és kiürítésére is képes. A védelem törlésével megvédheti a bennfentes támadásoktól a nem kötelező megőrzési idő kikényszerítésével a törölt kulcstartók esetében. A szervezeten belül a Microsoft nem tudja törölni a kulcstárolókat a helyreállítható törlés megőrzési időszak alatt. |Naplózás, megtagadás, letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>Az üzleti hatás elemzése a katasztrófák, a biztonsági hibák, a szolgáltatás elvesztése és a szolgáltatás rendelkezésre állása következményeinek kiértékelésére szolgál.

**Azonosító**: 1637.12 b2organizational. 2-12. b **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelniük a "biztonsági beállítások – helyreállítási konzol" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |A Windows rendszerű számítógépeknek a "biztonsági beállítások – helyreállítási konzol" kategória megadott Csoportházirend beállításaival kell rendelkezniük a hajlékonylemez-másolás és az összes meghajtó és mappa elérésének engedélyezéséhez. Ehhez a Szabályzathoz az szükséges, hogy a vendég konfigurációjának előfeltételei telepítve legyenek a szabályzat-hozzárendelési hatókörben. Részletekért keresse fel a következőt: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>Az üzletmenet-folytonossági kockázatértékelés (i) évente zajlik, és az üzleti erőforrások és folyamatok tulajdonosai teljes mértékben részt vesznek; (II) mérlegelje az összes üzleti folyamatot, és nem korlátozódik az információs eszközökre, de az adatbiztonságra vonatkozó eredményeket is tartalmazza; a és a (III) azonosítja, számszerűsíti és rangsorolja a kulcsfontosságú üzleti célkitűzésekkel és a szervezetre vonatkozó szempontokkal kapcsolatos kockázatokat, beleértve a kritikus erőforrásokat, a megszakadás következményeit, a megengedett kimaradási időt és a helyreállítási prioritásokat.

**Azonosító**: 1638.12 b2organizational. 345-12. b **tulajdonos**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Olyan virtuális gépek naplózása, amelyek nem rendelkeznek a vész-helyreállítási beállításokkal. A vész-helyreállítással kapcsolatos további információkért látogasson el a következő oldalra: [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> Az adott Azure Policy-definíciók rendelkezésre állása Azure Government és más nemzeti felhőkben is változhat.

## <a name="next-steps"></a>Következő lépések

További cikkek a Azure Policyról:

- A [szabályozás megfelelőségének](../concepts/regulatory-compliance.md) áttekintése.
- Tekintse meg a [kezdeményezés definíciójának struktúráját](../concepts/initiative-definition-structure.md).
- Tekintse át a további példákat [Azure Policy mintákon](./index.md).
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
