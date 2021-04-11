---
title: A CIS Microsoft Azure alapjaira vonatkozó szabályozási megfelelőségi részletek
description: A CIS Microsoft Azure alapjaival kapcsolatos alapszintű szabályozási megfelelőségi előírásoknak. Minden vezérlő egy vagy több olyan Azure Policy-definícióra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 03/31/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 1d21d04fe4347a2ff7735045e786520c106309c7
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091752"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-110-regulatory-compliance-built-in-initiative"></a>A CIS Microsoft Azure alapjaival kapcsolatos alapkövetelmények

A következő cikk azt ismerteti, hogyan jelennek meg a Azure Policy szabályozási megfelelőségi kezdeményezés definíciója a **megfelelőségi tartományokhoz** **és a** CIS Microsoft Azure founds benchmark 1.1.0-ben.
További információ erről a megfelelőségi szabványról: [CIS Microsoft Azure founds benchmark 1.1.0](https://www.cisecurity.org/benchmark/azure/). A _tulajdonjog_ megismeréséhez tekintse meg a [Azure Policy házirend-definíció](../concepts/definition-structure.md#type) és [a megosztott felelősség a felhőben](../../../security/fundamentals/shared-responsibility.md)című témakört.

A következő leképezések a **CIS Microsoft Azure founds benchmark 1.1.0** -vezérlőkre vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy adott **megfelelőségi tartományra** ugorhat. Számos vezérlő [Azure Policy](../overview.md) kezdeményezési definícióval van implementálva. A teljes kezdeményezési definíció áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot.
Ezután keresse meg és válassza ki a **CIS Microsoft Azure alapítványok benchmark v 1.1.0** szabályozási megfelelőségének beépített kezdeményezési definícióját.

Ez a beépített kezdeményezés a [CIS Microsoft Azure founds teljesítményteszt-tervezet](../../blueprints/samples/cis-azure-1-1-0.md)részeként van telepítve.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../overview.md) -definícióhoz vannak társítva.
> Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../how-to/get-compliance-data.md) ; azonban gyakran nem egy-az-egyhez vagy egy teljes egyezés egy vezérlőelem és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a szabályzat-definícióknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tartományok, a vezérlők és a Azure Policy-definíciók közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0.json).

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Győződjön meg arról, hogy a többtényezős hitelesítés engedélyezve van az összes privilegizált felhasználó számára

**Azonosító**: a CIS Azure 1,1 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A fiókok vagy erőforrások megszegésének megelőzése érdekében minden írási jogosultsággal rendelkező előfizetési fiók esetében engedélyezni kell a Multi-Factor Authentication (MFA) használatát. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Az Multi-Factor Authentication (MFA) minden tulajdonosi engedélyekkel rendelkező előfizetési fiók esetében engedélyezni kell a fiókok vagy erőforrások megszegésének megakadályozására. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Győződjön meg arról, hogy a többtényezős hitelesítés engedélyezve van az összes nem Kiemelt felhasználó számára

**Azonosító**: a CIS Azure 1,2 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Az olvasási jogosultsággal rendelkező összes előfizetés-fiókhoz engedélyezni kell Multi-Factor Authentication (MFA) használatát a fiókok vagy erőforrások megszegésének megelőzése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>Győződjön meg arról, hogy nincsenek vendég felhasználók

**Azonosító**: a CIS Azure 1,3 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |A nem figyelt hozzáférés megakadályozása érdekében el kell távolítani a tulajdonosi engedélyekkel rendelkező külső fiókokat az előfizetésből. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Az olvasási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből a nem figyelt hozzáférés megakadályozása érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Az írási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből a nem figyelt hozzáférés megakadályozása érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Győződjön meg arról, hogy nem jön létre egyéni előfizetés-tulajdonosi szerepkör

**Azonosító**: a CIS Azure 1,23 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az egyéni előfizetés tulajdonosi szerepkörei nem létezhetnek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Ez a szabályzat biztosítja, hogy egyetlen egyéni előfizetés-tulajdonosi szerepkör sem létezik. |Naplózás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-standard-pricing-tier-is-selected"></a>Győződjön meg arról, hogy a standard szintű díjszabás van kiválasztva

**Azonosító**: a CIS Azure 2,1 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az Azure Defender for App Service engedélyezése szükséges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Az Azure Defender for App Service kihasználja a felhő méretezését, és azt, hogy az Azure hogyan rendelkezik felhőalapú szolgáltatóként, hogy figyelje a gyakori webalkalmazás-támadásokat. |AuditIfNotExists, letiltva |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Az Azure Defender for Azure SQL Database-kiszolgálókat engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Az SQL-hez készült Azure Defender lehetővé teszi az adatbázis-biztonsági rések felszínre való felhasználhatóságát, valamint az SQL-adatbázisok fenyegetését jelző rendellenes tevékenységek észlelését, valamint a bizalmas adatok felderítését és besorolását. |AuditIfNotExists, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[Engedélyezve kell lennie az Azure Defender for Container nyilvántartójának](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |Az Azure Defender for Container-beállításjegyzékek biztonsági réseket biztosítanak az elmúlt 30 nap során húzott rendszerképekről, leküldve a regisztrációs adatbázisba vagy importálnak, és elérhetővé teszik a képek részletes megállapításait. |AuditIfNotExists, letiltva |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Az Azure Defender for Key Vault engedélyezése szükséges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Az Azure Defender for Key Vault további védelmi és biztonsági intelligencia-réteget biztosít a Key Vault-fiókok eléréséhez vagy kihasználásához szükséges szokatlan és potenciálisan ártalmas kísérletek észlelésével. |AuditIfNotExists, letiltva |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Engedélyezni kell az Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |A Kubernetes készült Azure Defender valós idejű veszélyforrások elleni védelmet biztosít a tároló környezetek számára, és riasztásokat hoz létre a gyanús tevékenységekhez. |AuditIfNotExists, letiltva |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Az Azure Defender for Servers szolgáltatást engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |A kiszolgálókhoz készült Azure Defender valós idejű veszélyforrások elleni védelmet biztosít a kiszolgálói munkaterhelések számára, és megerősítő javaslatokat hoz létre, valamint riasztásokat küld a gyanús tevékenységekről. |AuditIfNotExists, letiltva |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Engedélyezni kell az Azure Defender for SQL-kiszolgálókat a gépeken.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Az SQL-hez készült Azure Defender lehetővé teszi az adatbázis-biztonsági rések felszínre való felhasználhatóságát, valamint az SQL-adatbázisok fenyegetését jelző rendellenes tevékenységek észlelését, valamint a bizalmas adatok felderítését és besorolását. |AuditIfNotExists, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[Engedélyezni kell az Azure Defender for Storage szolgáltatást](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Az Azure Defender for Storage szolgáltatás szokatlan és potenciálisan ártalmas kísérletek észlelését teszi lehetővé a Storage-fiókok eléréséhez vagy kiaknázásához. |AuditIfNotExists, letiltva |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Győződjön meg arról, hogy a figyelési ügynök automatikus kiépítés értéke "on" értékre van állítva

**Azonosító**: a CIS Azure 2,2 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az Log Analytics ügynök automatikus üzembe helyezését engedélyezni kell az előfizetésen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |A biztonsági rések és fenyegetések figyeléséhez Azure Security Center adatokat gyűjt az Azure-beli virtuális gépekről. Az adatokat a korábban a Microsoft monitoring Agent (MMA) néven ismert Log Analytics ügynök gyűjti, amely különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat olvas be a gépről, és az adatokat az Log Analytics munkaterületre másolja az elemzéshez. Javasoljuk, hogy az automatikus kiépítés engedélyezésével automatikusan telepítse az ügynököt az összes támogatott Azure-beli virtuális gépre és a létrehozott újakra. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>Az ASC alapértelmezett házirend-beállítás "rendszerfrissítések figyelése" beállítása nem "Letiltva"

**Azonosító**: a CIS Azure 2,3 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A rendszerfrissítéseket telepíteni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A kiszolgálókon hiányzó biztonsági rendszerfrissítéseket a Azure Security Center a javaslatok szerint figyeli |AuditIfNotExists, letiltva |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>Az ASC alapértelmezett házirend beállítása "az operációs rendszer sebezhetőségének figyelése" nem "Letiltva"

**Azonosító**: a CIS Azure 2,4 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azokat a kiszolgálókat, amelyek nem felelnek meg a beállított alapkonfigurációnak, Azure Security Center a javaslatok szerint figyelik |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>Az ASC alapértelmezett házirend-beállításának biztosítása a "figyelő Endpoint Protection" nem "Letiltva"

**Azonosító**: a CIS Azure 2,5 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hiányzó Endpoint Protection figyelése Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |A telepített Endpoint Protection ügynök nélküli kiszolgálókat javaslatokként Azure Security Center figyeli |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>Ügyeljen arra, hogy a "lemez titkosításának figyelése" beállítás ne legyen letiltva az ASC alapértelmezett házirendjében

**Azonosító**: a CIS Azure 2,6 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A lemezes titkosítást a virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Az engedélyezett lemezes titkosítás nélküli virtuális gépeket javaslatokként Azure Security Center figyeli. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>Az ASC alapértelmezett házirend-beállítás "hálózati biztonsági csoportok figyelése" beállítása nem "Letiltva"

**Azonosító**: a CIS Azure 2,7 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a lehetséges támadási felület csökkentése érdekében. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>Az ASC alapértelmezett házirend-beállításának beállítása "a következő generációs tűzfal (NGFW) figyelésének engedélyezése" nem "Letiltva"

**Azonosító**: a CIS Azure 2,9 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG) való hozzáférés korlátozásával védi a virtuális gépeket a lehetséges fenyegetésekkel szemben. További információ a forgalom szabályozásáról a NSG-on [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Az alhálózatokat hálózati biztonsági csoporttal kell társítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |A hálózati biztonsági csoporttal (NSG) való hozzáférés korlátozásával védi az alhálózatot az esetleges fenyegetésekkel szemben. A NSG olyan Access Control lista (ACL) szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják az alhálózat felé irányuló hálózati forgalmat. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>Az ASC alapértelmezett házirend-beállítás "a sebezhetőségi felmérés figyelése" nem "Letiltva"

**Azonosító**: a CIS Azure 2,10 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A biztonsági rések felmérésére szolgáló megoldást engedélyezni kell a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Naplózza a virtuális gépeket annak észlelésére, hogy támogatott sebezhetőségi felmérési megoldást futtatnak-e. A biztonsági rések azonosítása és elemzése minden számítógépes kockázati és biztonsági program alapvető összetevője. A Azure Security Center Standard díjszabási szintje a virtuális gépek sebezhetőségi vizsgálatát is magában foglalja, többletköltség nélkül. Emellett a Security Center automatikusan üzembe helyezheti ezt az eszközt. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>Ügyeljen arra, hogy a "JIT hálózati hozzáférés figyelése" beállítás ne legyen "Letiltva".

**Azonosító**: a CIS Azure 2,12 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehető legrövidebb időn belül (JIT) való hozzáférést a Azure Security Center a javaslatok szerint figyeli |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>Az ASC alapértelmezett házirend-beállításának beállítása "az adaptív alkalmazás engedélyezési listájának figyelése" nem "Letiltva"

**Azonosító**: a CIS Azure 2,13 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazás-vezérlőelemek lehetővé teszik a gépen futó ismert, biztonságos alkalmazások listájának meghatározását, és riasztást küld, ha más alkalmazások futnak. Ez segít megerősíteni a gépeket a kártevők ellen. A szabályok konfigurálásának és karbantartásának egyszerűbbé tétele érdekében Security Center a gépi tanulás segítségével elemzi az egyes gépeken futó alkalmazásokat, és javaslatot tesz az ismert alkalmazások listájára. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>Az ASC alapértelmezett házirend-beállításának "az SQL-naplózás figyelése" beállítása nem "Letiltva"

**Azonosító**: a CIS Azure 2,14 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL Server naplózását engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A SQL Server naplózását engedélyezni kell ahhoz, hogy nyomon kövessék az adatbázis-tevékenységeket a kiszolgálón lévő összes adatbázisban, és azokat egy naplóba mentse. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>Az ASC alapértelmezett szabályzatának "az SQL-titkosítás figyelése" beállításának biztosítása nem "Letiltva"

**Azonosító**: a CIS Azure 2,15 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Az átlátható adattitkosítást engedélyezni kell a REST-alapú adatok védelme és a megfelelőségi követelmények teljesítése érdekében |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>Győződjön meg arról, hogy a "biztonsági kapcsolattartási e-mailek" be van állítva

**Azonosító**: a CIS Azure 2,16 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A biztonsági problémákhoz az előfizetéseknek kapcsolattartó e-mail címmel kell rendelkezniük](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Annak biztosítása érdekében, hogy a szervezet érintett személyei értesítést kapjanak, ha az egyik előfizetésnél potenciális biztonsági rést jelentenek, állítson be egy biztonsági kapcsolattartót az e-mailes értesítések fogadásához Security Centerról. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>Győződjön meg arról, hogy az "e-mail-értesítések küldése a nagy súlyosságú riasztásokhoz" beállítás értéke "on"

**Azonosító**: a CIS Azure 2,18 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A magas súlyosságú riasztások e-mail-értesítését engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Annak biztosítása érdekében, hogy a szervezet érintett személyei értesítést kapjanak, ha az egyik előfizetésnél potenciális biztonsági rést jelentenek, engedélyezze az e-mailes értesítéseket a nagy súlyosságú riasztásokhoz Security Center. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>Győződjön meg arról, hogy az "e-mailek küldése az előfizetés tulajdonosainak is" beállítás értéke "on"

**Azonosító**: a CIS Azure 2,19 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Engedélyezni kell az e-mailes értesítést a nagy súlyosságú riasztások előfizetésének tulajdonosának](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |Annak biztosítása érdekében, hogy az előfizetéshez tartozó tulajdonosok értesítést kapjanak, ha az előfizetésében potenciális biztonsági probléma merül fel, az e-mailes értesítéseket a nagy súlyosságú riasztásokhoz tartozó Security Center. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>Storage-fiókok

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Győződjön meg arról, hogy a "biztonságos átvitel szükséges" beállítás értéke "enabled" (engedélyezve)

**Azonosító**: a CIS Azure 3,1 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |A biztonságos átvitel naplózási követelménye a Storage-fiókban. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Győződjön meg arról, hogy a "nyilvános hozzáférési szint" a blob-tárolók magánjellegűre van beállítva

**Azonosító**: a CIS Azure 3,6 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Storage-fiók nyilvános hozzáférését nem szabad engedélyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Az Azure Storage szolgáltatásban tárolt tárolók és Blobok névtelen nyilvános olvasási hozzáférése kényelmes megoldást kínál az adatmegosztásra, de biztonsági kockázatokat jelenthet. Ha meg szeretné akadályozni, hogy a névtelen hozzáférések illetéktelenek legyenek, a Microsoft azt javasolja, hogy ne kelljen a Storage-fiókhoz hozzáférni, kivéve, ha a forgatókönyv megköveteli. |naplózás, megtagadás, letiltva |[2.0.1 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Győződjön meg arról, hogy a Storage-fiókok alapértelmezett hálózati hozzáférési szabálya megtagadás értékű

**Azonosító**: a CIS Azure 3,7 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Storage-fiókoknak korlátoznia kell a hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |A Storage-fiókok hálózati hozzáférését korlátozni kell. Konfigurálja a hálózati szabályokat úgy, hogy csak az engedélyezett hálózatokból származó alkalmazások férhessenek hozzá a Storage-fiókhoz. Az adott internetről vagy helyszíni ügyfelekről érkező kapcsolatok engedélyezéséhez az adott Azure-beli virtuális hálózatoktól vagy a nyilvános internetes IP-címtartományok felé irányuló adatforgalom megadható. |Naplózás, megtagadás, letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Győződjön meg arról, hogy a megbízható Microsoft-szolgáltatások engedélyezve vannak a Storage-fiók eléréséhez

**Azonosító**: a CIS Azure 3,8 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Storage-fiókoknak engedélyezniük kell a megbízható Microsoft-szolgáltatások elérését](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Egyes, a Storage-fiókokkal kommunikáló Microsoft-szolgáltatások olyan hálózatokból működnek, amelyek nem férhetnek hozzá a hálózati szabályokon keresztül. Annak érdekében, hogy az ilyen típusú szolgáltatás a kívánt módon működjön, engedélyezze a megbízható Microsoft-szolgáltatások készletét a hálózati szabályok megkerülése érdekében. Ezek a szolgáltatások erős hitelesítést alkalmaznak a Storage-fiók eléréséhez. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>Adatbázis-szolgáltatások

### <a name="ensure-that-auditing-is-set-to-on"></a>Győződjön meg arról, hogy a naplózás beállítás értéke "on"

**Azonosító**: a CIS Azure 4,1 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL Server naplózását engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A SQL Server naplózását engedélyezni kell ahhoz, hogy nyomon kövessék az adatbázis-tevékenységeket a kiszolgálón lévő összes adatbázisban, és azokat egy naplóba mentse. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>Győződjön meg arról, hogy a "AuditActionGroups" az SQL Server rendszerhez tartozó naplózási szabályzatban megfelelően van beállítva

**Azonosító**: a CIS Azure 4,2 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL naplózási beállításainak Action-Groups konfigurálva kell lennie a kritikus tevékenységek rögzítéséhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |A AuditActionsAndGroups tulajdonságnak legalább SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP, FAILED_DATABASE_AUTHENTICATION_GROUP, BATCH_COMPLETED_GROUP kell tartalmaznia, hogy alapos naplózást biztosítson |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Győződjön meg arról, hogy a naplózás megőrzése "nagyobb, mint 90 nap"

**Azonosító**: a CIS Azure 4,3 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Storage-fiók célhelyét naplózó SQL-kiszolgálókat 90 napos adatmegőrzéssel vagy magasabb értékkel kell konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Az incidensek vizsgálatához javasoljuk, hogy állítsa be az adatmegőrzést a SQL Server naplózását a Storage-fiók célhelyére legalább 90 napig. Győződjön meg arról, hogy a megfelelő adatmegőrzési szabályok megfelelnek azon régiók számára, amelyekben a rendszert üzemelteti. Ez esetenként szükséges a szabályozási előírásoknak való megfeleléshez. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>Győződjön meg arról, hogy az SQL Server "on" értékre van állítva a "speciális adatbiztonság"

**Azonosító**: a CIS Azure 4,4 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A speciális adatbiztonságot engedélyezni kell a felügyelt SQL-példányon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Minden felügyelt SQL-példány naplózása speciális adatbiztonság nélkül. |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |SQL serverek naplózása speciális adatbiztonság nélkül |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Győződjön meg arról, hogy Azure Active Directory rendszergazda konfigurálva van

**Azonosító**: a CIS Azure 4,8 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Az Azure AD-hitelesítés engedélyezéséhez az SQL Server Azure Active Directory-rendszergazdájának kiépítés naplózása. Az Azure AD-hitelesítés lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Győződjön meg arról, hogy az adattitkosítás "on" értékre van állítva SQL Database

**Azonosító**: a CIS Azure 4,9 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Az átlátható adattitkosítást engedélyezni kell a REST-alapú adatok védelme és a megfelelőségi követelmények teljesítése érdekében |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>Győződjön meg arról, hogy az SQL Server TDE-oltalmazója titkosítva van a BYOK (saját kulcs használata)

**Azonosító**: a CIS Azure 4,10 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SQL felügyelt példányainak az ügyfelek által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |A transzparens adattitkosítás (TDE) a saját kulccsal való implementálása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, nagyobb biztonságot nyújt egy HSM-alapú külső szolgáltatással, valamint a feladatok elkülönítését. Ez a javaslat a kapcsolódó megfelelőségi követelményeknek megfelelő szervezetekre vonatkozik. |AuditIfNotExists, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Az SQL-kiszolgálóknak az ügyfél által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |A transzparens adattitkosítás (TDE) a saját kulccsal való implementálása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, nagyobb biztonságot nyújt egy HSM-alapú külső szolgáltatással, valamint a feladatok elkülönítésének előléptetését. Ez a javaslat a kapcsolódó megfelelőségi követelményeknek megfelelő szervezetekre vonatkozik. |AuditIfNotExists, letiltva |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Győződjön meg arról, hogy az "SSL-kapcsolat érvényesítése" beállítás értéke "ENABLED" a MySQL adatbázis-kiszolgáló számára

**Azonosító**: a CIS Azure 4,11 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Győződjön meg arról, hogy a (z) "log_checkpoints" kiszolgálói paraméter "ON" értékre van beállítva a PostgreSQL adatbázis-kiszolgálóhoz

**Azonosító**: a CIS Azure 4,12 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A naplózási ellenőrzőpontokat engedélyezni kell a PostgreSQL adatbázis-kiszolgálóinak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Ez a házirend segítséget nyújt a PostgreSQL-adatbázisok ellenőrzésében a környezetben, log_checkpoints beállítás engedélyezése nélkül. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Győződjön meg arról, hogy az "SSL-kapcsolat érvényesítése" beállítás értéke "ENABLED" a PostgreSQL adatbázis-kiszolgáló számára

**Azonosító**: a CIS Azure 4,13 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Ez a konfiguráció azt kényszeríti, hogy az SSL mindig engedélyezve van az adatbázis-kiszolgáló eléréséhez. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Győződjön meg arról, hogy a (z) "log_connections" kiszolgálói paraméter "ON" értékre van beállítva a PostgreSQL adatbázis-kiszolgálóhoz

**Azonosító**: a CIS Azure 4,14 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A naplózási kapcsolatokat engedélyezni kell a PostgreSQL adatbázis-kiszolgálóinak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Ez a házirend segítséget nyújt a PostgreSQL-adatbázisok ellenőrzésében a környezetben, log_connections beállítás engedélyezése nélkül. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Győződjön meg arról, hogy a (z) "log_disconnections" kiszolgálói paraméter "ON" értékre van beállítva a PostgreSQL adatbázis-kiszolgálóhoz

**Azonosító**: a CIS Azure 4,15 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A leválasztásokat a PostgreSQL adatbázis-kiszolgálóihoz kell naplózni.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Ez a szabályzat segít a PostgreSQL-adatbázisok ellenőrzésében a környezetben log_disconnections engedélyezése nélkül. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Győződjön meg arról, hogy a (z) "connection_throttling" kiszolgálói paraméter "ON" értékre van beállítva a PostgreSQL adatbázis-kiszolgálóhoz

**Azonosító**: a CIS Azure 4,17 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A kapcsolatok szabályozását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Ezzel a szabályzattal a környezetében lévő PostgreSQL-adatbázisok naplózása nem engedélyezett a kapcsolatok szabályozása nélkül. Ez a beállítás lehetővé teszi, hogy az IP-címek ideiglenes kapcsolatának szabályozása túl sok érvénytelen jelszó-bejelentkezési hiba esetén. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

### <a name="ensure-that-a-log-profile-exists"></a>Győződjön meg arról, hogy létezik egy log-profil

**Azonosító**: CIS Azure 5.1.1 **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az Azure-előfizetéseknek log-profillal kell rendelkezniük a műveletnapló számára](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |Ez a szabályzat biztosítja, hogy a naplózási profil engedélyezve legyen-e a tevékenység naplófájljainak exportálásához. Ha nincs olyan napló-profil, amely a naplók egy Storage-fiókba vagy egy Event hubhoz való exportálására van létrehozva. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>Győződjön meg arról, hogy a tevékenység naplójának megőrzése 365 nap vagy nagyobb

**Azonosító**: CIS Azure 5.1.2 **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A tevékenység naplóját legalább egy évig meg kell őrizni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |Ez a házirend naplózza a tevékenységet, ha az adatmegőrzés nincs 365 vagy örökre értékre beállítva (a megőrzési napok értéke 0). |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>Ellenőrizze, hogy a naplózási profil rögzíti-e az összes tevékenységet

**Azonosító**: CIS Azure 5.1.3 **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor log-profilnak össze kell gyűjtenie a naplókat a "Write", "Delete" és "Action" kategóriákba.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Ez a szabályzat biztosítja, hogy a log-profil a "Write", a "Delete" és a "Action" kategóriákba gyűjti a naplókat. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>Győződjön meg arról, hogy a napló profilja rögzíti a tevékenységek naplóit minden régióhoz, beleértve a globálisat is

**Azonosító**: a CIS Azure 5.1.4 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor az összes régióból gyűjti a tevékenység naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Ez a házirend naplózza a Azure Monitor log profilt, amely nem exportál tevékenységeket az összes Azure által támogatott régióból, beleértve a globálisat is. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Győződjön meg arról, hogy a tevékenység naplóit tároló tároló nem nyilvánosan elérhető

**Azonosító**: a CIS Azure 5.1.5 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Storage-fiók nyilvános hozzáférését nem szabad engedélyezni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Az Azure Storage szolgáltatásban tárolt tárolók és Blobok névtelen nyilvános olvasási hozzáférése kényelmes megoldást kínál az adatmegosztásra, de biztonsági kockázatokat jelenthet. Ha meg szeretné akadályozni, hogy a névtelen hozzáférések illetéktelenek legyenek, a Microsoft azt javasolja, hogy ne kelljen a Storage-fiókhoz hozzáférni, kivéve, ha a forgatókönyv megköveteli. |naplózás, megtagadás, letiltva |[2.0.1 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Győződjön meg arról, hogy a tárolót tartalmazó Storage-fiók BYOK van titkosítva (használja a saját kulcsát)

**Azonosító**: a CIS Azure 5.1.6 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A BYOK rendelkező tárolót tartalmazó Storage-fióknak titkosítottnak kell lennie.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Ez a házirend azt naplózza, hogy a BYOK tartalmazó tárolót tartalmazó Storage-fiók titkosítva van-e. A házirend csak abban az esetben működik, ha a Storage-fiók a tevékenység naplóinak tervezése alapján ugyanazon az előfizetésen található. Az Azure Storage-titkosításról további információt itt találhat [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) .  |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Győződjön meg arról, hogy az Azure kulcstartó naplózása engedélyezve van

**Azonosító**: a CIS Azure 5.1.7 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Key Vault felügyelt HSM erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |Ha egy biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága során szeretné újból létrehozni a tevékenységek nyomvonalait, érdemes lehet naplózni az erőforrás-naplóknak a felügyelt HSM való engedélyezésével. Kérjük, kövesse az alábbi utasításokat: [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging) . |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[A Key Vault erőforrás-naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Az erőforrás-naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újra létrehozza a vizsgálat céljára szolgáló tevékenység-nyomvonalat, amikor biztonsági incidens következik be, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, letiltva |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Győződjön meg arról, hogy a tevékenység naplójának riasztása létezik a szabályzat-hozzárendelés létrehozásakor

**Azonosító**: CIS Azure 5.2.1 **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Egy adott házirend-művelethez léteznie kell egy műveletnapló-riasztásnak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Ez a házirend a megadott házirend-műveleteket naplózza, és nincsenek konfigurálva műveletnapló-riasztások. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Győződjön meg arról, hogy a hálózati biztonsági csoport létrehozása vagy frissítése esetén a műveletnapló riasztása létezik

**Azonosító**: a CIS Azure 5.2.2 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A konkrét felügyeleti műveletekre vonatkozóan léteznie kell egy műveletnapló-riasztásnak.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a házirend olyan konkrét felügyeleti műveleteket naplóz, amelyeken nincs konfigurálva műveletnapló-riasztás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Győződjön meg arról, hogy a hálózati biztonsági csoport törlésére vonatkozó műveletnapló-riasztás létezik

**Azonosító**: CIS Azure 5.2.3 **tulajdonjog**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A konkrét felügyeleti műveletekre vonatkozóan léteznie kell egy műveletnapló-riasztásnak.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a házirend olyan konkrét felügyeleti műveleteket naplóz, amelyeken nincs konfigurálva műveletnapló-riasztás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Győződjön meg arról, hogy a hálózati biztonsági csoport létrehozásához vagy frissítéséhez tartozó műveletnapló riasztása létezik

**Azonosító**: a CIS Azure 5.2.4 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A konkrét felügyeleti műveletekre vonatkozóan léteznie kell egy műveletnapló-riasztásnak.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a házirend olyan konkrét felügyeleti műveleteket naplóz, amelyeken nincs konfigurálva műveletnapló-riasztás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Győződjön meg arról, hogy a hálózati biztonsági csoport törlése szabályhoz tartozó műveletnapló-riasztás létezik

**Azonosító**: a CIS Azure 5.2.5 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A konkrét felügyeleti műveletekre vonatkozóan léteznie kell egy műveletnapló-riasztásnak.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a házirend olyan konkrét felügyeleti műveleteket naplóz, amelyeken nincs konfigurálva műveletnapló-riasztás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Győződjön meg arról, hogy a biztonsági megoldás létrehozása vagy frissítése során a tevékenység naplójának riasztása létezik

**Azonosító**: a CIS Azure 5.2.6 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Adott biztonsági műveletekre vonatkozóan léteznie kell egy műveletnapló-riasztásnak.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ez a házirend azokat a biztonsági műveleteket naplózza, amelyeken nincs konfigurálva műveletnapló-riasztás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Győződjön meg arról, hogy a biztonsági megoldás törlésére vonatkozó riasztás létezik

**Azonosító**: a CIS Azure 5.2.7 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Adott biztonsági műveletekre vonatkozóan léteznie kell egy műveletnapló-riasztásnak.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ez a házirend azokat a biztonsági műveleteket naplózza, amelyeken nincs konfigurálva műveletnapló-riasztás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Győződjön meg arról, hogy a műveletnapló riasztása létezik a SQL Server tűzfalszabály létrehozásához vagy frissítéséhez vagy törléséhez

**Azonosító**: a CIS Azure 5.2.8 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A konkrét felügyeleti műveletekre vonatkozóan léteznie kell egy műveletnapló-riasztásnak.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a házirend olyan konkrét felügyeleti műveleteket naplóz, amelyeken nincs konfigurálva műveletnapló-riasztás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>Győződjön meg arról, hogy a frissítési biztonsági házirendhez tartozó műveletnapló-riasztás létezik

**Azonosító**: a CIS Azure 5.2.9 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Adott biztonsági műveletekre vonatkozóan léteznie kell egy műveletnapló-riasztásnak.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ez a házirend azokat a biztonsági műveleteket naplózza, amelyeken nincs konfigurálva műveletnapló-riasztás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>Hálózatkezelés

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Győződjön meg arról, hogy az RDP-hozzáférés korlátozva van az internetről

**Azonosító**: a CIS Azure 6,1 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az internetről való RDP-hozzáférést le kell tiltani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Ez a házirend minden olyan hálózati biztonsági szabályt naplóz, amely engedélyezi az internetről az RDP-hozzáférést |Naplózás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Győződjön meg arról, hogy az SSH-hozzáférés korlátozva van az internetről

**Azonosító**: a CIS Azure 6,2 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az internetről érkező SSH-hozzáférést le kell tiltani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Ez a házirend minden olyan hálózati biztonsági szabályt naplóz, amely engedélyezi az internetről érkező SSH-hozzáférést |Naplózás, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Győződjön meg arról, hogy a Network Watcher engedélyezve van

**Azonosító**: a CIS Azure 6,5 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher egy regionális szolgáltatás, amely lehetővé teszi az Azure-ban, a-ben és az-ban lévő hálózati forgatókönyvek szintjének figyelését és diagnosztizálását. A forgatókönyvek szintjének figyelése lehetővé teszi a hibák diagnosztizálását a végpontok közötti hálózati szintű nézetben. A Network Watcher elérhető hálózati diagnosztikai és vizualizációs eszközök segítségével megismerheti, diagnosztizálhatja és elemezheti a hálózatát az Azure-ban. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-that-os-disk-are-encrypted"></a>Győződjön meg arról, hogy az operációs rendszer lemeze titkosítva van

**Azonosító**: a CIS Azure 7,1 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A lemezes titkosítást a virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Az engedélyezett lemezes titkosítás nélküli virtuális gépeket javaslatokként Azure Security Center figyeli. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>Győződjön meg arról, hogy az adatlemezek titkosítva vannak

**Azonosító**: a CIS Azure 7,2 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A lemezes titkosítást a virtuális gépeken kell alkalmazni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Az engedélyezett lemezes titkosítás nélküli virtuális gépeket javaslatokként Azure Security Center figyeli. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>Győződjön meg arról, hogy a "nem csatlakoztatott lemezek" titkosítva vannak

**Azonosító**: a CIS Azure 7,3 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A nem csatolt lemezeket titkosítani kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Ez a házirend minden nem csatlakoztatott lemezt naplóz a titkosítás engedélyezése nélkül. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Győződjön meg arról, hogy csak a jóváhagyott bővítmények vannak telepítve

**Azonosító**: a CIS Azure 7,4 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Csak a jóváhagyott virtuálisgép-bővítményeket kell telepíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Ez a házirend a nem jóváhagyott virtuálisgép-bővítményeket szabályozza. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Győződjön meg arról, hogy az összes Virtual Machines legújabb operációsrendszer-javításai vannak alkalmazva

**Azonosító**: a CIS Azure 7,5 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A rendszerfrissítéseket telepíteni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A kiszolgálókon hiányzó biztonsági rendszerfrissítéseket a Azure Security Center a javaslatok szerint figyeli |AuditIfNotExists, letiltva |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Győződjön meg arról, hogy az összes Virtual Machines Endpoint Protection telepítve van

**Azonosító**: a CIS Azure 7,6 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hiányzó Endpoint Protection figyelése Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |A telepített Endpoint Protection ügynök nélküli kiszolgálókat javaslatokként Azure Security Center figyeli |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Egyéb biztonsági megfontolások

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Győződjön meg arról, hogy a lejárati dátum minden kulcson be van állítva

**Azonosító**: a CIS Azure 8,1 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault kulcsoknak lejárati dátummal kell rendelkezniük](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |A titkosítási kulcsoknak meghatározott lejárati dátummal kell rendelkezniük, és nem lehet állandó. Az örökre érvényes kulcsok lehetővé teszik a támadók számára, hogy a kulcs biztonsága érdekében több időt biztosítanak. Ajánlott biztonsági eljárás a lejárati dátumok beállítása a titkosítási kulcsokon. |Naplózás, megtagadás, letiltva |[1.0.1 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Győződjön meg arról, hogy a lejárati dátum minden Titoknál be van állítva

**Azonosító**: a CIS Azure 8,2 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault a titkoknak lejárati dátummal kell rendelkezniük](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |A titkoknak meghatározott lejárati dátummal kell rendelkezniük, és nem lehet állandó. Az örökre érvényes titkos kulcsok lehetővé teszik az esetleges támadók számára, hogy megsértsék őket. Ajánlott biztonsági gyakorlat a lejárati dátumok beállítása a titkokon. |Naplózás, megtagadás, letiltva |[1.0.1 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Győződjön meg arról, hogy a kulcstartó helyreállítható

**Azonosító**: a CIS Azure 8,4 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Key Vault felügyelt HSM-nek engedélyezve kell lennie a kiürítési védelemmel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Egy Azure Key Vault felügyelt HSM rosszindulatú törlése állandó adatvesztéshez vezethet. A szervezet rosszindulatú bennfentes a felügyelt HSM Azure Key Vault törlésére és törlésére is képes. A védelem törlésével megvédheti a bennfentes támadásoktól a Azure Key Vault felügyelt HSM-hez tartozó, nem kötelező megőrzési időtartam kikényszerítésével. A szervezeten belül a Microsoft nem tudja törölni a Azure Key Vault felügyelt HSM-et a helyreállítható törlés megőrzési ideje alatt. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[A kulcstárolóknak engedélyezve kell lennie a kiürítési védelemmel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A kulcstartó rosszindulatú törlése maradandó adatvesztést eredményezhet. A szervezet rosszindulatú bennfentes a kulcstartók törlésére és kiürítésére is képes. A védelem törlésével megvédheti a bennfentes támadásoktól a nem kötelező megőrzési idő kikényszerítésével a törölt kulcstartók esetében. A szervezeten belül a Microsoft nem tudja törölni a kulcstárolókat a helyreállítható törlés megőrzési időszak alatt. |Naplózás, megtagadás, letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezése az Azure Kubernetes-szolgáltatásokon belül

**Azonosító**: a CIS Azure 8,5 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Szerepköralapú Access Control (RBAC) használata szükséges a Kubernetes-szolgáltatásokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Ha részletes szűrést szeretne biztosítani a felhasználók által végrehajtható műveletekhez, használja a Role-Based Access Control (RBAC) a Kubernetes Service-fürtök engedélyeinek kezeléséhez és a vonatkozó engedélyezési házirendek konfigurálásához. |Naplózás, letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Győződjön meg arról, App Service hitelesítés be van állítva Azure App Service

**Azonosító**: a CIS Azure 9,1 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A hitelesítést engedélyezni kell az API-alkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service a hitelesítés egy olyan szolgáltatás, amely megakadályozhatja, hogy a névtelen HTTP-kérelmek elérjék az API-alkalmazást, vagy hitelesítsék azokat, amelyek jogkivonatokkal rendelkeznek az API-alkalmazás elérése előtt |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[A hitelesítést engedélyezni kell a Function alkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service a hitelesítés egy olyan szolgáltatás, amely megakadályozhatja a névtelen HTTP-kérelmek elérését a Function alkalmazást, vagy hitelesítheti a tokenekkel rendelkezőket, mielőtt elérnék a Function alkalmazást |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[A hitelesítést engedélyezni kell a webalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service a hitelesítés egy olyan szolgáltatás, amely megakadályozza, hogy a névtelen HTTP-kérelmek elérjék a webalkalmazást, vagy hitelesítsék azokat, amelyek jogkivonatokkal rendelkeznek a webalkalmazás eléréséhez. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Győződjön meg arról, hogy a webalkalmazás az összes HTTP-forgalmat átirányítja a HTTPS-re Azure App Service

**Azonosító**: a CIS Azure 9,2 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A webalkalmazás csak HTTPS protokollon keresztül érhető el](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Győződjön meg arról, hogy a webalkalmazás a TLS-titkosítás legújabb verzióját használja.

**Azonosító**: a CIS Azure 9,3 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A legújabb TLS-verziót kell használni az API-alkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a függvényalkalmazás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A legújabb TLS-verziót kell használni a webalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Győződjön meg arról, hogy a webalkalmazáshoz "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" érték van beállítva a következőre: "on"

**Azonosító**: a CIS Azure 9,4 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy az API-alkalmazás "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" értékre van állítva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt igényeljen a bejövő kérésekhez. Csak az érvényes tanúsítvánnyal rendelkező ügyfelek érhetik el az alkalmazást. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Győződjön meg arról, hogy a (z) "on" értékre van állítva a webalkalmazás "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" értéke](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt igényeljen a bejövő kérésekhez. Csak az érvényes tanúsítvánnyal rendelkező ügyfelek érhetik el az alkalmazást. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[A Function apps szolgáltatásnak engedélyezve kell lennie az "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt igényeljen a bejövő kérésekhez. Csak az érvényes tanúsítvánnyal rendelkező ügyfelek érhetik el az alkalmazást. |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Győződjön meg arról, hogy engedélyezve van-e a regisztráció a Azure Active Directory App Service

**Azonosító**: a CIS Azure 9,5 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A felügyelt identitást az API-alkalmazásban kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Felügyelt identitás használata a fokozott hitelesítési biztonsághoz |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Felügyelt identitást kell használni a függvényalkalmazás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Felügyelt identitás használata a fokozott hitelesítési biztonsághoz |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[A felügyelt identitást a webalkalmazásban kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Felügyelt identitás használata a fokozott hitelesítési biztonsághoz |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a webalkalmazás futtatására használatos

**Azonosító**: a CIS Azure 9,7 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha az API-alkalmazás részeként van használatban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |A PHP-szoftverek esetében az újabb verziók a biztonsági hibák vagy a további funkciók befoglalása miatt kerülnek kiadásra. Ajánlott az API-alkalmazások legújabb PHP-verziójának használata, hogy kihasználhassa a biztonsági javítások előnyeit, ha van ilyen, és/vagy a legújabb verzió új funkciói. Jelenleg ez a szabályzat csak a linuxos webalkalmazásokra vonatkozik. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a webalkalmazás részeként van használatban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |A PHP-szoftverek esetében az újabb verziók a biztonsági hibák vagy a további funkciók befoglalása miatt kerülnek kiadásra. Javasoljuk, hogy a legújabb PHP-verziót használja a webalkalmazásokhoz, hogy kihasználhassa a biztonsági javítások előnyeit, ha van ilyen, és/vagy a legújabb verzió új funkciói. Jelenleg ez a szabályzat csak a linuxos webalkalmazásokra vonatkozik. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás futtatására használatos

**Azonosító**: a CIS Azure 9,8 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha az API-alkalmazás részeként van használatban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Az újabb verziók a Python-szoftverek számára biztonsági hibák vagy további funkciók befoglalása miatt lesznek elérhetők. Az API-alkalmazások legújabb Python-verziójának használata ajánlott annak érdekében, hogy kihasználhassa a biztonsági javításokat, ha vannak ilyenek, és/vagy a legújabb verzió új funkciói. Jelenleg ez a szabályzat csak a linuxos webalkalmazásokra vonatkozik. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a Function alkalmazás részeként van használatban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Az újabb verziók a Python-szoftverek számára biztonsági hibák vagy további funkciók befoglalása miatt lesznek elérhetők. Javasoljuk, hogy a legújabb Python-verziót használja a Function apps használatához, hogy kihasználhassa a biztonsági javítások előnyeit, ha van ilyen, és/vagy a legújabb verzió új funkciói. Jelenleg ez a szabályzat csak a linuxos webalkalmazásokra vonatkozik. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás részeként van használatban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Az újabb verziók a Python-szoftverek számára biztonsági hibák vagy további funkciók befoglalása miatt lesznek elérhetők. Ajánlott a Web Apps legújabb Python-verziójának használata, hogy kihasználhassa a biztonsági javítások előnyeit, ha van ilyen, és/vagy a legújabb verzió új funkciói. Jelenleg ez a szabályzat csak a linuxos webalkalmazásokra vonatkozik. |AuditIfNotExists, letiltva |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Győződjön meg arról, hogy a Java-verzió a legújabb, ha a webalkalmazás futtatására használatos

**Azonosító**: a CIS Azure 9,9 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a Java-verzió a legújabb, ha az API-alkalmazás részeként van használatban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Az újabb verziókat a rendszer időnként a biztonsági hibák vagy a további funkciók befoglalása miatt a javára bocsátja ki. Az API-alkalmazások legújabb Python-verziójának használata ajánlott annak érdekében, hogy kihasználhassa a biztonsági javításokat, ha vannak ilyenek, és/vagy a legújabb verzió új funkciói. Jelenleg ez a szabályzat csak a linuxos webalkalmazásokra vonatkozik. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha a Function alkalmazás részeként van használatban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |A Java-szoftverek esetében az újabb verziók a biztonsági hibák vagy a további funkciók befoglalása miatt lesznek elérhetők. A legújabb Java-verzió használata a Function apps esetében ajánlott, hogy kihasználhassa a biztonsági javítások előnyeit, ha van ilyen, és/vagy a legújabb verzió új funkciói. Jelenleg ez a szabályzat csak a linuxos webalkalmazásokra vonatkozik. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Győződjön meg arról, hogy a Java-verzió a legújabb, ha a webalkalmazás részeként van használatban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |A Java-szoftverek esetében az újabb verziók a biztonsági hibák vagy a további funkciók befoglalása miatt lesznek elérhetők. Javasoljuk, hogy a legújabb Java-verziót használja a webalkalmazásokhoz, hogy kihasználhassa a biztonsági javítások előnyeit, ha van ilyen, és/vagy a legújabb verzió új funkciói. Jelenleg ez a szabályzat csak a linuxos webalkalmazásokra vonatkozik. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a webalkalmazás futtatására használatos

**Azonosító**: a CIS Azure 9,10 **tulajdonjoga**: ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha az API-alkalmazás futtatásához használatos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Az újabb verziókat a rendszer a biztonsági hibák vagy a további funkciók befoglalása miatt a HTTP-re is kibocsátja. A webes alkalmazások legújabb HTTP-verziója segítségével kihasználhatja a biztonsági javítások előnyeit, ha vannak ilyenek, és/vagy az újabb verzió új funkciói. Jelenleg ez a szabályzat csak a linuxos webalkalmazásokra vonatkozik. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a Function app futtatásához használatos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Az újabb verziókat a rendszer a biztonsági hibák vagy a további funkciók befoglalása miatt a HTTP-re is kibocsátja. A webes alkalmazások legújabb HTTP-verziója segítségével kihasználhatja a biztonsági javítások előnyeit, ha vannak ilyenek, és/vagy az újabb verzió új funkciói. Jelenleg ez a szabályzat csak a linuxos webalkalmazásokra vonatkozik. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a webalkalmazás futtatására használatos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Az újabb verziókat a rendszer a biztonsági hibák vagy a további funkciók befoglalása miatt a HTTP-re is kibocsátja. A webes alkalmazások legújabb HTTP-verziója segítségével kihasználhatja a biztonsági javítások előnyeit, ha vannak ilyenek, és/vagy az újabb verzió új funkciói. Jelenleg ez a szabályzat csak a linuxos webalkalmazásokra vonatkozik. |AuditIfNotExists, letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> Az adott Azure Policy-definíciók rendelkezésre állása Azure Government és más nemzeti felhőkben is változhat.

## <a name="next-steps"></a>Következő lépések

További cikkek a Azure Policyról:

- A [szabályozás megfelelőségének](../concepts/regulatory-compliance.md) áttekintése.
- Tekintse meg a [kezdeményezés definíciójának struktúráját](../concepts/initiative-definition-structure.md).
- Tekintse át a további példákat [Azure Policy mintákon](./index.md).
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
