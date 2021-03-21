---
title: Azure Security Control – identitás-és Access Control
description: Azure Security Control – identitás és Access Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dda3dcd3cd1234b2d0830010297e760201ed6160
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549277"
---
# <a name="security-control-identity-and-access-control"></a>Biztonsági ellenőrzés: identitás-és Access Control

Az identitás-és hozzáférés-kezelési javaslatok az identitáson alapuló hozzáférés-vezérléssel kapcsolatos problémák kezelésére, a rendszergazdai hozzáférés zárolására, a személyazonossággal kapcsolatos eseményekre, a rendellenes fiókok viselkedésére és a szerepköralapú hozzáférés-vezérlésre összpontosítanak.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3,1 | 4.1 | Ügyfél |

Az Azure AD olyan beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

## <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3.2 | 4.2 | Ügyfél |

Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával. Más Azure-erőforrások, amelyek jelszó megadását igénylik a bonyolultsági követelményekkel és a jelszó minimális hosszával hozhatók létre, és a szolgáltatástól függően eltérőek. Ön felelős harmadik féltől származó alkalmazásokért és piactér-szolgáltatásért, amelyek az alapértelmezett jelszavakat használhatják.

## <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3.3 | 4.3 | Ügyfél |

Hozzon létre szabványos működési eljárásokat a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

A Microsoft-szolgáltatásokhoz igénybe vehet egy igény szerinti vagy elég hozzáférésű hozzáférést Azure AD Privileged Identity Management Kiemelt szerepkörökkel, és Azure Resource Manager is. 

- [További információ a Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3.4 | 4.4 | Ügyfél |

Ha lehetséges, használja a Azure Active Directory SSO-t ahelyett, hogy egyéni, különálló hitelesítő adatokat konfiguráljon a szolgáltatáson kívül. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata.

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../../active-directory/manage-apps/what-is-single-sign-on.md)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3.5 | 4,5, 11,5, 12,11, 16,3 | Ügyfél |

Engedélyezze az Azure AD MFA-t, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [MFA engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../../security-center/security-center-identity-access.md)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3,6 | 4,6, 11,6, 12,12 | Ügyfél |

Az Azure-erőforrások bejelentkezéséhez és konfigurálásához használja az MFA-t (emelt szintű hozzáférési munkaállomások).

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [MFA engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3.7 | 4,8, 4,9 | Ügyfél |

A naplók és a riasztások generálásához Azure Active Directory biztonsági jelentéseket használhat, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../../security-center/security-center-identity-access.md)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3,8 | 11,7 | Ügyfél |

A feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

## <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3.9 | 16,1, 16,2, 16,4, 16,5, 16,6 | Ügyfél |

Használja a Azure Active Directoryt központi hitelesítési és engedélyezési rendszeren. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [Azure AD-példány létrehozása és konfigurálása](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3,10 | 16,9, 16,10 | Ügyfél |

Az Azure AD olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. 

- [Az Azure AD jelentéskészítés ismertetése](../../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../../active-directory/governance/access-reviews-overview.md)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3,11 | 16,12 | Ügyfél |

Hozzáférése van az Azure AD bejelentkezési tevékenységekhez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely SIEM/monitoring eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplók és a bejelentkezési naplók Log Analytics munkaterületre való elküldésével. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3,12 | 16,13 | Ügyfél |

Az Azure AD kockázatkezelési és identitás-védelmi funkcióival automatikus válaszokat konfigurálhat a felhasználói identitásokkal kapcsolatos gyanús műveletekre. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 3,13 | 16 | Ügyfél |

A támogatási forgatókönyvek esetében, ahol a Microsoftnak hozzá kell férnie az ügyféladatok eléréséhez, Ügyfélszéf biztosít egy felületet, amellyel áttekintheti, jóváhagyhatja vagy elutasíthatja az ügyféladatok hozzáférési kérelmeit.

- [A Ügyfélszéf megismerése](../fundamentals/customer-lockbox-overview.md)


## <a name="next-steps"></a>Következő lépések

- Lásd a következő biztonsági vezérlést: [Adatvédelem](security-control-data-protection.md)