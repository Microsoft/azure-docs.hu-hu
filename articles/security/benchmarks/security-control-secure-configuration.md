---
title: Azure Security Control – biztonságos konfiguráció
description: Azure Security Control – biztonságos konfiguráció
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b13cc6f8bb50a3c9ec3cae519db387f9f32c950d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102615278"
---
# <a name="security-control-secure-configuration"></a>Biztonsági ellenőrzés: biztonságos konfiguráció

Az Azure-erőforrások biztonsági konfigurációjának létrehozása, implementálása és aktívan kezelése (nyomon követése, bejelentése, javítása), hogy a támadók ne tudják kiaknázni a sebezhető szolgáltatásokat és beállításokat.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.1 | 5,1 | Ügyfél |

Használjon Azure Policy aliasokat egyéni szabályzatok létrehozásához az Azure-erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. A beépített Azure Policy-definíciókat is használhatja.

Emellett Azure Resource Manager lehetősége van a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni, hogy a konfigurációk megfeleljenek/túllépik a szervezete biztonsági követelményeit.

Az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően Azure Security Center javaslatokat is alkalmazhat.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../../governance/policy/tutorials/create-and-manage.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../../security-center/recommendations-reference.md)

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.2 | 5,1 | Ügyfél |

A biztonsági konfigurációk minden számítási erőforráson való fenntartásához használjon Azure Security Center javaslatokat.  Emellett egyéni operációsrendszer-lemezképeket vagy Azure Automation állapot-konfigurációt is használhat a szervezete számára szükséges operációs rendszer biztonsági konfigurációjának létrehozásához.

- [Azure Security Center javaslatok figyelése](../../security-center/security-center-recommendations.md)

- [Biztonsági javaslatok – gyorsútmutató](../../security-center/recommendations-reference.md)

- [Azure Automation állapot konfigurációjának áttekintése](../../automation/automation-dsc-overview.md)

- [Virtuális merevlemez feltöltése és használata új Windows rendszerű virtuális gépek létrehozásához az Azure-ban](../../virtual-machines/windows/upload-generalized-managed.md)

- [Linuxos virtuális gép létrehozása egyéni lemezről az Azure CLI-vel](../../virtual-machines/linux/upload-vhd.md)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.3 | 5,2 | Ügyfél |

Az Azure-erőforrások biztonságos beállításainak érvénybe léptetéséhez használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.  Emellett Azure Resource Manager-sablonok használatával is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját. 

- [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../../azure-resource-manager/templates/overview.md)

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7,4 | 5,2 | Megosztott |

Az Azure számítási erőforrásain a sebezhetőségi felmérések elvégzéséhez Azure Security Center ajánlásokat követve.  Emellett Azure Resource Manager sablonokat, egyéni operációsrendszer-lemezképeket vagy Azure Automation állapot konfigurációját is használhatja a szervezete számára szükséges operációs rendszer biztonsági beállításainak fenntartásához.   A Microsoft virtuálisgép-sablonjai a Azure Automation kívánt állapot-konfigurációval együtt segíthetnek a biztonsági követelmények teljesítésében és fenntartásában. 

Azt is vegye figyelembe, hogy a Microsoft által közzétett Azure Marketplace virtuálisgép-rendszerképeket a Microsoft felügyeli és tartja karban. 

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../../security-center/deploy-vulnerability-assessment-vm.md)

- [Azure-beli virtuális gép létrehozása Azure Resource Manager sablonból](../../virtual-machines/windows/ps-template.md)

- [Azure Automation állapot konfigurációjának áttekintése](../../automation/automation-dsc-overview.md)

- [Windows rendszerű virtuális gép létrehozása a Azure Portalban](../../virtual-machines/windows/quick-create-portal.md)

- [A virtuálisgép-sablon letöltésével kapcsolatos információk](/previous-versions/azure/virtual-machines/windows/download-template)

- [Példaszkript egy VHD Azure-ba történő feltöltéséhez és új virtuális gép létrehozásához](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7,5 | 5.3 | Ügyfél |

Az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat és a kívánt állapotú konfigurációs parancsfájlokat. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.6 | 5.3 | Ügyfél |

Ha egyéni lemezképeket használ, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosíthatja, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez. Megosztott képkatalógus használatával a képeket megoszthatja a szervezeten belüli különböző felhasználókkal, egyszerű szolgáltatásokkal vagy AD-csoportokkal.  A tárolói lemezképek esetében tárolja azokat Azure Container Registryban, és használja ki az Azure RBAC annak biztosítására, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez.  

- [Az Azure RBAC ismertetése](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Az Azure-RBAC megismerése Container Registry](../../container-registry/container-registry-roles.md)

- [Az Azure RBAC konfigurálása](../../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Megosztott képgyűjtemény – áttekintés](../../virtual-machines/shared-image-galleries.md)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.7 | 5.4 | Ügyfél |

Az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Az Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Az adott erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet.  Emellett Azure Automation használatával is telepítheti a konfigurációs módosításokat.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](../../governance/policy/concepts/definition-structure.md#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7,8 | 5.4 | Ügyfél |

Azure Automation állapot-konfiguráció a kívánt állapot-konfigurációs (DSC) csomópontok egyike a felhőben vagy a helyszíni adatközpontban. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják. 

- [Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](../../automation/automation-dsc-onboarding.md)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.9 | 5,5 | Ügyfél |

Az Azure-erőforrásokra vonatkozó alapvizsgálatok elvégzéséhez használja a Azure Security Center.  Emellett az Azure-erőforrások konfigurációjának riasztására és naplózására Azure Policy is használhatja.

- [Javaslatok szervizelése Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.10 | 5,5 | Ügyfél |

A Azure Security Center használatával elvégezheti a tárolók operációsrendszer-és Docker-beállításainak alapkonfigurációját.

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](../../security-center/container-security.md)

## <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.11 | 13,1 | Ügyfél |

A Felhőbeli alkalmazások titkos felügyeletének egyszerűbbé és biztonságossá tételéhez használja a Managed Service Identityt a Azure Key Vaultvel együtt.

- [Integráció az Azure felügyelt identitásokkal](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault létrehozása](../../key-vault/secrets/quick-create-portal.md)

- [Hitelesítés Key Vault](../../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatok kiosztása](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7,12 | 4.1 | Ügyfél |

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását az Azure AD-ben. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitások konfigurálása](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7,13 | 18,1, 18,7 | Ügyfél |

Hitelesítő adatok beolvasása a kódban a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Következő lépések

- Tekintse meg a következő biztonsági vezérlőt:  [kártevők elleni védelem](security-control-malware-defense.md)