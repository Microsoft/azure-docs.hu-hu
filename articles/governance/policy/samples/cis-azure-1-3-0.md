---
title: A CIS és Microsoft Azure 1.3.0 szabályozási megfelelőségi adatai
description: A CIS Microsoft Azure Foundations Benchmark 1.3.0 szabályozási megfelelőség beépített kezdeményezésének részletei. Minden vezérlő egy vagy több olyan definícióra van Azure Policy, amelyek az értékelést segítik.
ms.date: 04/21/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 14a93281eb93bee1bf6ecd15da8811088a692c29
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865620"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-130-regulatory-compliance-built-in-initiative"></a>A CIS Microsoft Azure Foundations Benchmark 1.3.0 szabályozási megfelelőség beépített kezdeményezés részletei

A következő cikk részletesen bemutatja, Azure Policy megfelelőségi megfelelőség  beépített kezdeményezési definíciója hogyan felel meg a CIS Microsoft Azure Foundations Benchmark 1.3.0 megfelelőségi tartományainak és vezérlőinek. 
További információ erről a megfelelőségi szabványról: [CIS Microsoft Azure Foundations Benchmark 1.3.0](https://www.cisecurity.org/benchmark/azure/). A _tulajdonjoggal kapcsolatos_ további Azure Policy [szabályzatdefiníciók és](../concepts/definition-structure.md#type) megosztott [felelősségek a felhőben való használatának definíciójában.](../../../security/fundamentals/shared-responsibility.md)

Az alábbi leképezések a CIS Microsoft Azure **Foundations Benchmark 1.3.0 vezérlőire vannak leképezve.** A jobb oldalon található navigációval közvetlenül egy adott megfelelőségi **tartományra ugorhat.** A vezérlők nagy része egy Azure Policy [definícióval](../overview.md) van megvalósítva. A kezdeményezés teljes definíciójának áttekintéshez nyissa meg a **szabályzatot** a Azure Portal majd válassza a **Definíciók** lapot.
Ezután keresse meg és válassza ki a **CIS Microsoft Azure Foundations Benchmark v1.3.0** Szabályozási megfelelőség beépített kezdeményezési definícióját.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [definícióhoz Azure Policy](../overview.md) vannak társítva.
> Ezek a szabályzatok segíthetnek felmérni [a vezérlőnek](../how-to/get-compliance-data.md) való megfelelőséget; A vezérlők és egy vagy több szabályzat között azonban gyakran nem egy-az-egyhez vagy teljes egyezés van. Ezért a **szabályzatban megfelelő** Azure Policy csak magukra a szabályzatdefiníciókra vonatkoznak; Ez nem biztosítja, hogy teljes mértékben megfelel a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyekre jelenleg egyetlen Azure Policy sem vonatkozik. Ezért a megfelelőségi Azure Policy csak részlegesen látható a teljes megfelelőségi állapotról. A megfelelőségi tartományok, vezérlők és Azure Policy megfelelőségi szabvány definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_3_0.json)

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Győződjön meg arról, hogy a többtényezős hitelesítés engedélyezve van az összes kiemelt jogosultságú felhasználó számára

**Azonosító:** CIS Azure 1.1 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes írási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[Az MFA-t engedélyezni kell az előfizetés tulajdonosi engedélyekkel rendelkező fiókjainál](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A fiókok vagy erőforrások biztonságának elkerülése érdekében engedélyezni kell a többtényezős hitelesítést (MFA) minden tulajdonosi engedéllyel rendelkező előfizetési fiókhoz. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Győződjön meg arról, hogy a többtényezős hitelesítés engedélyezve van az összes nem kiemelt jogosultságú felhasználó számára

**Azonosító:** CIS Azure 1.2 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes olvasási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-guest-users-are-reviewed-on-a-monthly-basis"></a>Vendégfelhasználók havi áttekintésének biztosítása

**Azonosító:** CIS Azure 1.3 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből, hogy megelőzhető legyen a nem szükséges hozzáférés. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Az olvasási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből, hogy megelőzhető legyen a nemtorlott hozzáférés. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Az írási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Az írási jogosultságokkal rendelkező külső fiókokat el kell távolítani az előfizetésből, hogy megelőzhető legyen a nemtorlott hozzáférés. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Győződjön meg arról, hogy nem jött létre egyéni előfizetés-tulajdonosi szerepkör

**Azonosító:** CIS Azure 1.21 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az egyéni előfizetés-tulajdonosi szerepkörök nem létezhetnek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Ez a szabályzat gondoskodik arról, hogy ne létezik egyéni előfizetés-tulajdonosi szerepkör. |Naplózás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-azure-defender-is-set-to-on-for-servers"></a>Győződjön meg arról Azure Defender hogy a kiszolgálókhoz a Be van állítva.

**Azonosító:** CIS Azure 2.1 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender engedélyezni kell a kiszolgálókhoz való biztonsági adatokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |Azure Defender kiszolgálókhoz való használata valós idejű fenyegetésvédelmet biztosít a kiszolgálói számítási feladatok számára, és biztonsági javaslatokat, valamint gyanús tevékenységekre vonatkozó riasztásokat hoz létre. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-app-service"></a>Győződjön meg arról Azure Defender hogy a be van állítva a App Service

**Azonosító:** CIS Azure 2.2 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender a App Service engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender a App Service kihasználja a felhő méretezését és az Azure felhőszolgáltatóként való láthatóságát a gyakori webalkalmazás-támadások figyelése érdekében. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-azure-sql-database-servers"></a>Győződjön meg Azure Defender, hogy az adatbázis-kiszolgálók Azure SQL be van állítva

**Azonosító:** CIS Azure 2.3 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender a Azure SQL Database-kiszolgálókhoz engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender SQL-hez elérhető funkciókkal felderítheti és enyhítheti az adatbázisok lehetséges biztonsági réseit, észlelheti az SQL-adatbázisokat fenyegető fenyegetéseket jelző rendellenes tevékenységeket, valamint felderítheti és osztályozhatja a bizalmas adatokat. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-sql-servers-on-machines"></a>Győződjön meg Azure Defender, hogy az SQL-kiszolgálókon a gépeken a Be van állítva

**Azonosító:** CIS Azure 2.4 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender gépeken található SQL-kiszolgálókhoz engedélyezni kell az sql-kiszolgálókhoz való beállítást](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender SQL-hez elérhető funkciókkal felderítheti és enyhítheti az adatbázisok lehetséges biztonsági réseit, észlelheti az SQL-adatbázisokat fenyegető fenyegetéseket jelző rendellenes tevékenységeket, valamint felderítheti és osztályozhatja a bizalmas adatokat. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-storage"></a>Győződjön meg arról Azure Defender hogy a Storage beállítása Be van állítva

**Azonosító:** CIS Azure 2.5 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender storage-tárfiókot engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender Storage-hoz való hozzáféréssel észlelni lehet a tárfiókok hozzáférésére vagy kihasználására tett szokatlan és potenciálisan rosszindulatú kísérleteket. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-kubernetes"></a>Győződjön meg arról Azure Defender hogy a Kuberneteshez a Be van állítva

**Azonosító:** CIS Azure 2.6 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender for Kubernetes engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes valós idejű fenyegetésvédelmet biztosít a tárolóba helyezni képes környezetekhez, és riasztásokat hoz létre a gyanús tevékenységekről. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-container-registries"></a>Győződjön meg arról Azure Defender hogy a tárolóregisztrálókhoz a Be van állítva

**Azonosító:** CIS Azure 2.7 **tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender tárolóregisztrálókhoz engedélyezni kell a tárolóregisztrálókhoz való beállítást](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |Azure Defender tároló-beállításjegyzékek biztonsági rések keresését biztosítják az elmúlt 30 napban lekért, a regisztrációs adatbázisba leküldett vagy importált rendszerképek biztonsági rések vizsgálatához, és rendszerképenként részletes eredményeket szolgáltatnak. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-key-vault"></a>Győződjön meg Azure Defender, hogy az on (Be) van-e Key Vault

**Azonosító:** CIS Azure 2.8 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender a Key Vault engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender a Key Vault egy további védelmi és biztonsági intelligenciát biztosít azáltal, hogy észleli a Key Vault-fiókok hozzáférésére vagy kihasználására tett szokatlan és potenciálisan káros kísérleteket. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Győződjön meg arról, hogy a "Monitorozási ügynök automatikus kiépítése" beállítás be van állítva

**AZONOSÍTÓ:** CIS Azure 2.11 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Log Analytics-ügynök automatikus építésének engedélyezve kell lennie az előfizetésben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |A biztonsági rések és fenyegetések figyelése érdekében a Azure Security Center gyűjt adatokat az Azure-beli virtuális gépekről. Az adatokat a Korábban Microsoft Monitoring Agent (MMA) néven ismert Log Analytics-ügynök gyűjti, amely beolvassa a biztonsággal kapcsolatos különböző konfigurációkat és eseménynaplókat a gépről, és elemzés céljából átmásolja az adatokat a Log Analytics-munkaterületre. Javasoljuk, hogy az automatikus kiépítés engedélyezésével automatikusan telepítse az ügynököt az összes támogatott Azure-beli és újonnan létrehozott virtuális gépre. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-additional-email-addresses-is-configured-with-a-security-contact-email"></a>Győződjön meg arról, hogy a "További e-mail-címek" beállítás biztonsági kapcsolattartói e-mail-címmel van konfigurálva

**AZONOSÍTÓ:** CIS Azure 2.13 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az előfizetések biztonsági problémák esetén egy kapcsolattartási e-mail-címmel kell, hogy rendelkezik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Annak érdekében, hogy a szervezet érintett tagjai értesítést kapnak, ha az egyik előfizetésében biztonsági incidens történik, állítson be egy biztonsági kapcsolattartót, aki e-mailes értesítéseket kap a Security Center. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-notify-about-alerts-with-the-following-severity-is-set-to-high"></a>Győződjön meg arról, hogy a "Riasztások értesítése a következő súlyosságú riasztásokkal kapcsolatban" beállítás "High" (Magas)

**Azonosító:** CIS Azure 2.14 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a nagy súlyosságú riasztások e-mailes értesítését](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Annak érdekében, hogy a szervezet érintett tagjai értesítést kapnak, ha az egyik előfizetésében biztonsági incidens történik, engedélyezze az e-mailes értesítéseket a súlyos riasztások esetén a Security Center. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

## <a name="storage-accounts"></a>Storage-fiókok

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Győződjön meg arról, hogy a "Biztonságos átvitelre van szükség" beállítás engedélyezve van

**Azonosító:** CIS Azure 3.1 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Naplózási követelmény a biztonságos átvitelhez a tárfiókban. A biztonságos átvitel egy olyan lehetőség, amely arra kényszeríti a tárfiókot, hogy csak biztonságos kapcsolatokról (HTTPS) fogadjon kéréseket. A HTTPS használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Győződjön meg arról, hogy a "Nyilvános hozzáférési szint" beállítás Privát a blobtárolókhoz

**Azonosító:** CIS Azure 3.5 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfiók nyilvános hozzáférését le kell tartozni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Az Azure Storage tárolóihoz és blobjaihoz való névtelen nyilvános olvasási hozzáférés kényelmes módja az adatok megosztásának, de biztonsági kockázatot jelenthet. A Nem szükséges névtelen hozzáférés által okozott adatsértés elkerülése érdekében a Microsoft javasolja a tárfiókhoz való nyilvános hozzáférés megakadályozását, kivéve, ha a forgatókönyv ezt megköveteli. |naplózás, tiltás, letiltás |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Győződjön meg arról, hogy a Storage-fiókok alapértelmezett hálózati hozzáférési szabálya megtagadásra van beállítva

**Id**: CIS Azure 3.6 Ownership : **Customer**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfióknak korlátoznia kell a hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |A tárfiókok hálózati hozzáférését korlátozni kell. Konfigurálja úgy a hálózati szabályokat, hogy csak az engedélyezett hálózatokról származó alkalmazások férnek hozzá a tárfiókhoz. Az adott internetről vagy helyszíni ügyfelekről származó kapcsolatok lehetővé teszik a hozzáférést adott Azure-beli virtuális hálózatokról származó forgalomhoz vagy nyilvános internetes IP-címtartományok felé |Naplózás, Megtagadás, Letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[A tárfiókok virtuális hálózati szabályokkal korlátozzák a hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |Az IP-alapú szűrés helyett előnyben részesített módszerként virtuális hálózati szabályokkal védheti meg a tárfiókokat a potenciális fenyegetésekkel szemben. Az IP-alapú szűrés letiltása megakadályozza, hogy a nyilvános IP-címek hozzáférjenek a tárfiókhoz. |Naplózás, Megtagadás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Győződjön meg arról, hogy a "Megbízható Microsoft-szolgáltatások" engedélyezve van a tárfiók-hozzáféréshez

**Azonosító:** CIS Azure 3.7 **tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfióknak engedélyeznie kell a megbízható Microsoft-szolgáltatások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Egyes Microsoft-szolgáltatások tárfiókokkal kommunikáló felhasználók olyan hálózatokról működnek, amelyek nem kaphatnak hozzáférést a hálózati szabályokkal. Annak érdekében, hogy az ilyen típusú szolgáltatás a kívántnak megfelelően működjön, hagyja, hogy a megbízható Microsoft-szolgáltatások megkerüljék a hálózati szabályokat. Ezek a szolgáltatások erős hitelesítést fognak használni a tárfiók eléréséhez. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

### <a name="ensure-storage-for-critical-data-are-encrypted-with-customer-managed-key"></a>Győződjön meg arról, hogy a kritikus fontosságú adatok tárolása ügyfél által kezelt kulccsal van titkosítva

**Azonosító:** CIS Azure 3.9-tulajdonjog: Ügyfél 

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfiókok a felhasználó által felügyelt kulcsot használják a titkosításhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |A tárfiókot nagyobb rugalmassággal biztosíthatja az ügyfél által kezelt kulcsokkal. Felhasználó által kezelt kulcs megadásakor a megadott kulccsal védi és szabályozza az adatokat titkosító kulcs hozzáférését. Az ügyfél által kezelt kulcsok használata további képességeket biztosít a kulcstitkosítási kulcs rotációja vagy az adatok titkosítással való törlésének szabályozásához. |Naplózás, Letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

## <a name="database-services"></a>Adatbázis-szolgáltatások

### <a name="ensure-that-auditing-is-set-to-on"></a>Győződjön meg arról, hogy a "Naplózás" beállítás be van állítva

**Azonosító:** CIS Azure 4.1.1 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell az SQL Server naplózását](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Engedélyezni kell a SQL Server naplózását, hogy a kiszolgálón lévő összes adatbázis adatbázisában nyomon követjük az adatbázis-tevékenységeket, és egy auditnaplóba mentsük őket. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Győződjön meg arról, hogy az "Adattitkosítás" be van állítva egy SQL Database

**Azonosító:** CIS Azure 4.1.2 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[transzparens adattitkosítás SQL-adatbázisokon engedélyezni kell az adatbázis-adatokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Engedélyezni kell a transzparens adattitkosítást az aktív adatok védelme és a megfelelőségi követelmények teljesítéséhez |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Győződjön meg arról, hogy a "Naplózás" megőrzése "több mint 90 nap"

**Azonosító:** CIS Azure 4.1.3 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfiók célhelyének naplózásával konfigurált SQL-kiszolgálókat 90 napos megőrzéssel vagy annál nagyobb megőrzéssel kell konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Incidensvizsgálati célból javasoljuk, hogy legalább 90 napra SQL Server adatmegőrzést a tárfiók célhelyéhez. Ellenőrizze, hogy megfelelőek-e az üzemeltetési régióhoz szükséges adatmegőrzési szabályok. Ez olykor kötelező a jogszabályi előírásoknak való megfeleléshez. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-threat-protection-atp-on-a-sql-server-is-set-to-enabled"></a>Győződjön meg arról, hogy az Advanced Threat Protection (ATP) az SQL Serveren "Enabled" (Engedélyezve) állapotú

**Azonosító:** CIS Azure 4.2.1 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A speciális adatbiztonságot engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Napló minden SQL Managed Instance fejlett adatbiztonság nélkül. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Engedélyezni kell a speciális adatbiztonságot az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |SQL-kiszolgálók naplózása Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-vulnerability-assessment-va-is-enabled-on-a-sql-server-by-setting-a-storage-account"></a>Tárfiók beállításával győződjön meg arról, hogy a sebezhetőségi felmérés (VA) engedélyezve van az SQL Serveren

**Azonosító:** CIS Azure 4.2.2 Ownership : **Customer**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A biztonsági rések felmérését engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Naplót SQL Managed Instance összes olyan vizsgálatot, amelyen nincs engedélyezve az ismétlődő sebezhetőségi felmérési vizsgálatok. A sebezhetőségi felmérés felderítheti, nyomon követheti és orvosolhatja az adatbázisok lehetséges biztonsági réseit. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Napló Azure SQL kiszolgálókon, amelyeken nincs engedélyezve az ismétlődő biztonsági rések felmérésének ellenőrzése. A sebezhetőségi felmérés felderítheti, nyomon követheti és orvosolhatja az adatbázisok lehetséges biztonsági réseit. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="ensure-that-va-setting-send-scan-reports-to-is-configured-for-a-sql-server"></a>Győződjön meg arról, hogy a Vizsgálati jelentések küldése a alkalmazásba beállítás be van állítva egy SQL Serverhez

**Azonosító:** CIS Azure 4.2.4 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az SQL Server sebezhetőségi felmérési beállításainak tartalmaznia kell egy e-mail-címet a vizsgálati jelentések fogadására](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |Győződjön meg arról, hogy a Biztonsági rések felmérése beállítások "Vizsgálati jelentések küldése" mezője tartalmaz egy e-mail-címet. Ez az e-mail-cím megkapja a vizsgálati eredmények összegzését, miután rendszeres időközönként lefutott egy vizsgálat az SQL-kiszolgálókon. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Győződjön meg arról, hogy az SSL-kapcsolat kényszerítés beállítása "ENABLED" a PostgreSQL-adatbáziskiszolgálóhoz

**Azonosító:** CIS Azure 4.3.1 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell az SSL-kapcsolat kényszerítését a MySQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középen" támadásokkal szembeni védelemben. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Győződjön meg arról, hogy az SSL-kapcsolat kényszerítés beállítása ENGEDÉLYEZVE a MySQL-adatbáziskiszolgálón

**Azonosító:** CIS Azure 4.3.2 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az SSL-kapcsolat kényszerítését engedélyezni kell a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL támogatja a Azure Database for PostgreSQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése segít a "középen" támadásokkal szembeni védelemben a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával. Ez a konfiguráció kényszeríti, hogy az SSL mindig engedélyezve legyen az adatbázis-kiszolgáló eléréséhez. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Ellenőrizze, hogy az "log_checkpoints" kiszolgálóparaméter "ON" van-e beállítva a PostgreSQL-adatbáziskiszolgálóhoz

**Azonosító:** CIS Azure 4.3.3 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a napló ellenőrzőpontjait a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Ez a szabályzat segít a környezetében az esetleges PostgreSQL-adatbázisok naplózásában anélkül, hogy log_checkpoints engedélyezné. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Ellenőrizze, hogy a "log_connections" kiszolgálóparaméter "ON" (Be) van-e állítva a PostgreSQL-adatbáziskiszolgálóhoz

**AZONOSÍTÓ:** CIS Azure 4.3.4 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a naplókapcsolatokat a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Ez a szabályzat segít a környezetében az esetleges PostgreSQL-adatbázisok naplózásában anélkül, hogy log_connections engedélyezné. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Ellenőrizze, hogy a "log_disconnections" kiszolgálóparaméter "ON" (BE) van-e állítva a PostgreSQL-adatbáziskiszolgálóhoz

**Azonosító:** CIS Azure 4.3.5 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A PostgreSQL-adatbáziskiszolgálók leválasztásait naplózza.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Ez a szabályzat segít a környezetében az engedélyezett postgreSQL-adatbázisok log_disconnections naplózásában. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Ellenőrizze, hogy a "connection_throttling" kiszolgálóparaméter "ON" (Be) van-e állítva a PostgreSQL-adatbáziskiszolgálóhoz

**Azonosító:** CIS Azure 4.3.6 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a kapcsolat szabályozását a PostgreSQL-adatbáziskiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Ez a szabályzat segít a környezetében az esetleges PostgreSQL-adatbázisok naplózásában anélkül, hogy engedélyezve lenne a kapcsolat szabályozása. Ez a beállítás engedélyezi az IP-címenkénti ideiglenes kapcsolati szabályozást a túl sok érvénytelen jelszó-bejelentkezési hiba esetén. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Győződjön meg Azure Active Directory, hogy a rendszergazda konfigurálva van

**Azonosító:** CIS Azure 4.4 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Egy Azure Active Directory-rendszergazdát kell kiépítenünk az SQL-kiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Naplózást végez egy sql Azure Active Directory-rendszergazda számára az Azure AD-hitelesítés engedélyezéséhez. Az Azure AD-hitelesítés lehetővé teszi az adatbázis-felhasználók és más felhasználók egyszerűsített engedélykezelését és központosított identitáskezelését Microsoft-szolgáltatások |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-customer-managed-key"></a>Győződjön meg arról, hogy az SQL Server TDE-védője ügyfél által felügyelt kulccsal van titkosítva

**Azonosító:** CIS Azure 4.5 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A felügyelt SQL-példányok az ügyfelek által felügyelt kulcsokat használják az adatok titkosításához az](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |A transzparens adattitkosítás (TDE) saját kulccsal való megvalósítása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, nagyobb biztonságot nyújt A HSM-háttérszolgáltatással, valamint a feladatok elkülönítésének előléptetése. Ez a javaslat a kapcsolódó megfelelőségi követelményekhez tartozó szervezetekre vonatkozik. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Az SQL-kiszolgálóknak ügyfél által kezelt kulcsokat kell használniuk az adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |A transzparens adattitkosítás (TDE) saját kulccsal történő megvalósítása nagyobb átláthatóságot és irányítást biztosít a TDE-védő felett, nagyobb biztonságot nyújt a HSM-háttérszolgáltatással, és lehetővé teszi a feladatok elkülönítését. Ez a javaslat a kapcsolódó megfelelőségi követelményekhez tartozó szervezetekre vonatkozik. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Győződjön meg arról, hogy a tevékenységnaplókat tároló Storage-tároló nem nyilvánosan elérhető

**AZONOSÍTÓ:** CIS Azure 5.1.3 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tárfiók nyilvános hozzáférését nem szabad engedélyezettnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Az Azure Storage tárolóihoz és blobjaihoz való névtelen nyilvános olvasási hozzáférés kényelmes módja az adatok megosztásának, de biztonsági kockázatot jelenthet. A nem szükséges névtelen hozzáférés által okozott adatsértés elkerülése érdekében a Microsoft azt javasolja, hogy akadályozza meg a tárfiókhoz való nyilvános hozzáférést, kivéve, ha a forgatókönyv ezt megköveteli. |naplózás, tiltás, letiltás |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Győződjön meg arról, hogy a tevékenységnaplókat tartalmazó tárolót tartalmazó tárfiók BYOK-titkosítással van titkosítva (saját kulcs használata)

**Azonosító:** CIS Azure 5.1.4 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A tevékenységnaplókat tartalmazó tárolót tartalmazó tárfiókot BYOK-val kell titkosítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Ez a szabályzat naplózza, ha a tevékenységnaplókat tartalmazó tárolót tartalmazó Storage-fiók BYOK-titkosítással van titkosítva. A szabályzat csak akkor működik, ha a tárfiók terv szerint ugyanabban az előfizetésben található, mint a tevékenységnaplók. Az Azure Storage-titkosítással kapcsolatos további információkat itt [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) talál.  |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Győződjön meg arról, hogy az Azure KeyVault naplózása engedélyezve van

**Azonosító:** CIS Azure 5.1.5 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell a Key Vault naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli használatát vizsgálati célokra, amikor biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a szabályzat-hozzárendelés létrehozásához

**Azonosító:** CIS Azure 5.2.1 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Tevékenységnapló-riasztásnak kell léteznie adott szabályzatműveletekkel kapcsolatban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Ez a szabályzat meghatározott szabályzatműveleteket naplóz, és nem konfigurált tevékenységnapló-riasztásokat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-policy-assignment"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a szabályzat-hozzárendelés törléséhez

**Azonosító:** CIS Azure 5.2.2 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Tevékenységnapló-riasztásnak kell léteznie adott szabályzatműveletekkel kapcsolatban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Ez a szabályzat meghatározott szabályzatműveleteket naplóz, és nem konfigurált tevékenységnapló-riasztásokat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a hálózati biztonsági csoport létrehozásához vagy frissítéséhez

**Azonosító:** CIS Azure 5.2.3 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat adott felügyeleti műveleteket naplóz, és nincsenek konfigurálva tevékenységnapló-riasztások. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Győződjön meg arról, hogy a Hálózati biztonsági csoport törlése tevékenységnapló-riasztás létezik

**Azonosító:** CIS Azure 5.2.4 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat adott felügyeleti műveleteket naplóz, és nincsenek konfigurálva tevékenységnapló-riasztások. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a hálózati biztonsági csoport szabályának létrehozásához vagy frissítéséhez

**Azonosító:** CIS Azure 5.2.5 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat adott felügyeleti műveleteket naplóz, és nincsenek konfigurálva tevékenységnapló-riasztások. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a Hálózati biztonsági csoport törlése szabályhoz

**Azonosító:** CIS Azure 5.2.6 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat meghatározott felügyeleti műveleteket naplóz, és nem konfigurált tevékenységnapló-riasztásokat. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a biztonsági megoldás létrehozásához vagy frissítéséhez

**Azonosító:** CIS Azure 5.2.7 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott biztonsági műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ez a szabályzat meghatározott biztonsági műveleteket naplóz, amelyekhez nincs konfigurálva tevékenységnapló-riasztás. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a biztonsági megoldás törléséhez

**Azonosító:** CIS Azure 5.2.8 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott biztonsági műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ez a szabályzat naplóz bizonyos biztonsági műveleteket, amelyekhez nincs tevékenységnapló-riasztás konfigurálva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Ellenőrizze, hogy létezik-e tevékenységnapló-riasztás a tűzfalszabály létrehozásához, frissítéséhez SQL Server törléséhez

**Id:** CIS Azure 5.2.9 Ownership : **Customer**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adott felügyeleti műveletekhez tevékenységnapló-riasztásnak kell léteznie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ez a szabályzat adott felügyeleti műveleteket naplóz, és nincsenek konfigurálva tevékenységnapló-riasztások. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-diagnostic-logs-are-enabled-for-all-services-which-support-it"></a>Győződjön meg arról, hogy a diagnosztikai naplók engedélyezve vannak az azt támogató összes szolgáltatáshoz.

**Azonosító:** CIS Azure 5.3 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A diagnosztikai naplókat App Services engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Naplózza az alkalmazás diagnosztikai naplóinak engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli vizsgálatot, ha biztonsági incidens történik vagy a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[Engedélyezni kell az Azure Data Lake Store-erőforrásnaplókat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell a Azure Stream Analytics erőforrásnaplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell az erőforrásnaplókat a Batch-fiókokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell a Data Lake Analytics erőforrásnaplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell az erőforrásnaplókat az eseményközpontban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli, vizsgálati célokra való használatát; ha biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell a IoT Hub erőforrásnaplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli, vizsgálati célokra való használatát; ha biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell a Key Vault naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli használatát vizsgálati célokra, amikor biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell a Logic Apps naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Naplózza az erőforrásnaplók engedélyezését. Ez lehetővé teszi a tevékenységútvonalak újbóli, vizsgálati célokra való használatát; ha biztonsági incidens történik, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell az erőforrásnaplókat a Keresési szolgáltatásokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell a Service Bus naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Naplózza az erőforrásnaplók engedélyezését. Így újból létrehozhatja a vizsgálati célokra használható tevékenységútvonalat; biztonsági incidens vagy a hálózat biztonsága esetén |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Engedélyezni kell a Virtual Machine Scale Sets naplóit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Javasoljuk, hogy engedélyezze a naplókat, hogy a tevékenységnapló újra létre legyen hozva, amikor vizsgálatokra van szükség incidens vagy biztonsági incidens esetén. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

## <a name="networking"></a>Hálózatkezelés

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Győződjön meg arról, hogy az RDP-hozzáférés korlátozva van az internetről

**Azonosító:** CIS Azure 6.1 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az internetről való RDP-elérést le kell tiltani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Ez a szabályzat naplót végez minden olyan hálózati biztonsági szabályt, amely engedélyezi az RDP-elérést az internetről |Naplózás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Győződjön meg arról, hogy az SSH-hozzáférés korlátozva van az internetről

**Azonosító:** CIS Azure 6.2 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az internetről való SSH-hozzáférést le kell tiltani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Ez a szabályzat naplót naplól minden olyan hálózati biztonsági szabályt, amely engedélyezi az SSH-hozzáférést az internetről |Naplózás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Győződjön meg arról Network Watcher hogy az "Enabled" (Engedélyezve)

**Azonosító:** CIS Azure 6.5-tulajdonjog: Ügyfél 

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher egy regionális szolgáltatás, amely lehetővé teszi a feltételek figyelése és diagnosztizálása az Azure-ban, illetve az Azure-ban a hálózati forgatókönyvek szintjén. A forgatókönyvszintű figyelés lehetővé teszi a teljes hálózatszintű nézet problémáinak diagnosztizálát. A hálózati diagnosztikai és vizualizációs eszközök Network Watcher segítségével megértheti, diagnosztizálhatja és betekintheti a hálózatát az Azure-ban. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-virtual-machines-are-utilizing-managed-disks"></a>Győződjön meg Virtual Machines, hogy az alkalmazás Managed Disks

**Azonosító:** CIS Azure 7.1 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Felügyelt lemezeket nem használ virtuális gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Ez a szabályzat naplóz olyan virtuális gépeket, amelyek nem használják a felügyelt lemezeket |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |

### <a name="ensure-that-os-and-data-disks-are-encrypted-with-cmk"></a>Győződjön meg arról, hogy az "operációsrendszer- és adatlemezek" cmk-titkosítással vannak titkosítva

**Azonosító:** CIS Azure 7.2 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lemeztitkosítást kell alkalmazni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Az engedélyezett lemeztitkosítással nem rendelkező virtuális gépeket a Azure Security Center figyelik. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted-with-cmk"></a>Győződjön meg arról, hogy a "Nem gyorsítótárazó lemezek" CMK-val vannak titkosítva

**Azonosító:** CIS Azure 7.3 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A nem gyorsítótárazó lemezeket titkosítani kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Ez a szabályzat a titkosítás engedélyezése nélkül naplóz minden nem gyorsítótárazó lemezt. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Győződjön meg arról, hogy csak jóváhagyott bővítmények vannak telepítve

**Azonosító:** CIS Azure 7.4 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Csak jóváhagyott virtuálisgép-bővítményeket kell telepíteni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Ez a szabályzat a nem jóváhagyott virtuálisgép-bővítményeket szabályozza. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Győződjön meg arról, hogy az összes Virtual Machines legújabb operációsrendszer-javítás van alkalmazva

**Azonosító:** CIS Azure 7.5 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A rendszerfrissítéseket telepíteni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A kiszolgálók hiányzó biztonságirendszer-frissítéseit a Azure Security Center fogja figyelni |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Győződjön meg arról, hogy az összes Virtual Machines végpontvédelem telepítve van

**Azonosító:** CIS Azure 7.6 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hiányzó Endpoint Protection figyelése a Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |A telepített Endpoint Protection-ügynökkel nem Azure Security Center kiszolgálókat |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Egyéb biztonsági szempontok

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Győződjön meg arról, hogy a lejárati dátum minden kulcshoz be van állítva

**Azonosító:** CIS Azure 8.1 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault kulcsok lejárati dátummal kell, hogy egyedük legyen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |A titkosítási kulcsok lejárati dátumának meghatározottnak kell lennie, és nem lehet végleges. Az örök időre érvényes kulcsok több időt biztosítanak a potenciális támadók számára a kulcs feltörésében. Ajánlott biztonsági eljárás a titkosítási kulcsok lejárati dátumának beállítása. |Naplózás, Megtagadás, Letiltva |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Győződjön meg arról, hogy a lejárati dátum minden titkos kulcshoz be van állítva

**Azonosító:** CIS Azure 8.2-tulajdonjog: Ügyfél 

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault titkos kulcsok lejárati dátummal kell, hogy legyenek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |A titkos kulcsok lejárati dátumának meghatározottnak kell lennie, és nem lehet végleges. A örökre érvényes titkos kulcsok több időt biztosítanak a potenciális támadók számára a biztonságuk feltörésében. Ajánlott biztonsági eljárás a titkos kulcsok lejárati dátumának beállítása. |Naplózás, Megtagadás, Letiltva |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Győződjön meg arról, hogy a kulcstartó helyreállítható

**AZONOSÍTÓ:** CIS Azure 8.4 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A kulcstartóknál engedélyezni kell a véglegesen kiürítés elleni védelmet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A kulcstartó rosszindulatú törlése végleges adatvesztéshez vezethet. A szervezet egy rosszindulatú belső felhasználója törölheti és kiürítheti a kulcstartókat. A végleges törlés elleni védelem úgy védi a belső támadásokat, hogy kötelező megőrzési időszakot kényszerít ki a törölt kulcstartók számára. A szervezeten vagy a Microsofton belül senki sem fogja tudni véglegesen törölni a kulcstartókat a soft delete megőrzési időszak alatt. |Naplózás, Megtagadás, Letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezése az Azure Kubernetes Servicesben

**Azonosító:** CIS Azure 8.5 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Kubernetes Servicesben szerepköralapú Access Control (RBAC) kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |A felhasználók által végrehajtható műveletek részletes szűréséhez az Role-Based Access Control (RBAC) használatával kezelheti az engedélyeket a Kubernetes Service-fürtökben, és konfigurálhatja a megfelelő engedélyezési házirendeket. |Naplózás, Letiltva |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="app-service"></a>App Service

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Győződjön meg App Service, hogy a Hitelesítés be van állítva a Azure App Service

**Azonosító:** CIS Azure 9.1 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A hitelesítést engedélyezni kell az API-alkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service hitelesítés egy olyan funkció, amely megakadályozza, hogy a névtelen HTTP-kérések elérjék az API-alkalmazást, vagy hitelesítse azokat, amelyek jogkivonatokkal rendelkezik, mielőtt elérnék az API-alkalmazást |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[A hitelesítést engedélyezni kell a függvényalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service a hitelesítés egy olyan funkció, amely megakadályozza, hogy a névtelen HTTP-kérések elérjék a függvényalkalmazást, vagy hitelesítse azokat, amelyek jogkivonatokkal rendelkezik, mielőtt elérnék a függvényalkalmazást |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[A hitelesítést engedélyezni kell a webalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service a hitelesítés egy olyan funkció, amely megakadályozza, hogy névtelen HTTP-kérések érzék el a webalkalmazást, vagy hitelesítse azokat, amelyek jogkivonatokkal rendelkezik, mielőtt elérnék a webalkalmazást |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Győződjön meg arról, hogy a webalkalmazás átirányítja az összes HTTP-forgalmat a HTTPS-Azure App Service

**Azonosító:** CIS Azure 9.2 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A webalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Győződjön meg arról, hogy a webalkalmazás a TLS-titkosítás legújabb verzióját használja

**AZONOSÍTÓ:** CIS Azure 9.3 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az API-alkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A függvényalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Frissítés a TLS legújabb verziójára |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A webalkalmazásban a legújabb TLS-verziót kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Frissítés a legújabb TLS-verzióra |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Győződjön meg arról, hogy a webalkalmazás "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítása "Be"

**Azonosító:** CIS Azure 9.4 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy az API-alkalmazás "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítása "Be"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt kérjen a bejövő kérelmekhez. Csak az érvényes tanúsítvánnyal rendelkezik ügyfelek érik el az alkalmazást. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Győződjön meg arról, hogy a WEBalkalmazás "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítása "Be"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt kérjen a bejövő kérelmekhez. Csak az érvényes tanúsítvánnyal rendelkezik ügyfelek érik el az alkalmazást. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[A függvényalkalmazásokban engedélyezni kell az "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítást](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Az ügyféltanúsítványok lehetővé teszik, hogy az alkalmazás tanúsítványt kérjen a bejövő kérelmekhez. Az alkalmazást csak az érvényes tanúsítványokkal elérhető ügyfelek érik el. |Naplózás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Győződjön meg arról, hogy a Regisztráció Azure Active Directory engedélyezve van a App Service

**Azonosító:** CIS Azure 9.5 **tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A felügyelt identitást az API-alkalmazásban kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Felügyelt identitás használata a fokozott hitelesítési biztonság érdekében |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[A felügyelt identitást a függvényalkalmazásban kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Felügyelt identitás használata a fokozott hitelesítési biztonság érdekében |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[A felügyelt identitást a webalkalmazásban kell használni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Felügyelt identitás használata a fokozott hitelesítési biztonság érdekében |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a webalkalmazás futtatásához használja

**AZONOSÍTÓ:** CIS Azure 9.6 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha az API-alkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |A PHP-szoftverekhez biztonsági hibák vagy további funkciók miatt rendszeresen újabb verziók szabadulnak fel. Az API-alkalmazásokhoz a legújabb PHP-verzió használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a WEBalkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |A PHP-szoftverekhez biztonsági hibák vagy további funkciók miatt rendszeresen újabb verziók szabadulnak fel. A legújabb PHP-verzió webalkalmazáshoz való használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Ha a webalkalmazás futtatásához használja, győződjön meg arról, hogy a "Python-verzió" a legújabb

**Azonosító:** CIS Azure 9.7 **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha az API-alkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Időnként újabb verziók adták ki a Python-szoftverekhez biztonsági hibák miatt vagy további funkciók miatt. Az API-alkalmazásokhoz a legújabb Python-verzió használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a függvényalkalmazás részeként használja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Időnként újabb verziók adták ki a Python-szoftverekhez biztonsági hibák miatt vagy további funkciók miatt. A legújabb Python-verzió függvényalkalmazáshoz való használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás részeként használja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Időnként újabb verziók adták ki a Python-szoftverekhez biztonsági hibák miatt vagy további funkciók miatt. A legújabb Python-verzió webalkalmazáshoz való használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha a webalkalmazás futtatásához használja

**Azonosító:** CIS Azure 9.8 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha az API-alkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Rendszeres időközönként újabb verziók adták ki a Javát biztonsági hibák miatt vagy további funkciók miatt. Az API-alkalmazásokhoz a legújabb Python-verzió használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha a függvényalkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |A Java-szoftverekhez biztonsági hibák vagy további funkciók miatt rendszeresen újabb verziók szabadulnak fel. A legújabb Java-verzió függvényalkalmazáshoz való használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha a webalkalmazás részeként használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |A Java-szoftverek újabb verziói biztonsági hibák miatt vagy további funkciókkal adták ki őket. A legújabb Java-verzió webalkalmazáshoz való használata ajánlott, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy a legújabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Ha a webalkalmazás futtatásához használja, győződjön meg arról, hogy a "HTTP-verzió" a legújabb

**Azonosító:** CIS Azure 9.9 **tulajdonjoga:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha az API-alkalmazás futtatásához használja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |A HTTP-hez rendszeres időközönként újabb verziók is ki vannak adva biztonsági hibák vagy további funkciók miatt. A legújabb HTTP-verzió használata a webalkalmazások számára, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy az újabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a függvényalkalmazás futtatásához használja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |A HTTP-hez rendszeres időközönként újabb verziók is ki vannak adva biztonsági hibák vagy további funkciók miatt. A legújabb HTTP-verzió használata a webalkalmazások számára, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy az újabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a webalkalmazás futtatásához használja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |A HTTP-hez rendszeres időközönként újabb verziók is ki vannak adva biztonsági hibák vagy további funkciók miatt. A legújabb HTTP-verzió használata a webalkalmazások számára, hogy kihasználja a biztonsági javításokat (ha vannak) és/vagy az újabb verzió új funkcióit. Ez a szabályzat jelenleg csak a Linux-webalkalmazások esetében érvényes. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

### <a name="ensure-ftp-deployments-are-disabled"></a>Győződjön meg arról, hogy az FTP-telepítések le vannak tiltva

**Azonosító:** CIS Azure 9.10 **Tulajdonjog: Ügyfél**

|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az FTPS-re csak az API-alkalmazásban van szükség](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |FTPS kényszerítési funkció engedélyezése a fokozott biztonság érdekében |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[Csak FTPS szükséges a függvényalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |FTPS kényszerítési funkció engedélyezése a fokozott biztonság érdekében |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[FTPS-re van szükség a webalkalmazásban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |FTPS kényszerítési funkció engedélyezése a fokozott biztonság érdekében |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |

> [!NOTE]
> Az egyes Azure Policy a különböző országos Azure Government eltérőek lehetnek.

## <a name="next-steps"></a>Következő lépések

További cikkek a Azure Policy:

- [A jogszabályi megfelelőség](../concepts/regulatory-compliance.md) áttekintése.
- Lásd a [kezdeményezésdefiníció szerkezetét.](../concepts/initiative-definition-structure.md)
- Tekintsen át további példákat [a következő Azure Policy példákban:](./index.md).
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Ismerje meg, [hogyan szervizelődnek a nem megfelelő erőforrások.](../how-to/remediate-resources.md)
