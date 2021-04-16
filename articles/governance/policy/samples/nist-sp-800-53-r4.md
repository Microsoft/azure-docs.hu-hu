---
title: Az NIST SP 800-53 R4 jogszabályi megfelelési adatai
description: Az NIST SP 800-53 R4 szabályozási megfelelőség beépített kezdeményezésének részletei. Minden vezérlő egy vagy több olyan definícióra van Azure Policy, amelyek az értékelést segítik.
ms.date: 04/14/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 0fe88525bc574219729a7f90fba1bf82803e5854
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505343"
---
# <a name="details-of-the-nist-sp-800-53-r4-regulatory-compliance-built-in-initiative"></a>Az NIST SP 800-53 R4 szabályozási megfelelőség beépített kezdeményezés részletei

A következő cikk részletesen bemutatja, Azure Policy megfelelőségi szabályzatok  beépített  kezdeményezési definíciója hogyan felel meg az NIST SP 800-53 R4 megfelelőségi tartományának és vezérlőinek.
További információ erről a megfelelőségi szabványról: [NIST SP 800-53 R4.](https://nvd.nist.gov/800-53) A _tulajdonjoggal kapcsolatos_ további Azure Policy [szabályzatdefiníciók és](../concepts/definition-structure.md#type) megosztott [felelősségek a felhőben való alkalmazásában.](../../../security/fundamentals/shared-responsibility.md)

Az alábbi leképezések az **NIST SP 800-53 R4 vezérlőkre vannak leképezve.** A jobb oldalon található navigációval közvetlenül egy adott megfelelőségi **tartományra ugorhat.** Számos vezérlő egy új kezdeményezési [definícióval Azure Policy](../overview.md) van megvalósítva. A kezdeményezés teljes definíciójának áttekintéshez nyissa meg a **szabályzatot** a Azure Portal majd válassza a **Definíciók** lapot.
Ezután keresse meg és válassza ki az **NIST SP 800-53 R4** szabályozási megfelelőség beépített kezdeményezési definícióját.

Ez a beépített kezdeményezés az [NIST SP 800-53 R4](../../blueprints/samples/nist-sp-800-53-r4.md)tervminta részeként van üzembe helyezni.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [definícióhoz Azure Policy](../overview.md) vannak társítva.
> Ezek a szabályzatok segíthetnek felmérni [a](../how-to/get-compliance-data.md) vezérlőnek való megfelelőséget; A vezérlők és egy vagy több szabályzat között azonban gyakran nincs egy-az-egyhez vagy teljes egyezés. Ezért a **szabályzatban megfelelő** Azure Policy csak magukra a szabályzatdefiníciókra vonatkoznak; Ez nem biztosítja, hogy teljes mértékben megfelel a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyekre jelenleg egyetlen Azure Policy sem vonatkozik. Ezért a megfelelőségi Azure Policy a teljes megfelelőségi állapot részleges nézete. A megfelelőségi tartományok, vezérlők és Azure Policy megfelelőségi szabvány definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/NIST80053_audit.json)

## <a name="access-control"></a>Hozzáférés-vezérlés

### <a name="access-control-policy-and-procedures"></a>Access Control házirend és eljárások

**Azonosító:** NIST SP 800-53 R4 AC-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1000 – Access Control szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ef3cc79-733e-48ed-ab6f-7bf439e9b406) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1000.json) |
|[Microsoft Managed Control 1001 – Access Control szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e26f8c3-4bf3-4191-b8fc-d888805101b7) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1001.json) |

### <a name="account-management"></a>Fiókkezelés

**Azonosító:** NIST SP 800-53 R4 AC-2 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az elavult fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |Az elavult fiókokat el kell távolítani az előfizetéséből.  Az elavult fiókok olyan fiókok, amelyek bejelentkezése le van tiltva. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből.  Az elavult fiókok olyan fiókok, amelyek bejelentkezése le van tiltva. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből, hogy megelőzhető legyen a nemtorlott hozzáférés. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Az olvasási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből, hogy megelőzhető legyen a nem szükséges hozzáférés. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Az írási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Az írási jogosultságokkal rendelkező külső fiókokat el kell távolítani az előfizetésből, hogy megelőzhető legyen a nemtorlott hozzáférés. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[Microsoft Managed Control 1002 – Fiókkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F632024c2-8079-439d-a7f6-90af1d78cc65) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1002.json) |
|[Microsoft Managed Control 1003 – Fiókkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b68b179-3704-4ff7-b51d-7d65374d165d) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1003.json) |
|[Microsoft Managed Control 1004 – Fiókkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc17822dc-736f-4eb4-a97d-e6be662ff835) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1004.json) |
|[Microsoft Managed Control 1005 – Fiókkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b626abc-26d4-4e22-9de8-3831818526b1) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1005.json) |
|[Microsoft Managed Control 1006 – Fiókkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faae8d54c-4bce-4c04-b3aa-5b65b67caac8) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1006.json) |
|[Microsoft Managed Control 1007 – Fiókkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17200329-bf6c-46d8-ac6d-abf4641c2add) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1007.json) |
|[Microsoft Managed Control 1008 – Fiókkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8356cfc6-507a-4d20-b818-08038011cd07) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1008.json) |
|[Microsoft Managed Control 1009 – Fiókkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb26f8610-e615-47c2-abd6-c00b2b0b503a) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1009.json) |
|[Microsoft Managed Control 1010 – Fiókkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F784663a8-1eb0-418a-a98c-24d19bc1bb62) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1010.json) |
|[Microsoft Managed Control 1011 – Fiókkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7e6a54f3-883f-43d5-87c4-172dfd64a1f5) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1011.json) |
|[Microsoft Managed Control 1012 – Fiókkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefd7b9ae-1db6-4eb6-b0fe-87e6565f9738) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1012.json) |

### <a name="account-management--automated-system-account-management"></a>Fiókkezelési | Automatizált rendszerfiók-kezelés

**Azonosító:** NIST SP 800-53 R4 AC-2 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1013 – Fiókkezelés \| – Automatizált rendszerfiók-kezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fd7b917-d83b-4379-af60-51e14e316c61) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1013.json) |

### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Fiókkezelési | Ideiglenes/vészhelyzeti fiókok eltávolítása

**Azonosító:** NIST SP 800-53 R4 AC-2 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1014 – Az \| ideiglenes/vészhelyzeti fiókok fiókkezelésének eltávolítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5dee936c-8037-4df1-ab35-6635733da48c) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1014.json) |

### <a name="account-management--disable-inactive-accounts"></a>Fiókkezelési | Inaktív fiókok letiltása

**Azonosító:** NIST SP 800-53 R4 AC-2 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1015 – Fiókkezelés \| inaktív fiókok letiltása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F544a208a-9c3f-40bc-b1d1-d7e144495c14) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1015.json) |

### <a name="account-management--automated-audit-actions"></a>Fiókkezelési | Automatikus naplózási műveletek

**Azonosító:** NIST SP 800-53 R4 AC-2 (4) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1016 – Fiókkezelés \| – Automatizált naplózási műveletek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8b43277-512e-40c3-ab00-14b3b6e72238) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1016.json) |

### <a name="account-management--inactivity-logout"></a>Fiókkezelési | Inaktivitási kijelentkezés

**Azonosító:** NIST SP 800-53 R4 AC-2 (5) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1017 – Fiókkezelés \| inaktivitási kijelentkezés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fc3db37-e59a-48c1-84e9-1780cedb409e) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1017.json) |

### <a name="account-management--role-based-schemes"></a>Fiókkezelési | Role-Based sémák

**Azonosító:** NIST SP 800-53 R4 AC-2 (7) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Egy Azure Active Directory-rendszergazdát kell kiépítenünk az SQL-kiszolgálókhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Naplózást végez egy sql Azure Active Directory-rendszergazda számára az Azure AD-hitelesítés engedélyezéséhez. Az Azure AD-hitelesítés egyszerűsített engedélykezelést és központosított identitáskezelést tesz lehetővé az adatbázis-felhasználók és más Microsoft-szolgáltatások |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Egyéni RBAC-szabályok használatának naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Egyéni RBAC-szerepkörök helyett naplózhat olyan beépített szerepköröket, mint például a "Tulajdonos, a Közreműködés, az Olvasó", amelyek hibaarányt jeleznek. Az egyéni szerepkörök használata kivételként van kezelve, és szigorú felülvizsgálatot és fenyegetésmodellezést igényel |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Microsoft Managed Control 1018 – Fiókkezelési \| Role-Based sémák](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9121abf-e698-4ee9-b1cf-71ee528ff07f) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1018.json) |
|[Microsoft Managed Control 1019 – \| Fiókkezelési Role-Based sémái](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a3ee9b2-3977-459c-b8ce-2db583abd9f7) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1019.json) |
|[Microsoft Managed Control 1020 – \| Fiókkezelési Role-Based sémái](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b291ee8-3140-4cad-beb7-568c077c78ce) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1020.json) |
|[Service Fabric fürtök csak ügyfél-Azure Active Directory használjanak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Az ügyfél-hitelesítés használatának naplózása csak a Azure Active Directory keresztül Service Fabric |Naplózás, Megtagadás, Letiltva |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Fiókkezelési | A megosztott/csoportfiókok használatának korlátozásai

**Azonosító:** NIST SP 800-53 R4 AC-2 (9) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1021 – Megosztott/csoportfiókok használatának \| fiókkezelési korlátozásai](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a3eb0a3-428d-4669-baff-20a14eb4b551) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1021.json) |

### <a name="account-management--shared--group-account-credential-termination"></a>Fiókkezelési | Megosztott/csoportos fiók hitelesítő adatainak megszüntetése

**Azonosító:** NIST SP 800-53 R4 AC-2 (10) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1022 – Fiókkezelés \| megosztott /csoportfiók hitelesítő adatainak megszüntetése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F411f7e2d-9a0b-4627-a0b9-1700432db47d) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1022.json) |

### <a name="account-management--usage-conditions"></a>Fiókkezelési | Használati feltételek

**Azonosító:** NIST SP 800-53 R4 AC-2 (11) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1023 – Fiókkezelési \| használati feltételek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe55698b6-3dea-4aa9-99b9-d8218c6ab6e5) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1023.json) |

### <a name="account-management--account-monitoring--atypical-usage"></a>Fiókkezelési | Fiókfigyelés / atipikus használat

**Azonosító:** NIST SP 800-53 R4 AC-2 (12) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjainak időben elérhető hálózati hozzáférés-vezérléssel kell védeni őket](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehetséges igény szerinti hálózati (JIT)-hozzáférést a Azure Security Center figyeli javaslatként |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft Managed Control 1024 - Account Management \| Account Monitoring / Atypical Usage](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84914fb4-12da-4c53-a341-a9fd463bed10) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1024.json) |
|[Microsoft Managed Control 1025 – Fiókkezelési \| fiókfigyelés / atipikus használat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fadfe020d-0a97-45f4-a39c-696ef99f3a95) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1025.json) |

### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Fiókkezelési | Egyéni személyek fiókjainak High-Risk letiltása

**Azonosító:** NIST SP 800-53 R4 AC-2 (13) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1026 – Fiókkezelés – Fiókok letiltása \| egyéni High-Risk számára](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55419419-c597-4cd4-b51e-009fd2266783) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1026.json) |

### <a name="access-enforcement"></a>Hozzáférés-kényszerítési

**Azonosító:** NIST SP 800-53 R4 AC-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1027 – Hozzáférés-kényszerítési](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa76ca9b0-3f4a-4192-9a38-b25e4f8ae48c) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1027.json) |

### <a name="information-flow-enforcement"></a>Információáramlás kényszerítési

**Azonosító:** NIST SP 800-53 R4 AC-4 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A CORS nem engedélyezheti minden erőforrásnak, hogy hozzáférjen a webalkalmazásokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Az eredetközi erőforrás-megosztás (CORS) nem engedélyezheti minden tartomány számára a webalkalmazáshoz való hozzáférést. Csak a szükséges tartományok számára engedélyezze a webalkalmazással való interakciót. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[Microsoft Managed Control 1028 – Information Flow Enforcement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff171df5c-921b-41e9-b12b-50801c315475) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1028.json) |

### <a name="information-flow-enforcement--security-policy-filters"></a>Információáramlás kényszerítési | Biztonsági szabályzat szűrői

**Azonosító:** NIST SP 800-53 R4 AC-4 (8) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1029 - Information Flow Enforcement \| Security Policy Filters](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53ac8f8e-c2b5-4d44-8a2d-058e9ced9b69) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1029.json) |

### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Információáramlás kényszerítési | Az adatfolyamok fizikai/logikai elkülönítése

**Azonosító:** NIST SP 800-53 R4 AC-4 (21) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1030 – Information Flow Enforcement \| Physical /Logical Separation of Information Flows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3531453-b869-4606-9122-29c1cd6e7ed1) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1030.json) |

### <a name="separation-of-duties"></a>A feladatok elkülönítése

**Azonosító:** NIST SP 800-53 R4 AC-5 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Javasoljuk, hogy legfeljebb 3 előfizetés-tulajdonost jelöl ki, hogy csökkentse a feltört tulajdonossal való visszaélés veszélyét. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Windows rendszerű gépek naplózása, amelyeken hiányzik a Rendszergazdák csoportban megadott tagok bármelyike](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport nem tartalmaz a szabályzatparaméterben felsorolt egy vagy több tagot. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[A Rendszergazdák csoportban megadott tagokkal megjelölt Windows rendszerű gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport tartalmaz egy vagy több tagot a szabályzatparaméterben felsoroltak közül. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Microsoft Managed Control 1031 – A feladatok elkülönítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b93a801-fe25-4574-a60d-cb22acffae00) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1031.json) |
|[Microsoft Managed Control 1032 – A feladatok elkülönítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5aa85661-d618-46b8-a20f-ca40a86f0751) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1032.json) |
|[Microsoft Managed Control 1033 – A feladatok elkülönítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48540f01-fc11-411a-b160-42807c68896e) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1033.json) |
|[Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Javasoljuk, hogy a rendszergazdai hozzáférési redundancia érdekében több előfizetés-tulajdonost is kijelöljen. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege"></a>Legkevesebb jogosultság

**Azonosító:** NIST SP 800-53 R4 AC-6 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1034 – Least Privilege](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02a5ed00-6d2e-4e97-9a98-46c32c057329) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1034.json) |

### <a name="least-privilege--authorize-access-to-security-functions"></a>Least Privilege | Biztonsági funkciókhoz való hozzáférés jogosultsága

**Azonosító:** NIST SP 800-53 R4 AC-6 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1035 - Least Privilege \| Authorize Access To Security Functions](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca94b046-45e2-444f-a862-dc8ce262a516) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1035.json) |

### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Least Privilege | Nem emelt szintű hozzáférés a nem biztonsági funkciókhoz

**Azonosító:** NIST SP 800-53 R4 AC-6 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1036 - Least Privilege \| Non-Privileged Access For Nonsecurity Functions](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a16d673-8cf0-4dcf-b1d5-9b3e114fef71) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1036.json) |

### <a name="least-privilege--network-access-to-privileged-commands"></a>Least Privilege | Hálózati hozzáférés a privileged parancsokhoz

**Azonosító:** NIST SP 800-53 R4 AC-6 (3) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1037 - Least Privilege \| Network Access To Privileged Commands](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa4c2a3d-1294-41a3-9ada-0e540471e9fb) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1037.json) |

### <a name="least-privilege--privileged-accounts"></a>Least Privilege | Kiemelt jogosultságú fiókok

**Azonosító:** NIST SP 800-53 R4 AC-6 (5) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1038 – A legkevesebb jogosultsággal \| rendelkező kiemelt jogosultságú fiókok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26692e88-71b7-4a5f-a8ac-9f31dd05bd8e) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1038.json) |

### <a name="least-privilege--review-of-user-privileges"></a>Least Privilege | Felhasználói jogosultságok áttekintése

**Azonosító:** NIST SP 800-53 R4 AC-6 (7) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Javasoljuk, hogy legfeljebb 3 előfizetés-tulajdonost jelöl ki, hogy csökkentse a feltört tulajdonossal való visszaélés veszélyét. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Windows rendszerű gépek naplózása, amelyeken hiányzik a Rendszergazdák csoportban megadott tagok bármelyike](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport nem tartalmaz a szabályzatparaméterben felsorolt egy vagy több tagot. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[A Rendszergazdák csoportban megadott tagokkal megjelölt Windows rendszerű gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a helyi Rendszergazdák csoport tartalmaz egy vagy több tagot a szabályzatparaméterben felsoroltak közül. |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Microsoft Managed Control 1039 - Least Privilege \| Review Of User Privileges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a7b9de4-a8a2-4672-914d-c5f6752aa7f9) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1039.json) |
|[Microsoft Managed Control 1040 - Least Privilege \| Review Of User Privileges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F54205576-cec9-463f-ba44-b4b3f5d0a84c) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1040.json) |
|[Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Javasoljuk, hogy a rendszergazdai hozzáférési redundancia érdekében több előfizetés-tulajdonost is kijelöljen. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege--privilege-levels-for-code-execution"></a>Least Privilege | Jogosultsági szintek a kódvégrehajtáshoz

**Azonosító:** NIST SP 800-53 R4 AC-6 (8) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1041 – A legkevesebb jogosultsági \| jogosultsági szint a kódvégrehajtáshoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb3d8d15b-627a-4219-8c96-4d16f788888b) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1041.json) |

### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Least Privilege | A Privileged Functions használatának naplózása

**Azonosító:** NIST SP 800-53 R4 AC-6 (9) **Tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1042 - Least Privilege \| Auditing Use Of Privileged Functions](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F319dc4f0-0fed-4ac9-8fc3-7aeddee82c07) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1042.json) |

### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Least Privilege | A nem kiemelt jogosultságú felhasználók nem hajthatják végre az emelt szintű funkciókat

**Azonosító:** NIST SP 800-53 R4 AC-6 (10) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1043 - Least Privilege \| Prohibit non-Privileged Users from Executd Privileged Functions](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F361a77f6-0f9c-4748-8eec-bc13aaaa2455) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1043.json) |

### <a name="unsuccessful-logon-attempts"></a>Sikertelen bejelentkezési kísérletek

**Azonosító:** NIST SP 800-53 R4 AC-7 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1044 – Sikertelen bejelentkezési kísérletek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0abbac52-57cf-450d-8408-1208d0dd9e90) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1044.json) |
|[Microsoft Managed Control 1045 – Sikertelen bejelentkezési kísérletek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F554d2dd6-f3a8-4ad5-b66f-5ce23bd18892) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1045.json) |

### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Sikertelen bejelentkezési kísérletek | Mobileszköz végleges törlésével/törlésével

**Azonosító:** NIST SP 800-53 R4 AC-7 (2) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1046 - Automatic Account \| Lock Purge /Wipe Mobile Device](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b1aa965-7502-41f9-92be-3e2fe7cc392a) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1046.json) |

### <a name="system-use-notification"></a>Rendszerhasználati értesítés

**Azonosító:** NIST SP 800-53 R4 AC-8 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1047 – Rendszerhasználati értesítés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1ff6d62-a55c-41ab-90ba-90bb5b7b6f62) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1047.json) |
|[Microsoft Managed Control 1048 – Rendszerhasználati értesítés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F483e7ca9-82b3-45a2-be97-b93163a0deb7) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1048.json) |
|[Microsoft Managed Control 1049 – Rendszerhasználati értesítés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9adf7ba7-900a-4f35-8d57-9f34aafc405c) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1049.json) |

### <a name="concurrent-session-control"></a>Egyidejű munkamenet-vezérlés

**Azonosító:** NIST SP 800-53 R4 AC-10 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1050 – Egyidejű munkamenet-vezérlés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd20184c-b4ec-4ce5-8db6-6e86352d183f) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1050.json) |

### <a name="session-lock"></a>Munkamenet-zárolás

**Azonosító:** NIST SP 800-53 R4 AC-11 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1051 – Munkamenet-zárolás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7cac6ee9-b58b-40c8-a5ce-f0efc3d9b339) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1051.json) |
|[Microsoft Managed Control 1052 – Munkamenet-zárolás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F027cae1c-ec3e-4492-9036-4168d540c42a) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1052.json) |

### <a name="session-lock--pattern-hiding-displays"></a>Munkamenet-zárolási | Pattern-Hiding jelenik meg

**Azonosító:** NIST SP 800-53 R4 AC-11 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1053 – \| Munkamenet-zárolási Pattern-Hiding jelenik meg](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7582b19c-9dba-438e-aed8-ede59ac35ba3) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1053.json) |

### <a name="session-termination"></a>Munkamenet-megszakítás

**Azonosító:** NIST SP 800-53 R4 AC-12 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1054 – Munkamenetek megszakítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5807e1b4-ba5e-4718-8689-a0ca05a191b2) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1054.json) |

### <a name="session-termination--user-initiated-logouts--message-displays"></a>Munkamenet-megszakítási | User-Initiated kijelentkezés / Üzenet jelenik meg

**Azonosító:** NIST SP 800-53 R4 AC-12 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1055 – Munkamenetek \| megszakítása User-Initiated kijelentkezés/ Üzenet megjelenítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F769efd9b-3587-4e22-90ce-65ddcd5bd969) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1055.json) |
|[Microsoft Managed Control 1056 – MunkamenetUser-Initiated \| kijelentkezés/ Üzenet megjelenítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac43352f-df83-4694-8738-cfce549fd08d) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1056.json) |

### <a name="permitted-actions-without-identification-or-authentication"></a>Engedélyezett műveletek azonosítás vagy hitelesítés nélkül

**Azonosító:** NIST SP 800-53 R4 AC-14 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1057 – Engedélyezett műveletek azonosítás vagy hitelesítés nélkül](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78255758-6d45-4bf0-a005-7016bc03b13c) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1057.json) |
|[Microsoft Managed Control 1058 – Engedélyezett műveletek azonosítás vagy hitelesítés nélkül](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76e85d08-8fbb-4112-a1c1-93521e6a9254) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1058.json) |

### <a name="security-attributes"></a>Biztonsági attribútumok

**Azonosító:** NIST SP 800-53 R4 AC-16 **Tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A speciális adatbiztonságot engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Napló minden SQL Managed Instance fejlett adatbiztonság nélkül. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Engedélyezni kell a speciális adatbiztonságot az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |SQL-kiszolgálók naplózása Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="remote-access"></a>Távelérés

**Azonosító:** NIST SP 800-53 R4 AC-17 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1059 – Távelérés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa29b5d9f-4953-4afe-b560-203a6410b6b4) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1059.json) |
|[Microsoft Managed Control 1060 – Távelérés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a987fd-2003-45de-a120-014956581f2b) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1060.json) |

### <a name="remote-access--automated-monitoring--control"></a>Távelérési | Automatizált monitorozás /vezérlés

**Azonosító:** NIST SP 800-53 R4 AC-17 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rendszer által hozzárendelt felügyelt identitás hozzáadása a vendégkonfiguráció-hozzárendelések engedélyezéséhez identitások nélkül lévő virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Ez a szabályzat egy rendszer által hozzárendelt felügyelt identitást ad hozzá az Azure-ban üzemeltetett virtuális gépekhez, amelyek támogatottak a Vendégkonfiguráció által, de nincsenek felügyelt identitásaik. A rendszer által hozzárendelt felügyelt identitás minden vendégkonfiguráció-hozzárendelés előfeltétele, és a vendégkonfigurációs szabályzatdefiníciók használata előtt hozzá kell adni a gépekhez. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |módosítás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Rendszer által hozzárendelt felügyelt identitás hozzáadása a vendégkonfiguráció-hozzárendelések engedélyezéséhez a felhasználó által hozzárendelt identitással társított virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Ez a szabályzat egy rendszer által hozzárendelt felügyelt identitást ad hozzá a vendégkonfiguráció által támogatott, az Azure-ban üzemeltetett virtuális gépekhez, és legalább egy felhasználó által hozzárendelt identitással, de nem kap rendszer által hozzárendelt felügyelt identitást. A rendszer által hozzárendelt felügyelt identitás minden vendégkonfiguráció-hozzárendelés előfeltétele, és a vendégkonfigurációs szabályzatdefiníciók használata előtt hozzá kell adni a gépekhez. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |módosítás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Linux rendszerű gépek naplózása, amelyek lehetővé teszik a jelszavak nélküli fiókok távoli kapcsolatait](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a Linux rendszerű gépek jelszavak nélkül engedélyezik a távoli kapcsolatokat a fiókokból |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[A Linux vendégkonfigurációs bővítmény üzembe helyezése a vendégkonfiguráció-hozzárendelések Linux rendszerű virtuális gépeken való engedélyezéséhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Ez a szabályzat telepíti a Linux vendégkonfigurációs bővítményt az Azure-ban üzemeltetett, vendégkonfiguráció által támogatott Linux rendszerű virtuális gépekre. A Linux vendégkonfigurációs bővítmény előfeltétele az összes Linux vendégkonfiguráció-hozzárendelésnek, és a Linux vendégkonfigurációs szabályzatdefiníciók használata előtt telepíteni kell őket a gépekre. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[A Windows vendégkonfigurációs bővítmény üzembe helyezése a vendégkonfiguráció hozzárendelésének engedélyezéséhez Windows rendszerű virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Ez a szabályzat telepíti a Windows vendégkonfigurációs bővítményt az Azure-ban üzemeltetett, vendégkonfiguráció által támogatott Windows rendszerű virtuális gépekre. A Windows vendégkonfigurációs bővítmény előfeltétel minden Windows vendégkonfiguráció-hozzárendeléshez, és a Windows vendégkonfigurációs szabályzatdefiníciók használata előtt telepítve kell lennie a gépeken. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft Managed Control 1061 – Távelérés \| – Automatikus figyelés/vezérlés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ac22808-a2e8-41c4-9d46-429b50738914) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1061.json) |
|[A távoli hibakeresést ki kell kapcsolva a API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |A távoli hibakereséshez meg kell nyitni a bejövő portokat az API-alkalmazásokban. A távoli hibakeresést ki kell kapcsolva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[A függvényalkalmazások esetében a távoli hibakeresést ki kell kapcsolva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A távoli hibakereséshez meg kell nyitni a bejövő portokat a függvényalkalmazásokban. A távoli hibakeresést ki kell kapcsolva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[A webalkalmazások esetében a távoli hibakeresést ki kell kapcsolva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |A távoli hibakereséshez meg kell nyitni a bejövő portokat egy webalkalmazáson. A távoli hibakeresést ki kell kapcsolva. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[A tárfióknak korlátoznia kell a hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |A tárfiókok hálózati hozzáférését korlátozni kell. Konfigurálja úgy a hálózati szabályokat, hogy csak az engedélyezett hálózatokról származó alkalmazások férnek hozzá a tárfiókhoz. Az adott internetről vagy helyszíni ügyfelekről származó kapcsolatok lehetővé teszik a hozzáférést adott Azure-beli virtuális hálózatokról származó forgalomhoz vagy a nyilvános internetes IP-címtartományok felé |Naplózás, Megtagadás, Letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Távelérési | A titkosság/integritás védelme titkosítással

**Azonosító:** NIST SP 800-53 R4 AC-17 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1062 – \| Titkosság/integritás távelérés-védelme titkosítással](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4708723f-e099-4af1-bbf9-b6df7642e444) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1062.json) |

### <a name="remote-access--managed-access-control-points"></a>Távelérési | Felügyelt Access Control pontok

**Azonosító:** NIST SP 800-53 R4 AC-17 (3) **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1063 – Távelérés felügyelt \| Access Control pontok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F593ce201-54b2-4dd0-b34f-c308005d7780) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1063.json) |

### <a name="remote-access--privileged-commands--access"></a>Távelérési | Emelt szintű parancsok / Hozzáférés

**Azonosító:** NIST SP 800-53 R4 AC-17 (4) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1064 – Távelérés emelt szintű \| parancsai / Hozzáférés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb4d9508-cbf0-4a3c-bb5c-6c95b159f3fb) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1064.json) |
|[Microsoft Managed Control 1065 – Távelérés emelt szintű \| parancsai / Hozzáférés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff87b8085-dca9-4cf1-8f7b-9822b997797c) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1065.json) |

### <a name="remote-access--disconnect--disable-access"></a>Távelérési | Hozzáférés leválasztása/letiltása

**Azonosító:** NIST SP 800-53 R4 AC-17 (9) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1066 – Távelérés \| leválasztása /Hozzáférés letiltása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4455c2e8-c65d-4acf-895e-304916f90b36) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1066.json) |

### <a name="wireless-access"></a>Vezeték nélküli hozzáférés

**Azonosító:** NIST SP 800-53 R4 AC-18 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1067 – Vezeték nélküli hozzáférés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5e54f6-0127-44d0-8b61-f31dc8dd6190) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1067.json) |
|[Microsoft Managed Control 1068 – Vezeték nélküli hozzáférés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d045bca-a0fd-452e-9f41-4ec33769717c) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1068.json) |

### <a name="wireless-access--authentication-and-encryption"></a>Vezeték nélküli | Hitelesítés és titkosítás

**Azonosító:** NIST SP 800-53 R4 AC-18 (1) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1069 – Vezeték nélküli hozzáférés \| hitelesítése és titkosítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F91c97b44-791e-46e9-bad7-ab7c4949edbb) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1069.json) |

### <a name="wireless-access--disable-wireless-networking"></a>Vezeték nélküli | Vezeték nélküli hálózat letiltása

**Azonosító:** NIST SP 800-53 R4 AC-18 (3) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1070 – Vezeték nélküli hozzáférés letiltása \| Vezeték nélküli hálózat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68f837d0-8942-4b1e-9b31-be78b247bda8) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1070.json) |

### <a name="wireless-access--restrict-configurations-by-users"></a>Vezeték nélküli | Konfigurációk korlátozása felhasználók szerint

**Azonosító:** NIST SP 800-53 R4 AC-18 (4) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1071 – Vezeték nélküli hozzáférés felhasználók szerint \| korlátozza a konfigurációkat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a437f5b-9ad6-4f28-8861-de404d511ae4) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1071.json) |

### <a name="wireless-access--antennas--transmission-power-levels"></a>Vezeték nélküli | Az átvitel és az átviteli teljesítmény szintje

**Azonosító:** NIST SP 800-53 R4 AC-18 (5) **Tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1072 – Vezeték nélküli hozzáférés \| És Átviteli teljesítményszintek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ca29e41-34ec-4e70-aba9-6248aca18c31) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1072.json) |

### <a name="access-control-for-mobile-devices"></a>Access Control mobileszközökhöz

**Azonosító:** NIST SP 800-53 R4 AC-19 **Tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1073 – Access Control mobileszközökhöz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fab55cdb0-c7dd-4bd8-ae22-a7cea7594e9c) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1073.json) |
|[Microsoft Managed Control 1074 – Access Control mobileszközökhöz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F27a69937-af92-4198-9b86-08d355c7e59a) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1074.json) |

### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Access Control mobileszközök | Teljes eszköz/Container-Based titkosítás

**Azonosító:** NIST SP 800-53 R4 AC-19 (5) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1075 – Access Control for Mobile Devices \| Full Device /Container-Based Encryption](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc933d22-04df-48ed-8f87-22a3773d4309) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1075.json) |

### <a name="use-of-external-information-systems"></a>Külső információs rendszerek használata

**Azonosító:** NIST SP 800-53 R4 AC-20 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1076 – Külső információs rendszerek használata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98a4bd5f-6436-46d4-ad00-930b5b1dfed4) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1076.json) |
|[Microsoft Managed Control 1077 – Külső információs rendszerek használata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2dad3668-797a-412e-a798-07d3849a7a79) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1077.json) |

### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>A külső információs rendszerek | Az engedélyezett használat korlátai

**Azonosító:** NIST SP 800-53 R4 AC-20 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1078 – A külső információs rendszerek \| korlátainak használata engedélyezett használatra](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb25faf85-8a16-4f28-8e15-d05c0072d64d) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1078.json) |
|[Microsoft Managed Control 1079 – A külső információs rendszerek korlátainak \| használata engedélyezett használatra](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F85c32733-7d23-4948-88da-058e2c56b60f) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1079.json) |

### <a name="use-of-external-information-systems--portable-storage-devices"></a>A külső információs rendszerek | Hordozható tárolóeszközök

**Azonosító:** NIST SP 800-53 R4 AC-20 (2) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1080 – Külső információs rendszerek hordozható \| tárolóeszközeinek használata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F852981b4-a380-4704-aa1e-2e52d63445e5) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1080.json) |

### <a name="information-sharing"></a>Információmegosztás

**Azonosító:** NIST SP 800-53 R4 AC-21 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1081 – Információmegosztás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3867f2a9-23bb-4729-851f-c3ad98580caf) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1081.json) |
|[Microsoft Managed Control 1082 – Információmegosztás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24d480ef-11a0-4b1b-8e70-4e023bf2be23) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1082.json) |

### <a name="publicly-accessible-content"></a>Nyilvánosan elérhető tartalom

**Azonosító:** NIST SP 800-53 R4 AC-22 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1083 – Nyilvánosan elérhető tartalom](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e319cb6-2ca3-4a58-ad75-e67f484e50ec) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1083.json) |
|[Microsoft Managed Control 1084 – Nyilvánosan elérhető tartalom](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0eb15db-dd1c-4d1d-b200-b12dd6cd060c) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1084.json) |
|[Microsoft Managed Control 1085 – Nyilvánosan elérhető tartalom](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d117e0-38b0-4bbb-aaab-563be5dd10ba) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1085.json) |
|[Microsoft Managed Control 1086 – Nyilvánosan elérhető tartalom](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb321e6f-16a0-4be3-878f-500956e309c5) |A Microsoft ezt a Access Control implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1086.json) |

## <a name="awareness-and-training"></a>Tudatosság és képzés

### <a name="security-awareness-and-training-policy-and-procedures"></a>Biztonsági tudatosság és betanítás szabályzata és eljárásai

**Azonosító:** NIST SP 800-53 R4 AT-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1087 – Biztonsági tudatosság és képzés szabályzata és eljárásai](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F100c82ba-42e9-4d44-a2ba-94b209248583) |A Microsoft megvalósítja ezt a tudatosság- és képzésvezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1087.json) |
|[Microsoft Managed Control 1088 – Biztonsági tudatosság és képzés szabályzata és eljárásai](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d50f99d-1356-49c0-934a-45f742ba7783) |A Microsoft megvalósítja ezt a tudatosság- és képzésvezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1088.json) |

### <a name="security-awareness-training"></a>Biztonsági tudatosságot képző képzés

**Azonosító:** NIST SP 800-53 R4 AT-2 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1089 - Security Awareness Training](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef080e67-0d1a-4f76-a0c5-fb9b0358485e) |A Microsoft megvalósítja ezt a tudatosság- és képzésvezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1089.json) |
|[Microsoft Managed Control 1090 - Security Awareness Training](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fb740e5-cbc7-4d10-8686-d1bf826652b1) |A Microsoft megvalósítja ezt a Tudatosság és képzés vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1090.json) |
|[Microsoft Managed Control 1091 - Security Awareness Training](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb23bd715-5d1c-4e5c-9759-9cbdf79ded9d) |A Microsoft megvalósítja ezt a Tudatosság és képzés vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1091.json) |

### <a name="security-awareness-training--insider-threat"></a>Security Awareness Training | Insider Threat

**Azonosító:** NIST SP 800-53 R4 AT-2 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1092 - Security Awareness Training \| Insider Threat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8a29d47b-8604-4667-84ef-90d203fcb305) |A Microsoft megvalósítja ezt a Tudatosság és képzés vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1092.json) |

### <a name="role-based-security-training"></a>Role-Based betanítás

**Azonosító:** NIST SP 800-53 R4 AT-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1093 – Role-Based biztonsági képzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a0bdeeb-15f4-47e8-a1da-9f769f845fdf) |A Microsoft megvalósítja ezt a Tudatosság és képzés vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1093.json) |
|[Microsoft Managed Control 1094 – Role-Based biztonsági képzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b1853e0-8973-446b-b567-09d901d31a09) |A Microsoft megvalósítja ezt a tudatosság- és képzésvezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1094.json) |
|[Microsoft Managed Control 1095 – Role-Based biztonsági képzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc3f6f7a-057b-433e-9834-e8c97b0194f6) |A Microsoft megvalósítja ezt a tudatosság- és képzésvezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1095.json) |

### <a name="role-based-security-training--practical-exercises"></a>Role-Based biztonsági betanítás | Gyakorlati gyakorlatok

**Azonosító:** NIST SP 800-53 R4 AT-3 (3) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1096 – Role-Based biztonsági képzés gyakorlati \| gyakorlata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F420c1477-aa43-49d0-bd7e-c4abdd9addff) |A Microsoft megvalósítja ezt a tudatosság- és képzésvezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1096.json) |

### <a name="role-based-security-training--suspicious-communications-and-anomalous-system-behavior"></a>Role-Based biztonsági betanítás | Gyanús kommunikáció és rendellenes rendszerviselkedés

**Azonosító:** NIST SP 800-53 R4 AT-3 (4) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1097 – Role-Based biztonsági betanítás gyanús kommunikációra és \| rendellenes rendszerviselkedésre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3e4836-f19e-47eb-a8cd-c3ca150452c0) |A Microsoft megvalósítja ezt a Tudatosság és képzés vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1097.json) |

### <a name="security-training-records"></a>Biztonsági betanítás rekordjai

**Azonosító:** NIST SP 800-53 R4 AT-4 Ownership : **Shared**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1098 – Biztonsági képzési rekordok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84363adb-dde3-411a-9fc1-36b56737f822) |A Microsoft megvalósítja ezt a Tudatosság és képzés vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1098.json) |
|[Microsoft Managed Control 1099 – Biztonsági képzési rekordok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01910bab-8639-4bd0-84ef-cc53b24d79ba) |A Microsoft megvalósítja ezt a Tudatosság és képzés vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1099.json) |

## <a name="audit-and-accountability"></a>Naplózás és elszámoltathatóság

### <a name="audit-and-accountability-policy-and-procedures"></a>Naplózási és elszámoltathatósági szabályzat és eljárások

**Azonosító:** NIST SP 800-53 R4 AU-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1100 – Naplózási és elszámoltathatósági szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4057863c-ca7d-47eb-b1e0-503580cba8a4) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1100.json) |
|[Microsoft Managed Control 1101 – Naplózási és elszámoltathatósági szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7327b708-f0e0-457d-9d2a-527fcc9c9a65) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1101.json) |

### <a name="audit-events"></a>Naplózási események

**Azonosító:** NIST SP 800-53 R4 AU-2 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1102 – Naplózási események](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9943c16a-c54c-4b4a-ad28-bfd938cdbf57) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1102.json) |
|[Microsoft Managed Control 1103 – Naplózási események](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16feeb31-6377-437e-bbab-d7f73911896d) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1103.json) |
|[Microsoft Managed Control 1104 – Naplózási események](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcdd8d244-18b2-4306-a1d1-df175ae0935f) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1104.json) |
|[Microsoft Managed Control 1105 – Naplózási események](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b73f57b-587d-4470-a344-0b0ae805f459) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1105.json) |

### <a name="audit-events--reviews-and-updates"></a>Események naplózása | Felülvizsgálatok és frissítések

**Azonosító:** NIST SP 800-53 R4 AU-2 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1106 – Események \| áttekintésének és frissítésének naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd2b4feae-61ab-423f-a4c5-0e38ac4464d8) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1106.json) |

### <a name="content-of-audit-records"></a>A naplórekordok tartalma

**Azonosító:** NIST SP 800-53 R4 AU-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1107 – Auditrekordok tartalma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb29ed931-8e21-4779-8458-27916122a904) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1107.json) |

### <a name="content-of-audit-records--additional-audit-information"></a>A naplórekordok tartalma | További naplózási információk

**Azonosító:** NIST SP 800-53 R4 AU-3 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1108 – A naplózási rekordok tartalma \| – további naplózási információk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9ad559e-c12d-415e-9a78-e50fdd7da7ba) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1108.json) |

### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Naplórekordok tartalmának | Tervezett naplózási rekord tartalmának központosított kezelése

**Azonosító:** NIST SP 800-53 R4 AU-3 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics-munkaterület naplózása virtuális géphez – Jelentés eltérése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Nem megfelelőként jelenti a virtuális gépeket, ha nem jelentkeznek be a szabályzat-/kezdeményezési hozzárendelésben megadott Log Analytics-munkaterületre. |Ellenőrzési |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[A Log Analytics-ügynököt engedélyezni kell a felsorolt virtuálisgép-rendszerképekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Nem megfelelőként jelenti a virtuális gépeket, ha a virtuálisgép-rendszerkép nem szerepel a meghatározott listában, és az ügynök nincs telepítve. |AuditIfNotExists, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[A Log Analytics-ügynököt engedélyezni kell a virtuálisgép-méretezési csoportban a felsorolt virtuálisgép-rendszerképekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Nem megfelelőként jelenti a virtuálisgép-méretezési készleteket, ha a virtuálisgép-rendszerkép nem szerepel a meghatározott listában, és az ügynök nincs telepítve. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft Managed Control 1109 – A naplórekordok tartalma a tervezett naplórekordok tartalmának \| központosított kezelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d9ffa23-ad92-4d0d-b1f4-7db274cc2aec) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1109.json) |

### <a name="audit-storage-capacity"></a>Tárolókapacitás naplózása

**Azonosító:** NIST SP 800-53 R4 AU-4 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1110 – Tárolókapacitás naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6182bfa7-0f2a-43f5-834a-a2ddf31c13c7) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1110.json) |

### <a name="response-to-audit-processing-failures"></a>Válasz a naplózási feldolgozási hibákra

**Azonosító:** NIST SP 800-53 R4 AU-5 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A speciális adatbiztonságot engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Minden adat SQL Managed Instance adatbiztonság nélkül naplól. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Engedélyezni kell a speciális adatbiztonságot az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |SQL-kiszolgálók naplózása Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Diagnosztikai beállítás naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |A kiválasztott erőforrástípusok diagnosztikai beállításának naplózása |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Engedélyezni kell az SQL Server naplózását](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Engedélyezni kell a SQL Server naplózását, hogy a kiszolgálón lévő összes adatbázis adatbázisában nyomon követjük az adatbázis-tevékenységeket, és egy auditnaplóba mentsük őket. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Microsoft Managed Control 1111 – Válasz a naplózási feldolgozási hibákra](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21de687c-f15e-4e51-bf8d-f35c8619965b) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1111.json) |
|[Microsoft Managed Control 1112 – Válasz a naplózási feldolgozási hibákra](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd530aad8-4ee2-45f4-b234-c061dae683c0) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1112.json) |

### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Válasz a naplózási feldolgozási hibákra | Tárkapacitás naplózása

**Azonosító:** NIST SP 800-53 R4 AU-5 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1113 – Válasz a naplózási feldolgozási hibákra \| – Tárkapacitás naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F562afd61-56be-4313-8fe4-b9564aa4ba7d) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1113.json) |

### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Válasz a naplózási feldolgozási hibákra | Real-Time riasztások

**Azonosító:** NIST SP 800-53 R4 AU-5 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1114 – Válasz a naplófeldolgozási hibákra és \| Real-Time riasztások esetén](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c090801-59bc-4454-bb33-e0455133486a) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1114.json) |

### <a name="audit-review-analysis-and-reporting"></a>Naplózási áttekintés, elemzés és jelentéskészítés

**Azonosító:** NIST SP 800-53 R4 AU-6 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1115 – Naplózási áttekintés, elemzés és jelentéskészítés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b653845-2ad9-4e09-a4f3-5a7c1d78353d) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1115.json) |
|[Microsoft Managed Control 1116 – Naplózási áttekintés, elemzés és jelentéskészítés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e47bc51-35d1-44b8-92af-e2f2d8b67635) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1116.json) |

### <a name="audit-review-analysis-and-reporting--process-integration"></a>Naplózási áttekintés, elemzés és jelentéskészítés | Folyamatintegráció

**Azonosító:** NIST SP 800-53 R4 AU-6 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1117 – Naplózási áttekintés, elemzés és jelentéskészítési \| folyamat integrációja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fbfe680-6dbb-4037-963c-a621c5635902) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1117.json) |

### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Naplózási áttekintés, elemzés és jelentéskészítés | Naplótárak korrelálása

**Azonosító:** NIST SP 800-53 R4 AU-6 (3) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1118 – Audit review, Analysis, and Reporting \| Correlate Audit Repositories](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96f743d-a195-420d-983a-08aa06bc441e) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1118.json) |

### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Naplózási áttekintés, elemzés és jelentéskészítés | Központi áttekintés és elemzés

**Azonosító:** NIST SP 800-53 R4 AU-6 (4) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics-munkaterület naplózása virtuális géphez – Jelentés eltérése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Nem megfelelőként jelenti a virtuális gépeket, ha nem jelentkeznek be a szabályzat-/kezdeményezés-hozzárendelésben megadott Log Analytics-munkaterületre. |Ellenőrzési |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[A Log Analytics-ügynököt engedélyezni kell a felsorolt virtuálisgép-rendszerképekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Nem megfelelőként jelenti a virtuális gépeket, ha a virtuálisgép-rendszerkép nem szerepel a meghatározott listán, és az ügynök nincs telepítve. |AuditIfNotExists, Disabled |[2.0.0-s előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[A Log Analytics-ügynököt engedélyezni kell a virtuálisgép-méretezési csoportban a felsorolt virtuálisgép-rendszerképekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Nem megfelelőként jelenti a virtuálisgép-méretezési készleteket, ha a virtuálisgép-rendszerkép nem szerepel a meghatározott listán, és az ügynök nincs telepítve. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft Managed Control 1119 – Audit review, Analysis, and Reporting \| Central Review and Analysis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F845f6359-b764-4b40-b579-657aefe23c44) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1119.json) |

### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Naplózási áttekintés, elemzés és jelentéskészítés | Integrációs/ellenőrzési és monitorozási képességek

**Azonosító:** NIST SP 800-53 R4 AU-6 (5) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1120 – Naplózási felülvizsgálati, elemzési és jelentéskészítési \| integrációs/ vizsgálati és figyelési képességek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc69b870e-857b-458b-af02-bb234f7a00d3) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1120.json) |

### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Naplózási áttekintés, elemzés és jelentéskészítés | Korreláció a fizikai monitorozással

**Azonosító:** NIST SP 800-53 R4 AU-6 (6) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1121 – Naplózási áttekintés, elemzés és jelentéskészítés korrelációja a \| fizikai monitorozással](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc72b0eb9-1fc2-44e5-a866-e7cb0532f7c1) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1121.json) |

### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Naplózási áttekintés, elemzés és jelentéskészítés | Engedélyezett műveletek

**Azonosító:** NIST SP 800-53 R4 AU-6 (7) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1122 – Engedélyezett műveletek naplózása, elemzése és \| jelentése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F243ec95e-800c-49d4-ba52-1fdd9f6b8b57) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1122.json) |

### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Naplózási áttekintés, elemzés és jelentéskészítés | Naplózási szint módosítása

**Azonosító:** NIST SP 800-53 R4 AU-6 (10) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1123 – Naplózási felülvizsgálat, elemzés és jelentéskészítés \| naplózási szintjének módosítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03996055-37a4-45a5-8b70-3f1caa45f87d) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1123.json) |

### <a name="audit-reduction-and-report-generation"></a>Naplózás csökkentése és jelentés létrehozása

**Azonosító:** NIST SP 800-53 R4 AU-7 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1124 – Naplózás csökkentése és jelentés létrehozása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10152dd-78f8-4335-ae2d-ad92cc028da4) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1124.json) |
|[Microsoft Managed Control 1125 – Naplózás csökkentése és jelentés létrehozása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6ce745a-670e-47d3-a6c4-3cfe5ef00c10) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1125.json) |

### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Naplózási csökkentési és jelentés-| Automatikus feldolgozás

**Azonosító:** NIST SP 800-53 R4 AU-7 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1126 – Naplózás csökkentése és automatikus feldolgozás jelentés \| létrehozása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f37f71b-420f-49bf-9477-9c0196974ecf) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1126.json) |

### <a name="time-stamps"></a>Időbélyegek

**Id**: NIST SP 800-53 R4 AU-8 **Ownership**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1127 – Időbélyegek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ce328db-aef3-48ed-9f81-2ab7cf839c66) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1127.json) |
|[Microsoft Managed Control 1128 – Időbélyegek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef212163-3bc4-4e86-bcf8-705127086393) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1128.json) |

### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Időbélyegek | Szinkronizálás mérvadó időforrással

**Azonosító:** NIST SP 800-53 R4 AU-8 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1129 – Időbélyegek \| szinkronizálása mérvadó időforrással](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71bb965d-4047-4623-afd4-b8189a58df5d) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1129.json) |
|[Microsoft Managed Control 1130 – Időbélyegek \| szinkronizálása mérvadó időforrással](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd7c4c1d-51ee-4349-9dab-89a7f8c8d102) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1130.json) |

### <a name="protection-of-audit-information"></a>A naplózási információk védelme

**Azonosító:** NIST SP 800-53 R4 AU-9 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1131 – A naplózási információk védelme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb472a17e-c2bc-493f-b50b-42d55a346962) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1131.json) |

### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>A naplózási információk védelme | Biztonsági mentés naplózása különálló fizikai rendszereken/összetevőken

**Azonosító:** NIST SP 800-53 R4 AU-9 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1132 – A naplózási információk védelme \| Naplózási biztonsági mentés külön fizikai rendszereken/összetevőkben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05938e10-cdbd-4a54-9b2b-1cbcfc141ad0) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1132.json) |

### <a name="protection-of-audit-information--cryptographic-protection"></a>A naplózási információk védelme | Kriptográfiai védelem

**Azonosító:** NIST SP 800-53 R4 AU-9 (3) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1133 – A naplózási információk titkosítási \| védelmének védelme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90b60a09-133d-45bc-86ef-b206a6134bbe) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1133.json) |

### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>A naplózási információk védelme | Hozzáférés a kiemelt jogosultságú felhasználók alcsoportja szerint

**Azonosító:** NIST SP 800-53 R4 AU-9 (4) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1134 – A naplózási információkhoz való hozzáférés védelme a kiemelt jogosultságú felhasználók \| egy része számára](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e95f70e-181c-4422-9da2-43079710c789) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1134.json) |

### <a name="non-repudiation"></a>Letagadhatatlanság

**Azonosító:** NIST SP 800-53 R4 AU-10 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1135 – Nem repudiation](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c308b6b-2429-4b97-86cf-081b8e737b04) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1135.json) |

### <a name="audit-record-retention"></a>Naplórekord megőrzése

**Azonosító:** NIST SP 800-53 R4 AU-11 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1136 – Naplórekordok megőrzése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97ed5bac-a92f-4f6d-a8ed-dc094723597c) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1136.json) |

### <a name="audit-generation"></a>Naplózási generáció

**Azonosító:** NIST SP 800-53 R4 AU-12 **ownership**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A speciális adatbiztonságot engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Napló minden SQL Managed Instance fejlett adatbiztonság nélkül. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Engedélyezni kell a speciális adatbiztonságot az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |SQL-kiszolgálók naplózása Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Diagnosztikai beállítás naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |A kiválasztott erőforrástípusok diagnosztikai beállításának naplózása |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Log Analytics-munkaterület naplózása virtuális géphez – Jelentés eltérése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Nem megfelelőként jelenti a virtuális gépeket, ha nem jelentkeznek be a szabályzat-/kezdeményezési hozzárendelésben megadott Log Analytics-munkaterületre. |Ellenőrzési |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Engedélyezni kell az SQL Server naplózását](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Engedélyezni kell a SQL Server naplózását, hogy a kiszolgálón lévő összes adatbázis adatbázisában nyomon követjük az adatbázis-tevékenységeket, és egy auditnaplóba mentsük őket. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[A Log Analytics-ügynököt engedélyezni kell a felsorolt virtuálisgép-rendszerképekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Nem megfelelőként jelenti a virtuális gépeket, ha a virtuálisgép-rendszerkép nem szerepel a meghatározott listában, és az ügynök nincs telepítve. |AuditIfNotExists, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[A Log Analytics-ügynököt engedélyezni kell a virtuálisgép-méretezési csoportban a felsorolt virtuálisgép-rendszerképekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Nem megfelelőként jelenti a virtuálisgép-méretezési készleteket, ha a virtuálisgép-rendszerkép nem szerepel a meghatározott listában, és az ügynök nincs telepítve. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft Managed Control 1137 – Naplózás létrehozása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4344df62-88ab-4637-b97b-bcaf2ec97e7c) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1137.json) |
|[Microsoft Managed Control 1138 – Naplózás létrehozása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c284fc0-268a-4f29-af44-3c126674edb4) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1138.json) |
|[Microsoft Managed Control 1139 – Naplózás létrehozása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ed62522-de00-4dda-9810-5205733d2f34) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1139.json) |

### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Naplózási generáció | System-Wide/Time-Correlated Audit Trail

**Azonosító:** NIST SP 800-53 R4 AU-12 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1140 – Naplózási generáció \| System-Wide/Time-Correlated auditálási napló](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90d8b8ad-8ee3-4db7-913f-2a53fcff5316) |A Microsoft ezt a naplózási és elszámoltathatósági ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1140.json) |

### <a name="audit-generation--changes-by-authorized-individuals"></a>Naplózási generáció | Hitelesített személyek által végrehajtott módosítások

**Azonosító:** NIST SP 800-53 R4 AU-12 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1141 – Hitelesített személyek által végzett \| generációváltozások naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fdefbf4-93e7-4513-bc95-c1858b7093e0) |A Microsoft megvalósítja ezt a naplózási és elszámoltathatósági ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1141.json) |

## <a name="security-assessment-and-authorization"></a>Biztonsági felmérés és engedélyezés

### <a name="security-assessment-and-authorization-policy-and-procedures"></a>Biztonsági felmérési és engedélyezési házirend és eljárások

**Azonosító:** NIST SP 800-53 R4 CA-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1142 – Biztonsági felmérési és engedélyezési szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01524fa8-4555-48ce-ba5f-c3b8dcef5147) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1142.json) |
|[Microsoft Managed Control 1143 – Biztonsági felmérési és engedélyezési szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c6de11b-5f51-4f7c-8d83-d2467c8a816e) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1143.json) |

### <a name="security-assessments"></a>Biztonsági felmérések

**Azonosító:** NIST SP 800-53 R4 CA-2 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1144 – Biztonsági értékelések](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fa15ff1-a693-4ee4-b094-324818dc9a51) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1144.json) |
|[Microsoft Managed Control 1145 – Biztonsági értékelések](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0724970-9c75-4a64-a225-a28002953f28) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1145.json) |
|[Microsoft Managed Control 1146 – Biztonsági értékelések](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd83410c-ecb6-4547-8f14-748c3cbdc7ac) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1146.json) |
|[Microsoft Managed Control 1147 – Biztonsági értékelések](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fef824a-29a8-4a4c-88fc-420a39c0d541) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1147.json) |

### <a name="security-assessments--independent-assessors"></a>Biztonsági felmérések | Független értékelők

**Azonosító:** NIST SP 800-53 R4 CA-2 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1148 – Security Assessments \| Independent Assessors](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e62650-c7c2-4786-bdfa-17edc1673902) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1148.json) |

### <a name="security-assessments--specialized-assessments"></a>Biztonsági felmérések | Specializált értékelések

**Azonosító:** NIST SP 800-53 R4 CA-2 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1149 – Biztonsági értékelések \| specializált értékelések](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e1b855b-a013-481a-aeeb-2bcb129fd35d) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1149.json) |

### <a name="security-assessments--external-organizations"></a>Biztonsági felmérések | Külső szervezetek

**Azonosító:** NIST SP 800-53 R4 CA-2 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1150 – Biztonsági felmérések \| külső szervezetek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd630429d-e763-40b1-8fba-d20ba7314afb) |A Microsoft megvalósítja ezt a biztonsági felmérési és engedélyezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1150.json) |

### <a name="system-interconnections"></a>Rendszerösszekötők

**Azonosító:** NIST SP 800-53 R4 CA-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1151 – Rendszer-kapcsolatok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F347e3b69-7fb7-47df-a8ef-71a1a7b44bca) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1151.json) |
|[Microsoft Managed Control 1152 – Rendszer-kapcsolatok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbeff0acf-7e67-40b2-b1ca-1a0e8205cf1b) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1152.json) |
|[Microsoft Managed Control 1153 – Rendszer-kapcsolatok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61cf3125-142c-4754-8a16-41ab4d529635) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1153.json) |

### <a name="system-interconnections--unclassified-non-national-security-system-connections"></a>Rendszer-összekapcsolások | Nem besorolt nem országos biztonsági rendszerkapcsolatok

**Azonosító:** NIST SP 800-53 R4 CA-3 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1154 – Rendszerkapcsolatok nem besorolt nem országos \| biztonsági rendszerkapcsolatok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe757ceb9-93b3-45fe-a4f4-f43f64f1ac5a) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1154.json) |

### <a name="system-interconnections--restrictions-on-external-system-connections"></a>Rendszer-összekapcsolások | A külső rendszerkapcsolatok korlátozásai

**Azonosító:** NIST SP 800-53 R4 CA-3 (5) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1155 – A rendszer összekapcsolási korlátozásai \| a külső rendszerkapcsolatok esetén](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d33f9f1-12d0-46ad-9fbd-8f8046694977) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1155.json) |

### <a name="plan-of-action-and-milestones"></a>Terv és mérföldkövek

**Azonosító:** NIST SP 800-53 R4 CA-5 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1156 – terv és mérföldkövek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d52e864-9a3b-41ee-8f03-520815fe5378) |A Microsoft megvalósítja ezt a biztonsági felmérési és engedélyezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1156.json) |
|[Microsoft Managed Control 1157 – terv és mérföldkövek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F15495367-cf68-464c-bbc3-f53ca5227b7a) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1157.json) |

### <a name="security-authorization"></a>Biztonsági engedélyezés

**Azonosító:** NIST SP 800-53 R4 CA-6 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1158 – Biztonsági engedélyezés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffff50cf2-28eb-45b4-b378-c99412688907) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1158.json) |
|[Microsoft Managed Control 1159 – Biztonsági engedélyezés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0925f098-7877-450b-8ba4-d1e55f2d8795) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1159.json) |
|[Microsoft Managed Control 1160 – Biztonsági engedélyezés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e797ca6-2aa8-4333-b335-7036f1110c05) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1160.json) |

### <a name="continuous-monitoring"></a>Folyamatos monitorozás

**Azonosító:** NIST SP 800-53 R4 CA-7 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1161 – Folyamatos monitorozás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2f8f6c6-dde4-436b-a79d-bc50e129eb3a) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1161.json) |
|[Microsoft Managed Control 1162 – Folyamatos figyelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5770f3d6-8c2b-4f6f-bf0e-c8c8fc36d592) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1162.json) |
|[Microsoft Managed Control 1163 – Folyamatos monitorozás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F961663a1-8a91-4e59-b6f5-1eee57c0f49c) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1163.json) |
|[Microsoft Managed Control 1164 – Folyamatos monitorozás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fb8d3ce-9e96-481c-9c68-88d4e3019310) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1164.json) |
|[Microsoft Managed Control 1165 – Folyamatos monitorozás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47e10916-6c9e-446b-b0bd-ff5fd439d79d) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1165.json) |
|[Microsoft Managed Control 1166 – Folyamatos monitorozás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb02733d-3cc5-4bb0-a6cd-695ba2c2272e) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1166.json) |
|[Microsoft Managed Control 1167 – Folyamatos monitorozás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcbb2be76-4891-430b-95a7-ca0b0a3d1300) |A Microsoft megvalósítja ezt a biztonsági felmérési és engedélyezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1167.json) |

### <a name="continuous-monitoring--independent-assessment"></a>Folyamatos monitorozási | Független értékelés

**Azonosító:** NIST SP 800-53 R4 CA-7 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1168 - Continuous Monitoring \| Independent Assessment](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82409f9e-1f32-4775-bf07-b99d53a91b06) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1168.json) |

### <a name="continuous-monitoring--trend-analyses"></a>Folyamatos monitorozási | Trendelemzések

**Azonosító:** NIST SP 800-53 R4 CA-7 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1169 – Folyamatos monitorozási \| trendelemzések](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7ba2cb3-5675-4468-8b50-8486bdd998a5) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1169.json) |

### <a name="penetration-testing"></a>Behatolástesztelés

**Azonosító:** NIST SP 800-53 R4 CA-8 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1170 – Behatolástesztelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b78b9b3-ee3c-48e0-a243-ed6dba5b7a12) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1170.json) |

### <a name="penetration-testing--independent-penetration-agent-or-team"></a>Behatolástesztelési | Független behatolási ügynök vagy csapat

**Azonosító:** NIST SP 800-53 R4 CA-8 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1171 – \| Behatolástesztelési független behatolási ügynök vagy csapat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d4820bc-8b61-4982-9501-2123cb776c00) |A Microsoft ezt a biztonsági felmérési és engedélyezési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1171.json) |

### <a name="internal-system-connections"></a>Belső rendszerkapcsolatok

**Azonosító:** NIST SP 800-53 R4 CA-9 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1172 – Belső rendszerkapcsolatok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb43e946e-a4c8-4b92-8201-4a39331db43c) |A Microsoft megvalósítja ezt a biztonsági felmérési és engedélyezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1172.json) |
|[Microsoft Managed Control 1173 – Belső rendszerkapcsolatok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4aff9e7-2e60-46fa-86be-506b79033fc5) |A Microsoft megvalósítja ezt a biztonsági felmérési és engedélyezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1173.json) |

## <a name="configuration-management"></a>Konfigurációkezelés

### <a name="configuration-management-policy-and-procedures"></a>Konfigurációkezelési házirend és eljárások

**Azonosító:** NIST SP 800-53 R4 CM-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1174 – Konfigurációkezelési házirend és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42a9a714-8fbb-43ac-b115-ea12d2bd652f) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1174.json) |
|[Microsoft Managed Control 1175 – Konfigurációkezelési házirend és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6dab4254-c30d-4bb7-ae99-1d21586c063c) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1175.json) |

### <a name="baseline-configuration"></a>Alapkonfiguráció

**Azonosító:** NIST SP 800-53 R4 CM-2 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1176 – Alapkonfiguráció](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc30690a5-7bf3-467f-b0cd-ef5c7c7449cd) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1176.json) |

### <a name="baseline-configuration--reviews-and-updates"></a>Alapkonfiguráció | Felülvizsgálatok és frissítések

**Azonosító:** NIST SP 800-53 R4 CM-2 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1177 – Alapkonfigurációk \| áttekintése és frissítései](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63dbc7a8-e20b-4d38-b857-a7f6c0cd94bc) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1177.json) |
|[Microsoft Managed Control 1178 – Alapkonfigurációk \| áttekintése és frissítései](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7818b8f4-47c6-441a-90ae-12ce04e99893) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1178.json) |
|[Microsoft Managed Control 1179 – Alapkonfigurációk \| áttekintése és frissítései](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f9ce557-c8ab-4e6c-bb2c-9b8ed002c46c) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1179.json) |

### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Alapkonfiguráció | Automation-támogatás a pontossághoz/pénznemhez

**Azonosító:** NIST SP 800-53 R4 CM-2 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1180 – Az alapkonfiguráció \| automatizálásának támogatása pontossághoz/pénznemhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F874e7880-a067-42a7-bcbe-1a340f54c8cc) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1180.json) |

### <a name="baseline-configuration--retention-of-previous-configurations"></a>Alapkonfiguráció | Korábbi konfigurációk megőrzése

**Azonosító:** NIST SP 800-53 R4 CM-2 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1181 – A korábbi konfigurációk \| alapkonfiguráció-megőrzése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21839937-d241-4fa5-95c6-b669253d9ab9) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1181.json) |

### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Alapkonfiguráció | Rendszerek, összetevők vagy eszközök konfigurálása High-Risk területekhez

**Azonosító:** NIST SP 800-53 R4 CM-2 (7) **Tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1182 – Alapkonfiguráció rendszerek, összetevők vagy eszközök konfigurálása \| a High-Risk számára](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f34f554-da4b-4786-8d66-7915c90893da) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1182.json) |
|[Microsoft Managed Control 1183 - Baseline Configuration \| Configure Systems, Components, Or Devices For High-Risk Areas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5352e3e0-e63a-452e-9e5f-9c1d181cff9c) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1183.json) |

### <a name="configuration-change-control"></a>Konfigurációváltozás-vezérlés

**Azonosító:** NIST SP 800-53 R4 CM-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1184 – Konfigurációváltozás-vezérlés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13579d0e-0ab0-4b26-b0fb-d586f6d7ed20) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1184.json) |
|[Microsoft Managed Control 1185 – Konfigurációváltozás-vezérlés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6420cd73-b939-43b7-9d99-e8688fea053c) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1185.json) |
|[Microsoft Managed Control 1186 – Konfigurációváltozás-vezérlés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb95ba3bd-4ded-49ea-9d10-c6f4b680813d) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1186.json) |
|[Microsoft Managed Control 1187 – Konfigurációváltozás-vezérlés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9f2b2f9e-4ba6-46c3-907f-66db138b6f85) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1187.json) |
|[Microsoft Managed Control 1188 – Konfigurációváltozás-vezérlés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb20548a-c926-4e4d-855c-bcddc6faf95e) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1188.json) |
|[Microsoft Managed Control 1189 – Konfigurációváltozás-vezérlés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee45e02a-4140-416c-82c4-fecfea660b9d) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1189.json) |
|[Microsoft Managed Control 1190 – Konfigurációváltozás-vezérlés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc66a3d1e-465b-4f28-9da5-aef701b59892) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1190.json) |

### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfigurációváltozás-vezérlési | Automatizált dokumentum/értesítés / Változások bejelentése

**Azonosító:** NIST SP 800-53 R4 CM-3 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1191 – Konfigurációváltozás-vezérlés \| automatizált dokumentum/értesítés/ változások bejelentése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f26a61b-a74d-467c-99cf-63644db144f7) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1191.json) |
|[Microsoft Managed Control 1192 - Configuration Change Control \| Automated Document / Notification / Az eszköz változásai](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ebd97f7-b105-4f50-8daf-c51465991240) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1192.json) |
|[Microsoft Managed Control 1193 - Configuration Change Control \| Automated Document / Notification / Az eszköz változásai](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5fd629f-3075-4cae-ab53-bad65495a4ac) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1193.json) |
|[Microsoft Managed Control 1194 – Konfigurációváltozás-vezérlés \| – Automatizált dokumentum/értesítés / Változások bejelentése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc34667f-397e-4a65-9b72-d0358f0b6b09) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1194.json) |
|[Microsoft Managed Control 1195 – Konfigurációváltozás-vezérlés \| – Automatizált dokumentum/értesítés / Változások bejelentése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1e1d65c-1013-4484-bd54-991332e6a0d2) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1195.json) |
|[Microsoft Managed Control 1196 – Konfigurációváltozás-vezérlés \| – Automatizált dokumentum/értesítés / Változások bejelentése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e7f4ea4-dd62-44f6-8886-ac6137cf52b0) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1196.json) |

### <a name="configuration-change-control--test--validate--document-changes"></a>Konfigurációváltozás-vezérlési | Teszt/ Ellenőrzés / Módosítások dokumentálása

**Azonosító:** NIST SP 800-53 R4 CM-3 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1197 - Configuration Change Control \| Test / Validate / Document Changes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa20d2eaa-88e2-4907-96a2-8f3a05797e5c) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1197.json) |

### <a name="configuration-change-control--security-representative"></a>Konfigurációváltozás-vezérlési | Biztonsági képviselő

**Azonosító:** NIST SP 800-53 R4 CM-3 (4) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1198 - Configuration Change Control \| Security Representative](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff56be5c3-660b-4c61-9078-f67cf072c356) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1198.json) |

### <a name="configuration-change-control--cryptography-management"></a>Konfigurációváltozás-vezérlési | Titkosításkezelés

**Azonosító:** NIST SP 800-53 R4 CM-3 (6) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1199 – Configuration Change Control \| Cryptography Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9a08d1c-09b1-48f1-90ea-029bbdf7111e) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1199.json) |

### <a name="security-impact-analysis"></a>Biztonsági hatás elemzése

**Azonosító:** NIST SP 800-53 R4 CM-4 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1200 – Biztonsági hatáselemzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe98fe9d7-2ed3-44f8-93b7-24dca69783ff) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1200.json) |

### <a name="security-impact-analysis--separate-test-environments"></a>Biztonsági hatáselemzési | Különálló tesztkörnyezetek

**Azonosító:** NIST SP 800-53 R4 CM-4 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1201 - Security Impact Analysis \| Separate Test Environments](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7daef997-fdd3-461b-8807-a608a6dd70f1) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1201.json) |

### <a name="access-restrictions-for-change"></a>Változásra vonatkozó hozzáférési korlátozások

**Azonosító:** NIST SP 800-53 R4 CM-5 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1202 – Változásra vonatkozó hozzáférési korlátozások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40a2a83b-74f2-4c02-ae65-f460a5d2792a) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1202.json) |

### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Hozzáférési korlátozások a változási | Automatizált hozzáférés-kényszerítés /naplózás

**Azonosító:** NIST SP 800-53 R4 CM-5 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1203 - Access Restrictions For Change \| Automated Access Enforcement / Auditing](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9012d14-e3e6-4d7b-b926-9f37b5537066) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1203.json) |

### <a name="access-restrictions-for-change--review-system-changes"></a>Hozzáférési korlátozások a változási | Rendszerváltozások áttekintése

**Azonosító:** NIST SP 800-53 R4 CM-5 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1204 - Access Restrictions For Change \| Review System Changes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f4f6750-d1ab-4a4c-8dfd-af3237682665) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1204.json) |

### <a name="access-restrictions-for-change--signed-components"></a>Hozzáférési korlátozások a változási | Aláírt összetevők

**Azonosító:** NIST SP 800-53 R4 CM-5 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1205 – Az aláírt összetevők változására \| vonatkozó hozzáférési korlátozások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b070cab-0fb8-4e48-ad29-fc90b4c2797c) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1205.json) |

### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Hozzáférési korlátozások a változási | Az éles/műveleti jogosultságok korlátozása

**Azonosító:** NIST SP 800-53 R4 CM-5 (5) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1206 – Hozzáférési korlátozások a változáskorlát \| éles/műveleti jogosultságai számára](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0de232d-02a0-4652-872d-88afb4ae5e91) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1206.json) |
|[Microsoft Managed Control 1207 – Hozzáférési korlátozások a változáskorlát \| éles/műveleti jogosultságai számára](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8713a0ed-0d1e-4d10-be82-83dffb39830e) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1207.json) |

### <a name="configuration-settings"></a>Konfigurációs beállítások

**Azonosító:** NIST SP 800-53 R4 CM-6 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1208 – Konfigurációs beállítások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5ea87673-d06b-456f-a324-8abcee5c159f) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1208.json) |
|[Microsoft Managed Control 1209 – Konfigurációs beállítások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fce669c31-9103-4552-ae9c-cdef4e03580d) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1209.json) |
|[Microsoft Managed Control 1210 – Konfigurációs beállítások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3502c968-c490-4570-8167-1476f955e9b8) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1210.json) |
|[Microsoft Managed Control 1211 – Konfigurációs beállítások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a8b9dc8-6b00-4701-aa96-bba3277ebf50) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1211.json) |

### <a name="configuration-settings--automated-central-management--application--verification"></a>Konfigurációs beállítások | Automatizált központi felügyelet / Alkalmazás /Ellenőrzés

**Azonosító:** NIST SP 800-53 R4 CM-6 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1212 - Configuration Settings \| Automated Central Management / Application / Verification](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56d970ee-4efc-49c8-8a4e-5916940d784c) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1212.json) |

### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Konfigurációs beállítások | Válasz jogosulatlan módosításokra

**Azonosító:** NIST SP 800-53 R4 CM-6 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1213 – A konfigurációs beállítások \| a jogosulatlan módosításokra reagálnak](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81f11e32-a293-4a58-82cd-134af52e2318) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1213.json) |

### <a name="least-functionality"></a>A legkisebb funkcionalitás

**Azonosító:** NIST SP 800-53 R4 CM-7 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1214 - Least Functionality](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff714a4e2-b580-47b6-ae8c-f2812d3750f3) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1214.json) |
|[Microsoft Managed Control 1215 – A legkisebb funkcionalitás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88fc93e8-4745-4785-b5a5-b44bb92c44ff) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1215.json) |

### <a name="least-functionality--periodic-review"></a>A legkisebb | Rendszeres felülvizsgálat

**Azonosító:** NIST SP 800-53 R4 CM-7 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1216 – A legkevésbé működőképes funkciók rendszeres \| áttekintése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7894fe6a-f5cb-44c8-ba90-c3f254ff9484) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1216.json) |
|[Microsoft Managed Control 1217 – A legkevésbé működőképes funkciók rendszeres \| áttekintése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedea4f20-b02c-4115-be75-86c080e5c0ed) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1217.json) |

### <a name="least-functionality--prevent-program-execution"></a>A legkisebb | Program végrehajtásának megakadályozása

**Azonosító:** NIST SP 800-53 R4 CM-7 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A biztonságos alkalmazások meghatározásához szükséges adaptív alkalmazásvezérlőket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazásvezérlők engedélyezésével meghatározhatja a gépeken futó, ismerten biztonságos alkalmazások listáját, és riasztást küld, ha más alkalmazások is futnak. Ez segít a gépek kártevők elleni védelemében. A szabályok konfigurálási és karbantartásának egyszerűsítése érdekében a Security Center gépi tanulással elemzi az egyes gépeken futó alkalmazásokat, és javaslatot ad az ismert biztonságos alkalmazások listájára. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft Managed Control 1218 – A program végrehajtásának legkevesebb \| funkciója](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a1d0394-b9f5-493e-9e83-563fd0ac4df8) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1218.json) |

### <a name="least-functionality--authorized-software--whitelisting"></a>A legkevésbé | Engedélyezett szoftverek / engedélyezési lista

**Azonosító:** NIST SP 800-53 R4 CM-7 (5) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A biztonságos alkalmazások meghatározásához szükséges adaptív alkalmazásvezérlőket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazásvezérlők engedélyezésével meghatározhatja a gépeken futó, ismerten biztonságos alkalmazások listáját, és riasztást küld, ha más alkalmazások is futnak. Ez segít a gépek kártevők elleni megsokosodásában. A szabályok konfigurálási és karbantartásának egyszerűsítése érdekében a Security Center gépi tanulással elemzi az egyes gépeken futó alkalmazásokat, és javaslatot ad az ismerten biztonságos alkalmazások listájára. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft Managed Control 1219 - Least Functionality \| Authorized Software /Whitelisting](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a39ac75-622b-4c88-9a3f-45b7373f7ef7) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1219.json) |
|[Microsoft Managed Control 1220 - Least Functionality \| Authorized Software /Whitelisting](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc40f31a7-81e1-4130-99e5-a02ceea2a1d6) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1220.json) |
|[Microsoft Managed Control 1221 - Least Functionality \| Authorized Software /Whitelisting](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22589a07-0007-486a-86ca-95355081ae2a) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1221.json) |

### <a name="information-system-component-inventory"></a>Információsrendszer-összetevő leltára

**Azonosító:** NIST SP 800-53 R4 CM-8 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1222 – Information System Component Inventory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb39e62f-6bda-4558-8088-ec03d5670914) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1222.json) |
|[Microsoft Managed Control 1223 – Information System Component Inventory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a1bb01-ad5a-49c1-aad3-b0c893b2ec3a) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1223.json) |

### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Information System Component Inventory | Frissítések telepítések/eltávolításak során

**Azonosító:** NIST SP 800-53 R4 CM-8 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1224 – Information System Component Inventory \| Updates During Installations /Removals](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28cfa30b-7f72-47ce-ba3b-eed26c8d2c82) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1224.json) |

### <a name="information-system-component-inventory--automated-maintenance"></a>Information System Component Inventory | Automatikus karbantartás

**Azonosító:** NIST SP 800-53 R4 CM-8 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1225 – Information System Component Inventory Automated \| Maintenance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d096fe0-f510-4486-8b4d-d17dc230980b) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1225.json) |

### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Information System Component Inventory | Automatizált jogosulatlan összetevő-észlelés

**Azonosító:** NIST SP 800-53 R4 CM-8 (3) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1226 – Information System Component Inventory \| Automated Unauthorized Component Detection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc158eb1c-ae7e-4081-8057-d527140c4e0c) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1226.json) |
|[Microsoft Managed Control 1227 – Information System Component Inventory \| Automated Unauthorized Component Detection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03b78f5e-4877-4303-b0f4-eb6583f25768) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1227.json) |

### <a name="information-system-component-inventory--accountability-information"></a>Information System Component Inventory | Elszámoltathatósági információk

**Azonosító:** NIST SP 800-53 R4 CM-8 (4) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1228 - Information System Component Inventory \| Accountability Information](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F39c54140-5902-4079-8bb5-ad31936fe764) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1228.json) |

### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Information System Component Inventory | Nincs ismétlődő nyilvántartás az összetevőkről

**Azonosító:** NIST SP 800-53 R4 CM-8 (5) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1229 – Information System Component Inventory \| No Duplicate Accounting of Components](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03752212-103c-4ab8-a306-7e813022ca9d) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1229.json) |

### <a name="configuration-management-plan"></a>Konfigurációkezelési terv

**Azonosító:** NIST SP 800-53 R4 CM-9 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1230 – Konfigurációkezelési csomag](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11158848-f679-4e9b-aa7b-9fb07d945071) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1230.json) |
|[Microsoft Managed Control 1231 – Konfigurációkezelési csomag](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F244e0c05-cc45-4fe7-bf36-42dcf01f457d) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1231.json) |
|[Microsoft Managed Control 1232 – Konfigurációkezelési csomag](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F396ba986-eac1-4d6d-85c4-d3fda6b78272) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1232.json) |
|[Microsoft Managed Control 1233 – Konfigurációkezelési csomag](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d79001f-95fe-45d0-8736-f217e78c1f57) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1233.json) |

### <a name="software-usage-restrictions"></a>Szoftverhasználati korlátozások

**Azonosító:** NIST SP 800-53 R4 CM-10 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1234 – Szoftverhasználati korlátozások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb293f881-361c-47ed-b997-bc4e2296bc0b) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1234.json) |
|[Microsoft Managed Control 1235 – Szoftverhasználati korlátozások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc49c610b-ece4-44b3-988c-2172b70d6e46) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1235.json) |
|[Microsoft Managed Control 1236 – Szoftverhasználati korlátozások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ba3ed84-c768-4e18-b87c-34ef1aff1b57) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1236.json) |

### <a name="software-usage-restrictions--open-source-software"></a>Szoftverhasználati korlátozások | Nyílt forráskódú szoftver

**Azonosító:** NIST SP 800-53 R4 CM-10 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1237 – Szoftverhasználati korlátozások \| – Nyílt forráskódú szoftver](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe80b6812-0bfa-4383-8223-cdd86a46a890) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1237.json) |

### <a name="user-installed-software"></a>User-Installed Szoftver

**Azonosító:** NIST SP 800-53 R4 CM-11 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A biztonságos alkalmazások meghatározásához szükséges adaptív alkalmazásvezérlőket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazásvezérlők engedélyezésével meghatározhatja a gépeken futó, ismerten biztonságos alkalmazások listáját, és riasztást küld, ha más alkalmazások is futnak. Ez segít a gépek kártevők elleni megsokosodásában. A szabályok konfigurálási és karbantartásának egyszerűsítése érdekében a Security Center gépi tanulással elemzi az egyes gépeken futó alkalmazásokat, és javaslatot ad az ismerten biztonságos alkalmazások listájára. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft Managed Control 1238 – User-Installed Software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa36cedd4-3ffd-4b1f-8b18-aa71d8d87ce1) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1238.json) |
|[Microsoft Managed Control 1239 – User-Installed Software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0be51298-f643-4556-88af-d7db90794879) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1239.json) |
|[Microsoft Managed Control 1240 – User-Installed Software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F129eb39f-d79a-4503-84cd-92f036b5e429) |A Microsoft ezt a Configuration Management-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1240.json) |

### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>User-Installed Szoftveres | Jogosulatlan telepítésekkel kapcsolatos riasztások

**Azonosító:** NIST SP 800-53 R4 CM-11 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1241 – User-Installed \| szoftverriasztás jogosulatlan telepítések esetén](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feca4d7b2-65e2-4e04-95d4-c68606b063c3) |A Microsoft ezt a konfigurációkezelési vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1241.json) |

## <a name="contingency-planning"></a>Válságtervek

### <a name="contingency-planning-policy-and-procedures"></a>Válságterv-tervezési házirend és eljárások

**Azonosító:** NIST SP 800-53 R4 CP-1 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1242 – Válságterv-tervezési szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3b3293-667a-445e-a722-fa0b0afc0958) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1242.json) |
|[Microsoft Managed Control 1243 – Válságterv-tervezési szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca9a4469-d6df-4ab2-a42f-1213c396f0ec) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1243.json) |

### <a name="contingency-plan"></a>Válságterv

**Azonosító:** NIST SP 800-53 R4 CP-2 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1244 – Válságterv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a13a8f8-c163-4b1b-8554-d63569dab937) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1244.json) |
|[Microsoft Managed Control 1245 – Válságterv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0e45314-57b8-4623-80cd-bbb561f59516) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1245.json) |
|[Microsoft Managed Control 1246 – Válságterv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F398eb61e-8111-40d5-a0c9-003df28f1753) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1246.json) |
|[Microsoft Managed Control 1247 – Válságterv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e666db5-b2ef-4b06-aac6-09bfce49151b) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1247.json) |
|[Microsoft Managed Control 1248 – Válságterv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50fc602d-d8e0-444b-a039-ad138ee5deb0) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1248.json) |
|[Microsoft Managed Control 1249 – Válságterv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3bf4251-0818-42db-950b-afd5b25a51c2) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1249.json) |
|[Microsoft Managed Control 1250 – Válságterv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8de614d8-a8b7-4f70-a62a-6d37089a002c) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1250.json) |

### <a name="contingency-plan--coordinate-with-related-plans"></a>Válságterv | Koordinálás a kapcsolódó tervekkel

**Azonosító:** NIST SP 800-53 R4 CP-2 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Microsoft Managed Control 1251 – Válságterv \| koordinálása a kapcsolódó tervekkel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e2b3730-8c14-4081-8893-19dbb5de7348) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1251.json) |

### <a name="contingency-plan--capacity-planning"></a>Válságterv | Kapacitástervezés

**Azonosító:** NIST SP 800-53 R4 CP-2 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1252 – Válságterv \| kapacitástervezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa328fd72-8ff5-4f96-8c9c-b30ed95db4ab) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1252.json) |

### <a name="contingency-plan--resume-essential-missions--business-functions"></a>Válságterv | Alapvető küldetések/üzleti funkciók folytatása

**Azonosító:** NIST SP 800-53 R4 CP-2 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1253 – Válságterv \| – Alapvető küldetések és üzleti funkciók folytatása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0afce0b3-dd9f-42bb-af28-1e4284ba8311) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1253.json) |

### <a name="contingency-plan--resume-all-missions--business-functions"></a>Válságterv | Az összes küldetés és üzleti funkció folytatása

**Azonosító:** NIST SP 800-53 R4 CP-2 (4) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1254 – Válságterv \| az összes küldetés és üzleti funkció folytatásához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F704e136a-4fe0-427c-b829-cd69957f5d2b) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1254.json) |

### <a name="contingency-plan--continue--essential-missions--business-functions"></a>Válságterv | Alapvető küldetések és üzleti funkciók folytatása

**Azonosító:** NIST SP 800-53 R4 CP-2 (5) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1255 – Válságterv – Alapvető \| küldetések és üzleti funkciók folytatása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff3793f5e-937f-44f7-bfba-40647ef3efa0) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1255.json) |

### <a name="contingency-plan--identify-critical-assets"></a>Válságterv | Kritikus fontosságú eszközök azonosítása

**Azonosító:** NIST SP 800-53 R4 CP-2 (8) **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1256 – Válságterv – \| Kritikus eszközök azonosítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F232ab24b-810b-4640-9019-74a7d0d6a980) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1256.json) |

### <a name="contingency-training"></a>Válságképzés

**Azonosító:** NIST SP 800-53 R4 CP-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1257 – Válságképzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb958b241-4245-4bd6-bd2d-b8f0779fb543) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1257.json) |
|[Microsoft Managed Control 1258 – Válságképzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7814506c-382c-4d33-a142-249dd4a0dbff) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1258.json) |
|[Microsoft Managed Control 1259 – Válságképzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9e18f7-bad9-4d30-8806-a0c9d5e26208) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1259.json) |

### <a name="contingency-training--simulated-events"></a>Válságképzési | Szimulált események

**Azonosító:** NIST SP 800-53 R4 CP-3 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1260 – Válságképzés \| szimulált eseményei](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42254fc4-2738-4128-9613-72aaa4f0d9c3) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1260.json) |

### <a name="contingency-plan-testing"></a>Válságterv tesztelése

**Azonosító:** NIST SP 800-53 R4 CP-4 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1261 – Válságterv tesztelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65aeceb5-a59c-4cb1-8d82-9c474be5d431) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1261.json) |
|[Microsoft Managed Control 1262 – Válságterv tesztelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F831e510e-db41-4c72-888e-a0621ab62265) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1262.json) |
|[Microsoft Managed Control 1263 – Válságterv tesztelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41472613-3b05-49f6-8fe8-525af113ce17) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1263.json) |

### <a name="contingency-plan-testing--coordinate-with-related-plans"></a>Válságterv tesztelési | Koordinálás a kapcsolódó tervekkel

**Azonosító:** NIST SP 800-53 R4 CP-4 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Microsoft Managed Control 1264 – Válságterv tesztelése kapcsolódó \| tervekkel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd280d4b-50a1-42fb-a479-ece5878acf19) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1264.json) |

### <a name="contingency-plan-testing--alternate-processing-site"></a>Válságterv tesztelési | Alternatív feldolgozási hely

**Azonosító:** NIST SP 800-53 R4 CP-4 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1265 – Válságterv tesztelése alternatív \| feldolgozási webhely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa18adb5b-1db6-4a5b-901a-7d3797d12972) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1265.json) |
|[Microsoft Managed Control 1266 – Válságterv tesztelése alternatív \| feldolgozási webhely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b4a3eb2-c25d-40bf-ad41-5094b6f59cee) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1266.json) |

### <a name="alternate-storage-site"></a>Alternatív tárolási hely

**Azonosító:** NIST SP 800-53 R4 CP-6 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1267 – Alternatív tárolóhely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e97ba1d-be5d-4953-8da4-0cccf28f4805) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1267.json) |
|[Microsoft Managed Control 1268 – Alternatív tárolóhely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F23f6e984-3053-4dfc-ab48-543b764781f5) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1268.json) |

### <a name="alternate-storage-site--separation-from-primary-site"></a>Alternatív tárolási hely | Elkülönítés az elsődleges helytől

**Azonosító:** NIST SP 800-53 R4 CP-6 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1269 – A tárolóhely elsődleges helytől való \| elkülönítése alternatív verziójával](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19b9439d-865d-4474-b17d-97d2702fdb66) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1269.json) |

### <a name="alternate-storage-site--recovery-time--point-objectives"></a>Alternatív tárolási hely | Helyreállítási idő/pont célkitűzései

**Azonosító:** NIST SP 800-53 R4 CP-6 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1270 - Alternate Storage Site \| Recovery Time / Point Objectives](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53c76a39-2097-408a-b237-b279f7b4614d) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1270.json) |

### <a name="alternate-storage-site--accessibility"></a>Alternatív tárolási hely | Hozzáférhetőség

**Azonosító:** NIST SP 800-53 R4 CP-6 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1271 – Alternatív tárolóhely \| akadálymentessége](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3bfb53-9c46-4010-b3db-a7ba1296dada) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1271.json) |

### <a name="alternate-processing-site"></a>Alternatív feldolgozási hely

**Azonosító:** NIST SP 800-53 R4 CP-7 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Vészhelyreállítás nélküli virtuális gépek naplózása konfigurálva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Naplóba kell állítania a vészhelyreállítás konfigurálásával nem konfigurált virtuális gépeket. A vészhelyreállításról további információt itt [https://aka.ms/asr-doc](https://aka.ms/asr-doc) olvashat: . |auditIfNotExists (auditIfNotExists) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Microsoft Managed Control 1272 – Alternatív feldolgozási webhely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae46cf7a-e3fd-427b-9b91-44bc78e2d9d8) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1272.json) |
|[Microsoft Managed Control 1273 – Alternatív feldolgozási webhely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe77fcbf2-a1e8-44f1-860e-ed6583761e65) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1273.json) |
|[Microsoft Managed Control 1274 – Alternatív feldolgozási webhely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2aee175f-cd16-4825-939a-a85349d96210) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1274.json) |

### <a name="alternate-processing-site--separation-from-primary-site"></a>Alternatív feldolgozási hely | Elkülönítés az elsődleges helytől

**Azonosító:** NIST SP 800-53 R4 CP-7 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1275 – Másodlagos feldolgozási helyek \| elkülönítése az elsődleges helytől](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa23d9d53-ad2e-45ef-afd5-e6d10900a737) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1275.json) |

### <a name="alternate-processing-site--accessibility"></a>Alternatív feldolgozási hely | Hozzáférhetőség

**Azonosító:** NIST SP 800-53 R4 CP-7 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1276 – Alternatív feldolgozási webhely \| akadálymentessége](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe214e563-1206-4a43-a56b-ac5880c9c571) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1276.json) |

### <a name="alternate-processing-site--priority-of-service"></a>Alternatív feldolgozási hely | Szolgáltatás prioritása

**Azonosító:** NIST SP 800-53 R4 CP-7 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1277 - Alternate Processing Site \| Priority Of Service](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc43e829-3d50-4a0a-aa0f-428d551862aa) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1277.json) |

### <a name="alternate-processing-site--preparation-for-use"></a>Alternatív feldolgozási hely | Felkészülés a használatra

**Azonosító:** NIST SP 800-53 R4 CP-7 (4) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1278 – Alternatív feldolgozási hely \| előkészítése használatra](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e5ef485-9e16-4c53-a475-fbb8107eac59) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1278.json) |

### <a name="telecommunications-services"></a>Távközlési szolgáltatások

**Azonosító:** NIST SP 800-53 R4 CP-8 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1279 – Távközlési szolgáltatások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d00bcd6-963d-4c02-ad8e-b45fa50bf3b0) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1279.json) |

### <a name="telecommunications-services--priority-of-service-provisions"></a>Távközlési szolgáltatások | A szolgáltatásra vonatkozó kiszolgáltatás prioritása

**Azonosító:** NIST SP 800-53 R4 CP-8 (1) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1280 - Telecommunications Services \| Priority Of Service Provisions](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa108498-b3a8-4ffb-9e79-1107e76afad3) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1280.json) |
|[Microsoft Managed Control 1281 - Telecommunications Services \| Priority Of Service Provisions](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dc459b3-0e77-45af-8d71-cfd8c9654fe2) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1281.json) |

### <a name="telecommunications-services--single-points-of-failure"></a>Távközlési szolgáltatások | Meghibásodási pontok

**Azonosító:** NIST SP 800-53 R4 CP-8 (2) **Tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1282 - Telecommunications Services \| Single Points Of Failure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34042a97-ec6d-4263-93d2-8c1c46823b2a) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1282.json) |

### <a name="telecommunications-services--separation-of-primary--alternate-providers"></a>Távközlési szolgáltatások | Elsődleges/alternatív szolgáltatók elkülönítése

**Azonosító:** NIST SP 800-53 R4 CP-8 (3) **Tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1283 – Az elsődleges/alternatív szolgáltatók távközlési \| szolgáltatásainak elkülönítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9172e76-7f56-46e9-93bf-75d69bdb5491) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1283.json) |

### <a name="telecommunications-services--provider-contingency-plan"></a>Távközlési szolgáltatások | Szolgáltatói válságterv

**Azonosító:** NIST SP 800-53 R4 CP-8 (4) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1284 – Távközlési szolgáltatók \| válságterve](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942b3e97-6ae3-410e-a794-c9c999b97c0b) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1284.json) |
|[Microsoft Managed Control 1285 – Távközlési szolgáltatók \| válságterve](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01f7726b-db54-45c2-bcb5-9bd7a43796ee) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1285.json) |
|[Microsoft Managed Control 1286 – Távközlési szolgáltatók \| válságterve](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4f9b47a-2116-4e6f-88db-4edbf22753f1) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1286.json) |

### <a name="information-system-backup"></a>Információs rendszer biztonsági mentése

**Azonosító:** NIST SP 800-53 R4 CP-9 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1287 – Információs rendszer biztonsági mentése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F819dc6da-289d-476e-8500-7e341ef8677d) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1287.json) |
|[Microsoft Managed Control 1288 – Információs rendszer biztonsági mentése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d854c3b-a3e6-4ec9-9f0c-c7274dbaeb2f) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1288.json) |
|[Microsoft Managed Control 1289 – Információs rendszer biztonsági mentése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a724864-956a-496c-b778-637cb1d762cf) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1289.json) |
|[Microsoft Managed Control 1290 – Információs rendszer biztonsági mentése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F92f85ce9-17b7-49ea-85ee-ea7271ea6b82) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1290.json) |

### <a name="information-system-backup--testing-for-reliability--integrity"></a>Információs rendszer biztonsági mentésének | Megbízhatóság/integritás tesztelése

**Azonosító:** NIST SP 800-53 R4 CP-9 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1291 - Information System Backup \| Testing for Reliability /Integrity](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8fd073-9c85-4ee2-a9d0-2e4ec9eb8912) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1291.json) |

### <a name="information-system-backup--test-restoration-using-sampling"></a>Információs rendszer biztonsági mentésének | Visszaállítási teszt mintavételezéssel

**Azonosító:** NIST SP 800-53 R4 CP-9 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1292 – Az információs rendszer biztonsági mentési tesztje \| mintavételezéssel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd03516cf-0293-489f-9b32-a18f2a79f836) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1292.json) |

### <a name="information-system-backup--separate-storage-for-critical-information"></a>Információs rendszer biztonsági mentésének | A kritikus információk külön tárterülete

**Azonosító:** NIST SP 800-53 R4 CP-9 (3) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1293 – Information System \| Backup Separate Storage for Critical Information](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87f7cd82-2e45-4d0f-9e2f-586b0962d142) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1293.json) |

### <a name="information-system-backup--transfer-to-alternate-storage-site"></a>Információs rendszer biztonsági mentésének | Átvitel másik tárolóhelyre

**Azonosító:** NIST SP 800-53 R4 CP-9 (5) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1294 – Információs rendszer biztonsági \| mentésének átvitele másik tárolóhelyre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49dbe627-2c1e-438c-979e-dd7a39bbf81d) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1294.json) |

### <a name="information-system-recovery-and-reconstitution"></a>Információk Rendszer-helyreállítás és a behelyettesítés

**Azonosító:** NIST SP 800-53 R4 CP-10 **tulajdonos:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1295 – Információ Rendszer-helyreállítás és újrakonfigurálásról](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa895fbdb-204d-4302-9689-0a59dc42b3d9) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1295.json) |

### <a name="information-system-recovery-and-reconstitution--transaction-recovery"></a>Információk Rendszer-helyreállítás és az újrakonfigurálás | Tranzakció helyreállítása

**Azonosító:** NIST SP 800-53 R4 CP-10 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1296 – Információk Rendszer-helyreállítás és a tranzakció-helyreállítás \| újrakonfigurálásának ellenőrzése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe57b98a0-a011-4956-a79d-5d17ed8b8e48) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1296.json) |

### <a name="information-system-recovery-and-reconstitution--restore-within-time-period"></a>Információk Rendszer-helyreállítás és a behelyettesítés | Visszaállítás az időszakon belül

**Azonosító:** NIST SP 800-53 R4 CP-10 (4) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1297 – Információk Rendszer-helyreállítás visszaállításról és visszaállításról \| az időszakon belül](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93fd8af1-c161-4bae-9ba9-f62731f76439) |A Microsoft ezt a válságterv-ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1297.json) |

## <a name="identification-and-authentication"></a>Azonosítás és hitelesítés

### <a name="identification-and-authentication-policy-and-procedures"></a>Azonosítási és hitelesítési házirend és eljárások

**Azonosító:** NIST SP 800-53 R4 IA-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1298 – Azonosítási és hitelesítési házirend és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1dc784b5-4895-4d27-9d40-a06b032bd1ee) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1298.json) |
|[Microsoft Managed Control 1299 – Azonosítási és hitelesítési házirend és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4e54f7-9ab0-4bae-b6cc-457809948a89) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1299.json) |

### <a name="identification-and-authentication-organizational-users"></a>Azonosítás és hitelesítés (szervezeti felhasználók)

**Azonosító:** NIST SP 800-53 R4 IA-2 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1300 – Azonosítás és hitelesítés (szervezeti felhasználók)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99deec7d-5526-472e-b07c-3645a792026a) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1300.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés kiemelt jogosultságú fiókokhoz

**Azonosító:** NIST SP 800-53 R4 IA-2 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes írási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[Az MFA-t engedélyezni kell az előfizetés tulajdonosi engedélyekkel rendelkező fiókjainál](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A fiókok vagy erőforrások biztonságának elkerülése érdekében engedélyezni kell a többtényezős hitelesítést (MFA) minden tulajdonosi engedéllyel rendelkező előfizetési fiókhoz. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[Microsoft Managed Control 1301 – Azonosítás és hitelesítés (szervezeti felhasználók) hálózati hozzáférése kiemelt \| jogosultságú fiókokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8e0cc-ac23-468b-abe4-a8a1cc6d7a08) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1301.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés a nem kiemelt jogosultságú fiókokhoz

**Azonosító:** NIST SP 800-53 R4 IA-2 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedélyekkel rendelkező fiókokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A többtényezős hitelesítést (MFA) engedélyezni kell az összes olvasási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft Managed Control 1302 – Azonosítás és hitelesítés (szervezeti felhasználók) hálózati hozzáférése a nem \| kiemelt jogosultságú fiókokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09828c65-e323-422b-9774-9d5c646124da) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1302.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Azonosítás és hitelesítés (szervezeti felhasználók) | Helyi hozzáférés a kiemelt jogosultságú fiókokhoz

**Azonosító:** NIST SP 800-53 R4 IA-2 (3) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1303 – Azonosítás és hitelesítés (szervezet, felhasználók) helyi hozzáférése kiemelt \| jogosultságú fiókokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F80ca0a27-918a-4604-af9e-723a27ee51e8) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1303.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Azonosítás és hitelesítés (szervezeti felhasználók) | Helyi hozzáférés a nem kiemelt jogosultságú fiókokhoz

**Azonosító:** NIST SP 800-53 R4 IA-2 (4) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1304 – Azonosítás és hitelesítés (szervezet, felhasználók) helyi hozzáférése a nem kiemelt \| jogosultságú fiókokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ca71be3-16cb-4d39-8b50-7f8fd5e2f11b) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1304.json) |

### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Azonosítás és hitelesítés (szervezeti felhasználók) | Csoporthitelesítés

**Azonosító:** NIST SP 800-53 R4 IA-2 (5) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1305 – Azonosítás és hitelesítés (szervezet, felhasználók) \| csoporthitelesítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9166a8-1722-4b8f-847c-2cf3f2618b3d) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1305.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés kiemelt jogosultságú fiókokhoz – Replay Replay Resistant

**Azonosító:** NIST SP 800-53 R4 IA-2 (8) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1306 – Identification and Authentication (org. Users) \| Net. Hozzáférés a Priv. Accts-hez. – Visszajátszás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcafc6c3c-5fc5-4c5e-a99b-a0ccb1d34eff) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1306.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés a nem kiemelt jogosultságú fiókokhoz – Replay Replay Replay Resistant

**Azonosító:** NIST SP 800-53 R4 IA-2 (9) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1307 – Identification and Authentication (org. Users) \| Net. Nem Priv hozzáférés. Accts. – Visszajátszás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84e622c8-4bed-417c-84c6-b2fb0dd73682) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1307.json) |

### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Azonosítás és hitelesítés (szervezeti felhasználók) | Távelérés – Különálló eszköz

**Azonosító:** NIST SP 800-53 R4 IA-2 (11) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1308 – Azonosítás és hitelesítés (szervezet, felhasználók) \| távelérés – Különálló eszköz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81817e1c-5347-48dd-965a-40159d008229) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1308.json) |

### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Azonosítás és hitelesítés (szervezeti felhasználók) | Piv hitelesítő adatok elfogadása

**Azonosító:** NIST SP 800-53 R4 IA-2 (12) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1309 – Azonosítás és hitelesítés (szervezet, felhasználók) Piv hitelesítő adatok \| elfogadása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff355d62b-39a8-4ba3-abf7-90f71cb3b000) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1309.json) |

### <a name="device-identification-and-authentication"></a>Eszközazonosítás és -hitelesítés

**Azonosító:** NIST SP 800-53 R4 IA-3 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1310 – Eszközazonosítás és -hitelesítés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F450d7ede-823d-4931-a99d-57f6a38807dc) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1310.json) |

### <a name="identifier-management"></a>Azonosítókezelés

**Azonosító:** NIST SP 800-53 R4 IA-4 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1311 – Azonosítókezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7568697-0c9e-4ea3-9cec-9e567d14f3c6) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1311.json) |
|[Microsoft Managed Control 1312 – Azonosítókezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d6a5968-9eef-4c18-8534-376790ab7274) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1312.json) |
|[Microsoft Managed Control 1313 – Azonosítókezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36220f5b-79a1-4cdb-8c74-2d2449f9a510) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1313.json) |
|[Microsoft Managed Control 1314 – Azonosítókezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef0c8530-efd9-45b8-b753-f03083d06295) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1314.json) |
|[Microsoft Managed Control 1315 – Azonosítókezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87116-f1a1-4edb-bfbf-14e036f8d454) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1315.json) |

### <a name="identifier-management--identify-user-status"></a>Azonosítókezelési | Felhasználói állapot azonosítása

**Azonosító:** NIST SP 800-53 R4 IA-4 (4) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1316 – Azonosítókezelés \| – Felhasználói állapot azonosítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8ce14753-66e5-465d-9841-26ef55c09c0d) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1316.json) |

### <a name="authenticator-management"></a>Hitelesítők kezelése

**Azonosító:** NIST SP 800-53 R4 IA-5 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rendszer által hozzárendelt felügyelt identitás hozzáadása a vendégkonfiguráció-hozzárendelések engedélyezéséhez identitások nélkül lévő virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Ez a szabályzat egy rendszer által hozzárendelt felügyelt identitást ad hozzá az Azure-ban üzemeltetett virtuális gépekhez, amelyek támogatottak a vendégkonfigurációval, de nincsenek felügyelt identitásaik. A rendszer által hozzárendelt felügyelt identitás minden vendégkonfiguráció-hozzárendelés előfeltétele, és a vendégkonfigurációs szabályzatdefiníciók használata előtt hozzá kell adni a gépekhez. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |módosítás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Rendszer által hozzárendelt felügyelt identitás hozzáadása a vendégkonfiguráció-hozzárendelések engedélyezéséhez a felhasználó által hozzárendelt identitással társított virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Ez a szabályzat egy rendszer által hozzárendelt felügyelt identitást ad hozzá a vendégkonfiguráció által támogatott Azure-ban üzemeltetett virtuális gépekhez, és legalább egy felhasználó által hozzárendelt identitással, de nem kap rendszer által hozzárendelt felügyelt identitást. A rendszer által hozzárendelt felügyelt identitás minden vendégkonfiguráció-hozzárendelés előfeltétele, és a vendégkonfigurációs szabályzatdefiníciók használata előtt hozzá kell adni a gépekhez. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |módosítás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Naplóz olyan Linux rendszerű gépeket, amelyek nem 0644-re vannak beállítva a passwd fájlengedélyekkel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a passwd fájlengedélyekkel nem rendelkező Linux rendszerű gépek 0644-re vannak beállítva |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[Jelszó nélküli fiókkal rendelkező Linux rendszerű gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a Linux rendszerű gépek jelszó nélküli fiókkal vannak |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[Jelszavakat nem tároló Windows rendszerű gépek naplózása visszaverhető titkosítással](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a Windows rendszerű gépek nem tárolnak visszatitkosítást használó jelszavakat |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[A Linux vendégkonfigurációs bővítmény üzembe helyezése a vendégkonfiguráció-hozzárendelések Linux rendszerű virtuális gépeken való engedélyezéséhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Ez a szabályzat telepíti a Linux vendégkonfigurációs bővítményt az Azure-ban üzemeltetett, vendégkonfiguráció által támogatott Linux rendszerű virtuális gépekre. A Linux vendégkonfigurációs bővítmény előfeltétel minden Linux-vendégkonfiguráció-hozzárendeléshez, és a Linux vendégkonfigurációs szabályzatdefiníciók használata előtt telepíteni kell őket a gépekre. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[A Windows vendégkonfigurációs bővítmény üzembe helyezése a vendégkonfiguráció-hozzárendelések engedélyezéséhez Windows rendszerű virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Ez a szabályzat telepíti a Windows vendégkonfigurációs bővítményt az Azure-ban üzemeltetett, vendégkonfiguráció által támogatott Windows rendszerű virtuális gépekre. A Windows vendégkonfigurációs bővítmény előfeltétel minden Windows vendégkonfiguráció-hozzárendeléshez, és a Windows vendégkonfigurációs szabályzatdefiníciók használata előtt telepítve kell lennie a gépeken. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft Managed Control 1317 – Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8877f519-c166-47b7-81b7-8a8eb4ff3775) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1317.json) |
|[Microsoft Managed Control 1318 – Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffced5fda-3bdb-4d73-bfea-0e2c80428b66) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1318.json) |
|[Microsoft Managed Control 1319 – Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F66f7ae57-5560-4fc5-85c9-659f204e7a42) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1319.json) |
|[Microsoft Managed Control 1320 – Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f54c732-71d4-4f93-a696-4e373eca3a77) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1320.json) |
|[Microsoft Managed Control 1321 – Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb627cc6-3a9d-46b5-96b7-5fca49178a37) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1321.json) |
|[Microsoft Managed Control 1322 – Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d1d971e-467e-4278-9633-c74c3d4fecc4) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1322.json) |
|[Microsoft Managed Control 1323 – Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabe8f70b-680f-470c-9b86-a7edfb664ecc) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1323.json) |
|[Microsoft Managed Control 1324 – Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cfea2b3-7f77-497e-ac20-0752f2ff6eee) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1324.json) |
|[Microsoft Managed Control 1325 – Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1845796a-7581-49b2-ae20-443121538e19) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1325.json) |
|[Microsoft Managed Control 1326 – Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8605fc00-1bf5-4fb3-984e-c95cec4f231d) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1326.json) |

### <a name="authenticator-management--password-based-authentication"></a>Authenticator Management | Password-Based hitelesítés

**Azonosító:** NIST SP 800-53 R4 IA-5 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rendszer által hozzárendelt felügyelt identitás hozzáadása a vendégkonfiguráció-hozzárendelések engedélyezéséhez identitások nélkül lévő virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Ez a szabályzat egy rendszer által hozzárendelt felügyelt identitást ad hozzá az Azure-ban üzemeltetett virtuális gépekhez, amelyek támogatottak a vendégkonfigurációval, de nincsenek felügyelt identitásaik. A rendszer által hozzárendelt felügyelt identitás minden vendégkonfiguráció-hozzárendelés előfeltétele, és a vendégkonfigurációs szabályzatdefiníciók használata előtt hozzá kell adni a gépekhez. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |módosítás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Rendszer által hozzárendelt felügyelt identitás hozzáadása a vendégkonfiguráció-hozzárendelések engedélyezéséhez a felhasználó által hozzárendelt identitással társított virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Ez a szabályzat egy rendszer által hozzárendelt felügyelt identitást ad hozzá a vendégkonfiguráció által támogatott Azure-ban üzemeltetett virtuális gépekhez, és legalább egy felhasználó által hozzárendelt identitással, de nem kap rendszer által hozzárendelt felügyelt identitást. A rendszer által hozzárendelt felügyelt identitás minden vendégkonfiguráció-hozzárendelés előfeltétele, és a vendégkonfigurációs szabályzatdefiníciók használata előtt hozzá kell adni a gépekhez. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |módosítás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Az előző 24 jelszó újrahasználatát lehetővé tő Windows rendszerű gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b054a0d-39e2-4d53-bea3-9734cad2c69b) |Előfeltételként a szabályzat-hozzárendelési hatókörben kell telepíteni az előfeltételeket. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha az előző 24 jelszó újrahasználatát engedélyező Windows rendszerű gépek |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEnforce_AINE.json) |
|[70 napos maximális jelszóval nem védett Windows-gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ceb8dc2-559c-478b-a15b-733fbf1e3738) |Előfeltételként a szabályzat-hozzárendelési hatókörben kell telepíteni az előfeltételeket. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a jelszó maximális kora nem 70 nap |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMaximumPassword_AINE.json) |
|[A jelszó minimális kora nem 1 nap, windowsos gépek naplózása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237b38db-ca4d-4259-9e47-7882441ca2c0) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a jelszó minimális kora nem 1 nap |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMinimumPassword_AINE.json) |
|[Olyan Windows rendszerű gépek naplózása, amelyeken nincs engedélyezve a jelszó bonyolultsága beállítás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf16e0bb-31e1-4646-8202-60a235cc7e74) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha olyan Windows rendszerű gépeken, amelyeken nincs engedélyezve a jelszó összetettségi beállítása |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordComplexity_AINE.json) |
|[Windows rendszerű gépek naplózása, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2d0e922-65d0-40c4-8f87-ea6da2d307a2) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelési hatókörben. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha a windowsos gépek nem korlátozzák a jelszó minimális hosszát 14 karakterre |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordLength_AINE.json) |
|[Jelszavakat nem tároló Windows rendszerű gépek naplózása visszaverhető titkosítással](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Előfeltételként telepíteni kell az előfeltételeket a szabályzat-hozzárendelés hatókörében. Részletekért látogasson el [https://aka.ms/gcpol](https://aka.ms/gcpol) ide: . A gépek nem megfelelőek, ha olyan Windows rendszerű gépek, amelyek nem tárolnak visszatitkosítást használó jelszavakat |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[A Linux vendégkonfigurációs bővítmény üzembe helyezése a vendégkonfiguráció hozzárendelésének engedélyezéséhez Linux rendszerű virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Ez a szabályzat telepíti a Linux vendégkonfigurációs bővítményt az Azure-ban üzemeltetett, vendégkonfiguráció által támogatott Linux rendszerű virtuális gépekre. A Linux vendégkonfigurációs bővítmény előfeltétele az összes Linux vendégkonfiguráció-hozzárendelésnek, és a Linux vendégkonfigurációs szabályzatdefiníciók használata előtt telepíteni kell őket a gépekre. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[A Windows vendégkonfigurációs bővítmény üzembe helyezése a vendégkonfiguráció-hozzárendelések engedélyezéséhez Windows rendszerű virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Ez a szabályzat telepíti a Windows vendégkonfigurációs bővítményt az Azure-ban üzemeltetett, vendégkonfiguráció által támogatott Windows rendszerű virtuális gépekre. A Windows vendégkonfigurációs bővítmény előfeltétel minden Windows vendégkonfiguráció-hozzárendeléshez, és a Windows vendégkonfigurációs szabályzatdefiníciók használata előtt telepítve kell lennie a gépeken. A vendégkonfigurációval kapcsolatos további információkért látogasson el ide: [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft Managed Control 1327 – Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03188d8f-1ae5-4fe1-974d-2d7d32ef937d) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1327.json) |
|[Microsoft Managed Control 1328 – Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5c66fdc-3d02-4034-9db5-ba57802609de) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1328.json) |
|[Microsoft Managed Control 1329 – Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F498f6234-3e20-4b6a-a880-cbd646d973bd) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1329.json) |
|[Microsoft Managed Control 1330 – Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff75cedb2-5def-4b31-973e-b69e8c7bd031) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1330.json) |
|[Microsoft Managed Control 1331 – Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05460fe2-301f-4ed1-8174-d62c8bb92ff4) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1331.json) |
|[Microsoft Managed Control 1332 – Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068260be-a5e6-4b0a-a430-cd27071c226a) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1332.json) |

### <a name="authenticator-management--pki-based-authentication"></a>Authenticator Management | Pki-Based hitelesítés

**Azonosító:** NIST SP 800-53 R4 IA-5 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1333 – Authenticator Management \| Pki-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3298d6bf-4bc6-4278-a95d-f7ef3ac6e594) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1333.json) |
|[Microsoft Managed Control 1334 – Authenticator Management \| Pki-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44bfdadc-8c2e-4c30-9c99-f005986fabcd) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1334.json) |
|[Microsoft Managed Control 1335 – Authenticator Management \| Pki-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F382016f3-d4ba-4e15-9716-55077ec4dc2a) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1335.json) |
|[Microsoft Managed Control 1336 – Authenticator Management \| Pki-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77f56280-e367-432a-a3b9-8ca2aa636a26) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1336.json) |

### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Authenticator Management | Személyes vagy megbízható külső regisztráció

**Azonosító:** NIST SP 800-53 R4 IA-5 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1337 – Authenticator Management In-Person vagy megbízható \| külső regisztráció](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F463e5220-3f79-4e24-a63f-343e4096cd22) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1337.json) |

### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Authenticator Management | A jelszóerősség meghatározásának automatizált támogatása

**Azonosító:** NIST SP 800-53 R4 IA-5 (4) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1338 – Authenticator Management \| Automated Support for Password Strength Determination](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c59a207-6aed-41dc-83a2-e1ff66e4a4db) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1338.json) |

### <a name="authenticator-management--protection-of-authenticators"></a>Authenticator Management | A hitelesítők védelme

**Azonosító:** NIST SP 800-53 R4 IA-5 (6) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1339 – Authenticator Management \| Protection of Authenticators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F367ae386-db7f-4167-b672-984ff86277c0) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1339.json) |

### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Authenticator Management | Nincsenek beágyazott titkosítatlan statikus hitelesítők

**Azonosító:** NIST SP 800-53 R4 IA-5 (7) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1340 – Authenticator Management \| No Embedded Unencrypted Static Authenticators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe51ff84b-e5ea-408f-b651-2ecc2933e4c6) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1340.json) |

### <a name="authenticator-management--multiple-information-system-accounts"></a>Authenticator Management | Több információsrendszer-fiók

**Azonosító:** NIST SP 800-53 R4 IA-5 (8) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1341 – Authenticator Management \| Multiple Information System Accounts](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34cb7e92-fe4c-4826-b51e-8cd203fa5d35) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1341.json) |

### <a name="authenticator-management--hardware-token-based-authentication"></a>Authenticator Management | Hardveres Token-Based hitelesítés

**Azonosító:** NIST SP 800-53 R4 IA-5 (11) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1342 – Authenticator Management \| Hardware Token-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F283a4e29-69d5-4c94-b99e-29acf003c899) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1342.json) |

### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Authenticator Management | Gyorsítótárazott hitelesítők lejárata

**Azonosító:** NIST SP 800-53 R4 IA-5 (13) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1343 – Gyorsítótárazott hitelesítők \| felügyeletének lejárata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c251a55-31eb-4e53-99c6-e9c43c393ac2) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1343.json) |

### <a name="authenticator-feedback"></a>Authenticator visszajelzés

**Azonosító:** NIST SP 800-53 R4 IA-6 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1344 – Authenticator visszajelzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c895fe7-2d8e-43a2-838c-3a533a5b355e) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1344.json) |

### <a name="cryptographic-module-authentication"></a>Kriptográfiai modul hitelesítése

**Azonosító:** NIST SP 800-53 R4 IA-7 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1345 – Titkosítási modul hitelesítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff86aa129-7c07-4aa4-bbf5-792d93ffd9ea) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1345.json) |

### <a name="identification-and-authentication-non-organizational-users"></a>Azonosítás és hitelesítés (nem szervezeti felhasználók)

**Id**: NIST SP 800-53 R4 IA-8 **Ownership**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1346 – Azonosítás és hitelesítés (nem szervezeti felhasználók)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F464dc8ce-2200-4720-87a5-dc5952924cc6) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1346.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Azonosítás és hitelesítés (nem szervezeti felhasználók) | Piv hitelesítő adatok elfogadása más ügynökségektől

**Azonosító:** NIST SP 800-53 R4 IA-8 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1347 - Identification and Authentication (non-org. Felhasználók) \| PIV-tanúsítványok elfogadása. Más Agysból.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F131a2706-61e9-4916-a164-00e052056462) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1347.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Azonosítás és hitelesítés (nem szervezeti felhasználók) | Harmadik féltől származó hitelesítő adatok elfogadása

**Azonosító:** NIST SP 800-53 R4 IA-8 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1348 - Identification and Authentication (non-org. Felhasználók) \| Harmadik féltől származó hitelesítő adatok elfogadása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F855ced56-417b-4d74-9d5f-dd1bc81e22d6) |A Microsoft ezt az azonosítási és hitelesítési vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1348.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Azonosítás és hitelesítés (nem szervezeti felhasználók) | A Ficam-Approved termékek használata

**Azonosító:** NIST SP 800-53 R4 IA-8 (3) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1349 – Azonosítás és hitelesítés (nem szervezet. Felhasználók) \| A FICAM-Approved használata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17641f70-94cd-4a5d-a613-3d1143e20e34) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1349.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Azonosítás és hitelesítés (nem szervezeti felhasználók) | Az Ficam-Issued profilok használata

**Azonosító:** NIST SP 800-53 R4 IA-8 (4) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1350 – Azonosítás és hitelesítés (nem szervezet. Felhasználók) \| A FICAM-Issued használata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd77fd943-6ba6-4a21-ba07-22b03e347cc4) |A Microsoft ezt az azonosítási és hitelesítési vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1350.json) |

## <a name="incident-response"></a>Incidensmegoldás

### <a name="incident-response-policy-and-procedures"></a>Incidensválasz-szabályzat és eljárások

**Azonosító:** NIST SP 800-53 R4 IR-1 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1351 – Incidensválasz-szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbcfb6683-05e5-4ce6-9723-c3fbe9896bdd) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1351.json) |
|[Microsoft Managed Control 1352 – Incidensválasz-szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F518cb545-bfa8-43f8-a108-3b7d5037469a) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1352.json) |

### <a name="incident-response-training"></a>Incidensválasz betanítás

**Azonosító:** NIST SP 800-53 R4 IR-2 Ownership : **Shared**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1353 – Incidensválasz-képzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc785ad59-f78f-44ad-9a7f-d1202318c748) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1353.json) |
|[Microsoft Managed Control 1354 – Incidensválasz-képzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9fd92c17-163a-4511-bb96-bbb476449796) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1354.json) |
|[Microsoft Managed Control 1355 – Incidensválasz-képzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90e01f69-3074-4de8-ade7-0fef3e7d83e0) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1355.json) |

### <a name="incident-response-training--simulated-events"></a>Incidensválasz-betanítás | Szimulált események

**Azonosító:** NIST SP 800-53 R4 IR-2 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1356 – Incidensválasz-betanítás \| szimulált események](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8829f8f5-e8be-441e-85c9-85b72a5d0ef3) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1356.json) |

### <a name="incident-response-training--automated-training-environments"></a>Incidensválasz-betanítás | Automatizált betanító környezetek

**Azonosító:** NIST SP 800-53 R4 IR-2 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1357 – Incidensválasz-betanítás \| automatizált betanító környezetek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe4213689-05e8-4241-9d4e-8dd1cdafd105) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1357.json) |

### <a name="incident-response-testing"></a>Incidensválasz tesztelése

**Azonosító:** NIST SP 800-53 R4 IR-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1358 – Incidensválasz tesztelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feffbaeef-5bf4-400d-895e-ef8cbc0e64c7) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1358.json) |

### <a name="incident-response-testing--coordination-with-related-plans"></a>Incidensválasz-tesztelési | Koordináció a kapcsolódó tervekkel

**Azonosító:** NIST SP 800-53 R4 IR-3 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1359 – Incidensválasz \| tesztelése kapcsolódó tervekkel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47bc7ea0-7d13-4f7c-a154-b903f7194253) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1359.json) |

### <a name="incident-handling"></a>Incidenskezelés

**Azonosító:** NIST SP 800-53 R4 IR-4 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1360 – Incidenskezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbe5b05e7-0b82-4ebc-9eda-25e447b1a41e) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1360.json) |
|[Microsoft Managed Control 1361 – Incidenskezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ed3be1-7276-4452-9a5d-e4168565ac67) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1361.json) |
|[Microsoft Managed Control 1362 – Incidenskezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5d169442-d6ef-439b-8dca-46c2c3248214) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1362.json) |

### <a name="incident-handling--automated-incident-handling-processes"></a>Incidenskezelési | Automatizált incidenskezelési folyamatok

**Azonosító:** NIST SP 800-53 R4 IR-4 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1363 – Incidenskezelés automatizált \| incidenskezelési folyamatok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea3e8156-89a1-45b1-8bd6-938abc79fdfd) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1363.json) |

### <a name="incident-handling--dynamic-reconfiguration"></a>Incidenskezelési | Dinamikus újrakonfigurálás

**Azonosító:** NIST SP 800-53 R4 IR-4 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1364 – Incidenskezelés \| dinamikus újrakonfigurálás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c615c2a-dc83-4dda-8220-abce7b50c9bc) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1364.json) |

### <a name="incident-handling--continuity-of-operations"></a>Incidenskezelési | Az üzemeltetés folytonossága

**Azonosító:** NIST SP 800-53 R4 IR-4 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1365 – Incidenskezelési \| folytonossági műveletek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4116891d-72f7-46ee-911c-8056cc8dcbd5) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1365.json) |

### <a name="incident-handling--information-correlation"></a>Incidenskezelési | Információk korrelációja

**Azonosító:** NIST SP 800-53 R4 IR-4 (4) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1366 – Incidenskezelési \| információk korrelációja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06c45c30-ae44-4f0f-82be-41331da911cc) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1366.json) |

### <a name="incident-handling--insider-threats---specific-capabilities"></a>Incidenskezelési | Belső fenyegetések – Adott képességek

**Azonosító:** NIST SP 800-53 R4 IR-4 (6) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1367 – Incidenskezelés \| belső fenyegetések – Adott képességek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F435b2547-6374-4f87-b42d-6e8dbe6ae62a) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1367.json) |

### <a name="incident-handling--correlation-with-external-organizations"></a>Incidenskezelési | Korreláció külső szervezéssel

**Azonosító:** NIST SP 800-53 R4 IR-4 (8) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1368 – Incidenskezelés \| korreláció külső szervezésekkel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F465f32da-0ace-4603-8d1b-7be5a3a702de) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1368.json) |

### <a name="incident-monitoring"></a>Incidensfigyelés

**Azonosító:** NIST SP 800-53 R4 IR-5 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1369 – Incidensfigyelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18cc35ed-a429-486d-8d59-cb47e87304ed) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1369.json) |

### <a name="incident-monitoring--automated-tracking--data-collection--analysis"></a>Incidensfigyelési | Automatizált követés / Adatgyűjtés / Elemzés

**Azonosító:** NIST SP 800-53 R4 IR-5 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1370 – Incidensfigyelés \| automatikus nyomon követése / Adatgyűjtés / Elemzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F924e1b2d-c502-478f-bfdb-a7e09a0d5c01) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1370.json) |

### <a name="incident-reporting"></a>Incidensjelentés

**Azonosító:** NIST SP 800-53 R4 IR-6 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1371 – Incidensjelentés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9447f354-2c85-4700-93b3-ecdc6cb6a417) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1371.json) |
|[Microsoft Managed Control 1372 – Incidensjelentés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25b96717-c912-4c00-9143-4e487f411726) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1372.json) |

### <a name="incident-reporting--automated-reporting"></a>Incidensjelentési | Automatizált jelentéskészítés

**Azonosító:** NIST SP 800-53 R4 IR-6 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1373 – \| Incidensjelentések automatizált jelentéskészítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4cca950f-c3b7-492a-8e8f-ea39663c14f9) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1373.json) |

### <a name="incident-response-assistance"></a>Incidensválasz-segítség

**Id**: NIST SP 800-53 R4 IR-7 **Ownership**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1374 – Incidensválasz-segítség](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc5c8616-52ef-4e5e-8000-491634ed9249) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1374.json) |

### <a name="incident-response-assistance--automation-support-for-availability-of-information--support"></a>Incidensválasz-| Automation-támogatás az információk rendelkezésre állásához/támogatáshoz

**Azonosító:** NIST SP 800-53 R4 IR-7 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1375 – Incidensválasz-segítség \| automatizálási támogatása információk/támogatás rendelkezésre állásához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F00379355-8932-4b52-b63a-3bc6daf3451a) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1375.json) |

### <a name="incident-response-assistance--coordination-with-external-providers"></a>Incidensválasz-segítségnyújtási | Koordináció külső szolgáltatókkal

**Azonosító:** NIST SP 800-53 R4 IR-7 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1376 – Incidensválasz-segítségnyújtási \| koordináció külső szolgáltatókkal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F493a95f3-f2e3-47d0-af02-65e6d6decc2f) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1376.json) |
|[Microsoft Managed Control 1377 – Incidensválasz-segítségnyújtási \| koordináció külső szolgáltatókkal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68434bd1-e14b-4031-9edb-a4adf5f84a67) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1377.json) |

### <a name="incident-response-plan"></a>Incidensválasz-terv

**Azonosító:** NIST SP 800-53 R4 IR-8 Ownership : **Shared**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1378 – Incidensválasz-terv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97fceb70-6983-42d0-9331-18ad8253184d) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1378.json) |
|[Microsoft Managed Control 1379 – Incidensválasz-terv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9442dd2c-a07f-46cd-b55a-553b66ba47ca) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1379.json) |
|[Microsoft Managed Control 1380 – Incidensválasz-terv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4319b7e-ea8d-42ff-8a67-ccd462972827) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1380.json) |
|[Microsoft Managed Control 1381 – Incidensválasz-terv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe5368258-9684-4567-8126-269f34e65eab) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1381.json) |
|[Microsoft Managed Control 1382 – Incidensválasz-terv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F841392b3-40da-4473-b328-4cde49db67b3) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1382.json) |
|[Microsoft Managed Control 1383 – Incidensválasz-terv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4558451-e16a-4d2d-a066-fe12a6282bb9) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1383.json) |

### <a name="information-spillage-response"></a>Információ-kimaradásra adott válasz

**Azonosító:** NIST SP 800-53 R4 IR-9 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1384 – Information Spillage Response](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79fbc228-461c-4a45-9004-a865ca0728a7) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1384.json) |
|[Microsoft Managed Control 1385 – Information Spillage Response](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e495e65-8663-49ca-9b38-9f45e800bc58) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1385.json) |
|[Microsoft Managed Control 1386 – Information Spillage Response](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5120193e-91fd-4f9d-bc6d-194f94734065) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1386.json) |
|[Microsoft Managed Control 1387 – Information Spillage Response](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3007185-3857-43a9-8237-06ca94f1084c) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1387.json) |
|[Microsoft Managed Control 1388 – Information Spillage Response](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c7c575a-d4c5-4f6f-bd49-dee97a8cba55) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1388.json) |
|[Microsoft Managed Control 1389 – Információszivárgásra adott válasz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39e6fda-ae70-4891-a739-be7bba6d1062) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1389.json) |

### <a name="information-spillage-response--responsible-personnel"></a>Információ- és adatszivárgási válasz | Felelős személyzet

**Azonosító:** NIST SP 800-53 R4 IR-9 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1390 – Information Spillage Response \| Responsible Personnel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3b65b63-09ec-4cb5-8028-7dd324d10eb0) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1390.json) |

### <a name="information-spillage-response--training"></a>Információ- és adatszivárgási válasz | Képzés

**Azonosító:** NIST SP 800-53 R4 IR-9 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1391 – Information Spillage Response \| Training](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd6ac1a1-660e-4810-baa8-74e868e2ed47) |A Microsoft megvalósítja ezt az incidensválasz-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1391.json) |

### <a name="information-spillage-response--post-spill-operations"></a>Information Spillage Response | Becslő műveletek utáni műveletek

**Azonosító:** NIST SP 800-53 R4 IR-9 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1392 – Information Spillage Response \| Post-Spill Operations](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86dc819f-15e1-43f9-a271-41ae58d4cecc) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1392.json) |

### <a name="information-spillage-response--exposure-to-unauthorized-personnel"></a>Information Spillage Response | Jogosulatlan személyeknek való kitettség

**Azonosító:** NIST SP 800-53 R4 IR-9 (4) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1393 – Információszivárgási válasz illetéktelen \| személyeknek való kitettsége](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F731856d8-1598-4b75-92de-7d46235747c0) |A Microsoft ezt az incidensválasz-vezérlést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1393.json) |

## <a name="maintenance"></a>Karbantartás

### <a name="system-maintenance-policy-and-procedures"></a>Rendszerkarbantartási házirend és eljárások

**Azonosító:** NIST SP 800-53 R4 MA-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1394 – Rendszerkarbantartási szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4db56f68-3f50-45ab-88f3-ca46f5379a94) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1394.json) |
|[Microsoft Managed Control 1395 – Rendszerkarbantartási szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7207a023-a517-41c5-9df2-09d4c6845a05) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1395.json) |

### <a name="controlled-maintenance"></a>Szabályozott karbantartás

**Azonosító:** NIST SP 800-53 R4 MA-2 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1396 – Szabályozott karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F276af98f-4ff9-4e69-99fb-c9b2452fb85f) |A Karbantartás vezérlőt a Microsoft implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1396.json) |
|[Microsoft Managed Control 1397 – Szabályozott karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391af4ab-1117-46b9-b2c7-78bbd5cd995b) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1397.json) |
|[Microsoft Managed Control 1398 – Szabályozott karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F443e8f3d-b51a-45d8-95a7-18b0e42f4dc4) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1398.json) |
|[Microsoft Managed Control 1399 – Szabályozott karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2256e638-eb23-480f-9e15-6cf1af0a76b3) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1399.json) |
|[Microsoft Managed Control 1400 – Szabályozott karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96d5098-a604-4cdf-90b1-ef6449a27424) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1400.json) |
|[Microsoft Managed Control 1401 – Szabályozott karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb78ee928-e3c1-4569-ad97-9f8c4b629847) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1401.json) |

### <a name="controlled-maintenance--automated-maintenance-activities"></a>Szabályozott karbantartási | Automatizált karbantartási tevékenységek

**Azonosító:** NIST SP 800-53 R4 MA-2 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1402 – Szabályozott karbantartási \| automatizált karbantartási tevékenységek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a560d32-8075-4fec-9615-9f7c853f4ea9) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1402.json) |
|[Microsoft Managed Control 1403 – Szabályozott karbantartási \| automatizált karbantartási tevékenységek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F57149289-d52b-4f40-9fe6-5233c1ef80f7) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1403.json) |

### <a name="maintenance-tools"></a>Karbantartási eszközök

**Azonosító:** NIST SP 800-53 R4 MA-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1404 – Karbantartási eszközök](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d8f903-0cd6-449f-a172-50f6579c182b) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1404.json) |

### <a name="maintenance-tools--inspect-tools"></a>Karbantartási eszközök | Eszközök vizsgálata

**Azonosító:** NIST SP 800-53 R4 MA-3 (1) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1405 – Karbantartási eszközök \| – Eszközök vizsgálata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1a0bf3-409a-4b00-b60d-0b1f917f7e7b) |A Karbantartás vezérlőt a Microsoft implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1405.json) |

### <a name="maintenance-tools--inspect-media"></a>Karbantartási eszközök | Adathordozó vizsgálata

**Azonosító:** NIST SP 800-53 R4 MA-3 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1406 – Maintenance Tools \| Inspect Media](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0f5339c-9292-43aa-a0bc-d27c6b8e30aa) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1406.json) |

### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Karbantartási eszközök | Jogosulatlan eltávolítás megakadályozása

**Azonosító:** NIST SP 800-53 R4 MA-3 (3) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1407 – Karbantartási eszközök \| a jogosulatlan eltávolítás megakadályozásához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fff9fbd83-1d8d-4b41-aac2-94cb44b33976) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1407.json) |
|[Microsoft Managed Control 1408 – Karbantartási eszközök \| a jogosulatlan eltávolítás megakadályozásához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5f56ac6-4bb2-4086-bc41-ad76344ba2c2) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1408.json) |
|[Microsoft Managed Control 1409 – Karbantartási eszközök \| a jogosulatlan eltávolítás megakadályozásához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1880188-e51a-4772-b2ab-68f5e8bd27f6) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1409.json) |
|[Microsoft Managed Control 1410 – Karbantartási eszközök \| a jogosulatlan eltávolítás megakadályozásához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2596a9f-e59f-420d-9625-6e0b536348be) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1410.json) |

### <a name="nonlocal-maintenance"></a>Nem local karbantartás

**Azonosító:** NIST SP 800-53 R4 MA-4 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1411 – Nemlocal karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F898d4fe8-f743-4333-86b7-0c9245d93e7d) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1411.json) |
|[Microsoft Managed Control 1412 – Nemlokali karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3492d949-0dbb-4589-88b3-7b59601cc764) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1412.json) |
|[Microsoft Managed Control 1413 – Nemlokali karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faeedddb6-6bc0-42d5-809b-80048033419d) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1413.json) |
|[Microsoft Managed Control 1414 – Nemlocal karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce63a52-e47b-4ae2-adbb-6e40d967f9e6) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1414.json) |
|[Microsoft Managed Control 1415 – Nemlokali karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61a1dd98-b259-4840-abd5-fbba7ee0da83) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1415.json) |

### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Nemlocal maintenance | Nemlokalikus karbantartás dokumentálása

**Azonosító:** NIST SP 800-53 R4 MA-4 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1416 - Nonlocal Maintenance \| Document Nonlocal Maintenance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38dfd8a3-5290-4099-88b7-4081f4c4d8ae) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1416.json) |

### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Nemlocal maintenance | Hasonló biztonság /sanitizálás

**Azonosító:** NIST SP 800-53 R4 MA-4 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1417 - Nonlocal Maintenance \| Comparable Security /Sanitization](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7522ed84-70d5-4181-afc0-21e50b1b6d0e) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1417.json) |
|[Microsoft Managed Control 1418 - Nonlocal Maintenance \| Comparable Security / Sanitization](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e633fd-284e-4ea7-88b4-02ca157ed713) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1418.json) |

### <a name="nonlocal-maintenance--cryptographic-protection"></a>Nemlocal maintenance | Kriptográfiai védelem

**Azonosító:** NIST SP 800-53 R4 MA-4 (6) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1419 - Nonlocal Maintenance \| Cryptographic Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6747bf9-2b97-45b8-b162-3c8becb9937d) |A Karbantartás vezérlőt a Microsoft implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1419.json) |

### <a name="maintenance-personnel"></a>Karbantartó személyzet

**Azonosító:** NIST SP 800-53 R4 MA-5 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1420 – Karbantartó személyzet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05ae08cc-a282-413b-90c7-21a2c60b8404) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1420.json) |
|[Microsoft Managed Control 1421 – Karbantartó személyzet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe539caaa-da8c-41b8-9e1e-449851e2f7a6) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1421.json) |
|[Microsoft Managed Control 1422 – Karbantartó személyzet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea556850-838d-4a37-8ce5-9d7642f95e11) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1422.json) |

### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Karbantartási személyzet | Megfelelő hozzáféréssel nem

**Azonosító:** NIST SP 800-53 R4 MA-5 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1423 – Karbantartó személyek \| megfelelő hozzáférés nélkül](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7741669e-d4f6-485a-83cb-e70ce7cbbc20) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1423.json) |
|[Microsoft Managed Control 1424 – Karbantartó személyek \| megfelelő hozzáférés nélkül](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf55fc87-48e1-4676-a2f8-d9a8cf993283) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1424.json) |

### <a name="timely-maintenance"></a>Időbeni karbantartás

**Azonosító:** NIST SP 800-53 R4 MA-6 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1425 – Időbeni karbantartás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5983d99c-f39b-4c32-a3dc-170f19f6941b) |A Karbantartás vezérlőt a Microsoft valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1425.json) |

## <a name="media-protection"></a>Médiavédelem

### <a name="media-protection-policy-and-procedures"></a>Médiavédelmi szabályzat és eljárások

**Azonosító:** NIST SP 800-53 R4 MP-1 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1426 – Médiavédelmi szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21f639bc-f42b-46b1-8f40-7a2a389c291a) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1426.json) |
|[Microsoft Managed Control 1427 – Médiavédelmi szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc90e44f-d83f-4bdf-900f-3d5eb4111b31) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1427.json) |

### <a name="media-access"></a>Média-hozzáférés

**Azonosító:** NIST SP 800-53 R4 MP-2 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1428 – Media Access](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a77fcc7-b8d8-451a-ab52-56197913c0c7) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1428.json) |

### <a name="media-marking"></a>Média megjelölése

**Azonosító:** NIST SP 800-53 R4 MP-3 **Tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1429 – Médiajelölés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb07c9b24-729e-4e85-95fc-f224d2d08a80) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1429.json) |
|[Microsoft Managed Control 1430 – Médiajelölés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f559588-5e53-4b14-a7c4-85d28ebc2234) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1430.json) |

### <a name="media-storage"></a>Media Storage

**Azonosító:** NIST SP 800-53 R4 MP-4 **tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1431 – Media Storage](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7173c52-2b99-4696-a576-63dd5f970ef4) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1431.json) |
|[Microsoft Managed Control 1432 – Media Storage](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1140e542-b80d-4048-af45-3f7245be274b) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1432.json) |

### <a name="media-transport"></a>Media Transport

**Azonosító:** NIST SP 800-53 R4 MP-5 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1433 – Media Transport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b879b41-2728-41c5-ad24-9ee2c37cbe65) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1433.json) |
|[Microsoft Managed Control 1434 – Media Transport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c18f06b-a68d-41c3-8863-b8cd3acb5f8f) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1434.json) |
|[Microsoft Managed Control 1435 – Media Transport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa8d221b-d130-4637-ba16-501e666628bb) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1435.json) |
|[Microsoft Managed Control 1436 – Media Transport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28aab8b4-74fd-4b7c-9080-5a7be525d574) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1436.json) |

### <a name="media-transport--cryptographic-protection"></a>Media Transport | Kriptográfiai védelem

**Azonosító:** NIST SP 800-53 R4 MP-5 (4) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1437 – Media Transport \| Cryptographic Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d1eb6ed-bf13-4046-b993-b9e2aef0f76c) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1437.json) |

### <a name="media-sanitization"></a>Adathordozó-sanitizálás

**Azonosító:** NIST SP 800-53 R4 MP-6 **tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1438 - Media Sanitization](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40fcc635-52a2-4dbc-9523-80a1f4aa1de6) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1438.json) |
|[Microsoft Managed Control 1439 - Media Sanitization](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdce72873-c5f1-47c3-9b4f-6b8207fd5a45) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1439.json) |

### <a name="media-sanitization--review--approve--track--document--verify"></a>Médiaszabadítási | Áttekintés / Jóváhagyás / Nyomon követése / Dokumentum / Ellenőrzés

**Azonosító:** NIST SP 800-53 R4 MP-6 (1) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1440 - Media Sanitization \| Review / Approve / Track / Document / Verify](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F881299bf-2a5b-4686-a1b2-321d33679953) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1440.json) |

### <a name="media-sanitization--equipment-testing"></a>Médiaszabadítási | Berendezések tesztelése

**Azonosító:** NIST SP 800-53 R4 MP-6 (2) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1441 - Media Sanitization \| Equipment Testing](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6519d7f3-e8a2-4ff3-a935-9a9497152ad7) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1441.json) |

### <a name="media-sanitization--nondestructive-techniques"></a>Média sanitizálási | Nem destruktív technikák

**Azonosító:** NIST SP 800-53 R4 MP-6 (3) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1442 - Media Sanitization \| Nondestructive Techniques](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f26049b-2c5a-4841-9ff3-d48a26aae475) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1442.json) |

### <a name="media-use"></a>Médiahasználat

**Azonosító:** NIST SP 800-53 R4 MP-7 **tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1443 – Médiahasználat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd0ec6fa-a2e7-4361-aee4-a8688659a9ed) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1443.json) |

### <a name="media-use--prohibit-use-without-owner"></a>Médiahasználati | Tulajdonos nélküli használat tiltása

**Azonosító:** NIST SP 800-53 R4 MP-7 (1) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1444 – Médiahasználat \| tulajdonos nélkül történő használat tiltása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F666143df-f5e0-45bd-b554-135f0f93e44e) |A Microsoft ezt a Media Protection-vezérlőt implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1444.json) |

## <a name="physical-and-environmental-protection"></a>Fizikai és környezeti védelem

### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Fizikai és környezeti védelmi szabályzat és eljárások

**Azonosító:** NIST SP 800-53 R4 PE-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1445 – Fizikai és környezeti védelmi szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32d07d59-2716-4972-b37b-214a67ac4a37) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1445.json) |
|[Microsoft Managed Control 1446 – Fizikai és környezeti védelmi szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf6850fe-abba-468e-9ef4-d09ec7d983cd) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1446.json) |

### <a name="physical-access-authorizations"></a>Fizikai hozzáférés engedélyezése

**Azonosító:** NIST SP 800-53 R4 PE-2 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1447 – Fizikai hozzáférési engedélyek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9783a99-98fe-4a95-873f-29613309fe9a) |A Microsoft megvalósítja ezt a fizikai és környezeti védelmi szabályozást |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1447.json) |
|[Microsoft Managed Control 1448 – Fizikai hozzáférési engedélyek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F825d6494-e583-42f2-a3f2-6458e6f0004f) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1448.json) |
|[Microsoft Managed Control 1449 – Fizikai hozzáférési engedélyek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff784d3b0-5f2b-49b7-b9f3-00ba8653ced5) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1449.json) |
|[Microsoft Managed Control 1450 – Fizikai hozzáférési engedélyek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F134d7a13-ba3e-41e2-b236-91bfcfa24e01) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1450.json) |

### <a name="physical-access-control"></a>Fizikai Access Control

**Azonosító:** NIST SP 800-53 R4 PE-3 **Tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1451 – Fizikai Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3f1e5a3-25c1-4476-8cb6-3955031f8e65) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1451.json) |
|[Microsoft Managed Control 1452 – Fizikai Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82c76455-4d3f-4e09-a654-22e592107e74) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1452.json) |
|[Microsoft Managed Control 1453 – Fizikai Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9693b564-3008-42bc-9d5d-9c7fe198c011) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1453.json) |
|[Microsoft Managed Control 1454 – Fizikai Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad58985d-ab32-4f99-8bd3-b7e134c90229) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1454.json) |
|[Microsoft Managed Control 1455 – Fizikai Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068a88d4-e520-434e-baf0-9005a8164e6a) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1455.json) |
|[Microsoft Managed Control 1456 – Fizikai Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F733ba9e3-9e7c-440a-a7aa-6196a90a2870) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1456.json) |
|[Microsoft Managed Control 1457 – Fizikai Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2d9d3e6-8886-4305-865d-639163e5c305) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1457.json) |

### <a name="physical-access-control--information-system-access"></a>Fizikai Access Control | Információsrendszer-hozzáférés

**Azonosító:** NIST SP 800-53 R4 PE-3 (1) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1458 – Access Control \| Information System Access](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c19ceb7-56e9-4488-8ddb-b1eb3aa6d203) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1458.json) |

### <a name="access-control-for-transmission-medium"></a>Access Control átviteli adathordozóhoz

**Azonosító:** NIST SP 800-53 R4 PE-4 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1459 – Access Control for Transmission Medium](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75cc73c7-5cdb-479d-a06f-7b4d0dbb1da0) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1459.json) |

### <a name="access-control-for-output-devices"></a>Access Control eszközökre vonatkozó beállítások

**Azonosító:** NIST SP 800-53 R4 PE-5 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1460 – Access Control a kimeneti eszközökhöz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f3ce1bb-4f77-4695-8355-70b08d54fdda) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1460.json) |

### <a name="monitoring-physical-access"></a>Fizikai hozzáférés figyelése

**Azonosító:** NIST SP 800-53 R4 PE-6 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1461 – Fizikai hozzáférés figyelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faafef03e-fea8-470b-88fa-54bd1fcd7064) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1461.json) |
|[Microsoft Managed Control 1462 – Fizikai hozzáférés figyelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b1f3a9a-13a1-4b40-8420-36bca6fd8c02) |A Microsoft megvalósítja ezt a fizikai és környezeti védelmi szabályozást |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1462.json) |
|[Microsoft Managed Control 1463 – Fizikai hozzáférés figyelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F59721f87-ae25-4db0-a2a4-77cc5b25d495) |A Microsoft megvalósítja ezt a fizikai és környezeti védelmi szabályozást |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1463.json) |

### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Fizikai hozzáférés figyelése | Behatolási riasztások / térfigyelő berendezések

**Azonosító:** NIST SP 800-53 R4 PE-6 (1) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1464 – Fizikai hozzáférési behatolási \| riasztások és térfigyelő berendezések monitorozása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41256567-1795-4684-b00b-a1308ce43cac) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1464.json) |

### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Fizikai hozzáférés figyelése | Az információs rendszerekhez való fizikai hozzáférés figyelése

**Azonosító:** NIST SP 800-53 R4 PE-6 (4) **Tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1465 – Fizikai hozzáférés figyelése az információs rendszerekhez való \| fizikai hozzáférés figyelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6e41554-86b5-4537-9f7f-4fc41a1d1640) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1465.json) |

### <a name="visitor-access-records"></a>Látogatói hozzáférési rekordok

**Azonosító:** NIST SP 800-53 R4 PE-8 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1466 – Látogatói hozzáférési rekordok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d943a9c-a6f1-401f-a792-740cdb09c451) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1466.json) |
|[Microsoft Managed Control 1467 – Látogatói hozzáférési rekordok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5350cbf9-8bdd-4904-b22a-e88be84ca49d) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1467.json) |

### <a name="visitor-access-records--automated-records-maintenance--review"></a>Látogatói hozzáférési rekordok | Automatizált rekordok karbantartása /felülvizsgálata

**Azonosító:** NIST SP 800-53 R4 PE-8 (1) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1468 - Visitor Access Records \| Automated Records Maintenance / Review](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75603f96-80a1-4757-991d-5a1221765ddd) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1468.json) |

### <a name="power-equipment-and-cabling"></a>Energiaellátási berendezések és kábelek

**Azonosító:** NIST SP 800-53 R4 PE-9 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1469 – Energiaellátási berendezések és kábelek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff509c5b6-0de0-4a4e-9b2e-cd9cbf3a58fd) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1469.json) |

### <a name="emergency-shutoff"></a>Vészlejárat

**Azonosító:** NIST SP 800-53 R4 PE-10 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1470 – Vészlejárat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc89ba09f-2e0f-44d0-8095-65b05bd151ef) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1470.json) |
|[Microsoft Managed Control 1471 – Vészlejárat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7dd0e9ce-1772-41fb-a50a-99977071f916) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1471.json) |
|[Microsoft Managed Control 1472 – Vészlejárat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef869332-921d-4c28-9402-3be73e6e50c8) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1472.json) |

### <a name="emergency-power"></a>Vészhelyzeti energia

**Azonosító:** NIST SP 800-53 R4 PE-11 **Tulajdonos:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1473 – Vészhelyzeti energia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd7047705-d719-46a7-8bb0-76ad233eba71) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1473.json) |

### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Vészhelyzeti | Long-Term tápellátás – minimális működési képesség

**Azonosító:** NIST SP 800-53 R4 PE-11 (1) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1474 – Emergency Power \| Long-Term Alternate Power Supply – Minimal Operational Capability](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ad326e-d7a1-44b1-9a76-e17492efc9e4) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1474.json) |

### <a name="emergency-lighting"></a>Vészvilágítás

**Azonosító:** NIST SP 800-53 R4 PE-12 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1475 – Vészvilágítás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a63848-30cf-4081-937e-ce1a1c885501) |A Microsoft megvalósítja ezt a fizikai és környezeti védelmi szabályozást |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1475.json) |

### <a name="fire-protection"></a>Tűzvédelem

**Azonosító:** NIST SP 800-53 R4 PE-13 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1476 – Fire Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f3c4ac2-3e35-4906-a80b-473b12a622d7) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1476.json) |

### <a name="fire-protection--detection-devices--systems"></a>Fire Protection | Észlelési eszközök /rendszerek

**Azonosító:** NIST SP 800-53 R4 PE-13 (1) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1477 - Fire Protection \| Detection Devices / Systems](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4862a63c-6c74-4a9d-a221-89af3c374503) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1477.json) |

### <a name="fire-protection--suppression-devices--systems"></a>Fire Protection | Eszközök/rendszerek mellőzése

**Azonosító:** NIST SP 800-53 R4 PE-13 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1478 – Tűzvédelmet csökkentő \| eszközök /rendszerek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff997df46-cfbb-4cc8-aac8-3fecdaf6a183) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1478.json) |

### <a name="fire-protection--automatic-fire-suppression"></a>Fire Protection | Automatikus tűzelfojtás

**Azonosító:** NIST SP 800-53 R4 PE-13 (3) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1479 – Fire Protection \| Automatic Fire Suppression](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe327b072-281d-4f75-9c28-4216e5d72f26) |A Microsoft megvalósítja ezt a fizikai és környezeti védelmi szabályozást |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1479.json) |

### <a name="temperature-and-humidity-controls"></a>Hőmérséklet- és páratartalom-vezérlők

**Azonosító:** NIST SP 800-53 R4 PE-14 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1480 – Hőmérséklet- és páratartalom-vezérlők](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18a767cc-1947-4338-a240-bc058c81164f) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1480.json) |
|[Microsoft Managed Control 1481 – Hőmérséklet- és páratartalom-vezérlők](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F717a1c78-a267-4f56-ac58-ee6c54dc4339) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1481.json) |

### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Hőmérséklet- és páratartalom-vezérlők | Monitorozás riasztásokkal/értesítésekkel

**Azonosító:** NIST SP 800-53 R4 PE-14 (2) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1482 – Hőmérséklet- és páratartalom-vezérlők \| monitorozása riasztásokkal/értesítésekkel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9df4277e-8c88-4d5c-9b1a-541d53d15d7b) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1482.json) |

### <a name="water-damage-protection"></a>Vízsérülés elleni védelem

**Azonosító:** NIST SP 800-53 R4 PE-15 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1483 – Vízsérülés elleni védelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5cb81060-3c8a-4968-bcdc-395a1801f6c1) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1483.json) |

### <a name="water-damage-protection--automation-support"></a>Vízsérülés elleni védelem | Automation-támogatás

**Azonosító:** NIST SP 800-53 R4 PE-15 (1) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1484 – Vízsérülés elleni védelem \| automatizálásának támogatása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F486b006a-3653-45e8-b41c-a052d3e05456) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1484.json) |

### <a name="delivery-and-removal"></a>Kézbesítés és eltávolítás

**Azonosító:** NIST SP 800-53 R4 PE-16 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1485 – Kézbesítés és eltávolítás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50301354-95d0-4a11-8af5-8039ecf6d38b) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1485.json) |

### <a name="alternate-work-site"></a>Alternatív munkahelyi hely

**Azonosító:** NIST SP 800-53 R4 PE-17 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1486 – Alternatív munkahelyi webhely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb790345-a51f-43de-934e-98dbfaf9dca5) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1486.json) |
|[Microsoft Managed Control 1487 – Alternatív munkahelyi webhely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c3371d-c30c-4f58-abd9-30b8a8199571) |A Microsoft megvalósítja ezt a fizikai és környezeti védelmi szabályozást |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1487.json) |
|[Microsoft Managed Control 1488 – Alternatív munkahelyi webhely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8ef30eb-a44f-47af-8524-ac19a36d41d2) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1488.json) |

### <a name="location-of-information-system-components"></a>Az információs rendszer összetevőinek helye

**Azonosító:** NIST SP 800-53 R4 PE-18 **Ownership**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1489 – Az információs rendszer összetevőinek helye](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0a794f-1444-4c96-9534-e35fc8c39c91) |A Microsoft ezt a fizikai és környezeti védelmi ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1489.json) |

## <a name="planning"></a>Tervezés

### <a name="security-planning-policy-and-procedures"></a>Biztonsági tervezési házirend és eljárások

**Azonosító:** NIST SP 800-53 R4 PL-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1490 – Biztonsági tervezési szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e61da80-0957-4892-b70c-609d5eaafb6b) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1490.json) |
|[Microsoft Managed Control 1491 – Biztonsági tervezési szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1571dd40-dafc-4ef4-8f55-16eba27efc7b) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1491.json) |

### <a name="system-security-plan"></a>Rendszerbiztonsági terv

**Azonosító:** NIST SP 800-53 R4 PL-2 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1492 – Rendszerbiztonsági csomag](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ad5f307-e045-46f7-8214-5bdb7e973737) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1492.json) |
|[Microsoft Managed Control 1493 – Rendszerbiztonsági csomag](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22b469b3-fccf-42da-aa3b-a28e6fb113ce) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1493.json) |
|[Microsoft Managed Control 1494 – Rendszerbiztonsági csomag](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed09d84-3311-4853-8b67-2b55dfa33d09) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1494.json) |
|[Microsoft Managed Control 1495 – Rendszerbiztonsági csomag](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4978d0e-a596-48e7-9f8c-bbf52554ce8d) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1495.json) |
|[Microsoft Managed Control 1496 – Rendszerbiztonsági csomag](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ca96127-2f87-46ab-a4fc-0d2a786df1c8) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1496.json) |

### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>Rendszerbiztonsági terv | Tervezés/koordinálás más szervezeti entitásokkal

**Azonosító:** NIST SP 800-53 R4 PL-2 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1497 – Rendszerbiztonsági terv / Más szervezeti entitásokkal \| való együttműködés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e3c5583-1729-4d36-8771-59c32f090a22) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1497.json) |

### <a name="rules-of-behavior"></a>Viselkedési szabályok

**Azonosító:** NIST SP 800-53 R4 PL-4 ownership : **Shared**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1498 – Viselkedési szabályok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F633988b9-cf2f-4323-8394-f0d2af9cd6e1) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1498.json) |
|[Microsoft Managed Control 1499 – Viselkedési szabályok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe59671ab-9720-4ee2-9c60-170e8c82251e) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1499.json) |
|[Microsoft Managed Control 1500 – Viselkedési szabályok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9dd5b241-03cb-47d3-a5cd-4b89f9c53c92) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1500.json) |
|[Microsoft Managed Control 1501 – Viselkedési szabályok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88817b58-8472-4f6c-81fa-58ce42b67f51) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1501.json) |

### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>Viselkedési szabályok | Közösségimédia- és hálózatkorlátozások

**Azonosító:** NIST SP 800-53 R4 PL-4 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1502 – Viselkedési szabályok – \| közösségimédia- és hálózatkorlátozások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe901375c-8f01-4ac8-9183-d5312f47fe63) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1502.json) |

### <a name="information-security-architecture"></a>Információbiztonsági architektúra

**Azonosító:** NIST SP 800-53 R4 PL-8 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1503 – Information Security Architecture](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc1fa9c2f-d439-4ab9-8b83-81fb1934f81d) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1503.json) |
|[Microsoft Managed Control 1504 – Information Security Architecture](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e7c35d0-12d4-4e0c-80a2-8a352537aefd) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1504.json) |
|[Microsoft Managed Control 1505 – Information Security Architecture](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F813a10a7-3943-4fe3-8678-00dc52db5490) |A Microsoft megvalósítja ezt a tervezési vezérlőt |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1505.json) |

## <a name="personnel-security"></a>Személyzet biztonsága

### <a name="personnel-security-policy-and-procedures"></a>Személyzet biztonsági szabályzata és eljárásai

**Azonosító:** NIST SP 800-53 R4 PS-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1506 – Személyzet biztonsági szabályzata és eljárásai](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d2ff17-d604-4dd9-b607-9ecf63f28ad2) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1506.json) |
|[Microsoft Managed Control 1507 – Személyzet biztonsági szabályzata és eljárásai](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ccd1bf-e7ad-4851-93ce-6ec817469c1e) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1507.json) |

### <a name="position-risk-designation"></a>Pozíciókockázat megjelölése

**Azonosító:** NIST SP 800-53 R4 PS-2 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1508 – Pozíciókockázat megjelölése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76f500cc-4bca-4583-bda1-6d084dc21086) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1508.json) |
|[Microsoft Managed Control 1509 – Pozíciókockázat megjelölése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70792197-9bfc-4813-905a-bd33993e327f) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1509.json) |
|[Microsoft Managed Control 1510 – Pozíciókockázat megjelölése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79da5b09-0e7e-499e-adda-141b069c7998) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1510.json) |

### <a name="personnel-screening"></a>Személyzetszűrés

**Azonosító:** NIST SP 800-53 R4 PS-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1511 – Személyzetszűrés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9eae324-d327-4539-9293-b48e122465f8) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1511.json) |
|[Microsoft Managed Control 1512 – Személyzetszűrés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5a8324ad-f599-429b-aaed-f9c6e8c987a8) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1512.json) |

### <a name="personnel-screening--information-with-special-protection-measures"></a>Személyzetszűrés | Különleges védelmi intézkedésekre vonatkozó információk

**Azonosító:** NIST SP 800-53 R4 PS-3 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1513 – Személyzetszűrés adatai \| speciális védelmi intézkedésekkel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc416970d-b12b-49eb-8af4-fb144cd7c290) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1513.json) |
|[Microsoft Managed Control 1514 – Személyzetszűrés adatai \| speciális védelmi intézkedésekkel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed5ca00-0e43-434e-a018-7aab91461ba7) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1514.json) |

### <a name="personnel-termination"></a>Személyzet megszakítása

**Azonosító:** NIST SP 800-53 R4 PS-4 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1515 – Személyzet megszakítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02dd141a-a2b2-49a7-bcbd-ca31142f6211) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1515.json) |
|[Microsoft Managed Control 1516 – Személyzet megszakítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3cd269-156f-435b-b472-c3af34c032ed) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1516.json) |
|[Microsoft Managed Control 1517 – Személyzet megszakítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8f5ad423-50d6-4617-b058-69908f5586c9) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1517.json) |
|[Microsoft Managed Control 1518 – Személyzet megszakítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d58f734-c052-40e9-8b2f-a1c2bff0b815) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1518.json) |
|[Microsoft Managed Control 1519 – Személyzet megszakítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f13915a-324c-4ab8-b45c-2eefeeefb098) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1519.json) |
|[Microsoft Managed Control 1520 – Személyzet megszakítása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f2c513b-eb16-463b-b469-c10e5fa94f0a) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1520.json) |

### <a name="personnel-termination--automated-notification"></a>Személyzet megszakításának | Automatikus értesítés

**Azonosító:** NIST SP 800-53 R4 PS-4 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1521 – Személyzet megszüntetésével kapcsolatos \| automatizált értesítés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cbddf9c-a3aa-4330-a0f5-4c0c1f1862e5) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1521.json) |

### <a name="personnel-transfer"></a>Személyzet átadása

**Azonosító:** NIST SP 800-53 R4 PS-5 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1522 – Személyzet átadása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38b470cc-f939-4a15-80e0-9f0c74f2e2c9) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1522.json) |
|[Microsoft Managed Control 1523 – Személyzet átadása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5577a310-2551-49c8-803b-36e0d5e55601) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1523.json) |
|[Microsoft Managed Control 1524 – Személyzet átadása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72f1cb4e-2439-4fe8-88ea-b8671ce3c268) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1524.json) |
|[Microsoft Managed Control 1525 – Személyzet átadása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9be2f688-7a61-45e3-8230-e1ec93893f66) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1525.json) |

### <a name="access-agreements"></a>Hozzáférési szerződések

**Id**: NIST SP 800-53 R4 PS-6 **Ownership**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1526 – Hozzáférési szerződések](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F953e6261-a05a-44fd-8246-000e1a3edbb9) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1526.json) |
|[Microsoft Managed Control 1527 – Hozzáférési szerződések](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2823de66-332f-4bfd-94a3-3eb036cd3b67) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1527.json) |
|[Microsoft Managed Control 1528 – Hozzáférési szerződések](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdeb9797c-22f8-40e8-b342-a84003c924e6) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1528.json) |

### <a name="third-party-personnel-security"></a>Külső személyzet biztonsága

**Azonosító:** NIST SP 800-53 R4 PS-7 **Tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1529 – Külső személyzet biztonsága](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd74fdc92-1cb8-4a34-9978-8556425cd14c) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1529.json) |
|[Microsoft Managed Control 1530 – Külső személyzet biztonsága](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e8f9566-29f1-49cd-b61f-f8628a3cf993) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1530.json) |
|[Microsoft Managed Control 1531 – Külső személyzet biztonsága](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0643e0c-eee5-4113-8684-c608d05c5236) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1531.json) |
|[Microsoft Managed Control 1532 – Külső személyzet biztonsága](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2c66299-9017-4d95-8040-8bdbf7901d52) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1532.json) |
|[Microsoft Managed Control 1533 – Külső személyzet biztonsága](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbba2a036-fb3b-4261-b1be-a13dfb5fbcaa) |A Microsoft ezt a személyzet biztonsági szabályozását valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1533.json) |

### <a name="personnel-sanctions"></a>Személyzetre vonatkozó meglökkent

**Azonosító:** NIST SP 800-53 R4 PS-8 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Microsoft által felügyelt felügyelet 1534 – Személyzetre vonatkozó operációs rendszer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b2b263e-cd05-4488-bcbf-4debec7a17d9) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1534.json) |
|[Microsoft Managed Control 1535 – Személyzetre vonatkozó megszava](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9a165d2-967d-4733-8399-1074270dae2e) |A Microsoft ezt a személyzet biztonsági felügyeletét valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1535.json) |

## <a name="risk-assessment"></a>Kockázatértékelés

### <a name="risk-assessment-policy-and-procedures"></a>Kockázatfelmérési szabályzat és eljárások

**Azonosító:** NIST SP 800-53 R4 RA-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1536 – Kockázatfelmérési szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e40d9de-2ad4-4cb5-8945-23143326a502) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1536.json) |
|[Microsoft Managed Control 1537 – Kockázatfelmérési szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb19454ca-0d70-42c0-acf5-ea1c1e5726d1) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1537.json) |

### <a name="security-categorization"></a>Biztonsági kategorizálás

**Azonosító:** NIST SP 800-53 R4 RA-2 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1538 – Biztonsági kategorizálás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d7658b2-e827-49c3-a2ae-6d2bd0b45874) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1538.json) |
|[Microsoft Managed Control 1539 – Biztonsági kategorizálás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faabb155f-e7a5-4896-a767-e918bfae2ee0) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1539.json) |
|[Microsoft Managed Control 1540 – Biztonsági kategorizálás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff771f8cb-6642-45cc-9a15-8a41cd5c6977) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1540.json) |

### <a name="risk-assessment"></a>Kockázatértékelés

**Azonosító:** NIST SP 800-53 R4 RA-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1541 – Kockázatfelmérés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70f6af82-7be6-44aa-9b15-8b9231b2e434) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1541.json) |
|[Microsoft Managed Control 1542 – Kockázatfelmérés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feab340d0-3d55-4826-a0e5-feebfeb0131d) |A Microsoft ezt a kockázatfelmérési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1542.json) |
|[Microsoft Managed Control 1543 – Kockázatfelmérés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd00b778-b5b5-49c0-a994-734ea7bd3624) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1543.json) |
|[Microsoft Managed Control 1544 – Kockázatfelmérés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43ced7c9-cd53-456b-b0da-2522649a4271) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1544.json) |
|[Microsoft Managed Control 1545 – Kockázatfelmérés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f4b171a-a56b-4328-8112-32cf7f947ee1) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1545.json) |

### <a name="vulnerability-scanning"></a>Biztonsági rések vizsgálata

**Azonosító:** NIST SP 800-53 R4 RA-5 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell egy sebezhetőségi felmérési megoldást a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |A virtuális gépek naplózásával észleli, hogy támogatott sebezhetőségi felmérési megoldást futtatnak-e. Minden kiberkockázati és biztonsági program egyik fő összetevője a biztonsági rések azonosítása és elemzése. Azure Security Center standard tarifacsomagja többletköltség nélkül tartalmazza a biztonsági rések keresését a virtuális gépeken. Emellett a Security Center automatikusan üzembe helyezheti ezt az eszközt. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[A speciális adatbiztonságot engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Minden adat SQL Managed Instance adatbiztonság nélkül naplól. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Engedélyezni kell a speciális adatbiztonságot az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |SQL-kiszolgálók naplózása Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Microsoft Managed Control 1546 – Biztonsági rések vizsgálata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce1ea7e-4038-4e53-82f4-63e8859333c1) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1546.json) |
|[Microsoft Managed Control 1547 – Biztonsági rések vizsgálata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58abf9b8-c6d4-4b4b-bfb9-fe98fe295f52) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1547.json) |
|[Microsoft Managed Control 1548 – Biztonsági rések vizsgálata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3afe6c78-6124-4d95-b85c-eb8c0c9539cb) |A Microsoft ezt a kockázatfelmérési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1548.json) |
|[Microsoft Managed Control 1549 – Biztonsági rések vizsgálata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd6976a08-d969-4df2-bb38-29556c2eb48a) |A Microsoft ezt a kockázatfelmérési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1549.json) |
|[Microsoft Managed Control 1550 – Biztonsági rések vizsgálata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F902908fb-25a8-4225-a3a5-5603c80066c9) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1550.json) |
|[A gépek biztonsági konfigurációjának biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azok a kiszolgálók, amelyek nem tesznek eleget a konfigurált alapkonfigurációnak, a Azure Security Center figyelik. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[A virtuálisgép-méretezési készletek biztonsági konfigurációjának biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |A virtuálisgép-méretezési készletek operációsrendszer-biztonsági réseit naplózva megvédheti őket a támadásokkal szemben. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Az SQL-adatbázisok biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Figyelje a sebezhetőségi felmérés vizsgálatának eredményeit és az adatbázis biztonsági réseit javító javaslatokat. |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning--update-tool-capability"></a>Biztonsági rések | Frissítési eszköz képessége

**Azonosító:** NIST SP 800-53 R4 RA-5 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1551 – Biztonsági rések vizsgálata \| frissítési eszköz képessége](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bbda922-0172-4095-89e6-5b4a0bf03af7) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1551.json) |

### <a name="vulnerability-scanning--update-by-frequency--prior-to-new-scan--when-identified"></a>Biztonsági rések | Frissítés gyakoriság szerint / Új vizsgálat előtt / Ha azonosították

**Azonosító:** NIST SP 800-53 R4 RA-5 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1552 – Biztonsági rések vizsgálatának frissítése gyakoriság szerint /új vizsgálat előtt \| / Ha azonosították](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43684572-e4f1-4642-af35-6b933bc506da) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1552.json) |

### <a name="vulnerability-scanning--breadth--depth-of-coverage"></a>Biztonsági rések | Breadth /Depth of Coverage (Szélessége/ Lefedettség mélysége)

**Azonosító:** NIST SP 800-53 R4 RA-5 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1553 – Biztonsági rések \| vizsgálatának szélessége /mélysége](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e5225fe-cdfb-4fce-9aec-0fe20dd53b62) |A Microsoft ezt a kockázatfelmérési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1553.json) |

### <a name="vulnerability-scanning--discoverable-information"></a>Biztonsági rések | Felderíthető információk

**Azonosító:** NIST SP 800-53 R4 RA-5 (4) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1554 – Sebezhetőségi vizsgálat \| felderíthető adatai](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10984b4e-c93e-48d7-bf20-9c03b04e9eca) |A Microsoft ezt a kockázatfelmérési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1554.json) |

### <a name="vulnerability-scanning--privileged-access"></a>Biztonsági rések | Emelt szintű hozzáférés

**Azonosító:** NIST SP 800-53 R4 RA-5 (5) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1555 – Biztonsági rések vizsgálata emelt szintű \| hozzáféréssel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5afa8cab-1ed7-4e40-884c-64e0ac2059cc) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1555.json) |

### <a name="vulnerability-scanning--automated-trend-analyses"></a>Biztonsági rések | Automatizált trendelemzések

**Azonosító:** NIST SP 800-53 R4 RA-5 (6) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1556 – Biztonsági rések \| vizsgálatának automatizált trendelemzése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391ff8b3-afed-405e-9f7d-ef2f8168d5da) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1556.json) |

### <a name="vulnerability-scanning--review-historic-audit-logs"></a>Biztonsági rések | Korábbi auditnaplók áttekintése

**Azonosító:** NIST SP 800-53 R4 RA-5 (8) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1557 – Biztonsági rések vizsgálata \| – Korábbi auditnaplók áttekintése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36fbe499-f2f2-41b6-880e-52d7ea1d94a5) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1557.json) |

### <a name="vulnerability-scanning--correlate-scanning-information"></a>Biztonsági rések | Korrelálhatja a vizsgálattal kapcsolatos információkat

**Azonosító:** NIST SP 800-53 R4 RA-5 (10) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1558 – A biztonsági rések vizsgálatának \| korrelálható ellenőrzési információi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65592b16-4367-42c5-a26e-d371be450e17) |A Microsoft ezt a kockázatfelmérési ellenőrzést implementálja |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1558.json) |

## <a name="system-and-services-acquisition"></a>Rendszer- és szolgáltatásbeszerzés

### <a name="system-and-services-acquisition-policy-and-procedures"></a>Rendszer- és szolgáltatásbeszerzési szabályzat és eljárások

**Azonosító:** NIST SP 800-53 R4 SA-1 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1559 – Rendszer- és szolgáltatásbeszerzési szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45692294-f074-42bd-ac54-16f1a3c07554) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1559.json) |
|[Microsoft Managed Control 1560 – Rendszer- és szolgáltatásbeszerzési szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe29e0915-5c2f-4d09-8806-048b749ad763) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1560.json) |

### <a name="allocation-of-resources"></a>Erőforrások kiosztása

**Azonosító:** NIST SP 800-53 R4 SA-2 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1561 – Erőforrások kiosztása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40364c3f-c331-4e29-b1e3-2fbe998ba2f5) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1561.json) |
|[Microsoft Managed Control 1562 – Erőforrások kiosztása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4142013-7964-4163-a313-a900301c2cef) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1562.json) |
|[Microsoft Managed Control 1563 – Erőforrások kiosztása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9afe2edf-232c-4fdf-8e6a-e867a5c525fd) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1563.json) |

### <a name="system-development-life-cycle"></a>Rendszerfejlesztési életciklus

**Azonosító:** NIST SP 800-53 R4 SA-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1564 – Rendszerfejlesztési életciklus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F157f0ef9-143f-496d-b8f9-f8c8eeaad801) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1564.json) |
|[Microsoft Managed Control 1565 – Rendszerfejlesztési életciklus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45ce2396-5c76-4654-9737-f8792ab3d26b) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1565.json) |
|[Microsoft Managed Control 1566 – Rendszerfejlesztési életciklus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50ad3724-e2ac-4716-afcc-d8eabd97adb9) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1566.json) |
|[Microsoft Managed Control 1567 – Rendszerfejlesztési életciklus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe72edbf6-aa61-436d-a227-0f32b77194b3) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1567.json) |

### <a name="acquisition-process"></a>Beszerzési folyamat

**Azonosító:** NIST SP 800-53 R4 SA-4 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1568 – Beszerzési folyamat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8eae8-9854-495a-ac82-d2cd3eac02a6) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1568.json) |
|[Microsoft Managed Control 1569 – Beszerzési folyamat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad2f8e61-a564-4dfd-8eaa-816f5be8cb34) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1569.json) |
|[Microsoft Managed Control 1570 – Beszerzési folyamat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7fcf38d-bb09-4600-be7d-825046eb162a) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1570.json) |
|[Microsoft Managed Control 1571 – Beszerzési folyamat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb11c985b-f2cd-4bd7-85f4-b52426edf905) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1571.json) |
|[Microsoft Managed Control 1572 – Beszerzési folyamat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04f5fb00-80bb-48a9-a75b-4cb4d4c97c36) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1572.json) |
|[Microsoft Managed Control 1573 – Beszerzési folyamat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58c93053-7b98-4cf0-b99f-1beb985416c2) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1573.json) |
|[Microsoft Managed Control 1574 – Beszerzési folyamat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f935dab-83d6-47b8-85ef-68b8584161b9) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1574.json) |

### <a name="acquisition-process--functional-properties-of-security-controls"></a>Beszerzési folyamat | A biztonsági vezérlők funkcionális tulajdonságai

**Azonosító:** NIST SP 800-53 R4 SA-4 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1575 – A biztonsági vezérlők beszerzési \| folyamatának funkcionális tulajdonságai](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e1bb73-1b08-4dbe-9c62-8e2e92e7ec41) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1575.json) |

### <a name="acquisition-process--design--implementation-information-for-security-controls"></a>Beszerzési folyamat | A biztonsági vezérlők tervezési és megvalósítási információi

**Azonosító:** NIST SP 800-53 R4 SA-4 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1576 – Beszerzési folyamat tervezése / A \| biztonsági vezérlők implementációval kapcsolatos információi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f18c885-ade3-48c5-80b1-8f9216019c18) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1576.json) |

### <a name="acquisition-process--continuous-monitoring-plan"></a>Beszerzési folyamat | Folyamatos monitorozási terv

**Azonosító:** NIST SP 800-53 R4 SA-4 (8) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1577 – Beszerzési folyamat \| folyamatos monitorozási terve](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd922484a-8cfc-4a6b-95a4-77d6a685407f) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1577.json) |

### <a name="acquisition-process--functions--ports--protocols--services-in-use"></a>Beszerzési folyamat | Függvények / portok / protokollok / Használatban található szolgáltatások

**Azonosító:** NIST SP 800-53 R4 SA-4 (9) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1578 – Beszerzési folyamat funkciói \| / Portok / Protokollok / Használatban található szolgáltatások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45b7b644-5f91-498e-9d89-7402532d3645) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1578.json) |

### <a name="acquisition-process--use-of-approved-piv-products"></a>Beszerzési folyamat | Jóváhagyott Piv-termékek használata

**Azonosító:** NIST SP 800-53 R4 SA-4 (10) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1579 – Jóváhagyott Piv-termékek beszerzési \| folyamatának használata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e54c7ef-7457-430b-9a3e-ef8881d4a8e0) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1579.json) |

### <a name="information-system-documentation"></a>Információs rendszer dokumentációja

**Azonosító:** NIST SP 800-53 R4 SA-5 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Microsoft Managed Control 1580 – Információs rendszer dokumentációja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F854db8ac-6adf-42a0-bef3-b73f764f40b9) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1580.json) |
|[A Microsoft Managed Control 1581 – Információs rendszer dokumentációja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F742b549b-7a25-465f-b83c-ea1ffb4f4e0e) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1581.json) |
|[A Microsoft Managed Control 1582 – Információs rendszer dokumentációja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd9e2f38-259b-462c-bfad-0ad7ab4e65c5) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1582.json) |
|[A Microsoft Managed Control 1583 – Információs rendszer dokumentációja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0882d488-8e80-4466-bc0f-0cd15b6cb66d) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1583.json) |
|[A Microsoft Managed Control 1584 – Információs rendszer dokumentációja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5864522b-ff1d-4979-a9f8-58bee1fb174c) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1584.json) |

### <a name="security-engineering-principles"></a>Biztonsági mérnöki alapelvek

**Azonosító:** NIST SP 800-53 R4 SA-8 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1585 – Biztonsági mérnöki alapelvek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd57f8732-5cdc-4cda-8d27-ab148e1f3a55) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1585.json) |

### <a name="external-information-system-services"></a>Külső információs rendszer szolgáltatásai

**Azonosító:** NIST SP 800-53 R4 SA-9 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1586 – External Information System Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e3b2fbd-8f37-4766-a64d-3f37703dcb51) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1586.json) |
|[Microsoft Managed Control 1587 – External Information System Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32820956-9c6d-4376-934c-05cd8525be7c) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1587.json) |
|[Microsoft Managed Control 1588 – External Information System Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68ebae26-e0e0-4ecb-8379-aabf633b51e9) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1588.json) |

### <a name="external-information-system-services--risk-assessments--organizational-approvals"></a>External Information System Services | Kockázatértékelések / Szervezeti jóváhagyások

**Azonosító:** NIST SP 800-53 R4 SA-9 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1589 – External Information System Services \| Risk Assessments / Szervezeti jóváhagyások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ec7f9b-9478-40ff-8cfd-6a0d510081a8) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1589.json) |
|[Microsoft Managed Control 1590 - External Information System Services \| Risk Assessments / Organizational Approvals](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf296b8c-f391-4ea4-9198-be3c9d39dd1f) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1590.json) |

### <a name="external-information-system-services--identification-of-functions--ports--protocols--services"></a>External Information System Services | Függvények / portok / protokollok / szolgáltatások azonosítása

**Azonosító:** NIST SP 800-53 R4 SA-9 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1591 – External Information System Services \| Ident. Függvények / portok / protokollok / szolgáltatások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff751cdb7-fbee-406b-969b-815d367cb9b3) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1591.json) |

### <a name="external-information-system-services--consistent-interests-of-consumers-and-providers"></a>External Information System Services | A fogyasztók és szolgáltatók konzisztens érdeklődési körök

**Azonosító:** NIST SP 800-53 R4 SA-9 (4) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1592 - External Information System Services \| Consistent Interests Of Consumers And Providers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d01ba6c-289f-42fd-a408-494b355b6222) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1592.json) |

### <a name="external-information-system-services--processing-storage-and-service-location"></a>External Information System Services | Feldolgozás, tárolás és szolgáltatás helye

**Azonosító:** NIST SP 800-53 R4 SA-9 (5) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1593 – External Information System Services \| Processing, Storage és Service Location](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cd0a426-b5f5-4fe0-9539-a6043cdbc6fa) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1593.json) |

### <a name="developer-configuration-management"></a>Fejlesztői konfigurációkezelés

**Azonosító:** NIST SP 800-53 R4 SA-10 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1594 – Fejlesztői konfigurációkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F042ba2a1-8bb8-45f4-b080-c78cf62b90e9) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1594.json) |
|[Microsoft Managed Control 1595 – Fejlesztői konfigurációkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e0414e7-6ef5-4182-8076-aa82fbb53341) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1595.json) |
|[Microsoft Managed Control 1596 – Fejlesztői konfigurációkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21e25e01-0ae0-41be-919e-04ce92b8e8b8) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1596.json) |
|[Microsoft Managed Control 1597 – Fejlesztői konfigurációkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68b250ec-2e4f-4eee-898a-117a9fda7016) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1597.json) |
|[Microsoft Managed Control 1598 – Fejlesztői konfigurációkezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae7e1f5e-2d63-4b38-91ef-bce14151cce3) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1598.json) |

### <a name="developer-configuration-management--software--firmware-integrity-verification"></a>Fejlesztői konfigurációkezelési | Szoftver- és belsővezérlőprogram-integritás ellenőrzése

**Azonosító:** NIST SP 800-53 R4 SA-10 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1599 – Fejlesztői konfigurációkezelési \| szoftver / Belsővezérlőprogram-integritás ellenőrzése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0004bbf0-5099-4179-869e-e9ffe5fb0945) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1599.json) |

### <a name="developer-security-testing-and-evaluation"></a>Fejlesztői biztonsági tesztelés és értékelés

**Azonosító:** NIST SP 800-53 R4 SA-11 **tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1600 – Fejlesztői biztonsági tesztelés és értékelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc53f3123-d233-44a7-930b-f40d3bfeb7d6) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1600.json) |
|[Microsoft Managed Control 1601 – Fejlesztői biztonsági tesztelés és értékelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ee79a0c-addf-4ce9-9b3c-d9576ed5e20e) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1601.json) |
|[Microsoft Managed Control 1602 – Fejlesztői biztonsági tesztelés és értékelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fddae2e97-a449-499f-a1c8-aea4a7e52ec9) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1602.json) |
|[Microsoft Managed Control 1603 – Fejlesztői biztonsági tesztelés és értékelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b909c26-162f-47ce-8e15-0c1f55632eac) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1603.json) |
|[Microsoft Managed Control 1604 – Fejlesztői biztonsági tesztelés és értékelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44dbba23-0b61-478e-89c7-b3084667782f) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1604.json) |

### <a name="developer-security-testing-and-evaluation--static-code-analysis"></a>Fejlesztői biztonsági tesztelési és kiértékelési | Statikus kódelemzés

**Azonosító:** NIST SP 800-53 R4 SA-11 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1605 – Fejlesztői biztonsági tesztelés és értékelés \| statikus kódelemzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0062eb8b-dc75-4718-8ea5-9bb4a9606655) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1605.json) |

### <a name="developer-security-testing-and-evaluation--threat-and-vulnerability-analyses"></a>Fejlesztői biztonsági tesztelési és kiértékelési | Fenyegetés- és sebezhetőségi elemzések

**Azonosító:** NIST SP 800-53 R4 SA-11 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1606 – Fejlesztői biztonsági tesztelés és értékelés \| fenyegetésekkel és biztonsági résekkel kapcsolatos elemzések](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaa8a9a4-5bbe-4c72-98f6-a3a47ae2b1ca) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1606.json) |

### <a name="developer-security-testing-and-evaluation--dynamic-code-analysis"></a>Fejlesztői biztonsági tesztelési és kiértékelési | Dinamikus kódelemzés

**Azonosító:** NIST SP 800-53 R4 SA-11 (8) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1607 – Fejlesztői biztonsági tesztelés és \| kiértékelés dinamikus kódelemzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F976a74cf-b192-4d35-8cab-2068f272addb) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1607.json) |

### <a name="supply-chain-protection"></a>Ellátási lánc védelme

**Azonosító:** NIST SP 800-53 R4 SA-12 Ownership : **Shared**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1608 – Ellátási lánc védelme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb73b7b3b-677c-4a2a-b949-ad4dc4acd89f) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1608.json) |

### <a name="development-process-standards-and-tools"></a>Fejlesztési folyamat, szabványok és eszközök

**Azonosító:** NIST SP 800-53 R4 SA-15 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1609 – Fejlesztési folyamat, szabványok és eszközök](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e93fa71-42ac-41a7-b177-efbfdc53c69f) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1609.json) |
|[Microsoft Managed Control 1610 – Fejlesztési folyamat, szabványok és eszközök](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9f3fb54-4222-46a1-a308-4874061f8491) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1610.json) |

### <a name="developer-provided-training"></a>Developer-Provided betanítás

**Azonosító:** NIST SP 800-53 R4 SA-16 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1611 – Developer-Provided Training](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdda8a0c-ac32-43f6-b2f4-7dc1df03f43f) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1611.json) |

### <a name="developer-security-architecture-and-design"></a>Fejlesztői biztonsági architektúra és kialakítás

**Azonosító:** NIST SP 800-53 R4 SA-17 **Tulajdonos: Megosztott**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1612 – Fejlesztői biztonsági architektúra és kialakítás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2037b3d-8b04-4171-8610-e6d4f1d08db5) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1612.json) |
|[Microsoft Managed Control 1613 – Fejlesztői biztonsági architektúra és kialakítás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe2ad78b-8748-4bff-a924-f74dfca93f30) |A Microsoft megvalósítja ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1613.json) |
|[Microsoft Managed Control 1614 – Fejlesztői biztonsági architektúra és kialakítás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8154e3b3-cc52-40be-9407-7756581d71f6) |A Microsoft ezt a rendszer- és szolgáltatásbeszerzési ellenőrzést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1614.json) |

## <a name="system-and-communications-protection"></a>Rendszer- és kommunikációvédelem

### <a name="system-and-communications-protection-policy-and-procedures"></a>Rendszer- és kommunikációvédelmi házirend és eljárások

**Azonosító:** NIST SP 800-53 R4 SC-1 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1615 – Rendszer- és kommunikációvédelmi szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff35e02aa-0a55-49f8-8811-8abfa7e6f2c0) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1615.json) |
|[Microsoft Managed Control 1616 – Rendszer- és kommunikációvédelmi szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2006457a-48b3-4f7b-8d2e-1532287f9929) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1616.json) |

### <a name="application-partitioning"></a>Alkalmazás particionálása

**Azonosító:** NIST SP 800-53 R4 SC-2 **Ownership**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1617 – Alkalmazás particionálás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa631d8f5-eb81-4f9d-9ee1-74431371e4a3) |A Microsoft megvalósítja ezt a Rendszer- és Communications Protection-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1617.json) |

### <a name="security-function-isolation"></a>Biztonsági függvények elkülönítése

**Azonosító:** NIST SP 800-53 R4 SC-3 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1618 – Biztonsági funkciók elkülönítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff52f89aa-4489-4ec4-950e-8c96a036baa9) |A Microsoft megvalósítja ezt a Rendszer- és Communications Protection-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1618.json) |

### <a name="information-in-shared-resources"></a>Információk a megosztott erőforrásokban

**Azonosító:** NIST SP 800-53 R4 SC-4 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1619 – Információk a megosztott erőforrásokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc722e569-cb52-45f3-a643-836547d016e1) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1619.json) |

### <a name="denial-of-service-protection"></a>Szolgáltatásmegtagadásos védelem

**Azonosító:** NIST SP 800-53 R4 SC-5 Ownership : **Shared**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure DDoS Protection Standard beállítást engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |A DDoS Protection-szabványt minden olyan virtuális hálózaton engedélyezni kell, amely egy nyilvános IP-címmel rendelkező Application Gateway részét képezi. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|[Microsoft Managed Control 1620 – Szolgáltatásmegtagadásos védelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd17c826b-1dec-43e1-a984-7b71c446649c) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1620.json) |

### <a name="resource-availability"></a>Erőforrás rendelkezésre állása

**Azonosító:** NIST SP 800-53 R4 SC-6 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1621 – Erőforrás rendelkezésre állása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cb9f731-744a-4691-a481-ca77b0411538) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1621.json) |

### <a name="boundary-protection"></a>Boundary Protection

**Azonosító:** NIST SP 800-53 R4 SC-7 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptív hálózatkorrekozási javaslatokat kell alkalmazni az internetre néző virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és javaslatokat tesz a hálózati biztonsági csoport szabályára vonatkozóan, amelyek csökkentik a potenciális támadási felületet |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Minden hálózati portot korlátozni kell a virtuális géphez társított hálózati biztonsági csoportokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center azonosította, hogy a hálózati biztonsági csoportok egyes bejövő szabályai túl megengedőek. A bejövő szabályok nem engedélyezhetik az "Any" vagy "Internet" tartományokból való hozzáférést. Ez lehetővé teheti, hogy a támadók megcélják az erőforrásokat. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Microsoft Managed Control 1622 – Boundary Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fecf56554-164d-499a-8d00-206b07c27bed) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1622.json) |
|[Microsoft Managed Control 1623 – Boundary Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02ce1b22-412a-4528-8630-c42146f917ed) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1623.json) |
|[Microsoft Managed Control 1624 – Boundary Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37d079e3-d6aa-4263-a069-dd7ac6dd9684) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1624.json) |
|[A tárfióknak korlátoznia kell a hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |A tárfiókok hálózati hozzáférését korlátozni kell. Konfigurálja úgy a hálózati szabályokat, hogy csak az engedélyezett hálózatokról származó alkalmazások férnek hozzá a tárfiókhoz. Az adott internetről vagy helyszíni ügyfelekről származó kapcsolatok lehetővé teszik a hozzáférést adott Azure-beli virtuális hálózatokról származó forgalomhoz vagy nyilvános internetes IP-címtartományok felé |Naplózás, Megtagadás, Letiltva |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="boundary-protection--access-points"></a>Boundary Protection-| Hozzáférési pontok

**Azonosító:** NIST SP 800-53 R4 SC-7 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjainak időben elérhető hálózati hozzáférés-vezérléssel kell védeni őket](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A lehetséges igény szerinti hálózati (JIT)-hozzáférést a Azure Security Center figyeli javaslatként |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft Managed Control 1625 – Boundary Protection \| hozzáférési pontok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9b66a4d-70a1-4b47-8fa1-289cec68c605) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1625.json) |

### <a name="boundary-protection--external-telecommunications-services"></a>Boundary Protection | Külső távközlési szolgáltatások

**Azonosító:** NIST SP 800-53 R4 SC-7 (4) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A virtuális gépek felügyeleti portjainak a hálózat hozzáférés-vezérlésével kell védeni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |A hálózati igény szerinti (JIT) hozzáféréseket a Azure Security Center követik |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft Managed Control 1626 – Boundary Protection \| External Telecommunications Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8f6bddd-6d67-439a-88d4-c5fe39a79341) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1626.json) |
|[Microsoft Managed Control 1627 – Boundary Protection \| External Telecommunications Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd73310d-76fc-422d-bda4-3a077149f179) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1627.json) |
|[Microsoft Managed Control 1628 – Boundary Protection \| External Telecommunications Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67de62b4-a737-4781-8861-3baed3c35069) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1628.json) |
|[Microsoft Managed Control 1629 – Boundary Protection \| External Telecommunications Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc171b095-7756-41de-8644-a062a96043f2) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1629.json) |
|[Microsoft Managed Control 1630 – Boundary Protection \| External Telecommunications Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3643717a-3897-4bfd-8530-c7c96b26b2a0) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1630.json) |

### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Boundary Protection-| Megtagadás alapértelmezés szerint / Kivétel szerinti engedélyezése

**Azonosító:** NIST SP 800-53 R4 SC-7 (5) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1631 – Boundary \| Protection Deny by Default /Allow by Exception](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74ae9b8e-e7bb-4c9c-992f-c535282f7a2c) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1631.json) |

### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Boundary Protection-| Távoli eszközök felosztásos bújtatásának megakadályozása

**Azonosító:** NIST SP 800-53 R4 SC-7 (7) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1632 - Boundary Protection \| Prevent Split Tunneling For Remote Devices](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ce9073a-77fa-48f0-96b1-87aa8e6091c2) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1632.json) |

### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Boundary Protection | Forgalom útválasztása hitelesített proxykiszolgálókra

**Azonosító:** NIST SP 800-53 R4 SC-7 (8) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1633 – Boundary Protection \| Forgalomirányítás hitelesített proxykiszolgálókra](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F07557aa0-e02f-4460-9a81-8ecd2fed601a) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1633.json) |

### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Boundary Protection | Jogosulatlan kiszivárgás megakadályozása

**Azonosító:** NIST SP 800-53 R4 SC-7 (10) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1634 – Boundary Protection – Jogosulatlan \| kiszivárgás megakadályozása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F292a7c44-37fa-4c68-af7c-9d836955ded2) |A Microsoft megvalósítja ezt a Rendszer- és Communications Protection-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1634.json) |

### <a name="boundary-protection--host-based-protection"></a>Boundary Protection | Host-Based Protection

**Azonosító:** NIST SP 800-53 R4 SC-7 (12) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1635 – Boundary Protection \| Host-Based Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87551b5d-1deb-4d0f-86cc-9dc14cb4bf7e) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1635.json) |

### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Boundary Protection-| Biztonsági eszközök /mechanizmusok/ támogatási összetevők elkülönítése

**Azonosító:** NIST SP 800-53 R4 SC-7 (13) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1636 – Biztonsági \| eszközök/mechanizmusok/támogatási összetevők határvédelem-elkülönítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7b694eed-7081-43c6-867c-41c76c961043) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1636.json) |

### <a name="boundary-protection--fail-secure"></a>Boundary Protection-| Biztonságos feladatátvétel

**Azonosító:** NIST SP 800-53 R4 SC-7 (18) **Tulajdonjog:** Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1637 – Boundary Protection \| Fail Secure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4075bedc-c62a-4635-bede-a01be89807f3) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1637.json) |

### <a name="boundary-protection--dynamic-isolation--segregation"></a>Boundary Protection | Dinamikus elkülönítés / Elkülönítés

**Azonosító:** NIST SP 800-53 R4 SC-7 (20) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1638 – Boundary Protection \| Dynamic Isolation / Segregation](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49b99653-32cd-405d-a135-e7d60a9aae1f) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1638.json) |

### <a name="boundary-protection--isolation-of-information-system-components"></a>Boundary Protection | Az információsrendszer-összetevők elkülönítése

**Azonosító:** NIST SP 800-53 R4 SC-7 (21) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1639 – Az \| információsrendszer-összetevők határvédelem-elkülönítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78e8e649-50f6-4fe3-99ac-fedc2e63b03f) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1639.json) |

### <a name="transmission-confidentiality-and-integrity"></a>Átvitel titkosságának és integritásának biztosítása

**Azonosító:** NIST SP 800-53 R4 SC-8 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1640 – Átviteli titkosság és integritás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a289ce-6a20-4b75-a0f3-dc8601b6acd0) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1640.json) |

### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Átviteli titkosság és integritás | Kriptográfia vagy alternatív fizikai védelem

**Azonosító:** NIST SP 800-53 R4 SC-8 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Az API-alkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[A függvényalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Microsoft Managed Control 1641 - Transmission Confidentiality And Integrity \| Cryptographic Or Alternate Physical Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd39d4f68-7346-4133-8841-15318a714a24) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1641.json) |
|[Csak a biztonságos kapcsolatok Azure Cache for Redis engedélyezhető](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak SSL-en keresztüli kapcsolatok engedélyezésének naplózása Azure Cache for Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Engedélyezni kell a tárfiókokba történő biztonságos átvitelt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Naplózási követelmény a biztonságos átvitelhez a tárfiókban. A biztonságos átvitel egy olyan lehetőség, amely arra kényszeríti a tárfiókot, hogy csak biztonságos kapcsolatokról (HTTPS) fogadjon kéréseket. A HTTPS használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és megvédi az átvitel alatt található adatokat az olyan hálózati rétegbeli támadásoktól, mint a "man-in-the-middle", a lehallgatás és a munkamenet-eltérítés. |Naplózás, Megtagadás, Letiltva |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[A webalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A HTTPS használata biztosítja a kiszolgáló-/szolgáltatáshitelesítést, és megvédi az átvitel alatt található adatokat a hálózati réteg lehallgatási támadásaitól. |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |
|[A Windows-webkiszolgálókat biztonságos kommunikációs protokollok használatára kell konfigurálni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |Az interneten keresztül kommunikált információk adatvédelmének védelme érdekében a webkiszolgálóknak az iparági szabványnak megfelelő titkosítási protokoll, a TLS (TLS) Transport Layer Security kell használniuk. A TLS biztonsági tanúsítványokkal biztosítja a hálózaton keresztüli kommunikációt a gépek közötti kapcsolat titkosításához. A TLS 1.3 gyorsabb és biztonságosabb, mint a korábbi verziók: a TLS 1.0-1.2 és az SSL 2-3, amelyek mind örökölt protokollok. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

### <a name="network-disconnect"></a>Hálózat leválasztása

**Azonosító:** NIST SP 800-53 R4 SC-10 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1642 – Hálózat leválasztása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53397227-5ee3-4b23-9e5e-c8a767ce6928) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1642.json) |

### <a name="cryptographic-key-establishment-and-management"></a>Titkosítási kulcs létrehozása és kezelése

**Azonosító:** NIST SP 800-53 R4 SC-12 Ownership : **Shared**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1643 – Titkosítási kulcs létrehozása és kezelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8d492c-dd7a-46f7-a723-fa66a425b87c) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1643.json) |

### <a name="cryptographic-key-establishment-and-management--availability"></a>Titkosítási kulcs létrehozása és kezelése | Elérhetőség

**Azonosító:** NIST SP 800-53 R4 SC-12 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1644 – Titkosítási kulcs létrehozása és a felügyelet \| rendelkezésre állása](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7211477-c970-446b-b4af-062f37461147) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1644.json) |

### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Titkosítási kulcs létrehozása és kezelése | Szimmetrikus kulcsok

**Azonosító:** NIST SP 800-53 R4 SC-12 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1645 – Titkosítási kulcs létrehozása és kezelése \| szimmetrikus kulcsok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafbd0baf-ff1a-4447-a86f-088a97347c0c) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1645.json) |

### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Titkosítási kulcs létrehozása és kezelése | Aszimmetrikus kulcsok

**Azonosító:** NIST SP 800-53 R4 SC-12 (3) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1646 – Titkosítási kulcs létrehozása és kezelése \| aszimmetrikus kulcsok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F506814fa-b930-4b10-894e-a45b98c40e1a) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1646.json) |

### <a name="cryptographic-protection"></a>Kriptográfiai védelem

**Azonosító:** NIST SP 800-53 R4 SC-13 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1647 – Kriptográfiai védelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F791cfc15-6974-42a0-9f4c-2d4b82f4a78c) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1647.json) |

### <a name="collaborative-computing-devices"></a>Együttműködésen alapuló számítástechnikai eszközök

**Azonosító:** NIST SP 800-53 R4 SC-15 Ownership : **Shared**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1648 – Együttműködésen alapuló számítástechnikai eszközök](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a9eb14b-495a-4ebb-933c-ce4ef5264e32) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1648.json) |
|[Microsoft Managed Control 1649 – Együttműködésen alapuló számítástechnikai eszközök](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26d292cc-b0b8-4c29-9337-68abc758bf7b) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1649.json) |

### <a name="public-key-infrastructure-certificates"></a>Nyilvános kulcsú infrastruktúra tanúsítványai

**Azonosító:** NIST SP 800-53 R4 SC-17 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1650 – Nyilvános kulcsú infrastruktúra-tanúsítványok](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F201d3740-bd16-4baf-b4b8-7cda352228b7) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1650.json) |

### <a name="mobile-code"></a>Mobilkód

**Azonosító:** NIST SP 800-53 R4 SC-18 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1651 – Mobilkód](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6db63528-c9ba-491c-8a80-83e1e6977a50) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1651.json) |
|[Microsoft Managed Control 1652 – Mobilkód](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6998e84a-2d29-4e10-8962-76754d4f772d) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1652.json) |
|[Microsoft Managed Control 1653 – Mobile Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1c00a7-7fd0-42b0-8c5b-c45f6fa1f71b) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1653.json) |

### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**Azonosító:** NIST SP 800-53 R4 SC-19 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1654 – Voice Over Internet Protocol](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a2ee16e-ab1f-414a-800b-d1608835862b) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1654.json) |
|[Microsoft Managed Control 1655 – Voice Over Internet Protocol](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F121eab72-390e-4629-a7e2-6d6184f57c6b) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1655.json) |

### <a name="secure-name--address-resolution-service-authoritative-source"></a>Biztonságos név/címfeloldási szolgáltatás (mérvadó forrás)

**Azonosító:** NIST SP 800-53 R4 SC-20 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1656 – Biztonságos név / Címfeloldási szolgáltatás (mérvadó forrás)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cb067d5-c8b5-4113-a7ee-0a493633924b) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1656.json) |
|[Microsoft Managed Control 1657 – Biztonságos név / Címfeloldási szolgáltatás (mérvadó forrás)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90f01329-a100-43c2-af31-098996135d2b) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1657.json) |

### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Biztonságos név/ címfeloldási szolgáltatás (rekurzív vagy gyorsítótárazó feloldó)

**Azonosító:** NIST SP 800-53 R4 SC-21 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1658 – Biztonságos név/címfeloldási szolgáltatás (rekurzív vagy gyorsítótárazás-feloldó)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063b540e-4bdc-4e7a-a569-3a42ddf22098) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1658.json) |

### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Architektúra és kiépítés név/ címfeloldási szolgáltatáshoz

**Azonosító:** NIST SP 800-53 R4 SC-22 Ownership : **Shared**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1659 – Architektúra és kiépítés név/címfeloldási szolgáltatáshoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35a4102f-a778-4a2e-98c2-971056288df8) |A Microsoft megvalósítja ezt a Rendszer- és Communications Protection-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1659.json) |

### <a name="session-authenticity"></a>Munkamenet hitelessége

**Azonosító:** NIST SP 800-53 R4 SC-23 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1660 – Munkamenet hitelessége](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63096613-ce83-43e5-96f4-e588e8813554) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1660.json) |

### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Munkamenet hitelességének | Munkamenet-azonosítók érvénytelenítése a kijelentkezéskor

**Azonosító:** NIST SP 800-53 R4 SC-23 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1661 – A munkamenet hitelessége érvényteleníti a \| munkamenet-azonosítókat a kijelentkezéskor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c643c9a-1be7-4016-a5e7-e4bada052920) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1661.json) |

### <a name="fail-in-known-state"></a>Sikertelen, ismert állapotban

**Azonosító:** NIST SP 800-53 R4 SC-24 Ownership : **Shared**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1662 – A hiba ismert állapotban van](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F165cb91f-7ea8-4ab7-beaf-8636b98c9d15) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1662.json) |

### <a name="protection-of-information-at-rest"></a>Az adatok védelme

**Azonosító:** NIST SP 800-53 R4 SC-28 **Ownership**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1663 – Az adatok védelme](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60171210-6dde-40af-a144-bf2670518bfa) |A Microsoft ezt a rendszer- és kommunikációs védelmi vezérlőt valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1663.json) |

### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Az adatok védelme az | Kriptográfiai védelem

**Azonosító:** NIST SP 800-53 R4 SC-28 (1) **Tulajdonjog:** Ügyfél

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A speciális adatbiztonságot engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Napló minden SQL Managed Instance fejlett adatbiztonság nélkül. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Engedélyezni kell a speciális adatbiztonságot az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |SQL-kiszolgálók naplózása Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Lemeztitkosítást kell alkalmazni a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Az engedélyezett lemeztitkosítással nem rendelkező virtuális gépeket a Azure Security Center figyelik. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
|[Microsoft Managed Control 1664 – Adatvédelem az rest \| titkosítási funkciókban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2cdf6b8-9505-4619-b579-309ba72037ac) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1664.json) |
|[transzparens adattitkosítás SQL-adatbázisokon engedélyezni kell az adatbázis-adatokat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Engedélyezni kell a transzparens adattitkosítást az aktív adatok védelme és a megfelelőségi követelmények teljesítéséhez |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="process-isolation"></a>Folyamatelszigetelés

**Azonosító:** NIST SP 800-53 R4 SC-39 Ownership : **Shared**

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1665 – Folyamatelszigetelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5df3a55c-8456-44d4-941e-175f79332512) |A Microsoft ezt a rendszer- és kommunikációvédelmi vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1665.json) |

## <a name="system-and-information-integrity"></a>Rendszer- és információintegritás

### <a name="system-and-information-integrity-policy-and-procedures"></a>Rendszer- és információintegritási házirend és eljárások

**Azonosító:** NIST SP 800-53 R4 SI-1 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1666 – Rendszer- és információintegritási szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12e30ee3-61e6-4509-8302-a871e8ebb91e) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1666.json) |
|[Microsoft Managed Control 1667 – Rendszer- és információintegritási szabályzat és eljárások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd61880dc-6e38-4f2a-a30c-3406a98f8220) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1667.json) |

### <a name="flaw-remediation"></a>Hibák szervizelése

**Azonosító:** NIST SP 800-53 R4 SI-2 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Engedélyezni kell egy sebezhetőségi felmérési megoldást a virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |A virtuális gépek naplózásával észleli, hogy támogatott sebezhetőségi felmérési megoldást futtatnak-e. Minden kiberkockázati és biztonsági program egyik fő összetevője a biztonsági rések azonosítása és elemzése. Azure Security Center standard tarifacsomagja többletköltség nélkül tartalmazza a virtuális gépek biztonsági rések keresését. Emellett a Security Center automatikusan üzembe helyezheti ezt az eszközt. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Microsoft Managed Control 1668 – Hiba szervizelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fb0966e-be1d-42c3-baca-60df5c0bcc61) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1668.json) |
|[Microsoft Managed Control 1669 – Hiba szervizelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48f2f62b-5743-4415-a143-288adc0e078d) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1669.json) |
|[Microsoft Managed Control 1670 – Hiba szervizelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6108469-57ee-4666-af7e-79ba61c7ae0c) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1670.json) |
|[Microsoft Managed Control 1671 – Hiba szervizelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5bbef7-a316-415b-9b38-29753ce8e698) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1671.json) |
|[A virtuálisgép-méretezési készletek rendszerfrissítéseit telepíteni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Naplózhatja, hogy vannak-e hiányzó rendszerbiztonsági frissítések és kritikus frissítések, amelyek telepítése szükséges a Windows és Linux rendszerű virtuálisgép-méretezési készletek biztonságának biztosításához. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[A rendszerfrissítéseket telepíteni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A kiszolgálókon hiányzó biztonságirendszer-frissítéseket a Azure Security Center figyelheti javaslatként |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |
|[A gépek biztonsági konfigurációjának biztonsági réseit helyre kelligazító](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azok a kiszolgálók, amelyek nem tesznek eleget a konfigurált alapkonfigurációnak, a Azure Security Center figyelik javaslatként |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[A virtuálisgép-méretezési készletek biztonsági konfigurációjában található biztonsági réseket helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |A virtuálisgép-méretezési készletek operációsrendszer-biztonsági réseit naplózva megvédheti őket a támadásokkal szemben. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Az SQL-adatbázisok biztonsági réseit helyre kell ásni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Figyelje a sebezhetőségi felmérés vizsgálatának eredményeit és az adatbázis biztonsági réseit javító javaslatokat. |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="flaw-remediation--central-management"></a>Hiba szervizelési | Központi felügyelet

**Azonosító:** NIST SP 800-53 R4 SI-2 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1672 – Hiba szervizelési \| központi felügyelet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb45fe972-904e-45a4-ac20-673ba027a301) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1672.json) |

### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Hiba szervizelési | Automatizált hibák szervizelési állapota

**Azonosító:** NIST SP 800-53 R4 SI-2 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1673 – Hiba szervizelés \| , automatizált hiba szervizelési állapota](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdff0b90d-5a6f-491c-b2f8-b90aa402d844) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1673.json) |

### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Hiba szervizelési | A javítási műveletek hibáinak /teljesítménytesztek szervizlének ideje

**Azonosító:** NIST SP 800-53 R4 SI-2 (3) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1674 – Hibaszerkesztés ideje a hibák szervizelése \| érdekében / Teljesítménytesztek korrekciós műveletekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e9e233-dd0a-4bde-aea5-1371bce0e002) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1674.json) |
|[Microsoft Managed Control 1675 – Hibaszerkesztés ideje a hibák szervizelése \| érdekében / Teljesítménytesztek korrekciós műveletekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffacb66e0-1c48-478a-bed5-747a312323e1) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1675.json) |

### <a name="malicious-code-protection"></a>Rosszindulatú kódvédelem

**Azonosító:** NIST SP 800-53 R4 SI-3 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A végpontvédelmi megoldást telepíteni kell a virtuálisgép-méretezési készletekre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |A virtuálisgép-méretezési készleteken lévő végpontvédelmi megoldások meglétének és állapotának naplózása a fenyegetések és a biztonsági rések elleni védelem érdekében. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Managed Control 1676 – Rosszindulatú kódvédelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10fb58b-56a8-489e-9ce3-7ffe24e78e4b) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1676.json) |
|[Microsoft Managed Control 1677 – Rosszindulatú kódvédelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a248e1e-040f-43e5-bff2-afc3a57a3923) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1677.json) |
|[Microsoft Managed Control 1678 – Rosszindulatú kódvédelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd533cb0-b416-4be7-8e86-4d154824dfd7) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1678.json) |
|[Microsoft Managed Control 1679 – Rosszindulatú kódvédelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cf42a28-193e-41c5-98df-7688e7ef0a88) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1679.json) |
|[Hiányzó Endpoint Protection figyelése a Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |A telepített Endpoint Protection-ügynökkel nem Azure Security Center kiszolgálókat |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--central-management"></a>Rosszindulatú kódvédelmi | Központi felügyelet

**Azonosító:** NIST SP 800-53 R4 SI-3 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A végpontvédelmi megoldást telepíteni kell a virtuálisgép-méretezési készletekre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |A virtuálisgép-méretezési készleteken lévő végpontvédelmi megoldások meglétének és állapotának naplózása a fenyegetések és a biztonsági rések elleni védelem érdekében. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Managed Control 1680 - Malicious Code Protection \| Central Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399cd6ee-0e18-41db-9dea-cde3bd712f38) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1680.json) |
|[Hiányzó Endpoint Protection figyelése a Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |A telepített Endpoint Protection-ügynökkel nem Azure Security Center kiszolgálókat |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--automatic-updates"></a>Rosszindulatú kódvédelmi | Automatikus frissítések

**Azonosító:** NIST SP 800-53 R4 SI-3 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1681 - Malicious Code Protection \| Automatic Updates](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12623e7e-4736-4b2e-b776-c1600f35f93a) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1681.json) |

### <a name="malicious-code-protection--nonsignature-based-detection"></a>Rosszindulatú kódvédelmi | Nonsignature-Based észlelése

**Azonosító:** NIST SP 800-53 R4 SI-3 (7) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1682 – Rosszindulatú kódvédelem \| Nonsignature-Based észlelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F62b638c5-29d7-404b-8d93-f21e4b1ce198) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1682.json) |

### <a name="information-system-monitoring"></a>Információs rendszer figyelése

**Azonosító:** NIST SP 800-53 R4 SI-4 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A speciális adatbiztonságot engedélyezni kell a SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Napló minden SQL Managed Instance fejlett adatbiztonság nélkül. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Engedélyezni kell a magas szintű adatbiztonságot az SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |SQL-kiszolgálók naplózása Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Log Analytics-munkaterület naplózása virtuális géphez – Jelentés eltérése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Nem megfelelőként jelenti a virtuális gépeket, ha nem jelentkeznek be a szabályzat-/kezdeményezés-hozzárendelésben megadott Log Analytics-munkaterületre. |Ellenőrzési |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[A Log Analytics-ügynököt engedélyezni kell a felsorolt virtuálisgép-rendszerképekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Nem megfelelőként jelenti a virtuális gépeket, ha a virtuálisgép-rendszerkép nem szerepel a meghatározott listán, és az ügynök nincs telepítve. |AuditIfNotExists, Disabled |[2.0.0-s előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[A Log Analytics-ügynököt engedélyezni kell a virtuálisgép-méretezési csoportban a felsorolt virtuálisgép-rendszerképekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Nem megfelelőként jelenti a virtuálisgép-méretezési készleteket, ha a virtuálisgép-rendszerkép nem szerepel a meghatározott listán, és az ügynök nincs telepítve. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft Managed Control 1683 – Információs rendszer figyelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c79fee4-88dd-44ce-bbd4-4de88948c4f8) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1683.json) |
|[Microsoft Managed Control 1684 – Information System Monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16bfdb59-db38-47a5-88a9-2e9371a638cf) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1684.json) |
|[Microsoft Managed Control 1685 – Információs rendszer figyelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36b0ef30-366f-4b1b-8652-a3511df11f53) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1685.json) |
|[Microsoft Managed Control 1686 – Information System Monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe17085c5-0be8-4423-b39b-a52d3d1402e5) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1686.json) |
|[Microsoft Managed Control 1687 – Információs rendszerek figyelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a87fc7f-301e-49f3-ba2a-4d74f424fa97) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1687.json) |
|[Microsoft Managed Control 1688 – Information System Monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063c3f09-e0f0-4587-8fd5-f4276fae675f) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1688.json) |
|[Microsoft Managed Control 1689 – Information System Monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fde901f2f-a01a-4456-97f0-33cda7966172) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1689.json) |

### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Információsrendszer-figyelési | System-Wide behatolásészlelési rendszer

**Azonosító:** NIST SP 800-53 R4 SI-4 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1690 – Information System Monitoring \| System-Wide behatolásészlelési rendszer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2567a23-d1c3-4783-99f3-d471302a4d6b) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1690.json) |

### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Információsrendszer-figyelési | Automatizált eszközök az Real-Time elemzéshez

**Azonosító:** NIST SP 800-53 R4 SI-4 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1691 – Information System \| Monitoring Automated Tools for Real-Time Analysis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71475fb4-49bd-450b-a1a5-f63894c24725) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1691.json) |

### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Információsrendszer-figyelési | Bejövő és kimenő kommunikációs forgalom

**Azonosító:** NIST SP 800-53 R4 SI-4 (4) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1692 - Information System Monitoring \| Inbound And Outbound Communications Traffic](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ecda928-9df4-4dd7-8f44-641a91e470e8) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1692.json) |

### <a name="information-system-monitoring--system-generated-alerts"></a>Információsrendszer-figyelési | System-Generated riasztások

**Azonosító:** NIST SP 800-53 R4 SI-4 (5) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1693 – Information System Monitoring \| System-Generated riasztások](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa450eba6-2efc-4a00-846a-5804a93c6b77) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1693.json) |

### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Információsrendszer-figyelési | Kommunikációs forgalmi anomáliák elemzése

**Azonosító:** NIST SP 800-53 R4 SI-4 (11) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1694 – Information System Monitoring \| Analyze Communications Traffic Anomalies](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F426c4ac9-ff17-49d0-acd7-a13c157081c0) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1694.json) |

### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Információsrendszer-figyelési | Vezeték nélküli behatolásészlelés

**Azonosító:** NIST SP 800-53 R4 SI-4 (14) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1695 – Information System Monitoring \| Wireless Intrusion Detection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13fcf812-ec82-4eda-9b89-498de9efd620) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1695.json) |

### <a name="information-system-monitoring--correlate-monitoring-information"></a>Információsrendszer-figyelési | Monitorozási információk korrelál

**Azonosító:** NIST SP 800-53 R4 SI-4 (16) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1696 – Information System Monitoring \| Korrelálható figyelési információk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69d2a238-20ab-4206-a6dc-f302bf88b1b8) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1696.json) |

### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Információsrendszer-figyelési | Forgalomelemzés /Covert-kiszivárgás

**Azonosító:** NIST SP 800-53 R4 SI-4 (18) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1697 – Information System \| Monitoring Analyze Traffic / Covert Exfiltration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9873db2-18ad-46b3-a11a-1a1f8cbf0335) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1697.json) |

### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Információsrendszer-figyelési | Nagyobb kockázatot vállaló személyek

**Azonosító:** NIST SP 800-53 R4 SI-4 (19) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1698 – Információsrendszer-figyelő \| személyek, akik nagyobb kockázatot jelentenek](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F31b752c1-05a9-432a-8fce-c39b56550119) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1698.json) |

### <a name="information-system-monitoring--privileged-users"></a>Információsrendszer-figyelési | Kiemelt felhasználók

**Azonosító:** NIST SP 800-53 R4 SI-4 (20) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1699 – Az információs rendszer kiemelt \| felhasználóinak figyelése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69c7bee8-bc19-4129-a51e-65a7b39d3e7c) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1699.json) |

### <a name="information-system-monitoring--unauthorized-network-services"></a>Információsrendszer-figyelési | Jogosulatlan Network Services

**Azonosító:** NIST SP 800-53 R4 SI-4 (22) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1700 – Information System \| Monitoring Unauthorized Network Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7831b4ba-c3f4-4cb1-8c11-ef8d59438cd5) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1700.json) |

### <a name="information-system-monitoring--host-based-devices"></a>Információsrendszer-figyelési | Host-Based eszközök

**Azonosító:** NIST SP 800-53 R4 SI-4 (23) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1701 – Information System Monitoring \| Host-Based eszközök](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff25bc08f-27cb-43b6-9a23-014d00700426) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1701.json) |

### <a name="information-system-monitoring--indicators-of-compromise"></a>Információsrendszer-figyelési | A biztonság veszélyeztető mutatói

**Azonosító:** NIST SP 800-53 R4 SI-4 (24) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1702 - Information System Monitoring \| Indicators Of Compromise](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4dfc0855-92c4-4641-b155-a55ddd962362) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1702.json) |

### <a name="security-alerts-advisories-and-directives"></a>Biztonsági riasztások, tanácsadások és irányelvek

**Azonosító:** NIST SP 800-53 R4 SI-5 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1703 – Biztonsági riasztások, tanácsadások és direktívák](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F804faf7d-b687-40f7-9f74-79e28adf4205) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1703.json) |
|[Microsoft Managed Control 1704 – Biztonsági riasztások, tanácsadások és direktívák](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d44b6fa-1134-4ea6-ad4e-9edb68f65429) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1704.json) |
|[Microsoft Managed Control 1705 – Biztonsági riasztások, tanácsadások és direktívák](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff82e3639-fa2b-4e06-a786-932d8379b972) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1705.json) |
|[Microsoft Managed Control 1706 – Biztonsági riasztások, tanácsadások és direktívák](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff475ee0e-f560-4c9b-876b-04a77460a404) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1706.json) |

### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Biztonsági riasztások, tanácsadások és irányelvek | Automatizált riasztások és tanácsadás

**Azonosító:** NIST SP 800-53 R4 SI-5 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1707 – Biztonsági riasztások, tanácsadások és direktívák automatizált \| riasztásai és tanácsadásai](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4a2ac8-868a-4702-a345-6c896c3361ce) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1707.json) |

### <a name="security-function-verification"></a>Biztonsági függvény ellenőrzése

**Azonosító:** NIST SP 800-53 R4 SI-6 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1708 – Biztonsági funkciók ellenőrzése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a1e2c88-13de-4959-8ee7-47e3d74f1f48) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1708.json) |
|[Microsoft Managed Control 1709 – Biztonsági funkciók ellenőrzése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F025992d6-7fee-4137-9bbf-2ffc39c0686c) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1709.json) |
|[Microsoft Managed Control 1710 – Biztonsági függvény ellenőrzése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf2a93c8-e6dd-4c94-acdd-4a2eedfc478e) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1710.json) |
|[Microsoft Managed Control 1711 – Biztonsági függvény ellenőrzése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb083a535-a66a-41ec-ba7f-f9498bf67cde) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1711.json) |

### <a name="software-firmware-and-information-integrity"></a>Szoftver, belső vezérlőprogram és információintegritás

**Azonosító:** NIST SP 800-53 R4 SI-7 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1712 – Szoftverek, belső vezérlőprogram és információintegritás](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44e543aa-41db-42aa-98eb-8a5eb1db53f0) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1712.json) |

### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Szoftver,belső vezérlőprogram és információintegritás | Integritás-ellenőrzések

**Azonosító:** NIST SP 800-53 R4 SI-7 (1) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1713 – Szoftverek, belső vezérlőprogram és \| információintegritás-integritás ellenőrzése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d87c70b-5012-48e9-994b-e70dd4b8def0) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1713.json) |

### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Szoftver,belső vezérlőprogram és információintegritás | Automatizált értesítések az integritási szabálysértésekről

**Azonosító:** NIST SP 800-53 R4 SI-7 (2) **Tulajdonjog:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1714 – Szoftverek, belső vezérlőprogram és információintegritás automatizált értesítései az \| integritási szabálysértésekről](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe12494fa-b81e-4080-af71-7dbacc2da0ec) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1714.json) |

### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Szoftver-, belsővezérlőprogram- és információintegritási | Automatizált válasz az integritási szabálysértésekre

**Azonosító:** NIST SP 800-53 R4 SI-7 (5) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1715 – Szoftver, belső vezérlőprogram és információintegritás automatizált válaszadás \| az integritási szabálysértésekre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd469ae0-71a8-4adc-aafc-de6949ca3339) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1715.json) |

### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Szoftver-, belsővezérlőprogram- és információintegritási | Az észlelés és a reagálás integrációja

**Azonosító:** NIST SP 800-53 R4 SI-7 (7) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1716 – Az észlelés és a válasz szoftverek, belső vezérlőprogram és információintegritás \| integrációja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe54c325e-42a0-4dcf-b105-046e0f6f590f) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1716.json) |

### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Szoftver-, belsővezérlőprogram- és információintegritási | Bináris vagy gép végrehajtható kódja

**Azonosító:** NIST SP 800-53 R4 SI-7 (14) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1717 – Szoftver, belső vezérlőprogram és információintegritás \| bináris vagy számítógép végrehajtható kódja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967773fc-d9ab-4a4e-8ff6-f5e9e3f5dbef) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1717.json) |
|[Microsoft Managed Control 1718 – Szoftver, belső vezérlőprogram és információintegritás \| bináris vagy számítógép végrehajtható kódja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0dced7ab-9ce5-4137-93aa-14c13e06ab17) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1718.json) |

### <a name="spam-protection"></a>Levélszemét-védelem

**Azonosító:** NIST SP 800-53 R4 SI-8 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1719 – Levélszemét elleni védelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc13da9b4-fe14-4fe2-853a-5997c9d4215a) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1719.json) |
|[Microsoft Managed Control 1720 – Levélszemét elleni védelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44b9a7cd-f36a-491a-a48b-6d04ae7c4221) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1720.json) |

### <a name="spam-protection--central-management"></a>Levélszemét-| Központi felügyelet

**Azonosító:** NIST SP 800-53 R4 SI-8 (1) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1721 – Levélszemét-védelmi \| központi felügyelet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd207aaef-7c4d-4f8c-9dce-4d62dfa3d29a) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1721.json) |

### <a name="spam-protection--automatic-updates"></a>Levélszemét-| Automatikus frissítések

**Azonosító:** NIST SP 800-53 R4 SI-8 (2) **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1722 – Levélszemét elleni védelem \| automatikus frissítései](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1da06bd-25b6-4127-a301-c313d6873fff) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1722.json) |

### <a name="information-input-validation"></a>Információk bemenetének ellenőrzése

**Azonosító:** NIST SP 800-53 R4 SI-10 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1723 – Információbemenet-ellenőrzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe91927a0-ac1d-44a0-95f8-5185f9dfce9f) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1723.json) |

### <a name="error-handling"></a>Hibakezelés

**Azonosító:** NIST SP 800-53 R4 SI-11 **tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1724 – Hibakezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd07594d1-0307-4c08-94db-5d71ff31f0f6) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1724.json) |
|[Microsoft Managed Control 1725 – Hibakezelés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafc234b5-456b-4aa5-b3e2-ce89108124cc) |A Microsoft megvalósítja ezt a rendszer- és információintegritás-vezérlést |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1725.json) |

### <a name="information-handling-and-retention"></a>Információkezelés és -megőrzés

**Azonosító:** NIST SP 800-53 R4 SI-12 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1726 – Információkezelés és -megőrzés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaff1279-05e0-4463-9a70-8ba5de4c7aa4) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1726.json) |

### <a name="memory-protection"></a>Memóriavédelem

**Azonosító:** NIST SP 800-53 R4 SI-16 **Tulajdonos:** Megosztott

|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1727 – Memóriavédelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F697175a7-9715-4e89-b98b-c6f605888fa3) |A Microsoft ezt a rendszer- és információintegritás-vezérlést valósítja meg |Ellenőrzési |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1727.json) |

> [!NOTE]
> Az egyes Azure Policy a különböző országos Azure Government eltérőek lehetnek.

## <a name="next-steps"></a>Következő lépések

További cikkek a Azure Policy:

- [A jogszabályi megfelelőség](../concepts/regulatory-compliance.md) áttekintése.
- Lásd a [kezdeményezésdefiníció szerkezetét.](../concepts/initiative-definition-structure.md)
- Tekintsen át további példákat [a következő Azure Policy példákban:](./index.md).
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Ismerje meg, [hogyan szervizelődnek a nem megfelelő erőforrások.](../how-to/remediate-resources.md)
