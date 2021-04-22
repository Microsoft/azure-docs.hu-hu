---
title: A HIPAA HITRUST 9.2 jogszabályi megfelelési adatai
description: A HIPAA HITRUST 9.2 szabályozási megfelelőség beépített kezdeményezésének részletei. Minden vezérlő egy vagy több olyan Azure Policy, amelyek az értékelést segítik.
ms.date: 04/21/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: b2843e569854947a83b93c0dde5529cc865721e2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864378"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>A HIPAA HITRUST 9.2 szabályozási megfelelőség beépített kezdeményezés részletei

A következő cikk részletesen bemutatja, hogy a Azure Policy-megfelelőség  beépített kezdeményezési definíciója hogyan van leképezve a HIPAA HITRUST 9.2 megfelelőségi tartományaira és vezérlőire. 
További információ erről a megfelelőségi szabványról: [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html). A _tulajdonjoggal kapcsolatos_ további Azure Policy [szabályzatdefiníciók és](../concepts/definition-structure.md#type) megosztott felelősségek a [felhőben való meghatározásáról.](../../../security/fundamentals/shared-responsibility.md)

Az alábbi leképezések a **HIPAA HITRUST 9.2 vezérlőire vannak leképezve.** A jobb oldalon található navigációval közvetlenül egy adott megfelelőségi **tartományra ugorhat.** Számos vezérlő egy új kezdeményezési [definícióval Azure Policy](../overview.md) meg. A kezdeményezés teljes definíciójának áttekintéshez nyissa meg a **szabályzatot** a Azure Portal majd válassza a **Definíciók** lapot.
Ezután keresse meg és válassza ki a **HITRUST/HIPAA** szabályozási megfelelőség beépített kezdeményezési definícióját.

Ez a beépített kezdeményezés a [HIPAA HITRUST 9.2](../../blueprints/samples/hipaa-hitrust-9-2.md)tervminta részeként van üzembe helyezni.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [definícióhoz Azure Policy](../overview.md) társítva.
> Ezek a szabályzatok segíthetnek felmérni [a](../how-to/get-compliance-data.md) vezérlőnek való megfelelőséget; A vezérlők és egy vagy több szabályzat között azonban gyakran nincs egy-az-egyhez vagy egy teljes egyezés. Ezért a **szabályzatban megfelelő** Azure Policy csak magukra a szabályzatdefiníciókra vonatkoznak; Ez nem biztosítja, hogy teljes mértékben megfelel a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyekre jelenleg egyetlen Azure Policy sem vonatkoznak. Ezért a megfelelőségi Azure Policy csak részlegesen látható a teljes megfelelőségi állapotról. A megfelelőségi tartományok, vezérlők és Azure Policy megfelelőségi szabvány definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json)

## <a name="privilege-management"></a>Jogosultságkezelés

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>A virtualizált rendszereket üzemeltető rendszerek felügyeleti funkcióihoz vagy felügyeleti konzoljához való hozzáférés a személyzetre korlátozódik a legkevesebb jogosultság elve alapján, és technikai vezérlőkkel támogatott.

**Azonosító:** 11180.01c3System.6 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjainak időben elérhető hálózati hozzáférés-vezérléssel kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A hálózati igény szerinti (JIT) hozzáféréseket az Azure Security Center figyeli |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>A jogosultságok hivatalosan engedélyezettek és szabályozhatók, használatra és eseményenként vannak lefoglalva a felhasználók számára a funkcionális szerepkörükhöz (például felhasználó vagy rendszergazda), és minden rendszertermékhez/elemhez dokumentálva vannak.

**Azonosító:** 1143.01c1System.123 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A felügyeleti portokat be kell zárni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |A nyitott távfelügyeleti portok magas szintű kockázatot jelentenek a virtuális gép számára az internetalapú támadásokkal szemben. Ezek a támadások a hitelesítő adatok találgatásos támadását kísérelik meg, hogy rendszergazdai hozzáférést szerezzenek a géphez. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>A szervezet kifejezetten engedélyezi a hozzáférést bizonyos biztonsági funkciókhoz (hardverben, szoftverben és belső vezérlőprogramban üzembe helyezett), valamint a biztonsággal kapcsolatos információkhoz.

**Azonosító:** 1144.01c1System.4 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Javasoljuk, hogy legfeljebb 3 előfizetés-tulajdonost jelöl ki, hogy csökkentse a feltört tulajdonossal való visszaélés veszélyét. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>A rendszer megvalósítja a szerepköralapú hozzáférés-vezérlést, és képes minden felhasználót egy vagy több szerepkörhöz, és minden szerepkört egy vagy több rendszerfunkcióhoz leképezni.

**Azonosító:** 1145.01c2System.1 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Javasoljuk, hogy a rendszergazdai hozzáférési redundancia érdekében több előfizetés-tulajdonost is kijelöljen. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>A szervezet elősegíti az olyan programok fejlesztését és használatát, amelyek nem kell emelt szintű jogosultságokkal és rendszer-rutinokkal futtatni őket, hogy ne kelljen jogosultságokat kiadni a felhasználóknak.

**Azonosító:** 1146.01c2System.23 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből, hogy megelőzhető legyen a nem szükséges hozzáférés. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>Az emelt szintű jogosultságok a normál üzleti használathoz használtaktól eltérő felhasználói azonosítóhoz vannak rendelve, minden felhasználó egyetlen szerepkörben fér hozzá az emelt szintű szolgáltatásokhoz, és az ilyen emelt szintű hozzáférés minimálisra csökken.

**Azonosító:** 1147.01c2System.456 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből.  Az elavult fiókok olyan fiókok, amelyek bejelentkezése le van tiltva. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>A szervezet korlátozza az emelt szintű funkciókhoz és a biztonsággal kapcsolatos összes információhoz való hozzáférést.

**Azonosító:** 1148.01c2System.78 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Egyéni RBAC-szabályok használatának naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Egyéni RBAC-szerepkörök helyett naplózhat olyan beépített szerepköröket, mint a "Tulajdonos, Közreműködés, Olvasó", amelyek hibaarányt jeleznek. Az egyéni szerepkörök használata kivételként van kezelve, és szigorú felülvizsgálatot és fenyegetésmodellezést igényel |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[A Windows rendszerű gépeknek meg kell felelnie a "Biztonsági beállítások – Fiókok" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |A Windows rendszerű gépeken a megadott Csoportházirend "Biztonsági beállítások – Fiókok" kategóriában kell lennie az üres jelszavak helyi fiókhasználatának és a vendégfiók állapotának korlátozására. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>A szervezet elősegíti az információmegosztást azáltal, hogy lehetővé teszi a jogosult felhasználók számára, hogy meghatározzák az üzleti partnerek hozzáférését, amikor a szervezet által meghatározott belátást engedélyeznek, és manuális folyamatok vagy automatizált mechanizmusok alkalmazásával segítik a felhasználókat az információmegosztással/együttműködéssel kapcsolatos döntések meghozatalában.

**Azonosító:** 1149.01c2System.9 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Kubernetes Servicesben szerepköralapú Access Control (RBAC) kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |A felhasználók által végrehajtható műveletek részletes szűréséhez az Role-Based Access Control (RBAC) használatával kezelheti az engedélyeket a Kubernetes Service-fürtökben, és konfigurálhatja a megfelelő engedélyezési házirendeket. |Naplózás, Letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>A lefedett adatokat tároló, feldolgozó vagy átvitelű rendszerösszetevők hozzáférés-vezérlési rendszere az alapértelmezett &quot; Deny-all beállítással &quot; van beállítva.

**Azonosító:** 1150.01c2System.10 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A felügyeleti portokat be kell zárni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |A nyitott távfelügyeleti portok magas szintű kockázatot jelentenek a virtuális gép számára az internetalapú támadásokkal szemben. Ezek a támadások a hitelesítő adatok találgatásos támadását kísérelik meg, hogy rendszergazdai hozzáférést szerezzenek a géphez. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>A szervezet a rendszer-jogosultságú fiókokra korlátozza az információs rendszerekben az engedélyezést a felhasználók egy előre meghatározott részcsoportja számára.

**Azonosító:** 1151.01c3System.1 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Ajánlott legfeljebb 3 előfizetés-tulajdonost kijelölni, hogy csökkentsük a feltört tulajdonossal való biztonsági incidensek veszélyét. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>A szervezet ellenőrzi az emelt szintű funkciók végrehajtását az információs rendszereken, és gondoskodik arról, hogy az információs rendszerek megakadályozzák a nem kiemelt jogosultságú felhasználók kiemelt funkciók végrehajtását.

**Azonosító:** 1152.01c3System.2 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Javasoljuk, hogy a rendszergazdai hozzáférési redundancia érdekében több előfizetés-tulajdonost is kijelöljen. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>Minden, explicit módon nem kötelező fájlrendszer-hozzáférés le van tiltva, és csak az arra jogosult felhasználók férhetnek hozzá, ami kifejezetten szükséges a felhasználók feladatainak ellátásához.

**Azonosító:** 1153.01c3System.35 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Kubernetes Servicesben szerepköralapú Access Control (RBAC) kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |A felhasználók által végrehajtható műveletek részletes szűréséhez az Role-Based Access Control (RBAC) használatával kezelheti az engedélyeket a Kubernetes Service-fürtökben, és konfigurálhatja a megfelelő engedélyezési házirendeket. |Naplózás, Letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>Az alvállalkozók csak akkor biztosítanak minimális rendszer- és fizikai hozzáférést, ha a szervezet felméri, hogy az alvállalkozó megfelel-e a biztonsági követelményeknek, és az alvállalkozó beleegyezik a megfelelésbe.

**AZONOSÍTÓ:** 1154.01c3System.4 – 01.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Ajánlott legfeljebb 3 előfizetés-tulajdonost kijelölni, hogy csökkentsük a feltört tulajdonossal való biztonsági incidensek veszélyét. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>Felhasználóhitelesítés külső kapcsolatokhoz

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>Az erős hitelesítési módszerek, például a többtényezős hitelesítés, a Radius vagy a Kerberos (emelt szintű hozzáféréshez) és a CHAP (a telefonos módszerek hitelesítő adatainak titkosításához) a szervezet hálózatához való összes külső kapcsolatra alkalmazva vannak.

**Azonosító:** 1116.01j1Szervezeti.145 – 01.j **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés tulajdonosi engedélyekkel rendelkező fiókjaiban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A többtényezős hitelesítést (MFA) minden tulajdonosi engedéllyel rendelkező előfizetési fiókhoz engedélyezni kell, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>A szállítók és üzleti partnerek (például távoli karbantartás esetén) le vannak tiltva/inaktiválva, ha nincs használatban.

**Id**: 1117.01j1Organizational.23 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes írási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>A szervezetek titkosítást valósítanak meg (például VPN-megoldásokat vagy privát vonalakat), és az alkalmazottak, alvállalkozók vagy külső felek (például szállítók) távoli hozzáférését naplózza a szervezet &nbsp; hálózatához.

**AZONOSÍTÓ:** 1118.01j2Szervezeti.124 – 01.j **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes olvasási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>A rendszer ellenőrzi, hogy a hálózati berendezések nem rendelkeznek-e nem várt feltárcsázási képességekkel.

**AZONOSÍTÓ:** 1119.01j2Szervezeti.3 – 01.j **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjainak időben elérhető hálózati hozzáférés-vezérléssel kell védeni őket](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehetséges igény szerinti hálózati (JIT)-hozzáférést a Azure Security Center figyeli javaslatként |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>A távfelügyeleti munkamenetek engedélyezettek, titkosítva vannak, és fokozott biztonsági intézkedéseket alkalmaznak.

**Id**: 1121.01j3Organizational.2 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés tulajdonosi engedélyekkel rendelkező fiókjaiban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A többtényezős hitelesítést (MFA) minden tulajdonosi engedéllyel rendelkező előfizetési fiókhoz engedélyezni kell, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>Ha nem használ titkosítást a telefonos kapcsolatokhoz, az CIO vagy a kijelölt képviselő konkrét írásos engedélyt biztosít.

**Id**: 1173.01j1Organizational.6 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes írási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>A szervezet a felhasználók és az eszközök hitelesítésével védi a bizalmas adatokat tartalmazó rendszerek vezeték nélküli hozzáférését.

**AZONOSÍTÓ:** 1174.01j1Szervezeti.7 – 01.j **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes olvasási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>Az üzleti információk nyilvános hálózatokon keresztüli távoli elérése csak a sikeres azonosítás és hitelesítés után történik meg.

**AZONOSÍTÓ:** 1175.01j1Szervezeti.8 – 01.j **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjainak időben elérhető hálózati hozzáférés-vezérléssel kell védeni őket](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehetséges igény szerinti hálózati (JIT)-hozzáférést a Azure Security Center figyeli javaslatként |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>A szervezetnek újrahitelesítéssel rendelkező visszahívási képességre van szüksége az engedélyezett helyekről származó telefonos kapcsolatok ellenőrzéséhez.

**AZONOSÍTÓ:** 1176.01j2Szervezeti.5 – 01.j **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés tulajdonosi engedélyekkel rendelkező fiókjaiban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A többtényezős hitelesítést (MFA) minden tulajdonosi engedéllyel rendelkező előfizetési fiókhoz engedélyezni kell, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>A beszállítókhoz rendelt felhasználói azonosítókat a szervezet hozzáférési felülvizsgálati szabályzatának megfelelően, legalább évente felülvizsgálják.

**Id**: 1177.01j2Organizational.6 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes írási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>A csomópontok hitelesítése, beleértve a titkosítási technikákat (például gépi tanúsítványokat) a távoli felhasználók csoportjainak hitelesítésének alternatív módszereiként szolgál, ahol biztonságos, megosztott számítógép-létesítményhez csatlakoznak.

**Id**: 1178.01j2Organizational.7 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes olvasási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>Az információs rendszer figyeli és szabályozza a távelérési módszereket.

**AZONOSÍTÓ:** 1179.01j3Szervezeti.1 – 01.j **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjainak időben elérhető hálózati hozzáférés-vezérléssel kell védeni őket](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehetséges igény szerinti hálózati (JIT)-hozzáférést a Azure Security Center figyeli javaslatként |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Távoli diagnosztikai és konfigurációs portok védelme

### <a name="access-to-network-equipment-is-physically-protected"></a>A hálózati berendezésekhez való hozzáférés fizikailag védett.

**AZONOSÍTÓ:** 1192.01l1Szervezeti.1 – 01.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjainak egy időben elérhető hálózati hozzáférés-vezérléssel kell védettnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehetséges igény szerinti hálózati (JIT)-hozzáférést a Azure Security Center figyeli javaslatként |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>A diagnosztikai és konfigurációs portokhoz való hozzáférés vezérlői közé tartozik a kulcszárolás használata és a porthoz való fizikai hozzáférést vezérlő támogató eljárások megvalósítása.

**Id**: 1193.01l2Organizational.13 - 01.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A felügyeleti portokat be kell zárni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |A nyitott távfelügyeleti portok magas szintű kockázatot jelentenek a virtuális gép számára az internetalapú támadásokkal szemben. Ezek a támadások a hitelesítő adatok találgatásos támadását kísérelik meg, hogy rendszergazdai hozzáférést szerezzenek a géphez. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>A számítógépekre vagy hálózati rendszerekre telepített olyan portok, szolgáltatások és hasonló alkalmazások, amelyek nem szükségesek kifejezetten az üzleti funkciókhoz, le vannak tiltva vagy el vannak távolítva.

**Id**: 1194.01l2Organizational.2 - 01.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A webalkalmazások esetében a távoli hibakeresést ki kell kapcsolva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |A távoli hibakereséshez meg kell nyitni a bejövő portokat egy webalkalmazáson. A távoli hibakeresést ki kell kapcsolva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>A szervezet három száz és hatvenöt (365) napon belül felülvizsgálja az információs rendszert, hogy azonosítsa és letiltsa a szükségtelen és nem biztonságos funkciókat, portokat, protokollokat és/vagy szolgáltatásokat.

**AZONOSÍTÓ:** 1195.01l3Szervezeti.1 – 01.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A függvényalkalmazások esetében a távoli hibakeresést ki kell kapcsolva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A távoli hibakereséshez meg kell nyitni a bejövő portokat a függvényalkalmazásokban. A távoli hibakeresést ki kell kapcsolva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>A szervezet azonosítja az információs rendszeren található jogosulatlan (tiltólistán található) szoftvereket, megakadályozza a programok végrehajtását a nem engedélyezett (tiltólistán) szoftverprogramok listájának megfelelően, engedélyezési és tiltó kivételi szabályzatot alkalmaz az ismert jogosulatlan (tiltólistán) szoftverek végrehajtásának letiltása érdekében, és évente felülvizsgálja és frissíti a jogosulatlan (feketelistán) szoftverprogramok listáját.

**AZONOSÍTÓ:** 1196.01l3Szervezeti.24 – 01.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A távoli hibakeresést ki kell kapcsolva a API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |A távoli hibakereséshez meg kell nyitni a bejövő portokat az API-alkalmazásokban. A távoli hibakeresést ki kell kapcsolva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>A szervezet letiltja a Bluetooth és a társközi hálózati protokollokat az információs rendszeren belül, amelyek szükségtelennek vagy nem biztonságosnak vannak meghatározva.

**Azonosító:** 1197.01l3Szervezeti.3 – 01.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A biztonságos alkalmazások meghatározásához szükséges adaptív alkalmazásvezérlőket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazásvezérlők engedélyezésével meghatározhatja a gépeken futó, ismerten biztonságos alkalmazások listáját, és riasztást küld, ha más alkalmazások is futnak. Ez segít a gépek kártevők elleni védelemben való segítében. A szabályok konfigurálási és karbantartásának egyszerűsítése érdekében a Security Center gépi tanulással elemzi az egyes gépeken futó alkalmazásokat, és javaslatot ad az ismerten biztonságos alkalmazások listájára. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>Elkülönítés a hálózatokban

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>A szervezet biztonsági átjárói (például tűzfalak) biztonsági házirendeket kényszerítnek ki, és úgy vannak konfigurálva, hogy szűrik a tartományok közötti forgalmat, blokkolják a jogosulatlan hozzáférést, és a belső vezetékes, belső vezeték nélküli és külső hálózati szegmensek (például az internet) közötti elkülönítés fenntartására használatosak, beleértve a DMZ-ket is, és minden tartományra érvényes hozzáférés-vezérlési házirendeket kényszerítsen ki.

**Azonosító:** 0805.01m1Szervezeti.12 – 01.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ez a szabályzat napló App Service virtuális hálózati szolgáltatásvégpont használatára konfigurált összes olyan vizsgálatot, amely nincs konfigurálva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry virtuális hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a szabályzat naplót Container Registry virtuális hálózati szolgáltatásvégpont használatára konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ez a szabályzat naplót Cosmos DB virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Az eseményközpontnak virtuális hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ez a szabályzat naplót naplót ad minden olyan eseményközpontról, amely nincs virtuális hálózati szolgáltatásvégpont használatára konfigurálva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Az átjáró-alhálózatokat nem szabad hálózati biztonsági csoporttal konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Ez a szabályzat megtagadja, ha egy átjáró-alhálózat hálózati biztonsági csoporttal van konfigurálva. Ha egy hálózati biztonsági csoportot rendel egy átjáró-alhálózathoz, az átjáró működése leáll. |Tagadja |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Az internetre néző virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG-k) való hozzáférés korlátozásával megvédheti virtuális gépeit a lehetséges fenyegetésektől. További információ az NSG-k forgalmának szabályozásával kapcsolatban: [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ez a szabályzat naplót Key Vault virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ez a szabályzat naplót Service Bus virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ez a szabályzat naplót SQL Server virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[A tárfiókok virtuális hálózati szolgáltatásvégpontot használjanak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ez a szabályzat naplót végez minden olyan storage-fiókot, amely nincs virtuális hálózati szolgáltatásvégpont használatára konfigurálva. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Az alhálózatokat egy hálózati biztonsági csoporthoz kell hozzárendelni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Egy hálózati biztonsági csoporttal (NSG) korlátozhatja az alhálózat védelmét a lehetséges fenyegetésekkel szemben. Az NSG-k az alhálózatra Access Control (ACL) vonatkozó szabályok listáját tartalmazzák, amelyek engedélyezik vagy megtagadják az alhálózatra való hálózati forgalmat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a szabályzat minden olyan virtuális gépet naplól, amely nem jóváhagyott virtuális hálózathoz csatlakozik. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>A szervezeti hálózat logikailag és fizikailag szegmentálva van egy meghatározott biztonsági szegélyhálózattal és egy fokozatos vezérlőkészlettel, beleértve a nyilvánosan elérhető, a belső hálózattól logikailag elkülönített rendszerösszetevők alhálózatát a szervezeti követelmények alapján; A és a forgalom a szükséges funkciók és az adatok/rendszerek besorolása alapján van szabályozva a kockázatértékelés és a vonatkozó biztonsági követelmények alapján.

**Azonosító:** 0806.01m2Szervezeti.12356 – 01.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ez a szabályzat naplót App Service virtuális hálózati szolgáltatásvégpont használatára konfigurált összes olyan vizsgálatot, amely nincs konfigurálva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a szabályzat naplót Container Registry virtuális hálózati szolgáltatásvégpont használatára konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ez a szabályzat naplót Cosmos DB virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Az eseményközpontnak virtuális hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ez a szabályzat naplót naplót ad minden olyan eseményközpontról, amely nincs virtuális hálózati szolgáltatásvégpont használatára konfigurálva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Az átjáró-alhálózatokat nem szabad hálózati biztonsági csoporttal konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Ez a szabályzat megtagadja, ha egy átjáró-alhálózat hálózati biztonsági csoporttal van konfigurálva. Ha egy hálózati biztonsági csoportot rendel egy átjáró-alhálózathoz, az átjáró működése leáll. |Tagadja |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Az internetre néző virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG-k) való hozzáférés korlátozásával megvédheti virtuális gépeit a lehetséges fenyegetésektől. További információ az NSG-k forgalmának szabályozásával kapcsolatban: [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault virtuális hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ez a szabályzat napló Key Vault virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ez a szabályzat naplót Service Bus virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ez a szabályzat naplót SQL Server virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[A tárfiókok virtuális hálózati szolgáltatásvégpontot használjanak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ez a szabályzat naplót végez minden olyan storage-fiókot, amely nincs virtuális hálózati szolgáltatásvégpont használatára konfigurálva. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Az alhálózatokat egy hálózati biztonsági csoporthoz kell hozzárendelni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Egy hálózati biztonsági csoporttal (NSG) korlátozhatja az alhálózat védelmét a lehetséges fenyegetésekkel szemben. Az NSG-k az alhálózatra Access Control (ACL) vonatkozó szabályok listáját tartalmazzák, amelyek engedélyezik vagy megtagadják az alhálózatra való hálózati forgalmat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a szabályzat minden olyan virtuális gépet naplól, amely nem jóváhagyott virtuális hálózathoz csatlakozik. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>A fizikai kiszolgálók, alkalmazások vagy adatok virtualizált kiszolgálókra való áttelepítésekor a hálózatok elkülönülnek az éles szintű hálózatoktól.

**Azonosító:** 0894.01m2Szervezeti.7 – 01.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ez a szabályzat naplót App Service virtuális hálózati szolgáltatásvégpont használatára konfigurált összes olyan vizsgálatot, amely nincs konfigurálva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a szabályzat naplót Container Registry virtuális hálózati szolgáltatásvégpont használatára konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ez a szabályzat naplót Cosmos DB virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Network Watcher üzembe helyezése virtuális hálózatok létrehozásakor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |Ez a szabályzat létrehoz egy Network Watcher-erőforrást a virtuális hálózatokkal kapcsolatos régiókban. Meg kell győződni arról, hogy létezik egy networkWatcherRG nevű erőforráscsoport, amely a Network Watcher-példányok üzembe helyezésére lesz használva. |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[Az eseményközpontnak virtuális hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ez a szabályzat naplót naplól minden olyan eseményközpontot, amely nincs virtuális hálózati szolgáltatásvégpont használatára konfigurálva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Az átjáró-alhálózatokat nem szabad hálózati biztonsági csoporttal konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Ez a szabályzat megtagadja, ha egy átjáró-alhálózat hálózati biztonsági csoporttal van konfigurálva. Ha hálózati biztonsági csoportot rendel egy átjáró-alhálózathoz, az átjáró működése leáll. |Tagadja |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Az internetre néző virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG-k) való hozzáférés korlátozásával megvédheti virtuális gépeit a lehetséges fenyegetésektől. További információ az NSG-k forgalmának szabályozásával kapcsolatban: [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault virtuális hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ez a szabályzat napló Key Vault virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ez a szabályzat naplót Service Bus virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ez a szabályzat naplót SQL Server virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[A tárfiókok virtuális hálózati szolgáltatásvégpontot használjanak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ez a szabályzat naplóba veszi a virtuális hálózati szolgáltatásvégpont használatára nem konfigurált tárfiókokat. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Az alhálózatokat egy hálózati biztonsági csoporthoz kell hozzárendelni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Egy hálózati biztonsági csoporttal (NSG) korlátozhatja az alhálózat védelmét a lehetséges fenyegetésekkel szemben. Az NSG-k az alhálózatra Access Control hálózati forgalmat engedélyező vagy megtagadó ACL-szabályok listáját tartalmazzák. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a szabályzat minden olyan virtuális gépet naplól, amely nem jóváhagyott virtuális hálózathoz csatlakozik. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>Hálózati kapcsolat vezérlése

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>A hálózati forgalom szabályozása a szervezet hozzáférés-vezérlési szabályzatának megfelelően történik az egyes hálózati hozzáférési pontokkal vagy külső távközlési szolgáltatás felügyelt adapterével kapcsolatos tűzfallal és egyéb, hálózattal kapcsolatos korlátozásokkal.

**Azonosító:** 0809.01n2Szervezeti.1234 – 01.n **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptív hálózatkorrekozási javaslatokat kell alkalmazni az internetre néző virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és javaslatokat tesz a hálózati biztonsági csoport szabályára vonatkozóan, amelyek csökkentik a potenciális támadási felületet |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Az API-alkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Engedélyezni kell az SSL-kapcsolat kényszerítését a MySQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése segít a "középen" támadásokkal szembeni védelemben a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Az SSL-kapcsolat kényszerítését engedélyezni kell a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése segít a "középen" támadásokkal szembeni védelemben a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[A függvényalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Az internetkapcsolattal elérhető virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG-k) való hozzáférés korlátozásával megvédheti virtuális gépeit a lehetséges fenyegetésektől. További információ az NSG-k forgalmának szabályozásával kapcsolatban: [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Az API-alkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A függvényalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A webalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak SSL-en keresztüli kapcsolatok engedélyezésének naplózása Azure Cache for Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Naplózási követelmény a biztonságos átvitelhez a tárfiókban. A biztonságos átvitel egy olyan lehetőség, amely arra kényszeríti a tárfiókot, hogy csak biztonságos kapcsolatokról (HTTPS) fogadjon kéréseket. A HTTPS használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Az alhálózatokat egy hálózati biztonsági csoporthoz kell hozzárendelni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Egy hálózati biztonsági csoporttal (NSG) korlátozhatja az alhálózat védelmét a lehetséges fenyegetésekkel szemben. Az NSG-k az alhálózatra Access Control (ACL) vonatkozó szabályok listáját tartalmazzák, amelyek engedélyezik vagy megtagadják az alhálózatra való hálózati forgalmat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a szabályzat minden olyan virtuális gépet naplól, amely nem jóváhagyott virtuális hálózathoz csatlakozik. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A webalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>Az átvitt adatok biztonságosak, és legalább nyílt, nyilvános hálózatokon titkosítva vannak.

**Azonosító:** 0810.01n2Szervezeti.5 – 01.n **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptív hálózatkorrekozási javaslatokat kell alkalmazni az internetre néző virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és javaslatokat tesz a hálózati biztonsági csoport szabályára vonatkozóan, amelyek csökkentik a potenciális támadási felületet |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Az API-alkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Engedélyezni kell az SSL-kapcsolat kényszerítését a MySQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középen" támadásokkal szembeni védelemben. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Az SSL-kapcsolat kényszerítését engedélyezni kell a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középen" támadásokkal szembeni védelemben. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[A függvényalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Az internetkapcsolattal elérhető virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG-k) való hozzáférés korlátozásával megvédheti virtuális gépeit a lehetséges fenyegetésektől. További információ az NSG-k forgalmának szabályozásával kapcsolatban: [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Az API-alkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A függvényalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A webalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Csak a saját Azure Cache for Redis kell engedélyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak SSL-kapcsolaton keresztüli kapcsolatok engedélyezésének naplózása Azure Cache for Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Naplózási követelmény a biztonságos átvitelhez a tárfiókban. A biztonságos átvitel egy olyan lehetőség, amely arra kényszeríti a tárfiókot, hogy csak biztonságos kapcsolatokról (HTTPS) fogadjon kéréseket. A HTTPS használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Az alhálózatokat egy hálózati biztonsági csoporthoz kell hozzárendelni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Hálózati biztonsági csoport (NSG) korlátozza az alhálózat védelmét a lehetséges fenyegetésekkel szemben. Az NSG-k az alhálózatra Access Control (ACL) vonatkozó szabályok listáját tartalmazzák, amelyek engedélyezik vagy megtagadják az alhálózatra való hálózati forgalmat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a szabályzat minden olyan virtuális gépet naplól, amely nem jóváhagyott virtuális hálózathoz csatlakozik. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A webalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>A forgalmi szabályzat alóli kivételek dokumentálva vannak egy támogató küldetési/üzleti okkal, a kivétel időtartamával, és legalább évente felülvizsgálva vannak; A forgalomirányítási szabályzat kivételei el vannak távolítva, ha már nem támogatja egy explicit küldetés/üzleti szükség.

**AZONOSÍTÓ:** 0811.01n2Szervezeti.6 – 01.n **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptív hálózatkorrekozási javaslatokat kell alkalmazni az internetre néző virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és javaslatokat tesz a hálózati biztonsági csoport szabályára vonatkozóan, amelyek csökkentik a potenciális támadási felületet |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Az API-alkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Engedélyezni kell az SSL-kapcsolat kényszerítését a MySQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése segít a "középen" támadásokkal szembeni védelemben a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Az SSL-kapcsolat kényszerítését engedélyezni kell a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése segít a "középen" támadásokkal szembeni védelemben a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[A függvényalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Az internetre néző virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG-k) való hozzáférés korlátozásával megvédheti virtuális gépeit a lehetséges fenyegetésektől. További információ az NSG-k forgalmának szabályozásával kapcsolatban: [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Az API-alkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A függvényalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A webalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak SSL-en keresztüli kapcsolatok engedélyezésének naplózása Azure Cache for Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Naplózási követelmény a biztonságos átvitelhez a tárfiókban. A biztonságos átvitel egy olyan lehetőség, amely arra kényszeríti a tárfiókot, hogy csak biztonságos kapcsolatokról (HTTPS) fogadjon kéréseket. A HTTPS használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a man-in-the-middle, a lehallgatás és a munkamenet-eltérítés |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Az alhálózatokat egy hálózati biztonsági csoporthoz kell hozzárendelni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Egy hálózati biztonsági csoporttal (NSG) korlátozhatja az alhálózat védelmét a lehetséges fenyegetésekkel szemben. Az NSG-k az alhálózatra Access Control (ACL) vonatkozó szabályok listáját tartalmazzák, amelyek engedélyezik vagy megtagadják az alhálózatra való hálózati forgalmat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a szabályzat minden olyan virtuális gépet naplól, amely nem jóváhagyott virtuális hálózathoz csatlakozik. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A webalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>A nem távoli kapcsolatot létesítő távoli eszközök nem kommunikálhatnak külső (távoli) erőforrásokkal.

**Azonosító:** 0812.01n2Szervezeti.8 – 01.n **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptív hálózatkorrekozási javaslatokat kell alkalmazni az internetre néző virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és javaslatokat tesz a hálózati biztonsági csoport szabályára vonatkozóan, amelyek csökkentik a potenciális támadási felületet |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Az API-alkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Engedélyezni kell az SSL-kapcsolat kényszerítését a MySQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középen" támadásokkal szembeni védelemben. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Az SSL-kapcsolat kényszerítését engedélyezni kell a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középen" támadások elleni védelemben. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[A függvényalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Az internetkapcsolattal elérhető virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG-k) való hozzáférés korlátozásával megvédheti virtuális gépeit a lehetséges fenyegetésektől. További információ az NSG-k forgalmának szabályozásával kapcsolatban: [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Az API-alkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A függvényalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A webalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Csak a saját Azure Cache for Redis kell engedélyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak SSL-kapcsolaton keresztüli kapcsolatok engedélyezésének naplózása Azure Cache for Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Naplózási követelmény a biztonságos átvitelhez a tárfiókban. A biztonságos átvitel egy olyan lehetőség, amely arra kényszeríti a tárfiókot, hogy csak biztonságos kapcsolatokról (HTTPS) fogadjon kéréseket. A HTTPS használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Az alhálózatokat egy hálózati biztonsági csoporthoz kell hozzárendelni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Egy hálózati biztonsági csoporttal (NSG) korlátozhatja az alhálózat védelmét a lehetséges fenyegetésekkel szemben. Az NSG-k az alhálózatra Access Control (ACL) vonatkozó szabályok listáját tartalmazzák, amelyek engedélyezik vagy megtagadják az alhálózatra való hálózati forgalmat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a szabályzat minden olyan virtuális gépet naplól, amely nem jóváhagyott virtuális hálózathoz csatlakozik. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A webalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>A felhasználók a hozzáférés-vezérlési szabályzatnak, valamint a klinikai és üzleti alkalmazások követelményeinek megfelelően korlátozva vannak a felhasználók számára a belső hálózathoz való csatlakozásra vonatkozó alapértelmezett és kivételi tiltási szabályzattal a felügyelt felületeken.

**Azonosító:** 0814.01n1Szervezeti.12 – 01.n **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptív hálózatkorrekozási javaslatokat kell alkalmazni az internetre néző virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és javaslatokat tesz a hálózati biztonsági csoport szabályára vonatkozóan, amelyek csökkentik a potenciális támadási felületet |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Az API-alkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Engedélyezni kell az SSL-kapcsolat kényszerítését a MySQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése segít a "középen" támadásokkal szembeni védelemben a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Az SSL-kapcsolat kényszerítését engedélyezni kell a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése segít a "középen" támadásokkal szembeni védelemben a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[A függvényalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Az internetre néző virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG-k) való hozzáférés korlátozásával megvédheti virtuális gépeit a lehetséges fenyegetésektől. További információ az NSG-k forgalmának szabályozásával kapcsolatban: [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Az API-alkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A függvényalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A webalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak SSL-en keresztüli kapcsolatok engedélyezésének naplózása Azure Cache for Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Naplózási követelmény a biztonságos átvitelhez a tárfiókban. A biztonságos átvitel egy olyan lehetőség, amely arra kényszeríti a tárfiókot, hogy csak biztonságos kapcsolatokról (HTTPS) fogadjon kéréseket. A HTTPS használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a man-in-the-middle, a lehallgatás és a munkamenet-eltérítés |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Az alhálózatokat egy hálózati biztonsági csoporthoz kell hozzárendelni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Hálózati biztonsági csoport (NSG) korlátozza az alhálózat védelmét a lehetséges fenyegetésekkel szemben. Az NSG-k az alhálózatra Access Control (ACL) vonatkozó szabályok listáját tartalmazzák, amelyek engedélyezik vagy megtagadják az alhálózatra való hálózati forgalmat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ez a szabályzat minden olyan virtuális gépet naplól, amely nem jóváhagyott virtuális hálózathoz csatlakozik. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A webalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>Felhasználóazonosítás és -hitelesítés

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>A szervezet gondoskodik arról, hogy a redundáns felhasználói azonosítók ne legyen kibocsátva más felhasználók számára, és hogy minden felhasználó egyedileg legyen azonosítva és hitelesítve legyen az információs rendszerek helyi és távoli elérése érdekében.

**Id**: 11109.01q1Organizational.57 - 01.q **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés tulajdonosi engedélyekkel rendelkező fiókjaiban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A többtényezős hitelesítést (MFA) minden tulajdonosi engedéllyel rendelkező előfizetési fiókhoz engedélyezni kell, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>A nem szervezeti felhasználók (a szervezeti felhasználóktól különböző információsrendszer-felhasználók, például páciensek, ügyfelek, alvállalkozók vagy külső felhasználók) vagy a nem szervezeti felhasználók nevében működő folyamatokat, amelyek úgy vannak meghatározva, hogy hozzáférésre van szükségük a szervezet információs rendszereiben található információkhoz, egyedileg azonosíthatók és hitelesíthetők.

**Id**: 11110.01q1Organizational.6 - 01.q **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes írási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>PKI-alapú hitelesítés használata esetén az információs rendszer úgy ellenőrzi a tanúsítványokat, hogy egy elfogadott megbízhatósági horgonyhoz vezető tanúsítványútvonalat hoz létre és ellenőriz, beleértve a tanúsítvány állapotára vonatkozó információk ellenőrzését; a megfelelő titkos kulcshoz való hozzáférést kényszeríti ki; leképezi az identitást az adott személy vagy csoport megfelelő fiókjára; A és implementálja a visszavonási adatok helyi gyorsítótárát az útvonalfelderítés és -ellenőrzés támogatására arra az esetre, ha nem lehet hozzáférni a visszavonási információkhoz a hálózaton keresztül.

**Azonosító:** 11111.01q2System.4 – 01.q **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes olvasási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>Az információs rendszer olyan visszajátszás ellen védett hitelesítési mechanizmusokat alkalmaz, mint az nonce, az egyszeres jelszavak vagy az időbélyegek a rendszer-jogosultságú fiókok hálózati hozzáférésének védelme érdekében; A hardvertoken-alapú hitelesítés esetén pedig olyan mechanizmusokat alkalmaz, amelyek megfelelnek az NIST SP 800-63-2 elektronikus hitelesítési útmutatóban tárgyalt minimális jogkivonat-követelményeknek.

**Azonosító:** 11112.01q2Organizational.67 - 01.q **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Javasoljuk, hogy legfeljebb 3 előfizetés-tulajdonost jelöl ki, hogy csökkentse a feltört tulajdonossal való visszaélés veszélyét. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>A szervezet megköveteli, hogy az egy személy által egyedi elektronikus aláírásokat senki más ne használja fel és ne lehessen másokhoz hozzárendelni.

**Azonosító:** 11208.01q1Szervezeti.8 – 01.q **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Javasoljuk, hogy a rendszergazdai hozzáférési redundancia érdekében több előfizetés-tulajdonost is kijelölhet. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>Az elektronikus rekordokra végrehajtott elektronikus aláírásokat és kézzel írt aláírásokat a megfelelő elektronikus rekordokhoz kell kapcsolni.

**Id**: 11210.01q2Organizational.10 - 01.q **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Rendszergazdák csoportban megadott tagokkal megjelölt Windows rendszerű gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport a szabályzatparaméterben felsorolt egy vagy több tagot tartalmaz. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>Az aláírt elektronikus rekordoknak az aláírással kapcsolatos, emberi olvasásra használható adatokat kell tartalmazni.

**AZONOSÍTÓ:** 11211.01q2Szervezeti.11 – 01.q **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Rendszergazdák csoportban megadott tagok bármelyike hiányzik a Windows rendszerű gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Előfeltételként a szabályzat-hozzárendelési hatókörben kell telepíteni az előfeltételeket. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport nem tartalmaz egy vagy több olyan tagot, amely szerepel a szabályzatparaméterben. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>Az emelt szintű funkciókat (például a rendszerfelügyeletet) végző felhasználók külön fiókokat használnak ezen kiemelt funkciók végrehajtásához.

**AZONOSÍTÓ:** 1123.01q1System.2 – 01.q **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Rendszergazdák csoportban további fiókkal rendelkező Windows rendszerű gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Előfeltételként a szabályzat-hozzárendelési hatókörben kell telepíteni az előfeltételeket. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport olyan tagokat tartalmaz, amelyek nem szerepelnek a szabályzatparaméterben. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>A többtényezős hitelesítési módszerek a szervezeti házirendnek megfelelően vannak használva (például távoli hálózati hozzáféréshez).

**Azonosító:** 1125.01q2System.1 – 01.q **tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Rendszergazdák csoportban megadott tagokkal megjelölt Windows rendszerű gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport a szabályzatparaméterben felsorolt egy vagy több tagot tartalmaz. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>A többtényezős hitelesítéshez megadott jogkivonatok esetén a hozzáférés megadása előtt a személyes ellenőrzés szükséges.

**Azonosító:** 1127.01q2System.3 – 01.q **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows rendszerű gépek naplózása, amelyeken hiányzik a Rendszergazdák csoportban megadott tagok bármelyike](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport nem tartalmaz a szabályzatparaméterben felsorolt egy vagy több tagot. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>Külső felekkel kapcsolatos kockázatok azonosítása

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>A szervezeti információkhoz és rendszerekhez külső felek csak akkor férhetnek hozzá, ha megfelelő gondossággal hajtják végre, meg nem valósítják a megfelelő szabályozásokat, és aláírnak egy szerződést/szerződést, amely megfelel a biztonsági követelményeknek, és elfogadja a kötelezettségeket.

**Azonosító:** 1401.05i1Szervezeti.1239 – 05.i **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Naplózási követelmény a biztonságos átvitelhez a tárfiókban. A biztonságos átvitel egy olyan lehetőség, amely arra kényszeríti a tárfiókot, hogy csak biztonságos kapcsolatokról (HTTPS) fogadjon kéréseket. A HTTPS használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a man-in-the-middle, a lehallgatás és a munkamenet-eltérítés |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>A szervezet és a külső felek közötti távelérési kapcsolatok titkosítottak.

**AZONOSÍTÓ:** 1402.05i1Szervezeti.45 – 05.i **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A függvényalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>A külső feleknek biztosított hozzáférés a minimálisan szükséges, és csak a szükséges időtartamra adható meg.

**Azonosító:** 1403.05i1Szervezeti.67 – 05.i **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A webalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>A külső fél kellő gondosságával magában foglalnak állásinterjúkat, dokumentum-felülvizsgálatokat, ellenőrzőlistát, minősítési felülvizsgálatokat (pl. HITRUST) vagy más távoli &nbsp; eszközöket.

**Id**: 1404.05i2Organizational.1 - 05.i **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az API-alkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>A külső hozzáféréssel kapcsolatos kockázatok azonosítása figyelembe veszi a meghatározott problémák minimális készletét.

**Azonosító:** 1418.05i1Szervezeti.8 – 05.i **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell az SSL-kapcsolat kényszerítését a MySQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középen" támadásokkal szembeni védelemben. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>A szervezet éves felülvizsgálatot végez, amely magában foglalja az összes olyan partnert/külső szolgáltatót, amelytől az információbeszállítói lánc függ, és amely biztosítja az ésszerű információbiztonságot az információbeszállító láncban.

**Azonosító:** 1450.05i2Szervezeti.2 – 05.i **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az SSL-kapcsolat kényszerítését engedélyezni kell a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középen" támadásokkal szembeni védelemben. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>A felhőszolgáltatók olyan vezérlőket terveznek és valósítanak meg, amelyek mérsékelik és tartalmazzák az adatbiztonsági kockázatokat a feladatok, a szerepköralapú hozzáférés és a legkevesebb jogosultsággal rendelkező hozzáférés megfelelő elkülönítésével az ellátási láncban dolgozó összes személy számára.

**AZONOSÍTÓ:** 1451.05iCSPSzervezeti.2 – 05.i **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Csak a saját Azure Cache for Redis kell engedélyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak SSL-kapcsolaton keresztüli kapcsolatok engedélyezésének naplózása Azure Cache for Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>Naplózás naplózása

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>Létrejön egy biztonságos naplórekord a rendszeren végzett összes tevékenységhez (létrehozás, olvasás, frissítés, törlés), beleértve a lefedett adatokat is.

**Azonosító:** 1202.09aa1System.1 – 09.aa **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell az Azure Data Lake Store-erőforrásnaplókat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli, vizsgálati célokra való használatát; ha biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[A virtuálisgép-méretezési készletek rendszerfrissítéseit telepíteni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Naplózhatja, hogy vannak-e hiányzó rendszerbiztonsági frissítések és kritikus frissítések, amelyek telepítése szükséges a Windows és Linux rendszerű virtuálisgép-méretezési készletek biztonságának biztosításához. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>A naplórekordok közé tartozik az egyedi felhasználói azonosító, az adat tulajdonosának egyedi azonosítója, a végrehajtott függvény és az esemény dátuma/időpontja.

**Azonosító:** 1203.09aa1System.2 – 09.aa **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a Logic Apps erőforrásnaplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>A kiemelt jogosultságú felhasználók (rendszergazdák, operátorok stb.) tevékenységei közé tartozik az esemény sikeres/sikertelen volta, az esemény beeseményének ideje, az érintett fiók, az érintett folyamatok és az eseményre vonatkozó további információk.

**Azonosító:** 1204.09aa1System.3 – 09.aa **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a IoT Hub naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>Az elküldött és fogadott üzenetek naplói megmaradnak, beleértve az üzenet dátumát, időpontját, eredetét és célját, de a tartalmát nem.

**Azonosító:** 1205.09aa2System.1 – 09.aa **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell az erőforrásnaplókat a Batch-fiókokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli, vizsgálati célokra való használatát; ha biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>A naplózás mindig elérhető, miközben a rendszer aktív, és nyomon követi a kulcsfontosságú eseményeket, a sikeres/sikertelen adatelérést, a rendszerbiztonsági konfiguráció változásait, az emelt szintű vagy a segédprogramhasználatot, a riasztásokat, a védelmi rendszerek aktiválását és aktiválásának törlését (például A/V és IDS), az azonosítási és hitelesítési mechanizmusok aktiválását és deaktiválását, valamint a rendszerszintű objektumok létrehozását és &nbsp; törlését.

**Azonosító:** 1206.09aa2System.23 – 09.aa **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a Virtual Machine Scale Sets naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Javasoljuk, hogy engedélyezze a naplókat, hogy a tevékenységnapló újra létre legyen hozva, amikor vizsgálatokra van szükség incidens vagy biztonsági incidens esetén. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>A rendszer 90 napig őrzi meg a naplórekordokat, a régebbi naplórekordokat pedig egy évig archiválja.

**Azonosító:** 1207.09aa2System.4 – 09.aa **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a Azure Stream Analytics erőforrásnaplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell az erőforrásnaplókat az eseményközpontban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>Az auditnaplók a felügyeleti tevékenységekhez, a rendszer- és alkalmazásindításhoz/leállításhoz/hibákhoz, a fájlváltozáshoz és a biztonsági házirend módosításaihoz vannak karbantartva.

**Azonosító:** 1208.09aa3System.1 – 09.aa **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell az erőforrásnaplókat a Keresési szolgáltatásokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell a Service Bus naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli, vizsgálati célokra való használatát; ha biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>Az információs rendszer a következő részletes információkat tartalmazó naplórekordokat hozza létre: (i) elért fájlnév; (ii) az esemény kezdeményezésére használt program vagy parancs; és (iii) forrás- és célcímek.

**Azonosító:** 1209.09aa3System.2 – 09.aa **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A diagnosztikai naplókat App Services engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Naplózza az alkalmazás diagnosztikai naplóinak engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli vizsgálatot, ha biztonsági incidens történik vagy a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>A rendszer a szervezeten belül és kívül is naplózza a lefedett információk minden nyilvánosságra hozatalát, beleértve a közzététel típusát, az esemény dátumát és időpontját, a címzettet és a küldőt.

**Azonosító:** 1210.09aa3System.3 – 09.aa **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Diagnosztikai beállítás naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |A kiválasztott erőforrástípusok diagnosztikai beállításának naplózása |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Engedélyezni kell a Data Lake Analytics erőforrásnaplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>A szervezet minden kilencven (90) naponként ellenőrzi, hogy a lefedett információk minden egyes kivonata rögzítve van-e az adatok törléséről vagy a használatukról.

**Azonosító:** 1211.09aa3System.4 – 09.aa **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell az SQL Server naplózását](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Engedélyezni kell a SQL Server naplózását a kiszolgálón lévő összes adatbázis adatbázisában végzett adatbázis-tevékenységek nyomon követéséhez és auditnaplóba mentéséhez. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[A Managed HSM Azure Key Vault ben engedélyezni kell az erőforrásnaplókat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |Ha vizsgálati célból szeretné újból létrehozni a tevékenységnaplókat, amikor biztonsági incidens vagy a hálózat biztonsága sérül, az erőforrásnaplók managed HSM-en való engedélyezésével naplózhatja a naplókat. Kövesse az itt található utasításokat: [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging) . |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell a Key Vault erőforrásnaplókat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli használatát vizsgálati célokra, amikor biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>Rendszerhasználat figyelése

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>A rendszer legalább negyedévente figyeli és ellenőrzi az információs rendszerekhez való jogosulatlan távoli kapcsolatokat, és megfelelő lépéseket fog tenni, ha jogosulatlan kapcsolatot fedeznek fel.

**Azonosító:** 1120.09ab3System.9 – 09.ab **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor kell gyűjtenie a tevékenységnaplókat az összes régióból](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Ez a szabályzat naplóz Azure Monitor naplóprofilt, amely nem exportálja a tevékenységeket az Összes Azure által támogatott régióból, beleértve a globálist is. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>A szervezet figyeli az információs rendszert, hogy azonosítsa azokat a rendellenességeket vagy rendellenességeket, amelyek a rendszer meghibásodását vagy feltört állapotát jelzik, és segít meggyőződni arról, hogy a rendszer optimális, rugalmas és biztonságos állapotban működik.

**Azonosító:** 12100.09ab2System.15 – 09.ab **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Log Analytics-ügynököt virtuális gépekre kell telepíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Ez a szabályzat naplóz minden Windows/Linux rendszerű virtuális gépet, ha a Log Analytics-ügynök nincs telepítve. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>A szervezet határozza meg, hogy milyen gyakran kell áttekintenünk az auditnaplókat, hogyan dokumentáljuk a felülvizsgálatokat, és hogy milyen szerepköröket és felelősségi köröket kell bevetni a felülvizsgálatokat végző személyzetnek, beleértve a szükséges szakmai minősítéseket és egyéb minősítéseket.

**Azonosító:** 12101.09ab1Szervezeti.3 – 09.ab **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Log Analytics-ügynököt telepíteni kell a Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Ez a szabályzat naplóz minden Windows-/Linux-Virtual Machine Scale Sets ha a Log Analytics-ügynök nincs telepítve. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>A szervezet rendszeres időközönként teszteli a monitorozási és észlelési folyamatokat, orvosolja a hiányosságokat, és javítja a folyamatait.

**Azonosító:** 12102.09ab1Szervezeti.4 – 09.ab **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Naplózhatja a Windows rendszerű gépeket, amelyeken a Log Analytics-ügynök nem a várt módon csatlakozik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Előfeltételként a szabályzat-hozzárendelési hatókörben kell telepíteni az előfeltételeket. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha az ügynök nincs telepítve, vagy ha telepítve van, de a AgentConfigManager.MgmtSvcCfg COM-objektum azt adja vissza, hogy nem a házirend-paraméterben megadott azonosítóval van regisztrálva a munkaterületen. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>Az engedélyezett és a jogosulatlan hozzáférési kísérletek monitorozásával kapcsolatos összes vonatkozó jogi követelmény teljesül.

**Azonosító:** 1212.09ab1System.1 – 09.ab **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor naplóprofilnak naplókat kell gyűjtenie az "írás", "törlés" és "művelet" kategóriákhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Ez a szabályzat biztosítja, hogy a naplóprofil az "írás", "törlés" és "művelet" kategóriák naplóit gyűjtse |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>A szervezet környezetében üzembe helyezett automatizált rendszerekkel figyelhetők a kulcsfontosságú események és a rendellenes tevékenységek, valamint elemezhetők a rendszernaplók, amelyek eredményeit rendszeresen ellenőrzik.

**Azonosító:** 1213.09ab2System.128 – 09.ab **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Log Analytics-ügynök automatikus építésének engedélyezve kell lennie az előfizetésben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |A biztonsági rések és fenyegetések figyelése érdekében a Azure Security Center gyűjt adatokat az Azure-beli virtuális gépekről. Az adatokat a Korábban Microsoft Monitoring Agent (MMA) néven ismert Log Analytics-ügynök gyűjti, amely beolvassa a biztonsággal kapcsolatos különböző konfigurációkat és eseménynaplókat a gépről, és elemzés céljából átmásolja az adatokat a Log Analytics-munkaterületre. Javasoljuk, hogy az automatikus kiépítés engedélyezésével automatikusan üzembe helyeződjon az ügynök az összes támogatott Azure-beli és újonnan létrehozott virtuális gépre. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>A figyelés kiterjed az emelt szintű műveletekre, az engedélyezett hozzáférési vagy a jogosulatlan hozzáférési kísérletekre, beleértve az inaktivált fiókok elérésére tett kísérleteket, valamint a rendszerriasztásokat és -hibákat.

**Azonosító:** 1214.09ab2System.3456 – 09.ab **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor kell gyűjtenie a tevékenységnaplókat az összes régióból](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Ez a szabályzat naplóz a Azure Monitor naplóprofilt, amely nem exportálja a tevékenységeket az Összes Azure által támogatott régióból, beleértve a globálist is. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>A szervezet által alkalmazott naplózási és monitorozási rendszerek támogatják a naplózás csökkentését és a jelentés-készítést.

**Azonosító:** 1215.09ab2System.7 – 09.ab **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Log Analytics-ügynököt telepíteni kell a virtuális gépekre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Ez a szabályzat naplóz minden Windows-/Linux-alapú virtuális gépet, ha a Log Analytics-ügynök nincs telepítve. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>Az automatizált rendszerek a biztonsági rendszerek (pl. IPS/IDS) és rendszerrekordok figyelési tevékenységeinek napi áttekintésére, valamint az anomáliák azonosítására és dokumentálára használhatók.

**Azonosító:** 1216.09ab3System.12 – 09.ab **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Log Analytics-ügynököt telepíteni kell a Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Ez a szabályzat naplóz minden Windows-/Linux-Virtual Machine Scale Sets ha a Log Analytics-ügynök nincs telepítve. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>Riasztások jönnek létre a műszaki személyzet számára a gyanús tevékenységek vagy a gyanús szabálysértések elemzése és kivizsgálása érdekében.

**Azonosító:** 1217.09ab3System.3 – 09.ab **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Naplózhatja a Windows rendszerű gépeket, amelyeken a Log Analytics-ügynök nem a várt módon csatlakozik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha az ügynök nincs telepítve, vagy ha telepítve van, de a AgentConfigManager.MgmtSvcCfg COM-objektum azt adja vissza, hogy a házirend-paraméterben megadott azonosítótól különböző munkaterületen van regisztrálva. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>Az információs rendszer képes automatikusan feldolgozni a fontos események naplórekordjait a választható feltételek alapján.

**Azonosító:** 1219.09ab3System.10 – 09.ab **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor naplóprofilnak naplókat kell gyűjtenie az "írás", "törlés" és "művelet" kategóriákhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Ez a szabályzat biztosítja, hogy a naplóprofil az "írás", "törlés" és "művelet" kategóriák naplóit gyűjtse |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>A monitorozás magában foglalja a bejövő és kimenő kommunikációt és a fájlintegritás monitorozását.

**Azonosító:** 1220.09ab3System.56 – 09.ab **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Log Analytics-ügynök automatikus építésének engedélyezve kell lennie az előfizetésben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |A biztonsági rések és fenyegetések figyelése érdekében a Azure Security Center gyűjt adatokat az Azure-beli virtuális gépekről. Az adatokat a Korábban Microsoft Monitoring Agent (MMA) néven ismert Log Analytics-ügynök gyűjti, amely beolvassa a biztonsággal kapcsolatos különböző konfigurációkat és eseménynaplókat a gépről, és elemzés céljából átmásolja az adatokat a Log Analytics-munkaterületre. Javasoljuk, hogy az automatikus kiépítés engedélyezésével automatikusan telepítse az ügynököt az összes támogatott Azure-beli és újonnan létrehozott virtuális gépre. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>Rendszergazdai és operátori naplók

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>A szervezet biztosítja a megfelelő naplózás engedélyezését a rendszergazdai tevékenységek naplózása érdekében; és rendszeresen áttekinti a rendszergazdai és operátori naplókat.

**Azonosító:** 1270.09ad1System.12 – 09.ad **tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat meghatározott felügyeleti műveleteket naplóz, amelyekhez nincs konfigurálva tevékenységnapló-riasztás. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>A behatolásészlelési rendszer, amely a rendszer- és hálózati rendszergazdák felügyeletén kívül van felügyelve, a rendszer- és hálózatfelügyeleti tevékenységek megfelelőségét figyeli.

**Azonosító:** 1271.09ad1System.1 – **09.ad:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat adott felügyeleti műveleteket naplóz, és nincsenek konfigurálva tevékenységnapló-riasztások. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>A feladatok elkülönítése

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>A feladatok elkülönítése az információk és rendszerek jogosulatlan vagy véletlen módosításának kockázatát korlátozza.

**Azonosító:** 1229.09c1Szervezeti.1 – 09.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Kubernetes Servicesben szerepköralapú Access Control (RBAC) kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |A felhasználók által végrehajtható műveletek részletes szűréséhez az Role-Based Access Control (RBAC) használatával kezelheti az engedélyeket a Kubernetes Service-fürtökben, és konfigurálhatja a megfelelő engedélyezési házirendeket. |Naplózás, Letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>Az információs rendszerekhez egyetlen személy sem férhet hozzá, módosíthat vagy használhat hitelesítés vagy észlelés nélkül.

**Azonosító:** 1230.09c2Szervezeti.1 – 09.c **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Egyéni RBAC-szabályok használatának naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Egyéni RBAC-szerepkörök helyett naplózhat olyan beépített szerepköröket, mint például a "Tulajdonos, a Közreműködés, az Olvasó", amelyek hibaarányt jeleznek. Az egyéni szerepkörök használata kivételként van kezelve, és szigorú felülvizsgálatot és fenyegetésmodellezést igényel |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>A hozzáférés-vezérlésért felelős személyek hozzáférése az egyes felhasználók szerepköre és feladatai alapján a minimálisan szükséges mennyiségre korlátozódik, és ezek a személyek nem férhetnek hozzá az ezekhez a vezérlőkhöz kapcsolódó naplózási &nbsp; funkciókhoz.

**Azonosító:** 1232.09c3Szervezeti.12 – 09.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Felhasználói jogok kiosztása" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |A Windows rendszerű gépeken a "Felhasználói jogok kiosztása" kategóriában megadott Csoportházirend kell, hogy legyen a helyi bejelentkezés, az RDP, a hálózati hozzáférés és számos más felhasználói tevékenység engedélyezéséhez. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>A biztonsági naplózási tevékenységek függetlenek egymástól.

**Azonosító:** 1276.09c2Szervezeti.2 – 09.c **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az egyéni előfizetés-tulajdonosi szerepkörök nem létezhetnek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Ez a szabályzat biztosítja, hogy nem létezik egyéni előfizetés-tulajdonosi szerepkör. |Naplózás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>Az események kezdeményezése elkülönül az engedélyezéstől, így csökken az összeütközés lehetősége.

**Azonosító:** 1277.09c2Szervezeti.4 – 09.c **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Biztonsági beállítások – Felhasználói fiókok vezérlése" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |A Windows rendszerű gépeken a megadott Csoportházirend "Biztonsági beállítások – Felhasználói fiókok vezérlése" kategóriában kell megadni a rendszergazdák üzemmódját, a jogosultságszint-emelési kérés viselkedését, valamint a fájl- és beállításjegyzékbeli írási hibák virtualizálását. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>A szervezet azonosítja azokat a feladatokat, amelyek elkülönítést igényelnek, és meghatározza az információs rendszer hozzáférési engedélyeit a feladatok elkülönítésének támogatásához; A és az inkompatibilis feladatok több felhasználó között vannak elkülönítve, hogy minimalizálják a visszaélés vagy csalás lehetőségét.

**Id**: 1278.09c2Organizational.56 - 09.c **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Egyéni előfizetés-tulajdonosi szerepkörök nem létezhetnek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Ez a szabályzat biztosítja, hogy nem létezik egyéni előfizetés-tulajdonosi szerepkör. |Naplózás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>Rosszindulatú kód elleni védelem

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>A rendszer az összes végfelhasználói eszközön telepíti, működteti és frissíti a víruskeresőt és a kémprogram-elhárítóprogramokat, hogy rendszeres időközönként vizsgálatot végezzen a rendszereken a jogosulatlan szoftverek azonosításához és eltávolításához. Azok a kiszolgálói környezetek, amelyekhez a kiszolgálószoftver fejlesztői kifejezetten azt javasolják, hogy ne telepítsenek gazdagépalapú vírus- és kémprogram-elhárító szoftvereket, hálózati alapú kártevőészlelési (NBMD) megoldással eleget tehetnek ennek a követelménynek.

**AZONOSÍTÓ:** 0201.09j1Szervezeti.124 – 09.j **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A biztonságos alkalmazások meghatározásához szükséges adaptív alkalmazásvezérlőket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazásvezérlők engedélyezésével meghatározhatja a gépeken futó, ismerten biztonságos alkalmazások listáját, és riasztást küld, ha más alkalmazások is futnak. Ez segít a gépek kártevők elleni védelemében. A szabályok konfigurálási és karbantartásának egyszerűsítése érdekében a Security Center gépi tanulással elemzi az egyes gépeken futó alkalmazásokat, és javaslatot ad az ismert biztonságos alkalmazások listájára. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Alapértelmezett Microsoft IaaSAntimalware bővítmény telepítése Windows Serverhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |Ez a szabályzat egy Microsoft IaaSAntimalware bővítményt helyez üzembe alapértelmezett konfigurációval, ha a virtuális gép nincs kártevőirtó bővítve. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[Az Endpoint Protection megoldást telepíteni kell a virtuálisgép-méretezési készletekre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |A virtuálisgép-méretezési készleteken lévő végpontvédelmi megoldások meglétének és állapotának naplózása a fenyegetések és a biztonsági rések elleni védelem érdekében. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Antimalware Azure-beli virtuális gépeket a védelmi aláírások automatikus frissítésére kell konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |Ez a szabályzat naplót végez minden olyan Windows rendszerű virtuális gépen, amely nincs konfigurálva az Microsoft Antimalware-aláírások automatikus frissítésével. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Hiányzó Endpoint Protection figyelése a Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |A telepített Endpoint Protection-ügynökkel nem Azure Security Center kiszolgálókat |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[A rendszerfrissítéseket telepíteni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A kiszolgálók hiányzó biztonságirendszer-frissítéseit a Azure Security Center fogja figyelni |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>Biztonságimentás

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>Biztonsági másolat készül az adatokról és a szoftverekről, és a rendszer rendszeres időközönként teszteli az adathordozót és a visszaállítási eljárásokat.

**Azonosító:** 1616.09l1Szervezeti.16 – 09.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A hosszú távú georedundáns biztonsági mentést engedélyezni kell a Azure SQL adatbázisokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Ez a szabályzat napló Azure SQL Database a hosszú távú georedundáns biztonsági mentést nem engedélyező biztonsági mentések esetén. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>Meg van határozva és dokumentálva van az egyes rendszerekhez szükséges biztonsági mentések szintjének formális definíciója, beleértve az egyes rendszerek visszaállításának, a rendszerképezésre vonatkozó adatok hatókörét, a lemezkép készítésének gyakoriságát és a megőrzés időtartamát a vonatkozó szerződéses, jogi, szabályozási és üzleti követelmények alapján.

**Azonosító:** 1617.09l1Szervezeti.23 – 09.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A georedundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL lehetővé teszi, hogy az adatbázis-kiszolgáló redundancia-beállítását válassza. Beállítható georedundáns biztonsági mentési tárhelyre, amelyben az adatok nem csak a kiszolgálót tároló régióban tárolódnak, hanem egy párosított régióba is replikálhatók, hogy régióhiba esetén helyreállítási lehetőséget nyújtson. A georedundáns tárolás biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozása során engedélyezett. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>A biztonsági másolatok egy fizikailag biztonságos távoli helyen vannak tárolva, megfelelő távolságban ahhoz, hogy az elsődleges helyen található adatokkal való kárt okozva ésszerű mértékben védettek legyen, és megfelelő fizikai és környezeti intézkedések állnak rendelkezésre, amelyek biztosítják azok védelmét a távoli helyen.

**AZONOSÍTÓ:** 1618.09l1Szervezeti.45 – 09.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A georedundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL az adatbázis-kiszolgáló redundancia-beállításának kiválasztása. Beállítható georedundáns biztonsági mentési tárhelyre, amelyben az adatok nem csak a kiszolgálót tároló régióban tárolódnak, hanem egy párosított régióba is replikálhatók, hogy régióhiba esetén helyreállítási lehetőséget nyújtson. A georedundáns tárolás biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozása során engedélyezett. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>A biztonsági másolatok leltárrekordjai, beleértve a tartalmat és az aktuális helyet is, megmaradnak.

**AZONOSÍTÓ:** 1619.09l1Szervezeti.7 – 09.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A georedundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB az adatbázis-kiszolgáló redundancia-beállításának kiválasztása. Beállítható georedundáns biztonsági mentési tárra, amelyben az adatok nem csak a kiszolgálót tároló régióban tárolódnak, hanem egy párosított régióba is replikálhatók, hogy régióhiba esetén helyreállítási lehetőséget biztosítson. A georedundáns tárolás biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozása során engedélyezett. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>Ha a biztonsági mentési szolgáltatást a harmadik fél kézbesíti, a szolgáltatói szerződés részletes védelmet biztosít a biztonsági mentési információk titkosságának, integritásának és rendelkezésre állásának szabályozására.

**Azonosító:** 1620.09l1Szervezeti.8 – 09.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Az Azure-erőforrások védelmének biztosítása Virtual Machines engedélyezésével Azure Backup. Azure Backup egy biztonságos és költséghatékony adatvédelmi megoldás az Azure-hoz. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>Az automatizált eszközök az összes biztonsági mentés nyomon követésére használhatók.

**Id**: 1621.09l2Organizational.1 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A hosszú távú georedundáns biztonsági mentést engedélyezni kell a Azure SQL adatbázisokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Ez a szabályzat naplót Azure SQL Database, ha a georedundáns biztonsági mentés nincs engedélyezve. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>A biztonsági másolatok integritásának és biztonságának fenntartása biztosítja a jövőbeli rendelkezésre állást, valamint a biztonsági másolatok esetleges akadálymentességi problémáinak azonosítását és elhárítását a teljes területre kiterjedő katasztrófa esetén.

**AZONOSÍTÓ:** 1622.09l2Szervezeti.23 – 09.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A georedundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL lehetővé teszi, hogy az adatbázis-kiszolgáló redundancia-beállítását válassza. Beállítható georedundáns biztonsági mentési tárhelyre, amelyben az adatok nem csak a kiszolgálót tároló régióban tárolódnak, hanem egy párosított régióba is replikálhatók, hogy régióhiba esetén helyreállítási lehetőséget nyújtson. A georedundáns tárolás biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozása során engedélyezett. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>A bizalmasság biztosítása érdekében a lefedett információkról titkosított formában biztonsági adatokat kell biztonságiítani.

**AZONOSÍTÓ:** 1623.09l2Szervezeti.4 – 09.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A georedundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL az adatbázis-kiszolgáló redundancia-beállításának kiválasztása. Beállítható georedundáns biztonsági mentési tárra, amelyben az adatok nem csak a kiszolgálót tároló régióban tárolódnak, hanem egy párosított régióba is replikálhatók, hogy régióhiba esetén helyreállítási lehetőséget biztosítson. A georedundáns tárolás biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozása során engedélyezett. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>A szervezet naponta végez növekményes vagy különbözeti biztonsági mentéseket, hetente pedig teljes biztonsági mentéseket külön adathordozóra.

**Id**: 1624.09l3Organizational.12 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A georedundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB az adatbázis-kiszolgáló redundancia-beállításának kiválasztása. Beállítható georedundáns biztonsági mentési tárra, amelyben az adatok nem csak a kiszolgálót tároló régióban tárolódnak, hanem egy párosított régióba is replikálhatók, hogy régióhiba esetén helyreállítási lehetőséget biztosítson. A georedundáns tárolás biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozása során engedélyezett. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>A rendszer három (3) generációs biztonsági mentést (teljes és az összes kapcsolódó növekményes vagy különbségi biztonsági mentést) tárol a helyszínen, és a helyszíni és a nem helyszíni biztonsági mentéseket is névvel, dátummal, időponttal és művelettel naplózza.

**Id**: 1625.09l3Organizational.34 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Az Azure-erőforrások védelmének Virtual Machines engedélyezésével Azure Backup. Azure Backup egy biztonságos és költséghatékony adatvédelmi megoldás az Azure-hoz. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>A szervezet gondoskodik arról, hogy a kiszolgálók mozgása előtt elérhető legyen a lefedett információk aktuális, lekérhetők másolata.

**AZONOSÍTÓ:** 1626.09l3Szervezeti.5 – 09.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A georedundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL az adatbázis-kiszolgáló redundancia-beállításának kiválasztása. Beállítható georedundáns biztonsági mentési tárhelyre, amelyben az adatok nem csak a kiszolgálót tároló régióban tárolódnak, hanem egy párosított régióba is replikálhatók, hogy régióhiba esetén helyreállítási lehetőséget nyújtson. A georedundáns tárolás biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozása során engedélyezett. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>A szervezet minden biztonsági mentés után teszteli a biztonsági mentési adatokat a média megbízhatóságának és az információk integritásának ellenőrzéséhez, és ezt követően legalább évente.

**Azonosító:** 1627.09l3Szervezeti.6 – 09.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A georedundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB az adatbázis-kiszolgáló redundancia-beállításának kiválasztása. Beállítható georedundáns biztonsági mentési tárra, amelyben az adatok nem csak a kiszolgálót tároló régióban tárolódnak, hanem egy párosított régióba is replikálhatók, hogy régióhiba esetén helyreállítási lehetőséget biztosítson. A georedundáns tárolás biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozása során engedélyezett. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>A munkaerő tagjai azonosítják és közlik a munkaerővel az adatok biztonsági mentési folyamatának szerepköreit és feladatait; A saját eszközök használata (BYOD) felhasználóinak kell biztonsági másolatot készíteniük a szervezeti és/vagy ügyféladatokról az eszközeiken.

**Azonosító:** 1699.09l1Szervezeti.10 – 09.l **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Az Azure-erőforrások védelmének biztosítása Virtual Machines engedélyezésével Azure Backup. Azure Backup egy biztonságos és költséghatékony adatvédelmi megoldás az Azure-hoz. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>Hálózati vezérlők

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>A szervezet figyeli az információs rendszerhez való összes engedélyezett és jogosulatlan vezeték nélküli hozzáférést, és megtiltja a vezeték nélküli hozzáférési pontok (WAP-k) telepítését, kivéve, ha az CIO vagy kijelölt képviselő írásában kifejezetten engedélyt kapott.

**Azonosító:** 0858.09m1Szervezeti.4 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Minden hálózati portot korlátozni kell a virtuális géphez társított hálózati biztonsági csoportokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center, hogy a hálózati biztonsági csoportok egyes bejövő szabályai túl megengedőek. A bejövő szabályok nem engedélyeznek "Bármely" vagy "Internet" tartományból való hozzáférést. Ez lehetővé teheti, hogy a támadók megcélják az erőforrásokat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[A virtuális gépek felügyeleti portjainak időben elérhető hálózati hozzáférés-vezérléssel kell védeni őket](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehetséges igény szerinti hálózati (JIT)-hozzáférést a Azure Security Center figyeli javaslatként |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[A Windows rendszerű gépeknek meg kell felelnie a "Windows tűzfal tulajdonságai" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |A Windows rendszerű gépeken a megadott Csoportházirend "Windows tűzfal tulajdonságai" kategóriában kell lennie a tűzfal állapotára, kapcsolataira, szabálykezelésére és értesítésire. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>A szervezet biztosítja a hálózatokban található információk biztonságát, a hálózati szolgáltatások és információs szolgáltatások rendelkezésre állását a hálózaton keresztül, valamint a csatlakoztatott szolgáltatások jogosulatlan hozzáférés elleni védelmét.

**Azonosító:** 0859.09m1Szervezeti.78 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptív hálózatkorrekozási javaslatokat kell alkalmazni az internetre néző virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és javaslatokat tesz a hálózati biztonsági csoport szabályára vonatkozóan, amelyek csökkentik a potenciális támadási felületet |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>A szervezet hivatalosan felügyeli a hálózaton található berendezéseket, beleértve a felhasználói területek berendezéseit is.

**Azonosító:** 0860.09m1Szervezeti.9 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |Ez a szabályzat automatikusan telepíti a diagnosztikai beállításokat a hálózati biztonsági csoportok számára. A rendszer automatikusan létrehoz egy "{storagePrefixParameter}{NSGLocation}" nevű tárfiókot. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[Service Bus hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ez a szabályzat naplót Service Bus virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>Az eszközök azonosításához és hitelesítéséhez a helyi és/vagy a nagykiterületű hálózatokon, beleértve a vezeték nélküli hálózatokat is, az információs rendszer egy (i) megosztott ismert információs megoldást vagy (ii) egy szervezeti hitelesítési megoldást használ, amelynek pontos kiválasztása és erőssége az információs rendszer biztonsági kategorizálása alapján &nbsp; történik.

**Azonosító:** 0861.09m2Szervezeti.67 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ez a szabályzat naplót App Service virtuális hálózati szolgáltatásvégpont használatára konfigurált összes olyan vizsgálatot, amely nincs konfigurálva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[A Windows rendszerű gépeknek meg kell felelnie a "Biztonsági beállítások – Hálózati hozzáférés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |A Windows rendszerű gépeken a megadott Csoportházirend "Biztonsági beállítások – Hálózati hozzáférés" kategóriában kell lennie, beleértve a névtelen felhasználók, a helyi fiókok és a beállításjegyzékhez való távoli hozzáférés hozzáférését. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>A szervezet gondoskodik arról, hogy az információs rendszerek megvédjék az továbbított információk bizalmasságát és integritását, beleértve az átvitel előkészítését és a fogadást is.

**Azonosító:** 0862.09m2Szervezeti.8 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ez a szabályzat naplót SQL Server virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>A szervezet olyan tűzfalkonfigurációt épít ki, amely korlátozza a nem megbízható hálózatok és a lefedett információs környezetben található rendszerösszetevők közötti kapcsolatokat; A tűzfal konfigurációjának módosításai pedig frissülnek a hálózati diagramon.

**Azonosító:** 0863.09m2Szervezeti.910 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az eseményközpontnak virtuális hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ez a szabályzat naplót naplól minden olyan eseményközpontot, amely nincs virtuális hálózati szolgáltatásvégpont használatára konfigurálva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>A használati korlátozásokat és a megvalósítási útmutatót hivatalosan definiálják a VoIP-hez, beleértve a szolgáltatás hitelesítését és monitorozását.

**Azonosító:** 0864.09m2Szervezeti.12 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Cosmos DB hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ez a szabályzat naplót Cosmos DB virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>A szervezet (i) engedélyezi az információs rendszer és a szervezeten kívüli más információs rendszerek közötti kapcsolatokat a biztonsági szerződések vagy más hivatalos megállapodás összekapcsolásával; (ii) minden kapcsolatot, az interfész jellemzőit, a biztonsági követelményeket és a kommunikált információk természetét csatolja el; (iii) az összes elutasítását alkalmazza, kivételi szabályzat alapján engedélyezi az információs rendszer és a szervezeten kívüli más információs rendszerek közötti kapcsolatokat; A és (iv) egy alapértelmezett megtagadási szabályt alkalmaz, amely az állomásalapú tűzfalakon vagy portszűrő eszközöken keresztül eldobja az összes forgalmat a végpontjaikon (munkaállomásokon, kiszolgálókon stb.), kivéve a kifejezetten engedélyezett szolgáltatásokat és portokat.

**Azonosító:** 0865.09m2Szervezeti.13 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault virtuális hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ez a szabályzat napló Key Vault virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>A szervezet ismerteti a hálózati összetevők logikai felügyeletéhez szükséges csoportokat, szerepköröket és felelősségi köröket, és biztosítja a hálózati infrastruktúra elemeinek koordinációját és konzisztenciáját.

**Azonosító:** 0866.09m3Szervezeti.1516 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfióknak korlátoznia kell a hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |A tárfiókok hálózati hozzáférését korlátozni kell. Konfigurálja úgy a hálózati szabályokat, hogy csak az engedélyezett hálózatokról származó alkalmazások férnek hozzá a tárfiókhoz. Az adott internetről vagy helyszíni ügyfelekről származó kapcsolatok lehetővé teszik a hozzáférést adott Azure-beli virtuális hálózatokról származó forgalomhoz vagy nyilvános internetes IP-címtartományok felé |Naplózás, Megtagadás, Letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>A vezeték nélküli hozzáférési pontok biztonságos helyeken vannak elhelyezve, és leállnak, amikor nincsenek használatban (pl. családi, hétvégek).

**Azonosító:** 0867.09m3Szervezeti.17 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfiókok virtuális hálózati szolgáltatásvégpontot használjanak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ez a szabályzat naplóba veszi a virtuális hálózati szolgáltatásvégpont használatára nem konfigurált tárfiókokat. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>A szervezet egy tűzfal-konfigurációt hoz létre, amely a lefedett adatkörnyezethez szükséges bejövő és kimenő forgalmat a megfelelőre korlátozza.

**Azonosító:** 0868.09m3Szervezeti.18 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry virtuális hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a szabályzat naplót Container Registry virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>Az útválasztó konfigurációs fájljai védettek és szinkronizálva vannak.

**Azonosító:** 0869.09m3Szervezeti.19 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry virtuális hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a szabályzat naplót Container Registry virtuális hálózati szolgáltatásvégpont használatára nem konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>Az összes proxyhoz való hozzáférés le van tiltva, kivéve azokat a gazdagépeket, portokat és szolgáltatásokat, amelyekre kifejezetten szükség van.

**Azonosító:** 0870.09m3Szervezeti.20 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a szabályzat naplót Container Registry virtuális hálózati szolgáltatásvégpont használatára konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>A mérvadó DNS-kiszolgálók belső és külső szerepkörökbe vannak elkülönítve.

**Azonosító:** 0871.09m3Szervezeti.22 – 09.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry hálózati szolgáltatásvégpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a szabályzat naplót Container Registry virtuális hálózati szolgáltatásvégpont használatára konfigurált összes virtuális gépet. |Naplózás, Letiltva |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>A Network Services

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>A hálózati szolgáltatók/menedzserek által biztosított elfogadott szolgáltatásokat hivatalosan kezelik és figyelik a biztonságos szolgáltatásokat.

**AZONOSÍTÓ:** 0835.09n1Szervezeti.1 – 09.n **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A hálózati forgalom adatgyűjtési ügynökét Windows rendszerű virtuális gépekre kell telepíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center a Microsoft függőségi ügynökével gyűjti be az Azure-beli virtuális gépekről származó hálózati forgalmi adatokat, hogy lehetővé tegye a speciális hálózatvédelmi funkciókat, például a hálózati térképen a forgalom vizualizációját, a hálózatkorlátozási javaslatokat és a konkrét hálózati fenyegetéseket. |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[A virtuális gépeket új erőforrásokba kell Azure Resource Manager migrálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |Az új Azure Resource Manager használata a virtuális gépekhez olyan biztonsági fejlesztésekhez, mint például: erősebb hozzáférés-vezérlés (RBAC), jobb naplózás, Azure Resource Manager-alapú üzembe helyezés és irányítás, hozzáférés a felügyelt identitásokhoz, hozzáférés a titkos kulcsok kulcstartóihoz, Azure AD-alapú hitelesítés, valamint címkék és erőforráscsoportok támogatása a könnyebb biztonságkezelés érdekében |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>A szervezet hivatalosan engedélyezi és dokumentumokkal rendelkezik az információs rendszerek és a szervezeten kívüli más információs rendszerek egyes kapcsolódási jellemzőiről.

**Azonosító:** 0836.09.n2Szervezeti.1 – 09.n **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A hálózati forgalom adatgyűjtési ügynökét Linux rendszerű virtuális gépekre kell telepíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center a Microsoft függőségi ügynökével gyűjti be az Azure-beli virtuális gépekről származó hálózati forgalmi adatokat, hogy lehetővé tegye a speciális hálózatvédelmi funkciókat, például a hálózati térképen a forgalom vizualizációját, a hálózatkorlátozási javaslatokat és a konkrét hálózati fenyegetéseket. |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>A külső információszolgáltatókkal kötött formális megállapodások a biztonsággal és adatvédelemmel kapcsolatos konkrét kötelezettségeket foglalnak magukban.

**Azonosító:** 0837.09.n2Szervezeti.2 – 09.n **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher egy regionális szolgáltatás, amely lehetővé teszi a feltételek figyelése és diagnosztizálása az Azure-ban, illetve az Azure-ban a hálózati forgatókönyvek szintjén. A forgatókönyvszintű figyelés lehetővé teszi a problémák diagnosztizálásához a teljes hálózatszintű nézetben. A hálózati diagnosztikai és vizualizációs eszközök Network Watcher segítségével megértheti, diagnosztizálhatja és bepillantást nyerhet a hálózatba az Azure-ban. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>A szervezet folyamatosan ellenőrzi és frissíti az összekapcsolt biztonsági megállapodásokat a biztonsági követelmények betartatása érdekében.

**Azonosító:** 0885.09n2Szervezeti.3 – 09.n **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A hálózati forgalom adatgyűjtési ügynökét Linux rendszerű virtuális gépekre kell telepíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center a Microsoft függőségi ügynökével gyűjti össze az Azure-beli virtuális gépekről származó hálózati adatforgalmat, hogy lehetővé tegye a speciális hálózatvédelmi funkciókat, például a hálózati térképen a forgalom megjelenítését, a hálózatkorlátozási javaslatokat és a konkrét hálózati fenyegetéseket. |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>A szervezet egy formális megállapodás vagy egyéb dokumentum, például i) az összes engedélyezését, a kivételek megtagadása vagy a ii) az összes elutasítást, az engedélyezési kivételeket (előnyben részesített) házirendben alkalmazza, hogy lehetővé tegye bizonyos információs rendszerek számára a külső információs rendszerekhez való csatlakozást.

**Azonosító:** 0886.09n2Szervezeti.4 – 09.n **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher egy regionális szolgáltatás, amely lehetővé teszi a feltételek figyelése és diagnosztizálása az Azure-ban, illetve az Azure-ban a hálózati forgatókönyvek szintjén. A forgatókönyvszintű figyelés lehetővé teszi a teljes hálózatszintű nézet problémáinak diagnosztizálát. A hálózati diagnosztikai és vizualizációs eszközök Network Watcher segítségével megértheti, diagnosztizálhatja és betekintheti a hálózatát az Azure-ban. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>A szervezet megköveteli a külső/kiszervezett szolgáltatóktól a külső/kiszervezett szolgáltatások nyújtásához használt funkciók, portok és protokollok azonosítását.

**Azonosító:** 0887.09n2Szervezeti.5 – 09.n **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A hálózati forgalom adatgyűjtési ügynökét Windows rendszerű virtuális gépekre kell telepíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center a Microsoft függőségi ügynökével gyűjti be az Azure-beli virtuális gépekről származó hálózati forgalmi adatokat, hogy lehetővé tegye a speciális hálózatvédelmi funkciókat, például a hálózati térképen a forgalom vizualizációját, a hálózatkorlátozási javaslatokat és a konkrét hálózati fenyegetéseket. |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>A külső/külső szolgáltatóval kötött szerződés tartalmazza azt a specifikációt, amely szerint a szolgáltató felelős a megosztott információk védelméért.

**Id**: 0888.09n2Organizational.6 - 09.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher egy regionális szolgáltatás, amely lehetővé teszi a feltételek figyelése és diagnosztizálása az Azure-ban, illetve az Azure-ban a hálózati forgatókönyvek szintjén. A forgatókönyvszintű figyelés lehetővé teszi a problémák diagnosztizálásához a teljes hálózatszintű nézetben. A hálózati diagnosztikai és vizualizációs eszközök Network Watcher segítségével megértheti, diagnosztizálhatja és bepillantást nyerhet a hálózatba az Azure-ban. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>Cserélhető adathordozók kezelése

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>A szervezet az adatbesorolási szint alapján regisztrálja az adathordozókat (beleértve a laptopokat is) a használat előtt, észszerű korlátozásokat alkalmaz az ilyen adathordozók használatára, és megfelelő szintű fizikai és logikai védelmet (beleértve a titkosítást is) biztosít a lefedett információkat tartalmazó adathordozók számára, amíg megfelelően meg nem semmisülnek vagy vírusosulnak.

**AZONOSÍTÓ:** 0301.09o1Szervezeti.123 – 09.o **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[transzparens adattitkosítás SQL-adatbázisokon engedélyezni kell az adatbázis-adatokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Engedélyezni kell a transzparens adattitkosítást az aktív adatok védelme és a megfelelőségi követelmények teljesítéséhez |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>A szervezet védi és szabályozza a bizalmas adatokat tartalmazó adathordozókat az ellenőrzött területeken kívüli szállítás során.

**AZONOSÍTÓ:** 0302.09o2Szervezeti.1 – 09.o **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lemeztitkosítást kell alkalmazni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Az engedélyezett lemeztitkosítással nem rendelkező virtuális gépeket a Azure Security Center figyelik. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>A rendszer azonosítja a korlátozott használatot igénylő digitális és nem digitális médiatartalmakat, valamint a használatuk korlátozására használt konkrét védelmi intézkedések használatát.

**Azonosító:** 0303.09o2Szervezeti.2 – 09.o **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A nem gyorsítótárazó lemezeket titkosítani kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Ez a szabályzat a titkosítás engedélyezése nélkül naplóz minden nem gyorsítótárazó lemezt. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>A szervezet korlátozza az írható cserélhető adathordozók és a személyes tulajdonban lévő cserélhető adathordozók használatát a szervezeti rendszerekben.

**Id**: 0304.09o3Organizational.1 - 09.o **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Titkosítás megkövetelása Data Lake Store fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Ez a szabályzat biztosítja, hogy a titkosítás engedélyezve legyen minden Data Lake Store fiókon |Tagadja |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[A felügyelt SQL-példányok az ügyfelek által felügyelt kulcsokat használják az adatok titkosításához az](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |A transzparens adattitkosítás (TDE) saját kulccsal való megvalósítása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, fokozott biztonságot nyújt egy HSM-háttérszolgáltatással, és lehetővé teszi a feladatok elkülönítését. Ez a javaslat a kapcsolódó megfelelőségi követelményekhez tartozó szervezetekre vonatkozik. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Az SQL-kiszolgálóknak ügyfél által kezelt kulcsokat kell használniuk az adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |A transzparens adattitkosítás (TDE) saját kulccsal történő megvalósítása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, nagyobb biztonságot nyújt egy HSM-háttérszolgáltatással, és lehetővé teszi a feladatok elkülönítését. Ez a javaslat a kapcsolódó megfelelőségi követelményekhez tartozó szervezetekre vonatkozik. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>Az Információcsere házirendek és eljárások

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>A felhőszolgáltatók az iparág által elismert virtualizálási platformot és szabványos virtualizálási formátumokat (például Open Virtualization Format, OVF) használják az együttműködés biztosításához, és dokumentálták a használatban lévő hipervizorok egyéni módosításait, valamint az ügyfelek számára elérhető összes megoldásspecifikus virtualizálási hookot.

**Azonosító:** 0662.09sCSPOrganizational.2 – 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a WEBalkalmazás "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítása "Be"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt kérjen a bejövő kérelmekhez. Csak az érvényes tanúsítvánnyal rendelkezik ügyfelek érik el az alkalmazást. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>A szervezet hivatalosan több védelmi lehetőségről is gondoskodik, mielőtt engedélyezi az információs rendszerek használatát az információcseréhez.

**AZONOSÍTÓ:** 0901.09s1Szervezeti.1 – 09.s **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A CORS nem engedélyezheti minden erőforrásnak, hogy hozzáférjen a webalkalmazásokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Az eredetközi erőforrás-megosztás (CORS) nem engedélyezheti minden tartomány számára a webalkalmazáshoz való hozzáférést. Csak a szükséges tartományok számára engedélyezze a webalkalmazással való interakciót. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>A szervezet információs eszközeihez való távoli (külső) hozzáférés és a külső információkhoz való hozzáférés (amelyekhez a szervezet nem rendelkezik vezérléssel) egyértelműen meghatározott használati feltételeken alapul.

**Azonosító:** 0902.09s2Szervezeti.13 – 09.s **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A CORS nem engedélyezheti minden erőforrás számára a függvényalkalmazások hozzáférését](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Az eredetközi erőforrás-megosztás (CORS) nem engedélyezheti minden tartomány számára a függvényalkalmazáshoz való hozzáférést. Csak a szükséges tartományok számára engedélyezze a függvényalkalmazással való interakciót. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>A szervezet olyan feltételeket és feltételeket hoz létre, amelyek összhangban vannak a külső információs rendszereket tulajdonában, üzemeltető és/vagy karbantartó más szervezésekkel létesített megbízhatósági kapcsolatokkal, lehetővé téve a jogosult személyek számára, hogy (i) külső információs rendszerekből hozzáférjenek az információs rendszerhez; és (ii) a szervezet által vezérelt információk feldolgozása, tárolása vagy továbbítása külső információs rendszerek használatával.

**Id**: 0911.09s1Organizational.2 - 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A CORS nem engedélyezheti minden erőforrás számára az API-alkalmazáshoz való hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |Az eredetközi erőforrás-megosztás (CORS) nem engedélyezheti minden tartomány számára az API-alkalmazáshoz való hozzáférést. Csak a szükséges tartományok számára engedélyezze az API-alkalmazással való interakciót. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>A titkosítás a belső hálózathoz és a külső rendszerekhez való távelérési munkamenetek titkosságának és integritásának védelmére használatos.

**AZONOSÍTÓ:** 0912.09s1Szervezeti.4 – 09.s **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A webalkalmazások esetében a távoli hibakeresést ki kell kapcsolva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |A távoli hibakereséshez meg kell nyitni a bejövő portokat egy webalkalmazáson. A távoli hibakeresést ki kell kapcsolva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>Az erős titkosítási protokollokkal védhetőek a lefedett információk a kevésbé megbízható/nyitott nyilvános hálózatokon keresztüli átvitel során.

**Azonosító:** 0913.09s1Szervezeti.5 – 09.s **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A függvényalkalmazások esetében a távoli hibakeresést ki kell kapcsolva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A távoli hibakereséshez meg kell nyitni a bejövő portokat a függvényalkalmazásokban. A távoli hibakeresést ki kell kapcsolva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>A szervezet gondoskodik arról, hogy a kommunikációs védelmi követelmények, beleértve az információk cseréjének biztonságát is, a szabályzatfejlesztési és megfelelőségi auditok hatálya alá esnek.

**Azonosító:** 0914.09s1Szervezeti.6 – 09.s **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A távoli hibakeresést ki kell kapcsolva a API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |A távoli hibakereséshez meg kell nyitni a bejövő portokat az API-alkalmazásokban. A távoli hibakeresést ki kell kapcsolva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>A szervezet korlátozza a szervezet által vezérelt hordozható adathordozók használatát a jogosult személyek számára a külső információs rendszereken.

**Id**: 0915.09s2Organizational.2 - 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a WEBalkalmazás "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítása "Be"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt kérjen a bejövő kérelmekhez. Csak az érvényes tanúsítvánnyal rendelkezik ügyfelek érik el az alkalmazást. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>Az információs rendszer tiltja az együttműködésen alapuló számítástechnikai eszközök távoli aktiválását, és explicit jelzést ad az eszközökön fizikailag jelen található felhasználók számára.

**Azonosító:** 0916.09s2Szervezeti.4 – 09.s **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A CORS nem engedélyezheti minden erőforrásnak, hogy hozzáférjen a webalkalmazásokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Az eredetközi erőforrás-megosztás (CORS) nem engedélyezheti minden tartomány számára a webalkalmazáshoz való hozzáférést. Csak a szükséges tartományok számára engedélyezze a webalkalmazással való interakciót. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>A felhőszolgáltatók biztonságos (például nem tiszta szöveget és hitelesített) szabványosított hálózati protokollokat használnak az adatok importálásához és exportálásához, valamint a szolgáltatás kezeléséhez, és elérhetővé teszik a fogyasztók (bérlők) számára az érintett együttműködési és hordozhatósági szabványokat részletező dokumentumot.

**Azonosító:** 0960.09sCSPSzervezeti.1 – 09.s **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A CORS nem engedélyezheti minden erőforrás számára a függvényalkalmazások hozzáférését](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Az eredetközi erőforrás-megosztás (CORS) nem engedélyezheti minden tartomány számára a függvényalkalmazáshoz való hozzáférést. Csak a szükséges tartományok számára engedélyezze a függvényalkalmazással való interakciót. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>A személyzet megfelelően be van tanítva a vezető alapelvekről és eljárásokról minden típusú adatcseréhez (papír, papír és elektronikus).

**AZONOSÍTÓ:** 1325.09s1Szervezeti.3 – 09.s **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A függvényalkalmazások esetében a távoli hibakeresést ki kell kapcsolva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A távoli hibakereséshez meg kell nyitni a bejövő portokat a függvényalkalmazásokban. A távoli hibakeresést ki kell kapcsolva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>Online tranzakciók

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>A rendszer ellenőrzi az elektronikus kereskedelemben és online tranzakciókban részt vevő adatokat annak megállapításához, hogy az tartalmaz-e lefedett információkat.

**Azonosító:** 0943.09y1Szervezeti.1 – 09.y **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Naplózási követelmény a biztonságos átvitelhez a tárfiókban. A biztonságos átvitel egy olyan lehetőség, amely arra kényszeríti a tárfiókot, hogy csak biztonságos kapcsolatokról (HTTPS) fogadjon kéréseket. A HTTPS használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>Az összes érintett fél közötti kommunikációra használt protokollok titkosítási technikák (pl. SSL) használatával vannak biztosítva.

**Azonosító:** 0945.09y1Szervezeti.3 – 09.y **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Naplózhatja a megbízható legfelső szintű megbízható tanúsítványokban megadott tanúsítványokat nem tartalmazó Windows rendszerű gépeket](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a számítógép megbízható főtanúsítvány-tárolója (Cert:\LocalMachine\Root) nem tartalmazza a házirend-paraméter által felsorolt egy vagy több tanúsítványt. |auditIfNotExists (auditIfNotExists) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>A szervezetnek a tranzakcióban érintett összes félnek titkosítást kell használnia, és elektronikus aláírásokat kell használnia.

**Azonosító:** 0946.09y2Szervezeti.14 – 09.y **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak SSL-en keresztüli kapcsolatok engedélyezésének naplózása Azure Cache for Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>A szervezet gondoskodik arról, hogy a tranzakciós adatok tárolása a nyilvánosan elérhető környezeten kívülre (például a szervezet intranetén létező tárolóplatformon) ne legyen megőrizve és elérhetővé téve egy, az internetről közvetlenül elérhető tárolóeszközen.

**Azonosító:** 0947.09y2Szervezeti.2 – 09.y **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az SSL-kapcsolat kényszerítését engedélyezni kell a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középen" támadásokkal szembeni védelemben. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>Ha megbízható hitelesítésszolgáltatót használnak (például digitális aláírások és/vagy digitális tanúsítványok kiállítása és fenntartása céljából), a biztonság a teljes teljes tanúsítvány-/aláírás-kezelési folyamatba integrálva és beágyazva történik.

**Azonosító:** 0948.09y2Szervezeti.3 – 09.y **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell az SSL-kapcsolat kényszerítését a MySQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése segít a "középen" támadásokkal szembeni védelemben a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>A kommunikációhoz használt protokollok továbbfejleszthetők az új biztonsági rések kezelése érdekében, és a protokollok frissített verzióit a lehető leghamarabb alkalmazzák.

**Id**: 0949.09y2Organizational.5 - 09.y **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az API-alkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[A függvényalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Az API-alkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A függvényalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A webalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[A webalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>Működési szoftverek vezérlése

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>Csak a jogosult rendszergazdák hajthatnak végre jóváhagyott frissítéseket a szoftverekhez, alkalmazásokhoz és programkódtárakhoz az üzleti követelmények és a kiadás biztonsági következményei alapján.

**Azonosító:** 0605.10h1System.12 - 10.h **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A gépek biztonsági konfigurációjának biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azok a kiszolgálók, amelyek nem tesznek eleget a konfigurált alapkonfigurációnak, a Azure Security Center figyelik. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[A Windows rendszerű gépeknek meg kell felelnie a "Biztonsági beállítások – Naplózás" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |A Windows rendszerű gépeken a megadott Csoportházirend "Biztonsági beállítások – Naplózás" kategóriában kell lennie a naplózási házirend alkategóriái kényszerítéséhez és a leállításhoz, ha a biztonsági naplózás nem naplózhatók. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[A Windows rendszerű gépeknek meg kell felelnie a "Rendszer-naplózási házirendek – Fiókkezelés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |A Windows rendszerű gépeken a megadott Csoportházirend "Rendszer-naplózási házirendek – Fiókkezelés" kategóriában kell lennie az alkalmazás-, biztonsági és felhasználóicsoport-kezelési, valamint egyéb felügyeleti események naplózásához. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>Az alkalmazások és operációs rendszerek használhatósága, biztonsága és hatása az éles környezetben való használat előtt sikeresen tesztelve lett.

**Azonosító:** 0606.10h2System.1 – 10.h **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárolóbiztonsági konfigurációk biztonsági réseit helyre kell helyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Biztonsági rések naplózása olyan gépeken, amelyeken telepítve van a Docker, és javaslatokként jelennek meg a Azure Security Center. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>A szervezet a konfigurációvezérlő programja segítségével tartja fenn az irányítást az összes megvalósított szoftver és annak rendszerdokumentációja, valamint a megvalósított szoftverek és a kapcsolódó rendszerdokumentációk korábbi verzióinak archiválásához.

**Azonosító:** 0607.10h2System.23 – 10.h **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A biztonságos alkalmazások meghatározásához szükséges adaptív alkalmazásvezérlőket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazásvezérlők engedélyezésével meghatározhatja a gépeken futó, ismerten biztonságos alkalmazások listáját, és riasztást küld, ha más alkalmazások is futnak. Ez segít a gépek kártevők elleni védelemében. A szabályok konfigurálási és karbantartásának egyszerűsítése érdekében a Security Center gépi tanulással elemzi az egyes gépeken futó alkalmazásokat, és javaslatot ad az ismert biztonságos alkalmazások listájára. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[A virtuálisgép-méretezési készletek biztonsági beállításainak biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |A virtuálisgép-méretezési készletek operációsrendszer-biztonsági réseit naplózva megvédheti őket a támadásokkal szemben. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>Változásvezérlési eljárások

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>Az alkalmazásrendszerekért felelős vezetők a projekt vagy a támogatási környezet szigorú ellenőrzéséért (biztonságáért) is felelősek, és ellenőrzik, hogy az összes javasolt rendszerváltozást felülvizsgálják-e, és ellenőrzik, hogy nem veszélyeztetik-e a rendszer vagy az operációs környezet biztonságát.

**Azonosító:** 0635.10k1Szervezeti.12 – 10.k **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Rendszer-naplózási házirendek – Részletes követés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű gépeken a megadott Csoportházirend a "Rendszer-naplózási házirendek – Részletes követés" kategóriában kell lennie a DPAPI, a folyamat létrehozása/megszakítása, az RPC-események és a PNP-tevékenység naplózásához. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>A szervezet hivatalosan foglalkozik a céllal, a hatókörrel, a szerepkörökkel, a felelősségekkel, a felügyeleti kötelezettségvállalásokkal, a szervezeti entitások közötti koordinációval és a konfigurációkezelés megfelelőségével (például szabályzatok, szabványok, folyamatok révén).

**Azonosító:** 0636.10k2Szervezeti.1 – 10.k **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Rendszer-naplózási házirendek – Részletes követés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű gépeken a megadott Csoportházirend a "Rendszer-naplózási házirendek – Részletes követés" kategóriában kell lennie a DPAPI, a folyamat létrehozása/megszakítása, az RPC-események és a PNP-tevékenység naplózásához. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>A szervezet kifejlesztett, dokumentált és megvalósított egy konfigurációkezelési tervet az információs rendszerhez.

**Azonosító:** 0637.10k2Szervezeti.2 – 10.k **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Rendszer-naplózási házirendek – Részletes követés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű gépeken a megadott Csoportházirend"Rendszer-naplózási házirendek – Részletes követés" kategóriában kell lennie a DPAPI, a folyamat létrehozása/megszakítása, az RPC-események és a PNP-tevékenységek naplózásához. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>A változások hivatalos ellenőrzése, dokumentálása és kényszerítése az információs rendszerek sérülésének minimalizálása érdekében történik.

**Azonosító:** 0638.10k2Szervezeti.34569 – 10.k **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Rendszer-naplózási házirendek – Részletes követés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű gépeken a megadott Csoportházirend"Rendszer-naplózási házirendek – Részletes követés" kategóriában kell lennie a DPAPI, a folyamat létrehozása/megszakítása, az RPC-események és a PNP-tevékenységek naplózásához. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>A telepítési ellenőrzőlisták és biztonsági rések vizsgálatával érvényesíthető a kiszolgálók, munkaállomások, eszközök és berendezések konfigurációja, és biztosítható, hogy a konfiguráció megfeleljen a minimális normáknak.

**Azonosító:** 0639.10k2Szervezeti.78 – 10.k **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Rendszer-naplózási házirendek – Részletes követés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű gépeken a megadott Csoportházirend a "Rendszer-naplózási házirendek – Részletes követés" kategóriában kell lennie a DPAPI, a folyamat létrehozása/megszakítása, az RPC-események és a PNP-tevékenység naplózásához. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>A fejlesztés kiszervezése esetén a szerződés(ök) tartalmazza a biztonsággal kapcsolatos változásvezérlési eljárásokat, és kifejezetten megköveteli a fejlesztőtől, hogy kövesse nyomon a biztonsági hibákat és hibafeloldásokat a rendszeren, összetevőken vagy szolgáltatásokon belül, és jelentse az eredményeket a szervezet által meghatározott személyzetnek vagy szerepköröknek.

**Azonosító:** 0640.10k2Szervezeti.1012 – 10.k **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Rendszer-naplózási házirendek – Részletes követés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű gépeken a megadott Csoportházirend a "Rendszer-naplózási házirendek – Részletes követés" kategóriában kell lennie a DPAPI, a folyamat létrehozása/megszakítása, az RPC-események és a PNP-tevékenység naplózásához. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>A szervezet nem használ automatikus frissítéseket a kritikus rendszereken.

**AZONOSÍTÓ:** 0641.10k2Szervezeti.11 – 10.k **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Rendszer-naplózási házirendek – Részletes követés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű gépeken a megadott Csoportházirend"Rendszer-naplózási házirendek – Részletes követés" kategóriában kell lennie a DPAPI, a folyamat létrehozása/megszakítása, az RPC-események és a PNP-tevékenységek naplózásához. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>A szervezet az információs rendszer aktuális alapkonfigurációját fejleszti, dokumentumokkal és tartja karban, és szükség szerint ellenőrzi és frissíti az alapkonfigurációt.

**Azonosító:** 0642.10k3Szervezeti.12 – 10.k **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Rendszer-naplózási házirendek – Részletes követés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű gépeken a megadott Csoportházirend"Rendszer-naplózási házirendek – Részletes követés" kategóriában kell lennie a DPAPI, a folyamat létrehozása/megszakítása, az RPC-események és a PNP-tevékenység naplózásához. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>A szervezet (i) az információs rendszerben alkalmazott információs technológiai termékek kötelező konfigurációs beállításait hozza létre és dokumentumokban rögzíti a legújabb biztonsági alapkonfigurációk használatával; (ii) azonosítja, dokumentumokban hagyja jóvá és hagyja jóvá az egyes összetevők kötelezően meghatározott konfigurációs beállításaiból származó kivételeket explicit működési követelmények alapján; és (iii) figyeli és szabályozza a konfigurációs beállítások módosításait a szervezeti házirendek és eljárások szerint.

**Azonosító:** 0643.10k3Szervezeti.3 – 10.k **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Rendszer-naplózási házirendek – Részletes követés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű gépeken a megadott Csoportházirend a "Rendszer-naplózási házirendek – Részletes követés" kategóriában kell lennie a DPAPI, a folyamat létrehozása/megszakítása, az RPC-események és a PNP-tevékenység naplózásához. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>A szervezet automatizált mechanizmusokat alkalmaz a konfigurációs beállítások központi kezelésére, ellenőrzésére és ellenőrzésére; (ii) válasz a hálózati és rendszerbiztonsággal kapcsolatos konfigurációs beállítások jogosulatlan módosítására; és (iii) kényszeríti a hozzáférési korlátozásokat és a kényszerítési műveletek naplózását.

**Azonosító:** 0644.10k3Szervezeti.4 – 10.k **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Rendszer-naplózási házirendek – Részletes követés" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |A Windows rendszerű gépeken a megadott Csoportházirend a "Rendszer-naplózási házirendek – Részletes követés" kategóriában kell lennie a DPAPI, a folyamat létrehozása/megszakítása, az RPC-események és a PNP-tevékenység naplózásához. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>Technikai biztonsági rések ellenőrzése

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>A rendszer azonosítja a műszaki réseket, kiértékeli a kockázatokat, és időben kijavítja őket.

**Azonosító:** 0709.10m1Szervezeti.1 – 10.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell egy sebezhetőségi felmérési megoldást a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |A virtuális gépek naplózásával észleli, hogy támogatott sebezhetőségi felmérési megoldást futtatnak-e. Minden kiberkockázati és biztonsági program egyik fő összetevője a biztonsági rések azonosítása és elemzése. Azure Security Center standard tarifacsomagja többletköltség nélkül tartalmazza a biztonsági rések keresését a virtuális gépeken. Emellett a Security Center automatikusan üzembe helyezheti ezt az eszközt. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[A tárolóbiztonsági konfigurációk biztonsági réseit helyre kell helyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Biztonsági rések naplózása olyan gépeken, amelyeken telepítve van a Docker, és javaslatokként jelennek meg a Azure Security Center. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[A gépek biztonsági konfigurációjának biztonsági réseit helyre kelligazító](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azok a kiszolgálók, amelyek nem tesznek eleget a konfigurált alapkonfigurációnak, a Azure Security Center figyelik javaslatként |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[A virtuálisgép-méretezési készletek biztonsági konfigurációjának biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |A virtuálisgép-méretezési készletek operációsrendszer-biztonsági réseit naplózva megvédheti őket a támadásokkal szemben. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Az SQL-adatbázisok biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Figyelje a sebezhetőségi felmérés vizsgálatának eredményeit és az adatbázis biztonsági réseit javító javaslatokat. |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[A biztonsági rések felmérését engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Naplót SQL Managed Instance összes olyan vizsgálatot, amelyen nincs engedélyezve az ismétlődő sebezhetőségi felmérési vizsgálatok. A sebezhetőségi felmérés felderítheti, nyomon követheti és orvosolhatja az adatbázisok lehetséges biztonsági réseit. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Napló Azure SQL olyan kiszolgálókon, amelyeken nincs engedélyezve az ismétlődő sebezhetőségi felmérési vizsgálatok. A sebezhetőségi felmérés felderítheti, nyomon követheti és orvosolhatja az adatbázisok lehetséges biztonsági réseit. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[A Windows rendszerű gépeknek meg kell felelnie a "Biztonsági beállítások – Microsoft hálózati kiszolgáló" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |A Windows rendszerű gépeken az SMB v1 kiszolgáló letiltására Csoportházirend "Biztonsági beállítások – Microsoft hálózati kiszolgáló" kategóriába kell sorolni a megadott beállítási beállításokat. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>Minden rendszer- és hálózati összetevőhöz létezik egy meglevő konfigurációs szabvány.

**Azonosító:** 0710.10m2Szervezeti.1 – 10.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A biztonsági rések felmérését engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Naplót SQL Managed Instance összes olyan vizsgálatot, amelyen nincs engedélyezve az ismétlődő sebezhetőségi felmérési vizsgálatok. A sebezhetőségi felmérés felderítheti, nyomon követheti és orvosolhatja az adatbázisok lehetséges biztonsági réseit. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>A rendszerekben azonosított biztonsági rések figyelése, felmérése, rangsorolása és orvoslása egy technikai biztonsági réseket kezelő program áll a rendszerben.

**Azonosító:** 0711.10m2Szervezeti.23 – 10.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell egy sebezhetőségi felmérési megoldást a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |A virtuális gépek naplózásával észleli, hogy támogatott sebezhetőségi felmérési megoldást futtatnak-e. Minden kiberkockázati és biztonsági program egyik fő összetevője a biztonsági rések azonosítása és elemzése. Azure Security Center standard tarifacsomagja többletköltség nélkül tartalmazza a biztonsági rések keresését a virtuális gépeken. Emellett a Security Center automatikusan üzembe helyezheti ezt az eszközt. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>A javításokat a rendszer a telepítés előtt teszteli és értékeli.

**Azonosító:** 0713.10m2Szervezeti.5 – 10.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A gépek biztonsági konfigurációjának biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azok a kiszolgálók, amelyek nem tesznek eleget a konfigurált alapkonfigurációnak, a Azure Security Center figyelik. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>A technikai sebezhetőség-kezelési programot negyedévente értékeli ki a rendszer.

**Azonosító:** 0714.10m2Szervezeti.7 – 10.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuálisgép-méretezési készletek biztonsági konfigurációjának biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |A virtuálisgép-méretezési készletek operációsrendszer-biztonsági réseit naplózva megvédheti őket a támadásokkal szemben. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>A rendszerek megfelelő módon vannak megsokosodva (például csak a szükséges és biztonságos szolgáltatásokkal, portokkal és protokollokkal vannak konfigurálva).

**Azonosító:** 0715.10m2Szervezeti.8 – 10.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárolóbiztonsági konfigurációk biztonsági réseit helyre kell helyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Biztonsági rések naplózása olyan gépeken, amelyeken a Docker telepítve van, és javaslatokként jelennek meg a Azure Security Center. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>A szervezet szükség szerint, de nem kevesebb mint egyszer, 365 (365) napon belül, a szervezeti IS-eljárásoknak megfelelően végez biztonsági felülvizsgálatot.

**Azonosító:** 0716.10m3Szervezeti.1 – 10.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az SQL-adatbázisok biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Figyelje a sebezhetőségi felmérés vizsgálatának eredményeit és az adatbázis biztonsági réseit javító javaslatokat. |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>A biztonsági rések vizsgálatának eszközei közé tartozik az információs rendszer biztonsági réseit olvasott biztonsági rések használatra kész frissítése is.

**Azonosító:** 0717.10m3Szervezeti.2 – 10.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuálisgép-méretezési készletek biztonsági konfigurációjának biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |A virtuálisgép-méretezési készletek operációsrendszer-biztonsági réseit naplózva megvédheti őket a támadásokkal szemben. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>A szervezet biztonsági réseket keres az információs rendszerben és az üzemeltetett alkalmazásokban, hogy megállapítsa, havonta (automatikusan) vagy (manuálisan vagy automatikusan) határozzák meg a hibák szervizelésének állapotát, amikor a rendszereket és a hálózati környezeteket potenciálisan érintő új biztonsági réseket azonosít és jelent.

**Azonosító:** 0718.10m3Szervezeti.34 – 10.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A gépek biztonsági konfigurációjának biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azok a kiszolgálók, amelyek nem tesznek eleget a konfigurált alapkonfigurációnak, a Azure Security Center figyelik. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>A szervezet harminc (30) napon belül frissíti az információs rendszerek biztonsági réseit, vagy ha új biztonsági réseket azonosít és jelent.

**Azonosító:** 0719.10m3Szervezeti.5 – 10.m **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A biztonsági rések felmérését engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Naplót SQL Managed Instance összes olyan vizsgálatot, amelyen nincs engedélyezve az ismétlődő sebezhetőségi felmérési vizsgálatok. A sebezhetőségi felmérés felderítheti, nyomon követheti és orvosolhatja az adatbázisok lehetséges biztonsági réseit. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>Üzletmenet-folytonosság és kockázatfelmérés

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>A szervezet azonosítja az üzletmenet folytonosságát igénylő kritikus fontosságú üzleti folyamatokat.

**Azonosító:** 1634.12b1Szervezeti.1 – 12.b **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Vészhelyreállítás nélküli virtuális gépek naplózása konfigurálva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Naplóhatja a vészhelyreállítás konfigurálásával nem konfigurált virtuális gépeket. További információ a vészhelyreállításról: [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>Az üzletmenet folytonosságának információbiztonsági szempontjai az olyan események (vagy események sorozata) azonosításán alapulnak, amelyek a vállalat kritikus üzleti folyamatainak megszakadását okozhatják (például berendezések meghibásodása, emberi hibák, lopás, tűz, természeti katasztrófák). (ii) ezt egy kockázatfelmérés követi, amely meghatározza az ilyen megszakítások valószínűségét és hatását az idő, a kárskála és a helyreállítási idő szempontjából; (iii) a kockázatértékelés eredményei alapján kidolgoznak egy üzletmenet-folytonossági stratégiát, amely meghatározza az üzletmenet folytonosságának általános megközelítését; és (iv) a stratégia létrehozása után a vezetőség biztosítja az ajánlást, valamint a stratégia megvalósításához létrehozott és támogatott tervet.

**AZONOSÍTÓ:** 1635.12b1Szervezeti.2 – 12.b **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Key Vault Managed HSM-nek engedélyeznie kell a véglegesen kiürítés elleni védelmet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Egy felügyelt HSM Azure Key Vault törlése végleges adatvesztéshez vezethet. A szervezet egy rosszindulatú belső felhasználója törölheti és kiürítheti Azure Key Vault Managed HSM-et. A végleges törlés elleni védelem megvédi a belső támadásokkal szemben azáltal, hogy kötelező megőrzési időszakot kényszerít ki a managed HSM Azure Key Vault törlése esetén. A szervezeten vagy a Microsofton belül senki sem törölheti a felügyelt HSM Azure Key Vault a felügyelt HSM-et a törlési megőrzési időszak alatt. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[A kulcstartóknál engedélyezni kell a véglegesen kiürítés elleni védelmet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A kulcstartó rosszindulatú törlése végleges adatvesztéshez vezethet. A szervezet egy rosszindulatú belső felhasználója törölheti és kiürítheti a kulcstartókat. A végleges törlés elleni védelem úgy védi a belső támadásokat, hogy kötelező megőrzési időszakot kényszerít ki a törölt kulcstartók számára. A szervezeten vagy a Microsofton belül senki sem fogja tudni véglegesen törölni a kulcstartókat a soft delete megőrzési időszak alatt. |Naplózás, Megtagadás, Letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>Az üzleti hatás elemzésével kiértékelheti a katasztrófák, a biztonsági hibák, a szolgáltatáskimaradás és a szolgáltatás rendelkezésre állásának következményeit.

**Azonosító:** 1637.12b2Szervezeti.2 – 12.b **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Windows rendszerű gépeknek meg kell felelnie a "Biztonsági beállítások – Helyreállítási konzol" követelményeinek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |A Windows rendszerű gépeken a megadott Csoportházirend "Biztonsági beállítások – Helyreállítási konzol" kategóriában kell lennie a hajlékonylemez másolásának és az összes meghajtó és mappa elérésének engedélyezéséhez. Ez a szabályzat megköveteli a vendégkonfiguráció előfeltételeinek központi telepítését a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>Az üzletmenet-folytonossági kockázatfelmérések (i) évente, az üzleti erőforrások és folyamatok tulajdonosainak teljes közreműködésével vannak végrehajtva; (ii) fontolja meg az összes üzleti folyamatot, és nem korlátozódik az információs eszközökre, hanem az információbiztonsággal kapcsolatos eredményeket is tartalmazza; és (iii) azonosítja, számszerűsíti és rangsorolja a szervezet számára releváns kulcsfontosságú üzleti célkitűzésekkel és kritériumokkal kapcsolatos kockázatokat, beleértve a kritikus erőforrásokat, a megszakítások hatásait, a megengedett üzemkimaradásokat és a helyreállítási prioritásokat.

**Azonosító:** 1638.12b2Szervezeti.345 – 12.b **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Vészhelyreállítás nélkül konfigurált virtuális gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Naplóhatja a vészhelyreállítás konfigurálásával nem konfigurált virtuális gépeket. További információ a vészhelyreállításról: [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> Az egyes Azure Policy a különböző országos Azure Government eltérőek lehetnek.

## <a name="next-steps"></a>Következő lépések

További cikkek a Azure Policy:

- [A jogszabályi megfelelőség](../concepts/regulatory-compliance.md) áttekintése.
- Lásd a [kezdeményezésdefiníció szerkezetét.](../concepts/initiative-definition-structure.md)
- Tekintsen át további példákat [a következő Azure Policy példákban:](./index.md).
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Ismerje meg, [hogyan szervizelődnek a nem megfelelő erőforrások.](../how-to/remediate-resources.md)
