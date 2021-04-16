---
title: A CIS és Microsoft Azure 1.1.0 szabályozási megfelelőségi adatai
description: A CIS Microsoft Azure Benchmark 1.1.0 szabályozási megfelelőség beépített kezdeményezésének részletei. Minden vezérlő egy vagy több olyan Azure Policy, amelyek az értékelést segítik.
ms.date: 04/14/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 47e59a3ba1a31fd05ab9d1d87301b41c30c8bdfd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497744"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-110-regulatory-compliance-built-in-initiative"></a>A CIS Microsoft Azure Benchmark 1.1.0 szabályozási megfelelőség beépített kezdeményezés részletei

A következő cikk részletesen bemutatja, hogy a Azure Policy-megfelelőség beépített  kezdeményezési definíciója hogyan van leképezve a CIS Microsoft Azure Foundations Benchmark 1.1.0 megfelelőségi tartományaira és vezérlőire. 
További információ erről a megfelelőségi szabványról: [CIS Microsoft Azure Foundations Benchmark 1.1.0](https://www.cisecurity.org/benchmark/azure/). A _tulajdonjoggal kapcsolatos_ további Azure Policy [szabályzatdefiníciók és](../concepts/definition-structure.md#type) megosztott felelősségek a [felhőben való meghatározásáról.](../../../security/fundamentals/shared-responsibility.md)

Az alábbi leképezések a **CIS Microsoft Azure Foundations Benchmark 1.1.0 vezérlőire vannak leképezve.** A jobb oldalon található navigációval közvetlenül egy adott megfelelőségi **tartományra ugorhat.** Számos vezérlő egy új kezdeményezési [definícióval Azure Policy](../overview.md) meg. A kezdeményezés teljes definíciójának áttekintéshez nyissa meg a **szabályzatot** a Azure Portal majd válassza a **Definíciók** lapot.
Ezután keresse meg és válassza ki a **CIS Microsoft Azure Foundations Benchmark v1.1.0** Regulatory Compliance beépített kezdeményezési definícióját.

Ezt a beépített kezdeményezést a [CIS Microsoft Azure Foundations Benchmark 1.1.0](../../blueprints/samples/cis-azure-1-1-0.md)tervminta részeként telepítik.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [definícióhoz Azure Policy](../overview.md) társítva.
> Ezek a szabályzatok segíthetnek felmérni [a](../how-to/get-compliance-data.md) vezérlőnek való megfelelőséget; A vezérlők és egy vagy több szabályzat között azonban gyakran nincs egy-az-egyhez vagy egy teljes egyezés. Ezért a **szabályzatban megfelelő** Azure Policy csak magukra a szabályzatdefiníciókra vonatkoznak; Ez nem biztosítja, hogy teljes mértékben megfelel egy vezérlő összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyekre jelenleg egyetlen Azure Policy sem vonatkozik. Ezért a megfelelőségi Azure Policy csak részlegesen látható a teljes megfelelőségi állapotról. A megfelelőségi tartományok, vezérlők és Azure Policy megfelelőségi szabvány definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0.json)

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Győződjön meg arról, hogy a többtényezős hitelesítés engedélyezve van az összes kiemelt jogosultságú felhasználó számára

**AZONOSÍTÓ:** CIS Azure 1.1 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes írási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[Az MFA-t engedélyezni kell az előfizetés tulajdonosi engedélyekkel rendelkező fiókjaiban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A többtényezős hitelesítést (MFA) minden tulajdonosi engedéllyel rendelkező előfizetési fiókhoz engedélyezni kell, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Győződjön meg arról, hogy a többtényezős hitelesítés engedélyezve van az összes nem kiemelt jogosultságú felhasználó számára

**Azonosító:** CIS Azure 1.2 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes olvasási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>Győződjön meg arról, hogy nincsenek vendégfelhasználók

**AZONOSÍTÓ:** CIS Azure 1.3 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből, hogy megelőzhető legyen a nemmonitorált hozzáférés. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Az olvasási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből, hogy megelőzhető legyen a nemmonitorált hozzáférés. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Az írási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Az írási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből, hogy megelőzhető legyen a nemmonitorált hozzáférés. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Győződjön meg arról, hogy nem jött létre egyéni előfizetés-tulajdonosi szerepkör

**Azonosító:** CIS Azure 1.23 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Egyéni előfizetés-tulajdonosi szerepkörök nem létezhetnek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Ez a szabályzat biztosítja, hogy nem létezik egyéni előfizetés-tulajdonosi szerepkör. |Naplózás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-standard-pricing-tier-is-selected"></a>Győződjön meg arról, hogy a standard tarifacsomag van kiválasztva

**Azonosító:** CIS Azure 2.1 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender a App Service engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender a App Service kihasználja a felhő méretezését és az Azure felhőszolgáltatóként való láthatóságát a gyakori webalkalmazás-támadások figyelése érdekében. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure Defender a Azure SQL Database-kiszolgálókhoz engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender SQL-hez elérhető funkciókkal felderítheti és enyhítheti az adatbázisok lehetséges biztonsági réseit, észlelheti az SQL-adatbázisokat fenyegető fenyegetéseket jelző rendellenes tevékenységeket, valamint felderítheti és osztályozhatja a bizalmas adatokat. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[Azure Defender tárolóregisztrálókhoz engedélyezni kell a tárolóregisztrálókhoz való regisztrálást](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |Azure Defender tároló-beállításjegyzékek biztonsági rések vizsgálatával megkeresi az elmúlt 30 napban lekért, a regisztrációs adatbázisba leküldett vagy importált rendszerképeket, és rendszerképenként részletes eredményeket ad. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender a Key Vault engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender a Key Vault egy további védelmi és biztonsági intelligenciát biztosít azáltal, hogy észleli a Key Vault-fiókok hozzáférésére vagy kihasználására tett szokatlan és potenciálisan káros kísérleteket. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes valós idejű fenyegetésvédelmet biztosít a tárolóba helyezni képes környezetekhez, és riasztásokat hoz létre a gyanús tevékenységekről. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender engedélyezni kell a kiszolgálókhoz való biztonsági adatokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |Azure Defender kiszolgálókhoz való használata valós idejű fenyegetésvédelmet biztosít a kiszolgálói számítási feladatok számára, és biztonsági javaslatokat, valamint gyanús tevékenységekre vonatkozó riasztásokat hoz létre. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Azure Defender gépeken található SQL-kiszolgálókhoz engedélyezni kell az sql-kiszolgálókra vonatkozó adatokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender SQL-hez elérhető funkciókkal felderítheti és enyhítheti az adatbázisok lehetséges biztonsági réseit, észlelheti az SQL-adatbázisokat fenyegető fenyegetéseket jelző rendellenes tevékenységeket, valamint felderítheti és osztályozhatja a bizalmas adatokat. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[Azure Defender storage-tárfiókot engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender Storage-hoz való hozzáféréssel észlelni lehet a tárfiókok hozzáférésére vagy kihasználására tett szokatlan és potenciálisan rosszindulatú kísérleteket. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Győződjön meg arról, hogy "A monitorozási ügynök automatikus kiépítése" beállítás be van állítva

**Azonosító:** CIS Azure 2.2-tulajdonjog: Ügyfél 

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Log Analytics-ügynök automatikus építésének engedélyezve kell lennie az előfizetésben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |A biztonsági rések és fenyegetések figyelése érdekében a Azure Security Center gyűjt adatokat az Azure-beli virtuális gépekről. Az adatokat a Korábban Microsoft Monitoring Agent (MMA) néven ismert Log Analytics-ügynök gyűjti, amely beolvassa a biztonsággal kapcsolatos különböző konfigurációkat és eseménynaplókat a gépről, és elemzés céljából átmásolja az adatokat a Log Analytics-munkaterületre. Javasoljuk, hogy az automatikus kiépítés engedélyezésével automatikusan üzembe helyeződjon az ügynök az összes támogatott Azure-beli és újonnan létrehozott virtuális gépre. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>Győződjön meg arról, hogy az ASC "Rendszerfrissítések figyelése" alapértelmezett szabályzatbeállítása nincs letiltva

**Azonosító:** CIS Azure 2.3 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A rendszerfrissítéseket telepíteni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A kiszolgálók hiányzó biztonságirendszer-frissítéseit a Azure Security Center fogja figyelni |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>Győződjön meg arról, hogy az ASC "Operációs rendszer biztonsági résének figyelése" alapértelmezett szabályzatbeállítása nincs letiltva

**Azonosító:** CIS Azure 2.4 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A gépek biztonsági konfigurációjának biztonsági réseit helyre kelligazító](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azok a kiszolgálók, amelyek nem tesznek eleget a konfigurált alapkonfigurációnak, a Azure Security Center figyelik javaslatként |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>Győződjön meg arról, hogy az ASC "Endpoint Protection figyelése" alapértelmezett házirend-beállítása nem "Letiltva"

**Azonosító:** CIS Azure 2.5 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hiányzó Endpoint Protection figyelése a Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |A telepített Endpoint Protection-ügynökkel nem Azure Security Center kiszolgálókat |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>Győződjön meg arról, hogy az ASC "Lemeztitkosítás figyelése" alapértelmezett szabályzatbeállítása nincs letiltva

**Azonosító:** CIS Azure 2.6 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lemeztitkosítást kell alkalmazni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Az engedélyezett lemeztitkosítással nem rendelkező virtuális gépeket a Azure Security Center figyelik. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>Győződjön meg arról, hogy az ASC "Hálózati biztonsági csoportok figyelése" alapértelmezett szabályzatbeállítása nincs letiltva

**Azonosító:** CIS Azure 2.7 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptív hálózatkorrekozási javaslatokat kell alkalmazni az internetre néző virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és javaslatokat tesz a hálózati biztonsági csoport szabályára vonatkozóan, amelyek csökkentik a potenciális támadási felületet |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>Győződjön meg arról, hogy az ASC "Új generációs tűzfalak (NGFW) monitorozásának engedélyezése" alapértelmezett szabályzatbeállítása nincs letiltva

**Azonosító:** CIS Azure 2.9-tulajdonjog: Ügyfél 

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az internetre néző virtuális gépeket hálózati biztonsági csoportokkal kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |A hálózati biztonsági csoportokkal (NSG-k) való hozzáférés korlátozásával megvédheti virtuális gépeit a lehetséges fenyegetésektől. További információ az NSG-k forgalmának szabályozásával kapcsolatban: [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Az alhálózatokat egy hálózati biztonsági csoporthoz kell hozzárendelni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Hálózati biztonsági csoport (NSG) korlátozza az alhálózat védelmét a lehetséges fenyegetésekkel szemben. Az NSG-k az alhálózatra Access Control (ACL) vonatkozó szabályok listáját tartalmazzák, amelyek engedélyezik vagy megtagadják az alhálózatra való hálózati forgalmat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>Győződjön meg arról, hogy az ASC "Biztonsági rések felmérésének figyelése" alapértelmezett szabályzatbeállítása nincs letiltva

**Azonosító:** CIS Azure 2.10 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell egy sebezhetőségi felmérési megoldást a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |A virtuális gépek naplózásával észleli, hogy támogatott sebezhetőségi felmérési megoldást futtatnak-e. Minden kiberkockázati és biztonsági program egyik fő összetevője a biztonsági rések azonosítása és elemzése. Azure Security Center standard tarifacsomagja többletköltség nélkül tartalmazza a biztonsági rések keresését a virtuális gépeken. Emellett a Security Center automatikusan üzembe helyezheti ezt az eszközt. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>Győződjön meg arról, hogy az ASC "JIT hálózati hozzáférés figyelése" alapértelmezett szabályzatbeállítása nem "Letiltva"

**Azonosító:** CIS Azure 2.12 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjainak a hálózat hozzáférés-vezérlésével kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A hálózati igény szerinti (JIT) hozzáféréseket a Azure Security Center követik |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>Győződjön meg arról, hogy az ASC "Adaptív alkalmazások engedélyezési listára való alkalmazásának figyelése" alapértelmezett szabályzatbeállítása nem "Letiltva"

**Azonosító:** CIS Azure 2.13 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A biztonságos alkalmazások meghatározásához szükséges adaptív alkalmazásvezérlőket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazásvezérlők engedélyezésével meghatározhatja a gépeken futó, ismerten biztonságos alkalmazások listáját, és riasztást küld, ha más alkalmazások is futnak. Ez segít a gépek kártevők elleni megsokosodásában. A szabályok konfigurálási és karbantartásának egyszerűsítése érdekében a Security Center gépi tanulással elemzi az egyes gépeken futó alkalmazásokat, és javaslatot ad az ismerten biztonságos alkalmazások listájára. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>Győződjön meg arról, hogy az ASC "Az SQL-naplózás monitorozása" alapértelmezett szabályzatbeállítása nem "Letiltva"

**Azonosító:** CIS Azure 2.14 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell az SQL Server naplózását](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Engedélyezni kell a SQL Server naplózását a kiszolgálón lévő összes adatbázis adatbázisában végzett adatbázis-tevékenységek nyomon követéséhez és auditnaplóba mentéséhez. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>Győződjön meg arról, hogy az ASC "Monitor SQL Encryption" alapértelmezett szabályzatbeállítása nincs letiltva

**Azonosító:** CIS Azure 2.15 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[transzparens adattitkosítás SQL-adatbázisokon engedélyezni kell az adatbázis-adatokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Engedélyezni kell a transzparens adattitkosítást az aktív adatok védelme és a megfelelőségi követelmények teljesítéséhez |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>Győződjön meg arról, hogy a "Biztonsági kapcsolattartó e-mailjei" be van állítva

**Azonosító:** CIS Azure 2.16 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az előfizetések biztonsági problémák esetén egy kapcsolattartási e-mail-címmel kell, hogy rendelkezik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Annak érdekében, hogy a szervezet érintett tagjai értesítést kapják, ha az egyik előfizetésében biztonsági incidens történt, állítson be egy biztonsági kapcsolattartót, aki e-mailes értesítéseket kap a Security Center. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>Győződjön meg arról, hogy "E-mail-értesítés küldése nagy súlyosságú riasztások számára" beállítás be van állítva

**Azonosító:** CIS Azure 2.18 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a nagy súlyosságú riasztások e-mailes értesítését](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Annak érdekében, hogy a szervezet érintett tagjai értesítést kapják, ha az egyik előfizetésében biztonsági incidens történik, engedélyezze az e-mailes értesítéseket a súlyos riasztások esetén a Security Center. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>Ellenőrizze, hogy az "E-mail küldése az előfizetés tulajdonosainak is" beállítás be van-e állítva

**ID**: CIS Azure 2.19 Ownership : **Customer**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a nagy súlyosságú riasztások e-mailes értesítését az előfizetés tulajdonosának](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |Annak érdekében, hogy az előfizetés tulajdonosai értesítést kapják, ha az előfizetésben biztonsági incidens lehetséges, állítsa be az e-mailes értesítéseket az előfizetés tulajdonosainak a súlyossági riasztások esetén a Security Center. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>Storage-fiókok

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Győződjön meg arról, hogy a "Biztonságos átvitelre van szükség" beállítás engedélyezve van

**Azonosító:** CIS Azure 3.1 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Naplózási követelmény a biztonságos átvitelhez a tárfiókban. A biztonságos átvitel egy olyan lehetőség, amely arra kényszeríti a tárfiókot, hogy csak biztonságos kapcsolatokról (HTTPS) fogadjon kéréseket. A HTTPS használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a man-in-the-middle, a lehallgatás és a munkamenet-eltérítés |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Győződjön meg arról, hogy a "Nyilvános hozzáférési szint" privát a blobtárolókhoz

**AZONOSÍTÓ:** CIS Azure 3.6 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfiók nyilvános hozzáférését nem szabad engedélyezettnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Az Azure Storage tárolóihoz és blobjaihoz való névtelen nyilvános olvasási hozzáférés kényelmes módja az adatok megosztásának, de biztonsági kockázatot jelenthet. A nem szükséges névtelen hozzáférés által okozott adatsértés elkerülése érdekében a Microsoft azt javasolja, hogy akadályozza meg a tárfiókhoz való nyilvános hozzáférést, kivéve, ha a forgatókönyv ezt megköveteli. |audit, deny, disabled |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Győződjön meg arról, hogy a Storage-fiókok alapértelmezett hálózati hozzáférési szabálya megtagadásra van beállítva

**Azonosító:** CIS Azure 3.7 **tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfiókok korlátozzák a hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |A tárfiókok hálózati hozzáférését korlátozni kell. Konfigurálja úgy a hálózati szabályokat, hogy csak az engedélyezett hálózatokról származó alkalmazások férnek hozzá a tárfiókhoz. Az adott internetről vagy helyszíni ügyfelekről származó kapcsolatok lehetővé teszik a hozzáférést adott Azure-beli virtuális hálózatokról származó forgalomhoz vagy a nyilvános internetes IP-címtartományok felé |Naplózás, Megtagadás, Letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Győződjön meg arról, hogy a "Megbízható Microsoft-szolgáltatások" engedélyezve van a tárfiókhoz való hozzáféréshez

**Azonosító:** CIS Azure 3.8 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfióknak engedélyeznie kell a hozzáférést a megbízható Microsoft-szolgáltatások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Egyes Microsoft-szolgáltatások tárfiókokkal kommunikáló felhasználók olyan hálózatokról működnek, amelyek nem kaphatnak hozzáférést hálózati szabályokkal. Annak érdekében, hogy az ilyen típusú szolgáltatás a kívántnak megfelelően működjön, engedélyezze, hogy a megbízható Microsoft-szolgáltatások megkerüljék a hálózati szabályokat. Ezek a szolgáltatások erős hitelesítést fognak használni a tárfiók eléréséhez. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>Adatbázis-szolgáltatások

### <a name="ensure-that-auditing-is-set-to-on"></a>Győződjön meg arról, hogy a "Naplózás" beállítás be van állítva

**Azonosító:** CIS Azure 4.1 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell az SQL Server naplózását](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Engedélyezni kell a SQL Server naplózását a kiszolgálón lévő összes adatbázis adatbázisában végzett adatbázis-tevékenységek nyomon követéséhez és auditnaplóba mentéséhez. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>Győződjön meg arról, hogy az SQL Server "naplózási" szabályzatában az "AuditActionGroups" beállítás megfelelően van beállítva

**Azonosító:** CIS Azure 4.2 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az SQL naplózási beállításainak konfigurálva kell Action-Groups kritikus tevékenységek rögzítésére](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |Az AuditActionsAndGroups tulajdonságnak tartalmaznia kell legalább SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP, FAILED_DATABASE_AUTHENTICATION_GROUP BATCH_COMPLETED_GROUP naplózást |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Győződjön meg arról, hogy a "Naplózás" megőrzése "több mint 90 nap"

**Azonosító:** CIS Azure 4.3 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfiók célhelyének naplózásával konfigurált SQL-kiszolgálókat 90 napos megőrzéssel vagy annál nagyobb megőrzéssel kell konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Incidensvizsgálati célokból javasoljuk, hogy legalább 90 napra SQL Server adatmegőrzést a tárfiók célhelyéhez. Győződjön meg arról, hogy a szükséges adatmegőrzési szabályoknak megfelelőek az üzemeltetési régiói. Ez olykor kötelező a szabályozási szabványoknak való megfeleléshez. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>Győződjön meg Advanced Data Security, hogy az SQL Serveren a "Advanced Data Security" beállítás be van állítva

**Azonosító:** CIS Azure 4.4 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A speciális adatbiztonságot engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Napló minden SQL Managed Instance fejlett adatbiztonság nélkül. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Engedélyezni kell a magas szintű adatbiztonságot az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |SQL-kiszolgálók naplózása Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Győződjön meg Azure Active Directory, hogy a rendszergazda konfigurálva van

**AZONOSÍTÓ:** CIS Azure 4.8 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Egy Azure Active Directory kell kiépítve az SQL-kiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Naplót kell Azure Active Directory sql-kiszolgáló rendszergazdájának jogosultságát az Azure AD-hitelesítés engedélyezéséhez. Az Azure AD-hitelesítés egyszerűsített engedélykezelést és központosított identitáskezelést tesz lehetővé az adatbázis-felhasználók és más Microsoft-szolgáltatások |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Győződjön meg arról, hogy az "Adattitkosítás" be van állítva egy SQL Database

**Azonosító:** CIS Azure 4.9 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[transzparens adattitkosítás SQL-adatbázisokon engedélyezni kell az adatbázis-adatokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Engedélyezni kell a transzparens adattitkosítást az aktív adatok védelme és a megfelelőségi követelmények teljesítéséhez |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>Győződjön meg arról, hogy az SQL Server TDE-védője BYOK-val van titkosítva (saját kulcs használata)

**Azonosító:** CIS Azure 4.10 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A felügyelt SQL-példányok az ügyfelek által felügyelt kulcsokat használják az adatok titkosításához az](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |A transzparens adattitkosítás (TDE) saját kulccsal való megvalósítása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, nagyobb biztonságot nyújt egy HSM-háttérszolgáltatással, és lehetővé teszi a feladatok elkülönítését. Ez a javaslat a kapcsolódó megfelelőségi követelményekhez tartozó szervezetekre vonatkozik. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Az SQL-kiszolgálóknak ügyfél által kezelt kulcsokat kell használniuk az adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |A transzparens adattitkosítás (TDE) saját kulccsal történő megvalósítása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, nagyobb biztonságot nyújt a HSM-háttérszolgáltatással, és lehetővé teszi a feladatok elkülönítését. Ez a javaslat a kapcsolódó megfelelőségi követelményekhez tartozó szervezetekre vonatkozik. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Győződjön meg arról, hogy az SSL-kapcsolat kényszerítés beállítása ENGEDÉLYEZVE a MySQL-adatbáziskiszolgálón

**Azonosító:** CIS Azure 4.11 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell az SSL-kapcsolat kényszerítését a MySQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése segít a "középen" támadásokkal szembeni védelemben a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Ellenőrizze, hogy az "log_checkpoints" kiszolgálóparaméter "ON" van-e beállítva a PostgreSQL-adatbáziskiszolgálóhoz

**Azonosító:** CIS Azure 4.12 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A PostgreSQL-adatbáziskiszolgálókhoz engedélyezni kell a napló ellenőrzőpontjait](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Ez a szabályzat segít a környezetében az esetleges PostgreSQL-adatbázisok naplózásában anélkül, hogy log_checkpoints engedélyezné. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Győződjön meg arról, hogy az SSL-kapcsolat kényszerítés beállítása "ENABLED" a PostgreSQL-adatbáziskiszolgálóhoz

**Azonosító:** CIS Azure 4.13 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az SSL-kapcsolat kényszerítését engedélyezni kell a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középen" támadásokkal szembeni védelemben. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Ellenőrizze, hogy a "log_connections" kiszolgálóparaméter "ON" (BE) van-e állítva a PostgreSQL-adatbáziskiszolgálóhoz

**Azonosító:** CIS Azure 4.14 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a naplókapcsolatokat a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Ez a szabályzat segít a környezetében az esetleges PostgreSQL-adatbázisok naplózásában anélkül, hogy log_connections engedélyezné. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Ellenőrizze, hogy a "log_disconnections" kiszolgálóparaméter "ON" van-e beállítva a PostgreSQL-adatbáziskiszolgálóhoz

**Azonosító:** CIS Azure 4.15 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A kapcsolat bontását a PostgreSQL-adatbáziskiszolgálókhoz kell naplózni.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Ez a szabályzat segít a környezetében bármilyen PostgreSQL-adatbázis naplózásában anélkül, hogy log_disconnections engedélyezné. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Ellenőrizze, hogy a "connection_throttling" kiszolgálóparaméter "ON" (BE) van-e állítva a PostgreSQL-adatbáziskiszolgálóhoz

**Azonosító:** CIS Azure 4.17 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a kapcsolat szabályozását a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Ez a szabályzat segít a környezetében az esetleges PostgreSQL-adatbázisok naplózásában anélkül, hogy engedélyezve lenne a kapcsolat szabályozása. Ez a beállítás engedélyezi a kapcsolat IP-címenkénti ideiglenes szabályozását a túl sok érvénytelen jelszó-bejelentkezési hiba esetén. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

### <a name="ensure-that-a-log-profile-exists"></a>Naplóprofil létezésének ellenőrzése

**AZONOSÍTÓ:** CIS Azure 5.1.1 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az Azure-előfizetések tevékenységnapló-profiljának kell lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |Ez a szabályzat biztosítja, hogy a naplóprofil engedélyezve legyen-e a tevékenységnaplók exportálására. Naplózza, ha nincs létrehozott naplóprofil a naplók tárfiókba vagy eseményközpontba való exportálásához. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>Győződjön meg arról, hogy a Tevékenységnapló megőrzése beállítás 365 nap vagy annál nagyobb

**Azonosító:** CIS Azure 5.1.2 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tevékenységnaplót legalább egy évig meg kell őrizni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |Ez a szabályzat naplóz a tevékenységnaplót, ha a megőrzési idő nincs beállítva 365 napra vagy örökre (a megőrzési napok 0-ra vannak beállítva). |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>Ellenőrizze, hogy a naplózási profil rögzíti-e az összes tevékenységet

**AZONOSÍTÓ:** CIS Azure 5.1.3 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor naplóprofilnak naplókat kell gyűjtenie az "írás", "törlés" és "művelet" kategóriákhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Ez a szabályzat biztosítja, hogy a naplóprofil az "írás", "törlés" és "művelet" kategóriák naplóit gyűjtse |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>Győződjön meg arról, hogy a naplóprofil rögzíti az összes régió tevékenységnaplóit, beleértve a globálisokat is

**Azonosító:** CIS Azure 5.1.4 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor kell gyűjtenie a tevékenységnaplókat az összes régióból](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Ez a szabályzat naplóz Azure Monitor naplóprofilt, amely nem exportálja a tevékenységeket az Összes Azure által támogatott régióból, beleértve a globálist is. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Győződjön meg arról, hogy a tevékenységnaplókat tároló Storage-tároló nem nyilvánosan elérhető

**Azonosító:** CIS Azure 5.1.5 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfiók nyilvános hozzáférését le kell tartozni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Az Azure Storage tárolóihoz és blobjaihoz való névtelen nyilvános olvasási hozzáférés kényelmes módja az adatok megosztásának, de biztonsági kockázatot jelenthet. A Nem szükséges névtelen hozzáférés által okozott adatsértés elkerülése érdekében a Microsoft javasolja a tárfiókhoz való nyilvános hozzáférés megakadályozását, kivéve, ha a forgatókönyv ezt megköveteli. |audit, deny, disabled |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Győződjön meg arról, hogy a tevékenységnaplókat tartalmazó tárolót tartalmazó tárfiók BYOK-titkosítással van titkosítva (saját kulcs használata)

**Azonosító:** CIS Azure 5.1.6 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tevékenységnaplókat tartalmazó tárolót tartalmazó tárfiókot BYOK-val kell titkosítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Ez a szabályzat naplózza, ha a tevékenységnaplókat tartalmazó tárolót tartalmazó Storage-fiók BYOK-val van titkosítva. A szabályzat csak akkor működik, ha a tárfiók ugyanabban az előfizetésben található, mint a tevékenységnaplók. Az Azure Storage-titkosítással kapcsolatos további információkat itt [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) talál: .  |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Győződjön meg arról, hogy az Azure KeyVault naplózása engedélyezve van

**Azonosító:** CIS Azure 5.1.7 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Managed HSM Azure Key Vault ben engedélyezni kell az erőforrásnaplókat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |Ha vizsgálati célból szeretné újból létrehozni a tevékenységnaplókat, amikor biztonsági incidens vagy a hálózat biztonsága sérül, az erőforrásnaplók managed HSM-en való engedélyezésével naplózhatja a naplókat. Kövesse az itt található utasításokat: [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging) . |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell a Key Vault naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli használatát vizsgálati célokra, amikor biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a szabályzat-hozzárendelés létrehozásához

**Azonosító:** CIS Azure 5.2.1 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott szabályzatműveletekkel kapcsolatban tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Ez a szabályzat naplóz bizonyos szabályzatműveleteket, és nem konfigurált tevékenységnapló-riasztásokat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a hálózati biztonsági csoport létrehozásához vagy frissítéséhez

**Azonosító:** CIS Azure 5.2.2 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat meghatározott felügyeleti műveleteket naplóz, amelyekhez nincs konfigurálva tevékenységnapló-riasztás. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Győződjön meg arról, hogy a Hálózati biztonsági csoport törlése tevékenységnapló-riasztás létezik

**Azonosító:** CIS Azure 5.2.3 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat meghatározott felügyeleti műveleteket naplóz, amelyekhez nincs konfigurálva tevékenységnapló-riasztás. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a hálózati biztonsági csoport szabályának létrehozásához vagy frissítéséhez

**Azonosító:** CIS Azure 5.2.4 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat meghatározott felügyeleti műveleteket naplóz, amelyekhez nincs konfigurálva tevékenységnapló-riasztás. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a Hálózati biztonsági csoport törlése szabályhoz

**Azonosító:** CIS Azure 5.2.5 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat adott felügyeleti műveleteket naplóz, és nincsenek konfigurálva tevékenységnapló-riasztások. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a biztonsági megoldás létrehozásához vagy frissítéséhez

**Azonosító:** CIS Azure 5.2.6 **Tulajdonos: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott biztonsági műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ez a szabályzat naplóz bizonyos biztonsági műveleteket, amelyekhez nincs tevékenységnapló-riasztás konfigurálva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Győződjön meg arról, hogy a Biztonsági megoldás törlése tevékenységnapló-riasztás létezik

**Azonosító:** CIS Azure 5.2.7 **Tulajdonos: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott biztonsági műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ez a szabályzat meghatározott biztonsági műveleteket naplóz, amelyekhez nincs konfigurálva tevékenységnapló-riasztás. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a tűzfalszabály létrehozásához, frissítéséhez SQL Server törléséhez

**Azonosító:** CIS Azure 5.2.8 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat meghatározott felügyeleti műveleteket naplóz, amelyekhez nincs konfigurálva tevékenységnapló-riasztás. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a biztonsági szabályzat frissítéséhez

**Azonosító:** CIS Azure 5.2.9 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott biztonsági műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ez a szabályzat meghatározott biztonsági műveleteket naplóz, amelyekhez nincs konfigurálva tevékenységnapló-riasztás. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>Hálózatkezelés

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Győződjön meg arról, hogy az RDP-hozzáférés korlátozva van az internetről

**AZONOSÍTÓ:** CIS Azure 6.1 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az internetről való RDP-elérést le kell tiltani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Ez a szabályzat naplót naplót végez minden olyan hálózati biztonsági szabályt, amely engedélyezi az RDP-hozzáférést az internetről |Naplózás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Győződjön meg arról, hogy az SSH-hozzáférés korlátozva van az internetről

**Azonosító:** CIS Azure 6.2-tulajdonjog: Ügyfél 

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az internetről való SSH-hozzáférést le kell tiltani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Ez a szabályzat naplót végez minden olyan hálózati biztonsági szabályt, amely engedélyezi az SSH-hozzáférést az internetről |Naplózás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Győződjön meg arról Network Watcher hogy az "Enabled" (Engedélyezve)

**Azonosító:** CIS Azure 6.5-tulajdonjog: Ügyfél 

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher egy regionális szolgáltatás, amely lehetővé teszi a feltételek figyelése és diagnosztizálása az Azure-ban, illetve az Azure-ban a hálózati forgatókönyvek szintjén. A forgatókönyvszintű figyelés lehetővé teszi a teljes hálózatszintű nézet problémáinak diagnosztizálát. A hálózati diagnosztikai és vizualizációs eszközök Network Watcher segítségével megértheti, diagnosztizálhatja és betekintheti az Azure-beli hálózatát. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-that-os-disk-are-encrypted"></a>Győződjön meg arról, hogy az "operációsrendszer-lemez" titkosítva van

**Azonosító:** CIS Azure 7.1 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lemeztitkosítást kell alkalmazni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Az engedélyezett lemeztitkosítással nem rendelkező virtuális gépeket a Azure Security Center figyelik. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>Győződjön meg arról, hogy az "Adatlemezek" titkosítva van

**Azonosító:** CIS Azure 7.2 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lemeztitkosítást kell alkalmazni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Az engedélyezett lemeztitkosítással nem rendelkező virtuális gépeket a Azure Security Center figyelik. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>Győződjön meg arról, hogy a "Nem gyorsítótárazó lemezek" titkosítva van

**Azonosító:** CIS Azure 7.3 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A nem gyorsítótárazó lemezeket titkosítani kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Ez a házirend naplóz minden nem gyorsítótárazó lemezt titkosítás engedélyezése nélkül. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Győződjön meg arról, hogy csak jóváhagyott bővítmények vannak telepítve

**Azonosító:** CIS Azure 7.4 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Csak jóváhagyott virtuálisgép-bővítményeket kell telepíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Ez a szabályzat a nem jóváhagyott virtuálisgép-bővítményeket szabályozza. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Győződjön meg arról, hogy az összes Virtual Machines legújabb operációsrendszer-javítás van alkalmazva

**Azonosító:** CIS Azure 7.5 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A rendszerfrissítéseket telepíteni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A kiszolgálók hiányzó biztonságirendszer-frissítéseit a Azure Security Center fogja figyelni |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Győződjön meg arról, hogy az összes Virtual Machines végpontvédelem telepítve van

**Azonosító:** CIS Azure 7.6 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hiányzó Endpoint Protection figyelése a Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |A telepített Endpoint Protection-ügynökkel nem Azure Security Center kiszolgálókat |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Egyéb biztonsági szempontok

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Győződjön meg arról, hogy a lejárati dátum minden kulcshoz be van állítva

**Azonosító:** CIS Azure 8.1-tulajdonjog: Ügyfél 

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault kulcsok lejárati dátummal kell, hogy egyedük legyen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |A titkosítási kulcsok lejárati dátumának meghatározottnak kell lennie, és nem lehet végleges. Az örök időre érvényes kulcsok több időt biztosítanak a potenciális támadók számára a kulcs feltörésében. Ajánlott biztonsági eljárás a titkosítási kulcsok lejárati dátumának beállítása. |Naplózás, Megtagadás, Letiltva |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Győződjön meg arról, hogy a lejárati dátum minden titkos kulcshoz be van állítva

**Azonosító:** CIS Azure 8.2 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault titkos kulcsok lejárati dátummal kell, hogy legyenek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |A titkos kulcsok lejárati dátumának meghatározottnak kell lennie, és nem lehet végleges. A örökre érvényes titkos kulcsok több időt biztosítanak a potenciális támadók számára azok feltörésében. Ajánlott biztonsági eljárás a titkos kulcsok lejárati dátumának beállítása. |Naplózás, Megtagadás, Letiltva |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Győződjön meg arról, hogy a kulcstartó helyreállítható

**Azonosító:** CIS Azure 8.4 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Key Vault Managed HSM-nek engedélyeznie kell a véglegesen kiürítés elleni védelmet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Egy felügyelt HSM Azure Key Vault törlése végleges adatvesztéshez vezethet. A szervezet egy rosszindulatú belső felhasználója törölheti és kiürítheti Azure Key Vault Managed HSM-et. A végleges törlés elleni védelem megvédi a belső támadásokkal szemben azáltal, hogy kötelező megőrzési időszakot kényszerít ki a managed HSM Azure Key Vault törlése esetén. A szervezeten vagy a Microsofton belül senki sem törölheti a felügyelt HSM Azure Key Vault a felügyelt HSM-et a törlési megőrzési időszak alatt. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[A kulcstartóknál engedélyezni kell a véglegesen kiürítés elleni védelmet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A kulcstartó rosszindulatú törlése végleges adatvesztéshez vezethet. A szervezet egy rosszindulatú belső felhasználója törölheti és kiürítheti a kulcstartókat. A végleges törlés elleni védelem úgy védi a belső támadásokat, hogy kötelező megőrzési időszakot kényszerít ki a törölt kulcstartók számára. A szervezeten vagy a Microsofton belül senki sem fogja tudni véglegesen törölni a kulcstartókat a soft delete megőrzési időszak alatt. |Naplózás, Megtagadás, Letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezése az Azure Kubernetes Servicesben

**Azonosító:** CIS Azure 8.5-tulajdonjog: Ügyfél 

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Kubernetes Servicesben szerepköralapú Access Control (RBAC) kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |A felhasználók által végrehajtható műveletek részletes szűréséhez az Role-Based Access Control (RBAC) használatával kezelheti az engedélyeket a Kubernetes Service-fürtökben, és konfigurálhatja a megfelelő engedélyezési házirendeket. |Naplózás, Letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Győződjön meg App Service, hogy a Hitelesítés be van állítva a Azure App Service

**Azonosító:** CIS Azure 9.1 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A hitelesítést engedélyezni kell az API-alkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service a hitelesítés egy olyan funkció, amely megakadályozza, hogy a névtelen HTTP-kérések elérjék az API-alkalmazást, vagy hitelesítse azokat, amelyek jogkivonatokkal rendelkezik, mielőtt elérnék az API-alkalmazást |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[A hitelesítést engedélyezni kell a függvényalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service a hitelesítés egy olyan funkció, amely megakadályozza, hogy a névtelen HTTP-kérések elérjék a függvényalkalmazást, vagy hitelesítse azokat, amelyek jogkivonatokkal rendelkezik, mielőtt elérnék a függvényalkalmazást |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[A hitelesítést engedélyezni kell a webalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service a hitelesítés egy olyan szolgáltatás, amely megakadályozza, hogy a névtelen HTTP-kérések elérjék a webalkalmazást, vagy hitelesítse azokat, amelyek jogkivonatokkal rendelkezik, mielőtt elérnék a webalkalmazást |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Győződjön meg arról, hogy a webalkalmazás átirányítja az összes HTTP-forgalmat a HTTPS-Azure App Service

**Azonosító:** CIS Azure 9.2 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A webalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Győződjön meg arról, hogy a webalkalmazás a TLS-titkosítás legújabb verzióját használja

**Azonosító:** CIS Azure 9.3 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az API-alkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A függvényalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A webalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Győződjön meg arról, hogy a webalkalmazás "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítása "Be"

**Azonosító:** CIS Azure 9.4 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy az API-alkalmazás "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítása "Be"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt kérjen a bejövő kérelmekhez. Csak az érvényes tanúsítvánnyal rendelkezik ügyfelek érik el az alkalmazást. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Győződjön meg arról, hogy a WEBalkalmazás "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítása "Be"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt kérjen a bejövő kérelmekhez. Csak az érvényes tanúsítvánnyal rendelkezik ügyfelek érik el az alkalmazást. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[A függvényalkalmazásokban engedélyezni kell az "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítást](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt kérjen a bejövő kérelmekhez. Az alkalmazást csak az érvényes tanúsítványokkal elérhető ügyfelek érik el. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Győződjön meg arról, hogy a Regisztráció Azure Active Directory engedélyezve van a App Service

**Azonosító:** CIS Azure 9.5 **tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A felügyelt identitást az API-alkalmazásban kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Felügyelt identitás használata a fokozott hitelesítési biztonság érdekében |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[A felügyelt identitást a függvényalkalmazásban kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Felügyelt identitás használata a fokozott hitelesítési biztonság érdekében |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[A felügyelt identitást a webalkalmazásban kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Felügyelt identitás használata a fokozott hitelesítési biztonság érdekében |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a webalkalmazás futtatásához használja

**Azonosító:** CIS Azure 9.7 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha az API-alkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |A PHP-szoftverekhez rendszeres időközönként újabb verziók adták ki biztonsági hibák miatt vagy további funkciókkal. Az API-alkalmazásokhoz a legújabb PHP-verzió használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a WEBalkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |A PHP-szoftverekhez rendszeres időközönként újabb verziók adták ki biztonsági hibák miatt vagy további funkciókkal. A webalkalmazások legújabb PHP-verziójának használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás futtatásához használja

**Azonosító:** CIS Azure 9.8 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha az API-alkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Időnként újabb verziók adták ki a Python-szoftverekhez biztonsági hibák miatt vagy további funkciók miatt. Az API-alkalmazásokhoz a legújabb Python-verzió használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a függvényalkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Időnként újabb verziók adták ki a Python-szoftverekhez biztonsági hibák miatt vagy további funkciók miatt. A legújabb Python-verzió függvényalkalmazáshoz való használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Időnként újabb verziók adták ki a Python-szoftverekhez biztonsági hibák miatt vagy további funkciók miatt. A legújabb Python-verzió webalkalmazáshoz való használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha a webalkalmazás futtatásához használja

**Azonosító:** CIS Azure 9.9 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha az API-alkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Rendszeres időközönként újabb verziók adták ki a Javát biztonsági hibák miatt vagy további funkciók miatt. Az API-alkalmazásokhoz a legújabb Python-verzió használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha a függvényalkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |A Java-szoftverekhez biztonsági hibák vagy további funkciók miatt rendszeresen újabb verziók szabadulnak fel. A legújabb Java-verzió függvényalkalmazáshoz való használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha a webalkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |A Java-szoftverek újabb verzióit rendszeres időközönként biztonsági hibák vagy további funkciók miatt adták ki. A legújabb Java-verzió webalkalmazáshoz való használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Ha a webalkalmazás futtatásához használja, győződjön meg arról, hogy a "HTTP-verzió" a legújabb

**Azonosító:** CIS Azure 9.10 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha az API-alkalmazás futtatásához használja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |A HTTP-hez rendszeres időközönként újabb verziók is ki vannak adva biztonsági hibák vagy további funkciók miatt. A legújabb HTTP-verzió használata a webalkalmazások számára, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy az újabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Ha a függvényalkalmazás futtatásához használja, győződjön meg arról, hogy a "HTTP-verzió" a legújabb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |A HTTP-hez rendszeres időközönként újabb verziók is ki vannak adva biztonsági hibák vagy további funkciók miatt. A legújabb HTTP-verzió használata a webalkalmazások számára, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy az újabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a webalkalmazás futtatásához használja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Rendszeres időközönként újabb verziók adták ki a HTTP-hez biztonsági hibák miatt vagy további funkciók miatt. A legújabb HTTP-verzió használata a webalkalmazások számára, hogy kihasználja az esetleges biztonsági javításokat és/vagy az újabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> Az egyes Azure Policy a különböző országos Azure Government eltérőek lehetnek.

## <a name="next-steps"></a>Következő lépések

További cikkek a Azure Policy:

- [A jogszabályi megfelelőség](../concepts/regulatory-compliance.md) áttekintése.
- Lásd a [kezdeményezésdefiníció szerkezetét.](../concepts/initiative-definition-structure.md)
- Tekintsen át további példákat [a következő Azure Policy példákban:](./index.md).
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Ismerje meg, [hogyan szervizelődnek a nem megfelelő erőforrások.](../how-to/remediate-resources.md)
