---
title: Beépített Azure AD-szerepkörök – Azure Active Directory
description: A Azure Active Directory szerepköröket és engedélyeket ismerteti.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 04/20/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf614922503212488c822ac020960b0ddb99fc28
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780064"
---
# <a name="azure-ad-built-in-roles"></a>Beépített Azure AD-szerepkörök

A Azure Active Directory (Azure AD) szolgáltatásban, ha egy másik rendszergazdának vagy nem rendszergazdának kell kezelnie az Azure AD-erőforrásokat, hozzárendelhet hozzájuk egy Azure AD-szerepkört, amely biztosítja a számukra szükséges engedélyeket. Hozzárendelhet például szerepköröket a felhasználók hozzáadásának vagy módosításának, a felhasználói jelszavak alaphelyzetbe állításának, a felhasználói licencek kezelésének vagy a tartománynevek kezelésének lehetővé tenniük.

Ez a cikk azokat a beépített Azure AD-szerepköröket sorolja fel, amelyek az Azure AD-erőforrások felügyeletének engedélyezése érdekében rendelhetők hozzá. A szerepkörök hozzárendelésére vonatkozó információkért lásd: [Azure AD-szerepkörök hozzárendelése felhasználókhoz.](manage-roles-portal.md)

## <a name="all-roles"></a>Minden szerepkör

> [!div class="mx-tableFixed"]
> | Szerepkör | Leírás | Sablon azonosítója |
> | --- | --- | --- |
> | [Alkalmazás-rendszergazda](#application-administrator) | Létrehozhatja és kezelheti az alkalmazásregisztrációk és a vállalati alkalmazások összes aspektusát. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Alkalmazásfejlesztő](#application-developer) | A "Felhasználók regisztrálhat alkalmazásokat" beállítástól függetlenül hozhat létre alkalmazásregisztrációkat. | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Támadás hasznos terhelésének szerzője](#attack-payload-author) | Támadási hasznos okat hozhat létre, amelyek később a rendszergazda által indíthatóak. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Támadásszimulációs rendszergazda](#attack-simulation-administrator) | Létrehozhatja és kezelheti a támadásszimulációs kampányok összes aspektusát. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Hitelesítési rendszergazda](#authentication-administrator) | Bármely nem rendszergazda felhasználó hitelesítési módszerének információit megtekintheti, beállíthatja és alaphelyzetbe állíthatja. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Hitelesítési házirend rendszergazdája](#authentication-policy-administrator) | A hitelesítési módszerek és a jelszóvédelmi szabályzatok minden aspektusát létrehozhatja és kezelheti. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Azure AD-hez csatlakozott eszköz helyi rendszergazdája](#azure-ad-joined-device-local-administrator) | Az ehhez a szerepkörhöz hozzárendelt felhasználók felkerülnek a helyi rendszergazdák csoportjába az Azure AD-hez csatlakozott eszközökön. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps-rendszergazda](#azure-devops-administrator) | Kezelheti az Azure DevOps szervezeti szabályzatát és beállításait. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure Information Protection rendszergazda](#azure-information-protection-administrator) | A termék minden aspektusát Azure Information Protection kezelheti. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [B2C IEF kulcskészlet-rendszergazda](#b2c-ief-keyset-administrator) | Kezelheti az összevonás és a titkosítás titkos kulcsát a Identity Experience Framework (IEF). | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [B2C IEF-házirend-rendszergazda](#b2c-ief-policy-administrator) | Megbízhatósági keretrendszer szabályzatokat hozhat létre és kezelhet a Identity Experience Framework (IEF) segítségével. | 3edaf63-341e-4475-9f94-5c398ef6c070 |
> | [Számlázási rendszergazda](#billing-administrator) | Elvégezheti a számlázással kapcsolatos gyakori feladatokat, például frissítheti a fizetési adatokat. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Felhőalkalmazás-rendszergazda](#cloud-application-administrator) | Az alkalmazásregisztrációk és a vállalati alkalmazások összes aspektusát létrehozhatja és kezelheti az alkalmazásproxy kivételével. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Felhőalapú eszköz rendszergazdája](#cloud-device-administrator) | Korlátozott hozzáférés az eszközök kezeléséhez az Azure AD-ban. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Megfelelőségi rendszergazda](#compliance-administrator) | Olvashatja és kezelheti a megfelelőségi konfigurációkat és jelentéseket az Azure AD-ban és Microsoft 365. | 17315797-102d-40b4-93e0-432062caca18 |
> | [Megfelelőségiadat-rendszergazda](#compliance-data-administrator) | Megfelelőségi tartalmat hoz létre és kezel. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Feltételes hozzáférés rendszergazdája](#conditional-access-administrator) | Kezelheti a feltételes hozzáférési képességeket. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Ügyfélstűdő hozzáférés-jóváhagyó](#customer-lockbox-access-approver) | Jóváhagyhatja a Microsoft támogatási kéréseket az ügyfél szervezeti adatainak eléréséhez. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Asztali elemzés rendszergazda](#desktop-analytics-administrator) | Elérheti és kezelheti az asztali felügyeleti eszközöket és szolgáltatásokat. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Könyvtárolvasók](#directory-readers) | Olvashatja az alapszintű címtáradatokat. Gyakran használják címtár olvasási hozzáférésének megadására az alkalmazásokhoz és vendégekhez. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Címtár-szinkronizálási fiókok](#directory-synchronization-accounts) | Csak a Azure AD Connect használja. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Címtárírók](#directory-writers) | Olvashatja és írhatja az alapszintű könyvtáradatokat. Nem felhasználók számára szánt alkalmazásokhoz való hozzáférés megadásához. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Tartománynév-rendszergazda](#domain-name-administrator) | Kezelheti a tartományneveket a felhőben és a helyszínen. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365-rendszergazda](#dynamics-365-administrator) | A Dynamics 365 termék minden aspektusát képes kezelni. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Exchange-rendszergazda](#exchange-administrator) | Az Exchange-termék minden aspektusát képes kezelni. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Exchange-címzett rendszergazdája](#exchange-recipient-administrator) | Létrehozhat vagy frissíthet Exchange Online-címzetteket az Exchange Online-szervezeten belül. | 31392ffb-586c-42d1-9346-e59415a2cc4e |
> | [Külső azonosító felhasználói folyamat rendszergazdája](#external-id-user-flow-administrator) | A felhasználói folyamatok minden aspektusát létrehozhatja és kezelheti. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Külső azonosító felhasználói folyamat attribútum-rendszergazdája](#external-id-user-flow-attribute-administrator) | Létrehozhatja és kezelheti az összes felhasználói folyamat számára elérhető attribútumsémát. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Külső identitásszolgáltató rendszergazdája](#external-identity-provider-administrator) | Konfigurálhatja az identitásszolgáltatókat a közvetlen összevonásban való használatra. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Globális rendszergazda](#global-administrator) | Az Azure AD és az Azure AD Microsoft-szolgáltatások összes aspektusát képes kezelni. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Globális olvasó](#global-reader) | Mindent olvashat, amit a globális rendszergazda tud, de nem frissít semmit. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Csoport-rendszergazda](#groups-administrator) | A szerepkör tagjai csoportokat hozhatnak létre/kezelhet, csoportbeállításokat hozhatnak létre és kezelhet, például elnevezési és elévülési szabályzatokat, valamint megtekinthetik a csoporttevékenységeket és a naplózási jelentéseket. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Vendég meghívója](#guest-inviter) | Meghívhat vendégfelhasználót a "Tagok meghívhat vendégfelhasználók" beállítástól függetlenül. | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Helpdesk Administrator](#helpdesk-administrator) | Új jelszavakat állíthat vissza a nem rendszergazdák és a segítő rendszergazdák számára. | 729827e3-9c14-49f7-b-9608f156bbb8 |
> | [Hibrid identitás-rendszergazda](#hybrid-identity-administrator) | Kezelheti az AD–Azure AD felhőalapú üzembe Azure AD Connect és összevonási beállításokat. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Insights-rendszergazda](#insights-administrator) | Rendszergazdai hozzáféréssel rendelkezik a Microsoft 365 Insights alkalmazásban. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Insights Business Leader](#insights-business-leader) | Az M365 Insights alkalmazással megtekintheti és megoszthatja az irányítópultokat és elemzéseket. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune-rendszergazda](#intune-administrator) | Az Intune termék minden aspektusát képes kezelni. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala-rendszergazda](#kaizala-administrator) | Kezelheti a Microsoft Kaizala beállításait. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Tudás-rendszergazda](#knowledge-administrator) | Konfigurálhatja a tudást, a tanulást és más intelligens funkciókat. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Licenc-rendszergazda](#license-administrator) | Kezelheti a felhasználók és csoportok terméklicenceit. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [Üzenetközpont adatvédelmi olvasó](#message-center-privacy-reader) | Csak az Office 365 biztonsági üzeneteit és frissítéseit Üzenetközpont olvashatja. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Üzenetközpont Olvasó](#message-center-reader) | Csak az Office 365-ben olvashatja a szervezet üzeneteit Üzenetközpont frissítéseket. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Modern kereskedelmi felhasználó](#modern-commerce-user) | Kezelheti egy vállalat, részleg vagy csapat kereskedelmi vásárlását. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Hálózati rendszergazda](#network-administrator) | Kezelheti a hálózati helyeket, és áttekintheti a Microsoft 365 szoftverszolgáltatás-alkalmazások vállalati hálózattervezési elemzéseit. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Office-alkalmazások rendszergazdája](#office-apps-administrator) | Kezelheti az Office-alkalmazások felhőszolgáltatásait, beleértve a szabályzat- és beállításkezelést, valamint az "újdonságok" funkciótartalom kiválasztásának, kijelölésének és közzétételének képességét a végfelhasználói eszközökön. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [1. szintű partnertámogatás](#partner-tier1-support) | Ne használja a - nem általános használatra szolgál. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [2. szintű partnertámogatás](#partner-tier2-support) | Ne használja a - nem általános használatra szolgál. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Jelszókezelő](#password-administrator) | Új jelszavakat állíthat vissza a nem rendszergazdák és a jelszó-rendszergazdák számára. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI rendszergazda](#power-bi-administrator) | A termék minden aspektusát képes Power BI kezelni. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform rendszergazda](#power-platform-administrator) | A Microsoft Dynamics 365, a PowerApps és a Microsoft Flow összes aspektusát létrehozhatja és kezelheti. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Nyomtató-rendszergazda](#printer-administrator) | A nyomtatók és nyomtató-összekötők minden aspektusát képes kezelni. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Nyomtató-technikus](#printer-technician) | Regisztrálhatja a nyomtatókat, és megszűkülheti a nyomtatók regisztrációját, és frissítheti a nyomtató állapotát. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Privileged Authentication Administrator](#privileged-authentication-administrator) | Bármely felhasználó (rendszergazda vagy nem rendszergazda) hitelesítési módszerének megtekintéséhez, beállításhoz és alaphelyzetbe állításhoz férhet hozzá. | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Kiemelt szerepkör rendszergazdája](#privileged-role-administrator) | Kezelheti az Azure AD-beli szerepkör-hozzárendeléseket és a szerepkör-hozzárendelések Privileged Identity Management. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Jelentések olvasója](#reports-reader) | Olvashatja a bejelentkezési és auditálási jelentéseket. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Rendszergazda keresése](#search-administrator) | A Microsoft Search-beállítások összes aspektusát létrehozhatja és kezelheti. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Keresésszerkesztő](#search-editor) | Létrehozhatja és kezelheti a szerkesztői tartalmakat, például a könyvjelzőket, a Q-t és az As-t, a helyeket és a padlósíkot. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Biztonsági rendszergazda](#security-administrator) | Olvashatja a biztonsági információkat és jelentéseket, és kezelheti a konfigurációt az Azure AD-ben és az Office 365-ben. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Biztonsági operátor](#security-operator) | Biztonsági eseményeket hoz létre és kezel. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Biztonsági olvasó](#security-reader) | Olvashatja az Azure AD és az Office 365 biztonsági információit és jelentéseit. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Szolgáltatástámogatási rendszergazda](#service-support-administrator) | Olvashatja a szolgáltatás állapotinformációját, és kezelheti a támogatási jegyeket. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [SharePoint-rendszergazda](#sharepoint-administrator) | A SharePoint szolgáltatás minden aspektusát képes kezelni. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Skype Vállalati verzió rendszergazdája](#skype-for-business-administrator) | A Skype Vállalati verzió termék minden aspektusát kezelheti. | 75941009-915a-4869-abe7-691bff18279e |
> | [Teams-rendszergazda](#teams-administrator) | Kezelheti a Microsoft Teams szolgáltatást. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Teams kommunikációs adminisztrátor](#teams-communications-administrator) | A Microsoft Teams szolgáltatásban kezelheti a hívási és értekezletek funkcióit. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Teams kommunikációs támogatási szakember](#teams-communications-support-engineer) | Speciális eszközökkel elháríthatja a Teams kommunikációs problémáit. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [A Teams kommunikációs támogatási szakembere](#teams-communications-support-specialist) | Alapszintű eszközökkel elháríthatja a Teamsben a kommunikációs problémákat. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Teams-eszközök rendszergazdája](#teams-devices-administrator) | A Teams-tanúsítvánnyal rendelkező eszközökön elvégezheti a felügyelethez kapcsolódó feladatokat. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Használati adatokat összegző jelentések olvasója](#usage-summary-reports-reader) | A Usage Analytics és a Productivity Score Microsoft 365 bérlői szintű összesítéseket láthatja. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Felhasználói rendszergazda](#user-administrator) | Kezelheti a felhasználók és csoportok minden aspektusát, beleértve a jelszavak visszaállítását korlátozott rendszergazdák számára. | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>alkalmazás-rendszergazda

Az ebben a szerepkörben a felhasználók a vállalati alkalmazások, alkalmazásregisztrációk és alkalmazásproxy-beállítások minden aspektusát létrehozhatják és kezelhetik. Vegye figyelembe, hogy az ehhez a szerepkörhöz rendelt felhasználók nem kerülnek tulajdonosként az új alkalmazásregisztrációk vagy vállalati alkalmazások létrehozásakor.

Ez a szerepkör a delegált engedélyek és az alkalmazásengedélyek beleegyezését is lehetővé teszi, az alkalmazásengedélyek kivételével mind a Microsoft Graph, mind az Azure AD Graph esetében.

> [!IMPORTANT]
> Ez a kivétel azt jelenti, hogy  továbbra is beleegyezhet az alkalmazásengedélyekbe más alkalmazásokhoz (például nem a Microsofttól vagy az Ön által regisztrált alkalmazásokhoz). Ezeket az _engedélyeket_ továbbra is kérheti az alkalmazásregisztráció részeként, de ezeknek az engedélyeknek a megadásához _(azaz_ hozzájárulásához) egy kiemeltebb rendszergazdára, például globális rendszergazdára van szükség.
>
>Ez a szerepkör lehetővé teszi az alkalmazás hitelesítő adatainak kezelését. A szerepkörhöz rendelt felhasználók hitelesítő adatokat adhatnak egy alkalmazáshoz, és ezeket a hitelesítő adatokat használják az alkalmazás identitásának megszemélyesítésére. Ha az alkalmazás identitása hozzáférést kapott egy erőforráshoz, például felhasználó vagy más objektumok létrehozásához vagy frissítéséhez, akkor a szerepkörhöz rendelt felhasználó végrehajthatja ezeket a műveleteket az alkalmazás megszemélyesítése közben. Az alkalmazás identitásának megszemélyesítése jogosultsági szint emelése lehet a felhasználó szerepkör-hozzárendelései által. Fontos tudni, hogy a felhasználók alkalmazás-rendszergazdai szerepkörhöz való hozzárendelése lehetőséget biztosít számukra az alkalmazás identitásának megszemélyesítésére.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/applications/create | Minden típusú alkalmazás létrehozása |
> | microsoft.directory/applications/delete | Az összes alkalmazástípus törlése |
> | microsoft.directory/applications/applicationProxy/read | Az alkalmazásproxy összes tulajdonságának olvasása |
> | microsoft.directory/applications/applicationProxy/update | Az alkalmazásproxy összes tulajdonságának frissítése |
> | microsoft.directory/applications/applicationProxyAuthentication/update | A hitelesítés frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/applicationProxySslCertificate/update | Ssl-tanúsítványbeállítások frissítése az alkalmazásproxyhoz |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | Alkalmazásproxy URL-beállításainak frissítése |
> | microsoft.directory/applications/appRoles/update | Az appRoles tulajdonság frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/audience/update | Az alkalmazások célközönség-tulajdonságának frissítése |
> | microsoft.directory/applications/authentication/update | A hitelesítés frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/basic/update | Az alkalmazások alapvető tulajdonságainak frissítése |
> | microsoft.directory/applications/credentials/update | Alkalmazás hitelesítő adatainak frissítése |
> | microsoft.directory/applications/owners/update | Alkalmazások tulajdonosainak frissítése |
> | microsoft.directory/applications/permissions/update | A felfedett engedélyek és a szükséges engedélyek frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/policies/update | Alkalmazások szabályzatának frissítése |
> | microsoft.directory/applications/verification/update | Az applicationsverification tulajdonság frissítése |
> | microsoft.directory/applications/synchronization/standard/read | Az alkalmazásobjektumhoz társított kiépítési beállítások olvasása |
> | microsoft.directory/applicationTemplates/példányos | Katalógusalkalmazások példányának létrehozása alkalmazássablonból |
> | microsoft.directory/auditLogs/allProperties/read | Az auditnaplók összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/connectors/create | Alkalmazásproxy-összekötők létrehozása |
> | microsoft.directory/connectors/allProperties/read | Az alkalmazásproxy-összekötők összes tulajdonságának olvasása |
> | microsoft.directory/connectorGroups/create | Alkalmazásproxy-összekötő csoportok létrehozása |
> | microsoft.directory/connectorGroups/delete | Alkalmazásproxy-összekötőcsoportok törlése |
> | microsoft.directory/connectorGroups/allProperties/read | Az alkalmazásproxy-összekötő csoportok összes tulajdonságának olvasása |
> | microsoft.directory/connectorGroups/allProperties/update | Az alkalmazásproxy-összekötő csoportok összes tulajdonságának frissítése |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0-engedélyengedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/applicationPolicies/create | Alkalmazás-szabályzatok létrehozása |
> | microsoft.directory/applicationPolicies/delete | Alkalmazás-szabályzatok törlése |
> | microsoft.directory/applicationPolicies/standard/read | Az alkalmazás-házirendek szabványos tulajdonságainak olvasása |
> | microsoft.directory/applicationPolicies/owners/read | Tulajdonosok olvasása az alkalmazás-szabályzatokkal |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Az objektumokra alkalmazott alkalmazás-szabályzatok listájának olvasása |
> | microsoft.directory/applicationPolicies/basic/update | Az alkalmazás-szabályzatok szabványos tulajdonságainak frissítése |
> | microsoft.directory/applicationPolicies/owners/update | Az alkalmazás-szabályzatok tulajdonosi tulajdonságának frissítése |
> | microsoft.directory/provisioningLogs/allProperties/read | Kiépítési naplók összes tulajdonságának olvasása |
> | microsoft.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása |
> | microsoft.directory/servicePrincipals/delete | Szolgáltatásnév törlése |
> | microsoft.directory/servicePrincipals/disable | Szolgáltatásnév letiltása |
> | microsoft.directory/servicePrincipals/enable | Szolgáltatásnevek engedélyezése |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Jelszó egyszeri bejelentkezési hitelesítő adatainak kezelése szolgáltatásnéven |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Titkos kulcsok és hitelesítő adatok alkalmazás-kiépítési adatainak kezelése |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Alkalmazás-kiépítési szinkronizálási feladatok elindításakor, újraindítása és szüneteltetése |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Alkalmazás-kiépítési szinkronizálási feladatok és séma létrehozása és kezelése |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Jelszó egyszeri bejelentkezési hitelesítő adatainak olvasása a szolgáltatásnéven |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Hozzájárulás megadása az alkalmazásengedélyek és a delegált engedélyek számára bármely felhasználó vagy az összes felhasználó nevében, kivéve a Microsoft Graph és az Azure AD Graph alkalmazásengedélyei esetén |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Szolgáltatásnév szerepkör-hozzárendelésének frissítése |
> | microsoft.directory/servicePrincipals/audience/update | A szolgáltatásnév célközönség-tulajdonságainak frissítése |
> | microsoft.directory/servicePrincipals/authentication/update | Szolgáltatásnév hitelesítési tulajdonságainak frissítése |
> | microsoft.directory/servicePrincipals/basic/update | Egyszerű szolgáltatásnév alaptulajdonságának frissítése |
> | microsoft.directory/servicePrincipals/credentials/update | Szolgáltatásnév hitelesítő adatainak frissítése |
> | microsoft.directory/servicePrincipals/owners/update | Szolgáltatásnév-tulajdonosok frissítése |
> | microsoft.directory/servicePrincipals/permissions/update | Szolgáltatásnév-engedélyek frissítése |
> | microsoft.directory/servicePrincipals/policies/update | Szolgáltatásnév-szabályzatok frissítése |
> | microsoft.directory/servicePrincipals/tag/update | A szolgáltatásnév címketulajdonságának frissítése |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | A szolgáltatásnévhez társított kiépítési beállítások olvasása |
> | microsoft.directory/signInReports/allProperties/read | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="application-developer"></a>Alkalmazásfejlesztő

Az ebben a szerepkörben a felhasználók akkor hozhatnak létre alkalmazásregisztrációkat, ha a "Users can register applications" (A felhasználók regisztrálnak alkalmazásokat) beállítás nemre van állítva. Ez a szerepkör a saját nevében történő hozzájáruláshoz is engedélyt ad, ha "A felhasználók engedélyt kaphatnak arra, hogy az alkalmazások a nevükben hozzáférjenek a vállalati adatokhoz" beállítás Nem beállításra van állítva. Az új alkalmazásregisztrációk vagy vállalati alkalmazások létrehozásakor a rendszer tulajdonosként hozzáadja a szerepkörhöz rendelt felhasználókat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/applications/createAsOwner | Hozza létre az összes alkalmazástípust, és a létrehozó lesz az első tulajdonos |
> | microsoft.directory/appRoleAssignments/createAsOwner | Alkalmazás-szerepkör-hozzárendelések létrehozása a létrehozóval az első tulajdonosként |
> | microsoft.directory/oAuth2PermissionGrants/createAsOwner | OAuth 2.0-engedélyengedélyek létrehozása létrehozóként |
> | microsoft.directory/servicePrincipals/createAsOwner | Szolgáltatásnév létrehozása, első tulajdonosként a létrehozóval |

## <a name="attack-payload-author"></a>Támadás hasznos terhelésének szerzője

Az ebbe a szerepkörbe betöltő felhasználók létrehozhatnak támadási hasznos okat, de ténylegesen nem indítják el vagy ütemezik azokat. Ezután a támadási hasznos adatok a bérlő összes rendszergazdája számára elérhetők, akik szimuláció létrehozásához használhatja őket.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Támadási hasznos adatok létrehozása és kezelése a támadási szimulátorban |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Jelentések olvasása a támadásszimulációs válaszokról és a kapcsolódó betanításról |

## <a name="attack-simulation-administrator"></a>Támadásszimuláció rendszergazdája

Az ebben a szerepkörben a felhasználók létrehozhatják és kezelhetik a támadásszimuláció létrehozásának, a szimuláció indításának/ütemezésének, valamint a szimulációs eredmények áttekintésének minden aspektusát. Ennek a szerepkörnek a tagjai a bérlő összes szimulációja számára biztosítják ezt a hozzáférést.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Támadási hasznos adatok létrehozása és kezelése a támadásszimulátorban |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Jelentések olvasása a támadásszimulációs válaszokról és a kapcsolódó betanításról |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Támadásszimulációs sablonok létrehozása és kezelése a támadásszimulátorban |

## <a name="authentication-administrator"></a>Hitelesítési rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók bármilyen hitelesítési módszert (beleértve a jelszavakat is) állíthatnak be vagy állíthatnak vissza nem rendszergazdák és bizonyos szerepkörök számára. A hitelesítési rendszergazdák megkövetelhetik a nem rendszergazdai jogosultságokkal rendelkező vagy egyes szerepkörökhöz hozzárendelt felhasználóktól, hogy regisztrálják újra a meglévő, nem jelszóval rendelkező hitelesítő adatokat (például MFA vagy FIDO), és visszavonhatják az **MFA-t** az eszközön, amely a következő bejelentkezéskor kérni fogja az MFA-t. Azon szerepkörök listáját, amelyekhez a hitelesítési rendszergazda olvashatja vagy frissítheti a hitelesítési módszereket, lásd: Jelszó-visszaállítási [engedélyek.](#password-reset-permissions)

A [Privileged Authentication rendszergazdai](#privileged-authentication-administrator) szerepkör engedéllyel rendelkezik az újraregisztráció és a többtényezős hitelesítés minden felhasználóra való kényszerítéhez.

A [hitelesítési házirend rendszergazdai](#authentication-policy-administrator) szerepköre engedélyekkel rendelkezik a bérlő hitelesítési módszerére vonatkozó házirend beállításhoz, amely meghatározza, hogy az egyes felhasználók milyen módszereket regisztrálnak és használhatnak.

| Szerepkör | A felhasználók hitelesítési módszereinek kezelése | Felhasználónkénti MFA kezelése | MFA-beállítások kezelése | Hitelesítési módszer szabályzatának kezelése | Jelszóvédelmi szabályzat kezelése |
| ---- | ---- | ---- | ---- | ---- | ---- |
| hitelesítési rendszergazda | Igen egyes felhasználók esetén (lásd fent) | Igen egyes felhasználók esetén (lásd fent) | Nem | Nem | Nem |
| Privileged Authentication Administrator| Igen minden felhasználó számára | Igen minden felhasználó számára | Nem | Nem | Nem |
| Hitelesítési házirend rendszergazdája | Nem |Nem | Igen | Igen | Yes |

> [!IMPORTANT]
> Az ezzel a szerepkörhöz hozzáférő felhasználók módosíthatják azon személyek hitelesítő adatait, akik bizalmas vagy privát adatokhoz, vagy kritikus konfigurációhoz férhetnek hozzá az Azure Active Directory. A felhasználó hitelesítő adatainak módosítása azt a képességet jelentheti, hogy a felhasználó identitását és engedélyét feltételezi. Például:
>
>* Az alkalmazásregisztráció és a vállalati alkalmazástulajdonosok, akik kezelhetik a sajátjukhoz használt alkalmazások hitelesítő adatait. Előfordulhat, hogy ezek az alkalmazások emelt szintű engedélyekkel rendelkezik az Azure AD-ban, és máshol nem kapnak jogosultságot a hitelesítési rendszergazdáknak. Ezen az útvonalon a hitelesítési rendszergazda felteheti az alkalmazás tulajdonosának identitását, majd az alkalmazás hitelesítő adatainak frissítésével tovább feltételezheti egy rendszer-jogosultságú alkalmazás identitását.
>* Azure-előfizetések tulajdonosai, akik bizalmas vagy privát adatokhoz vagy kritikus konfigurációhoz férhetnek hozzá az Azure-ban.
>* Biztonsági csoport és Microsoft 365 csoporttulajdonosok, akik kezelhetik a csoporttagságot. Ezek a csoportok hozzáférést adhatnak az Azure AD-beli és máshol bizalmas vagy privát információkhoz vagy kritikus konfigurációkhoz.
>* Rendszergazdák az Azure AD-n kívüli más szolgáltatásokban, például az Exchange Online-ban, az Office Biztonsági és megfelelőségi központban és az emberi erőforrások rendszerében.
>* Nem rendszergazdák, például vezetők, jogi személyek és emberi erőforrások alkalmazottai, akik bizalmas vagy személyes adatokhoz férnek hozzá.

> [!IMPORTANT]
> Ez a szerepkör nem tudja kezelni az MFA-beállításokat az örökölt MFA felügyeleti portálon vagy a HardverES OATH tokenekkel. Ugyanezek a függvények az Azure AD [PowerShell-modul Set-MsolUser](/powershell/module/msonline/set-msoluser) parancsmag használatával is elérhetőek.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | A felhasználói frissítési jogkivonatok érvénytelenítésének kényszerítése a kijelentkezésben |
> | microsoft.directory/users/strongAuthentication/update | A felhasználók erős hitelesítési tulajdonságának frissítése |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="authentication-policy-administrator"></a>Hitelesítési házirend rendszergazdája

Az ezzel a szerepkörrel kapcsolatos felhasználók konfigurálják a hitelesítési módszerek szabályzatát, a bérlői szintű MFA-beállításokat és a jelszóvédelmi szabályzatot. Ez a szerepkör engedélyezi a Jelszóvédelem beállításainak kezelését: intelligens zárolási konfigurációk és az egyéni tiltott jelszavak listájának frissítése.

A [hitelesítési rendszergazda](#authentication-administrator) és a [Privileged Authentication](#privileged-authentication-administrator) rendszergazdai szerepköre engedéllyel rendelkezik a regisztrált hitelesítési módszerek kezeléséhez a felhasználókon, és minden felhasználóra kényszerítheti az újraregisztrációt és a többtényezős hitelesítést.

| Szerepkör | A felhasználó hitelesítési módszereinek kezelése | Felhasználónkénti MFA kezelése | MFA-beállítások kezelése | Hitelesítési módszer szabályzatának kezelése | Jelszóvédelmi szabályzat kezelése |
| ---- | ---- | ---- | ---- | ---- | ---- |
| hitelesítési rendszergazda | Igen egyes felhasználók esetén (lásd fent) | Igen egyes felhasználók esetén (lásd fent) | Nem | Nem | Nem |
| Privileged Authentication Administrator| Igen minden felhasználó számára | Igen minden felhasználó számára | Nem | Nem | Nem |
| Hitelesítési házirend rendszergazdája | Nem | Nem | Igen | Igen | Yes |

> [!IMPORTANT]
> Ez a szerepkör nem tudja kezelni az MFA-beállításokat az örökölt MFA felügyeleti portálon vagy a Hardver OATH tokenekkel.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/organization/strongAuthentication/update | Vállalat erős hitelesítési tulajdonságainak frissítése |
> | microsoft.directory/userCredentialPolicies/create | Hitelesítőadat-szabályzatok létrehozása felhasználók számára |
> | microsoft.directory/userCredentialPolicies/delete | Hitelesítőadat-szabályzatok törlése felhasználók számára |
> | microsoft.directory/userCredentialPolicies/standard/read | Hitelesítő házirendek szabványos tulajdonságainak olvasása felhasználók számára |
> | microsoft.directory/userCredentialPolicies/owners/read | A hitelesítő házirendek tulajdonosainak olvasása felhasználók számára |
> | microsoft.directory/userCredentialPolicies/policyAppliedTo/read | A policy.applies olvasásaNavigációs hivatkozás |
> | microsoft.directory/userCredentialPolicies/basic/update | Alapszintű szabályzatok frissítése felhasználók számára |
> | microsoft.directory/userCredentialPolicies/owners/update | Hitelesítő házirendek tulajdonosainak frissítése felhasználók számára |
> | microsoft.directory/userCredentialPolicies/tenantDefault/update | A policy.isOrganizationDefault tulajdonság frissítése |

## <a name="azure-ad-joined-device-local-administrator"></a>Azure AD-hez csatlakozott eszköz helyi rendszergazdája

Ez a szerepkör csak további helyi rendszergazdaként érhető el az [Eszközbeállítások lapon.](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/) Az ezzel a szerepkört használó felhasználók helyi számítógép-rendszergazdákká válnak az Windows 10-hoz Azure Active Directory. Nem képesek eszközobjektumokat kezelni a Azure Active Directory.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/groupSettings/standard/read | Alapszintű tulajdonságok olvasása a csoportbeállításokról |
> | microsoft.directory/groupSettingTemplates/standard/read | Alapszintű tulajdonságok olvasása csoportbeállítás-sablonokon |

## <a name="azure-devops-administrator"></a>Azure DevOps-rendszergazda

Az ezzel a szerepkörű felhasználók kezelhetik az Azure DevOps-szabályzatot, hogy az új Azure DevOps-szervezetek létrehozását konfigurálható felhasználókra vagy csoportokra korlátozzák. Az ebben a szerepkörben a felhasználók bármely Olyan Azure DevOps-szervezeten keresztül kezelhetik ezt a szabályzatot, amelyet a vállalat Azure AD-szervezete szolgál ki. Ez a szerepkör nem biztosít más Azure DevOps-specifikus engedélyeket (például projektgyűjtemény-rendszergazdákat) a vállalat Azure AD-szervezete által nyújtott Azure DevOps-szervezetek egyikében sem.

Az ebben a szerepkörben a felhasználók minden vállalati Azure DevOps-szabályzatot kezelni tudnak.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.devOps/allEntities/allTasks | Az Azure DevOps olvasása és konfigurálása |

## <a name="azure-information-protection-administrator"></a>Azure Information Protection rendszergazda

Az ezzel a szerepkörsel rendelkező felhasználók a Azure Information Protection rendelkezik. Ez a szerepkör lehetővé teszi címkék konfigurálását a Azure Information Protection szabályzathoz, a védelmi sablonok kezeléséhez és a védelem aktiválásához. Ez a szerepkör nem biztosít engedélyeket az Identity Protection Centerben, a Privileged Identity Management, Microsoft 365 Service Health vagy az Office 365 Security & Compliance Centerben.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.informationProtection/allEntities/allTasks | Az alkalmazás minden aspektusának Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="b2c-ief-keyset-administrator"></a>B2C IEF-kulcskészlet rendszergazdája

A felhasználó létrehozhatja és kezelheti a jogkivonatok titkosításához, a jogkivonat-aláíráshoz és a titkosításhoz/visszafejtéséhez szükséges szabályzatkulcsokat és titkos kódokat. Azáltal, hogy új kulcsokat ad hozzá a meglévő kulcstárolókhoz, ez a korlátozott rendszergazda szükség szerint, a meglévő alkalmazások befolyásolása nélkül átveheti a titkos kulcsokat. Ez a felhasználó a titkos kulcsok teljes tartalmát és lejárati dátumait a létrehozásuk után is láthatja.

> [!IMPORTANT]
> Ez egy bizalmas szerepkör. A kulcskészlet-rendszergazdai szerepkört gondosan kell ellenőrizni, és körültekintően kell hozzárendelni az éles üzem előtti és éles környezetben.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Az engedélyezési házirendek összes tulajdonságának olvasása és frissítése |

## <a name="b2c-ief-policy-administrator"></a>B2C IEF házirend-rendszergazda

Az ebben a szerepkörben a felhasználók létrehozhatják, olvashatják, frissítheti és törölhetik az Azure AD B2C összes egyéni szabályzatát, így teljes körűen szabályozni Identity Experience Framework szabályzatokat a megfelelő Azure AD B2C szervezetben. A szabályzatok szerkesztésével ez a felhasználó közvetlen összevonást létesíthet külső identitásszolgáltatókkal, módosíthatja a címtársémát, módosíthatja a felhasználó által elérhető összes tartalmat (HTML, CSS, JavaScript), módosíthatja a követelményeket a hitelesítés létrehozására, új felhasználókat hozhat létre, felhasználói adatokat küldhet külső rendszerekre, beleértve a teljes migrálást, és szerkesztheti az összes felhasználói adatot, beleértve a bizalmas mezőket, például jelszavakat és telefonszámokat. Ezzel szemben ez a szerepkör nem módosíthatja a titkosítási kulcsokat, és nem szerkesztheti a szervezet összevonáshoz használt titkos kulcsait.

> [!IMPORTANT]
> A B2 IEF házirend-rendszergazda rendkívül bizalmas szerepkör, amelyet nagyon korlátozottan kell hozzárendelni az éles környezetben rendelkező szervezetekhez. Ezeknek a felhasználóknak a tevékenységeit érdemes szorosan naplólni, különösen az éles környezetben található szervezeteknél.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Kulcskészletek olvasása és konfigurálása a Azure Active Directory B2C |

## <a name="billing-administrator"></a>Számlázási adminisztrátor

Lebonyolítja a vásárlásokat, kezeli az előfizetéseket és a támogatási jegyeket, valamint figyeli a szolgáltatás állapotát.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/organization/basic/update | Alapvető tulajdonságok frissítése a szervezeten |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.commerce.billing/allEntities/allTasks | Az Office 365-számlázás összes aspektusának kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="cloud-application-administrator"></a>Felhőalkalmazás-rendszergazda

Az ebben a szerepkörben a felhasználók ugyanazokkal az engedélyekkel rendelkezik, mint az Alkalmazás-rendszergazda szerepkör, kivéve az alkalmazásproxy kezelését. Ez a szerepkör lehetővé teszi a vállalati alkalmazások és alkalmazásregisztrációk összes aspektusának létrehozására és kezelésére. Az ehhez a szerepkörhöz rendelt felhasználók nem kerülnek tulajdonosként az új alkalmazásregisztrációk vagy vállalati alkalmazások létrehozásakor.

Ez a szerepkör a delegált engedélyek és az alkalmazásengedélyek hozzájárulását is lehetővé teszi, az alkalmazásengedélyek kivételével mind a Microsoft Graph, mind az Azure AD Graph esetében.

> [!IMPORTANT]
> Ez a kivétel azt jelenti, hogy  továbbra is beleegyezhet az alkalmazásengedélyekbe más alkalmazások (például a nem Microsofttól vagy az Ön által regisztrált alkalmazások) esetében. Ezeket az _engedélyeket_ továbbra is kérheti az alkalmazásregisztráció részeként, de ezeknek az engedélyeknek a megadásához _(azaz_ hozzájárulásához) egy kiemeltebb rendszergazdára, például globális rendszergazdára van szükség.
>
>Ez a szerepkör lehetővé teszi az alkalmazás hitelesítő adatainak kezelését. A szerepkörhöz rendelt felhasználók hitelesítő adatokat adhatnak egy alkalmazáshoz, és ezeket a hitelesítő adatokat használják az alkalmazás identitásának megszemélyesítésére. Ha az alkalmazás identitása hozzáférést kapott egy erőforráshoz, például felhasználó vagy más objektumok létrehozásához vagy frissítéséhez, akkor a szerepkörhöz rendelt felhasználó végrehajthatja ezeket a műveleteket az alkalmazás megszemélyesítése közben. Az alkalmazás identitásának megszemélyesítése jogosultsági szint emelése lehet a felhasználó szerepkör-hozzárendelései által. Fontos tudni, hogy a felhasználók alkalmazás-rendszergazdai szerepkörhöz való hozzárendelése lehetőséget biztosít számukra az alkalmazás identitásának megszemélyesítésére.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/applications/create | Minden típusú alkalmazás létrehozása |
> | microsoft.directory/applications/delete | Az összes alkalmazástípus törlése |
> | microsoft.directory/applications/appRoles/update | Az appRoles tulajdonság frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/audience/update | Az alkalmazások célközönség-tulajdonságának frissítése |
> | microsoft.directory/applications/authentication/update | A hitelesítés frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/basic/update | Az alkalmazások alapvető tulajdonságainak frissítése |
> | microsoft.directory/applications/credentials/update | Alkalmazás hitelesítő adatainak frissítése |
> | microsoft.directory/applications/owners/update | Alkalmazások tulajdonosainak frissítése |
> | microsoft.directory/applications/permissions/update | A felfedett engedélyek és a szükséges engedélyek frissítése minden alkalmazástípus esetében |
> | microsoft.directory/applications/policies/update | Alkalmazások szabályzatának frissítése |
> | microsoft.directory/applications/verification/update | Az applicationsverification tulajdonság frissítése |
> | microsoft.directory/applications/synchronization/standard/read | Az alkalmazásobjektumhoz társított kiépítési beállítások olvasása |
> | microsoft.directory/applicationTemplates/példányos | Katalógusalkalmazások példányának létrehozása alkalmazássablonból |
> | microsoft.directory/auditLogs/allProperties/read | Az auditnaplók összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0-engedélyengedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/applicationPolicies/create | Alkalmazás-szabályzatok létrehozása |
> | microsoft.directory/applicationPolicies/delete | Alkalmazás-szabályzatok törlése |
> | microsoft.directory/applicationPolicies/standard/read | Az alkalmazás-házirendek szabványos tulajdonságainak olvasása |
> | microsoft.directory/applicationPolicies/owners/read | Tulajdonosok olvasása az alkalmazás-szabályzatokkal |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Az objektumokra alkalmazott alkalmazás-szabályzatok listájának olvasása |
> | microsoft.directory/applicationPolicies/basic/update | Az alkalmazás-házirendek szabványos tulajdonságainak frissítése |
> | microsoft.directory/applicationPolicies/owners/update | Az alkalmazás-szabályzatok tulajdonosi tulajdonságának frissítése |
> | microsoft.directory/provisioningLogs/allProperties/read | A kiépítési naplók összes tulajdonságának olvasása |
> | microsoft.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása |
> | microsoft.directory/servicePrincipals/delete | Szolgáltatásnév törlése |
> | microsoft.directory/servicePrincipals/disable | Szolgáltatásnév letiltása |
> | microsoft.directory/servicePrincipals/enable | Szolgáltatásnevek engedélyezése |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Jelszó egyszeri bejelentkezési hitelesítő adatainak kezelése szolgáltatásnéven |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Titkos kulcsok és hitelesítő adatok alkalmazás-kiépítési adatainak kezelése |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Alkalmazás-kiépítési szinkronizálási feladatok elindításakor, újraindítása és szüneteltetése |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Alkalmazás-kiépítési szinkronizálási feladatok és séma létrehozása és kezelése |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Jelszó egyszeri bejelentkezési hitelesítő adatainak olvasása szolgáltatásnéven |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Jóváhagyás megadása az alkalmazásengedélyek és a delegált engedélyek számára bármely felhasználó vagy az összes felhasználó nevében, kivéve a Microsoft Graph és az Azure AD Graph alkalmazásengedélyei kivételével |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Szolgáltatásnév szerepkör-hozzárendelésének frissítése |
> | microsoft.directory/servicePrincipals/audience/update | Célközönség tulajdonságainak frissítése szolgáltatásnéven |
> | microsoft.directory/servicePrincipals/authentication/update | Szolgáltatásnév hitelesítési tulajdonságainak frissítése |
> | microsoft.directory/servicePrincipals/basic/update | Egyszerű szolgáltatásnév alaptulajdonságának frissítése |
> | microsoft.directory/servicePrincipals/credentials/update | Szolgáltatásnév hitelesítő adatainak frissítése |
> | microsoft.directory/servicePrincipals/owners/update | Szolgáltatásnév-tulajdonosok frissítése |
> | microsoft.directory/servicePrincipals/permissions/update | Szolgáltatásnév-engedélyek frissítése |
> | microsoft.directory/servicePrincipals/policies/update | Szolgáltatásnév-szabályzatok frissítése |
> | microsoft.directory/servicePrincipals/tag/update | A szolgáltatásnév címketulajdonságának frissítése |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | A szolgáltatásnévhez társított olvasási kiépítési beállítások |
> | microsoft.directory/signInReports/allProperties/read | A bejelentkezési jelentések minden tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="cloud-device-administrator"></a>Felhőalapú eszköz rendszergazdája

Az ebben a szerepkörben található felhasználók engedélyezhetik, letilthatják és törölhetik az Eszközöket az Azure AD-ban, és olvashatják Windows 10 BitLocker-kulcsokat (ha vannak) a Azure Portal. A szerepkör nem biztosít engedélyeket az eszköz egyéb tulajdonságainak kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Az auditnaplók összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metaadatok és -kulcs olvasása az eszközökön |
> | microsoft.directory/devices/delete | Eszközök törlése az Azure AD-ból |
> | microsoft.directory/devices/disable | Eszközök letiltása az Azure AD-ban |
> | microsoft.directory/devices/enable | Eszközök engedélyezése az Azure AD-ban |
> | microsoft.directory/devices/extensionAttributes/update | Frissítse az devices.extensionAttributes tulajdonság összes értékét |
> | microsoft.directory/deviceManagementPolicies/standard/read | Az eszközkezelési alkalmazás-házirendek szabványos tulajdonságainak olvasása |
> | microsoft.directory/deviceManagementPolicies/basic/update | Alapszintű tulajdonságok frissítése az eszközkezelési alkalmazás-szabályzatokkal |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Az eszközregisztrációs szabályzatok szabványos tulajdonságainak olvasása |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Az eszközregisztrációs szabályzatok alapszintű tulajdonságainak frissítése |
> | microsoft.directory/signInReports/allProperties/read | A bejelentkezési jelentések minden tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |

## <a name="compliance-administrator"></a>Megfelelőségi rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók a megfelelőségi funkciók kezeléséhez szükséges engedélyekkel a Microsoft 365 Megfelelőségi központ, Microsoft 365 Felügyeleti központ, az Azure és az Office 365 Biztonsági & megfelelőségi központban. A hozzárendelt felhasználók az Exchange felügyeleti központban és a Teamsben található összes funkciót is kezelhetik& Skype Vállalati verzió felügyeleti központját, és támogatási jegyeket hozhatnak létre az Azure-hoz és a Microsoft 365. További információt az About [Microsoft 365 admin roles (Rendszergazdai szerepkörök Microsoft 365) című témakörben található.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

In | A(nak)
----- | ----------
[Microsoft 365 Megfelelőségi központ](https://protection.office.com) | A szervezeti adatok védelme és kezelése a Microsoft 365 szolgáltatásokban<br>Megfelelőségi riasztások kezelése
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | A szervezet szabályozási megfelelőségi tevékenységeinek nyomon követése, hozzárendelése és ellenőrzése
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Adatirányítás kezelése<br>Jogi és adatvizsgálatot végez<br>Adat tulajdonosi kérelem kezelése<br><br>Ez a szerepkör ugyanazokkal [](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) az engedélyekkel rendelkezik, mint a Megfelelőségi rendszergazda szerepkörcsoport az Office 365 Biztonsági & Megfelelőségi központ szerepköralapú hozzáférés-vezérlésében.
[megkezdése](/intune/role-based-access-control) | Az összes megtekintése Intune naplózási adatainak naplózása
[Cloud App Security](/cloud-app-security/manage-admins) | Csak olvasási engedélyekkel rendelkezik, és képes a riasztások kezelésére<br>Fájl-szabályzatokat hozhat létre és módosíthat, valamint fájlirányítási műveleteket engedélyezhet<br>Megtekintheti az összes beépített jelentést a adatkezelés

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.directory/entitlementManagement/allProperties/read | Az Összes tulajdonság olvasása az Azure AD-jogosultságkezelésben |
> | microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 Compliance Manager összes aspektusának kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="compliance-data-administrator"></a>Megfelelőségiadat-rendszergazda

Az ezzel a szerepkörsel rendelkező felhasználók engedéllyel rendelkezik az adatok nyomon követéséhez a Microsoft 365 Megfelelőségi központ, Microsoft 365 Felügyeleti központ és az Azure-ban. A felhasználók az Exchange felügyeleti központban, a Compliance Managerben és a Teamsben & Skype Vállalati verzió felügyeleti központban is nyomon követhetik a megfelelőségi adatokat, és támogatási jegyeket hozhatnak létre az Azure-hoz és Microsoft 365. [Ez a dokumentáció](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) részletesen be rendelkezik a megfelelőségi rendszergazda és a megfelelőségiadat-rendszergazda közötti különbségekről.

In | A(nak)
----- | ----------
[Microsoft 365 Megfelelőségi központ](https://protection.office.com) | Megfelelőséghez kapcsolódó szabályzatok figyelése Microsoft 365 szolgáltatásokban<br>Megfelelőségi riasztások kezelése
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | A szervezet szabályozási megfelelőségi tevékenységeinek nyomon követése, hozzárendelése és ellenőrzése
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Adatirányítás kezelése<br>Jogi és adatvizsgálatot végez<br>Adat tulajdonosi kérelem kezelése<br><br>Ez a szerepkör ugyanazokkal az engedélyekkel rendelkezik, mint a Megfelelőségi adatok rendszergazdai [szerepkörcsoportja](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) az Office 365 Biztonsági & Megfelelőségi központ szerepköralapú hozzáférés-vezérlésében.
[megkezdése](/intune/role-based-access-control) | Az összes megtekintése Intune naplózási adatai
[Cloud App Security](/cloud-app-security/manage-admins) | Csak olvasási engedélyekkel rendelkezik, és képes a riasztások kezelésére<br>Létrehozhat és módosíthat fájl-szabályzatokat, és engedélyezheti a fájlirányítási műveleteket<br>Megtekintheti az összes beépített jelentést a adatkezelés

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése a Microsoft Cloud App Security |
> | microsoft.azure.informationProtection/allEntities/allTasks | Az alkalmazás minden aspektusának Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 Compliance Manager összes aspektusának kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="conditional-access-administrator"></a>Feltételes hozzáférés rendszergazdája

Az ezzel a szerepkörhöz hozzáférő felhasználók a feltételes Azure Active Directory beállításokat is kezelhetik.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/conditionalAccessPolicies/create | Feltételes hozzáférési szabályzatok létrehozása |
> | microsoft.directory/conditionalAccessPolicies/delete | Feltételes hozzáférési szabályzatok törlése |
> | microsoft.directory/conditionalAccessPolicies/standard/read | A szabályzatok feltételes hozzáférésének olvasása |
> | microsoft.directory/conditionalAccessPolicies/owners/read | A feltételes hozzáférési szabályzatok tulajdonosainak olvasása |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Olvassa el a feltételes hozzáférési szabályzatok "alkalmazott" tulajdonságát |
> | microsoft.directory/conditionalAccessPolicies/basic/update | A feltételes hozzáférési szabályzatok alapszintű tulajdonságainak frissítése |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Feltételes hozzáférési szabályzatok tulajdonosainak frissítése |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | Az alapértelmezett bérlő frissítése a feltételes hozzáférési szabályzatok számára |
> | microsoft.directory/crossTenantAccessPolicies/create | Bérlők közötti hozzáférési szabályzatok létrehozása |
> | microsoft.directory/crossTenantAccessPolicies/delete | Bérlők közötti hozzáférési szabályzatok törlése |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | A bérlők közötti hozzáférési szabályzatok alapvető tulajdonságainak olvasása |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | Bérlők közötti hozzáférési szabályzatok tulajdonosainak olvasása |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | A több-bérlős hozzáférési szabályzatok policyAppliedTo tulajdonságának olvasása |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | Bérlők közötti hozzáférési szabályzatok alapvető tulajdonságainak frissítése |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | Bérlők közötti hozzáférési szabályzatok tulajdonosainak frissítése |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | Az alapértelmezett bérlő frissítése a bérlők közötti hozzáférési szabályzatok számára |

## <a name="customer-lockbox-access-approver"></a>Ügyfélstűdi hozzáférés-jóváhagyó

Kezeli [Ügyfélszéf kérelmeket](/office365/admin/manage/customer-lockbox-requests) a szervezetben. E-mail-értesítéseket kapnak a Ügyfélszéf kérelmekről, és jóváhagyhatják és elutasíthatják a Microsoft 365 Felügyeleti központ. Be- és kikapcsolhatja Ügyfélszéf funkciót. Csak a globális rendszergazdák állíthatják alaphelyzetbe a szerepkörhöz rendelt személyek jelszavát.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | Az alkalmazás minden aspektusának Ügyfélszéf |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="desktop-analytics-administrator"></a>Asztali elemzés rendszergazda

Az ebben a szerepkörben a felhasználók kezelhetik a Asztali elemzés szolgáltatást. Ez magában foglalja az eszközleltár megtekintésének, az üzembe helyezési tervek létrehozásának, valamint az üzembe helyezés és az állapot megtekintésének képességét.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | A Asztali elemzés |

## <a name="directory-readers"></a>Könyvtárolvasók

Az ebben a szerepkörben található felhasználók olvashatják az alapszintű címtáradatokat. Ezt a szerepkört a következőhöz kell használni:

* Adott vendégfelhasználók számára biztosít olvasási hozzáférést ahelyett, hogy minden vendégfelhasználónak ad hozzáférést.
* Nem rendszergazdai felhasználók adott halmazának hozzáférésének megadása a Azure Portal ha "Az Azure AD-portálhoz való hozzáférés korlátozása csak rendszergazdákra" beállítás "Igen" van beállítva.
* Hozzáférés megadása a szolgáltatásnévnek ahhoz a könyvtárhoz, ahol a Directory.Read.All nem választható.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/administrativeUnits/standard/read | Felügyeleti egységek alapvető tulajdonságainak olvasása |
> | microsoft.directory/administrativeUnits/members/read | A felügyeleti egységek tagjainak olvasása |
> | microsoft.directory/applications/standard/read | Az alkalmazások szabványos tulajdonságainak olvasása |
> | microsoft.directory/applications/owners/read | Alkalmazások tulajdonosainak olvasása |
> | microsoft.directory/applications/policies/read | Alkalmazások házirendeinek olvasása |
> | microsoft.directory/contacts/standard/read | Alapszintű tulajdonságok olvasása a kapcsolattartókról az Azure AD-ban |
> | microsoft.directory/contacts/memberOf/read | Az Azure AD összes kapcsolattartója csoporttagságának olvasása |
> | microsoft.directory/contracts/standard/read | A partnerszerződések alapszintű tulajdonságainak olvasása |
> | microsoft.directory/devices/standard/read | Alapvető tulajdonságok olvasása az eszközökön |
> | microsoft.directory/devices/memberOf/read | Eszköztagság olvasása |
> | microsoft.directory/devices/registeredOwners/read | Eszközök regisztrált tulajdonosainak olvasása |
> | microsoft.directory/devices/registeredUsers/read | Az eszközök regisztrált felhasználóinak olvasása |
> | microsoft.directory/directoryRoles/standard/read | Alapszintű tulajdonságok frissítése az Azure AD-szerepkörökben |
> | microsoft.directory/directoryRoles/eligibleMembers/read | Az Azure AD-szerepkörök jogosult tagjainak olvasása |
> | microsoft.directory/directoryRoles/members/read | Az Azure AD-szerepkörök összes tagjának olvasása |
> | microsoft.directory/domains/standard/read | Alapvető tulajdonságok olvasása tartományokon |
> | microsoft.directory/groups/standard/read | Alapszintű tulajdonságok olvasása csoportokon |
> | microsoft.directory/groups/appRoleAssignments/read | Csoportok alkalmazásszerepk szerepkör-hozzárendelésének olvasása |
> | microsoft.directory/groups/memberOf/read | Olvassa el, hogy mely csoportok tagjai az Azure AD-nek |
> | microsoft.directory/groups/members/read | Csoportok tagjainak olvasása |
> | microsoft.directory/groups/owners/read | Csoportok tulajdonosainak olvasása |
> | microsoft.directory/groups/settings/read | Csoportok beállításainak olvasása |
> | microsoft.directory/groupSettings/standard/read | Alapszintű tulajdonságok olvasása a csoportbeállításokban |
> | microsoft.directory/groupSettingTemplates/standard/read | Alapszintű tulajdonságok olvasása csoportbeállítási sablonokon |
> | microsoft.directory/oAuth2PermissionGrants/standard/read | OAuth 2.0-engedélyengedélyek alapszintű tulajdonságainak olvasása |
> | microsoft.directory/organization/standard/read | A szervezet alapvető tulajdonságainak olvasása |
> | microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Megbízható hitelesítésszolgáltató olvasása jelszó nélküli hitelesítéshez |
> | microsoft.directory/applicationPolicies/standard/read | Az alkalmazás-házirendek szabványos tulajdonságainak olvasása |
> | microsoft.directory/roleAssignments/standard/read | A szerepkör-hozzárendelések alapszintű tulajdonságainak olvasása |
> | microsoft.directory/roleDefinitions/standard/read | A szerepkör-definíciók alapszintű tulajdonságainak olvasása |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Szolgáltatásnév szerepkör-hozzárendelésének olvasása |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Szolgáltatásnévhez rendelt szerepkör-hozzárendelések olvasása |
> | microsoft.directory/servicePrincipals/standard/read | Szolgáltatásnév alapvető tulajdonságainak olvasása |
> | microsoft.directory/servicePrincipals/memberOf/read | Csoporttagság olvasása szolgáltatástagságokkal |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Delegált engedélyengedélyek olvasása a szolgáltatásnévhez |
> | microsoft.directory/servicePrincipals/owners/read | Szolgáltatásnév tulajdonosának olvasása |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Szolgáltatásnév tulajdonában lévő objektumok olvasása |
> | microsoft.directory/servicePrincipals/policies/read | Szolgáltatásnév-házirendek olvasása |
> | microsoft.directory/subscribedSkus/standard/read | Az előfizetések alapszintű tulajdonságainak olvasása |
> | microsoft.directory/users/standard/read | Alapszintű tulajdonságok olvasása a felhasználókon |
> | microsoft.directory/users/appRoleAssignments/read | A felhasználók alkalmazás-szerepkör-hozzárendelésének olvasása |
> | microsoft.directory/users/directReports/read | A felhasználók közvetlen jelentésének olvasása |
> | microsoft.directory/users/manager/read | A felhasználók olvasáskezelője |
> | microsoft.directory/users/memberOf/read | Felhasználók csoporttagságának olvasása |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Delegált engedélyengedélyek olvasása a felhasználók számára |
> | microsoft.directory/users/ownedDevices/read | Felhasználók saját eszközeinek olvasása |
> | microsoft.directory/users/ownedObjects/read | Felhasználók saját objektumának olvasása |
> | microsoft.directory/users/registeredDevices/read | Felhasználók regisztrált eszközeinek olvasása |

## <a name="directory-synchronization-accounts"></a>Címtár-szinkronizálási fiókok

Ne használja. Ez a szerepkör automatikusan hozzá lesz rendelve a Azure AD Connect szolgáltatáshoz, és semmilyen más használatra nem használható vagy támogatott.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/applications/create | Minden típusú alkalmazás létrehozása |
> | microsoft.directory/applications/delete | Az összes alkalmazástípus törlése |
> | microsoft.directory/applications/appRoles/update | Az appRoles tulajdonság frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/audience/update | Az alkalmazások célközönség-tulajdonságának frissítése |
> | microsoft.directory/applications/authentication/update | A hitelesítés frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/basic/update | Az alkalmazások alapvető tulajdonságainak frissítése |
> | microsoft.directory/applications/credentials/update | Alkalmazás hitelesítő adatainak frissítése |
> | microsoft.directory/applications/owners/update | Alkalmazások tulajdonosainak frissítése |
> | microsoft.directory/applications/permissions/update | A felfedett engedélyek és a szükséges engedélyek frissítése minden alkalmazástípus esetében |
> | microsoft.directory/applications/policies/update | Alkalmazások szabályzatának frissítése |
> | microsoft.directory/organization/dirSync/update | A szervezeti címtár-szinkronizálási tulajdonság frissítése |
> | microsoft.directory/policies/create | Szabályzatok létrehozása az Azure AD-ban |
> | microsoft.directory/policies/delete | Szabályzatok törlése az Azure AD-ban |
> | microsoft.directory/policies/standard/read | A házirendek alapszintű tulajdonságainak olvasása |
> | microsoft.directory/policies/owners/read | Szabályzatok tulajdonosainak olvasása |
> | microsoft.directory/policies/policyAppliedTo/read | A policies.policyAppliedTo tulajdonság olvasása |
> | microsoft.directory/policies/basic/update | A szabályzatok alapszintű tulajdonságainak frissítése |
> | microsoft.directory/policies/owners/update | Szabályzattulajdonosok frissítése |
> | microsoft.directory/policies/tenantDefault/update | Alapértelmezett szervezeti szabályzatok frissítése |
> | microsoft.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása |
> | microsoft.directory/servicePrincipals/delete | Szolgáltatásnév törlése |
> | microsoft.directory/servicePrincipals/enable | Szolgáltatásnevek engedélyezése |
> | microsoft.directory/servicePrincipals/disable | Szolgáltatásnév letiltása |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Jelszó egyszeri bejelentkezési hitelesítő adatainak kezelése szolgáltatásnéven |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Jelszó egyszeri bejelentkezési hitelesítő adatainak olvasása szolgáltatásnéven |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Szolgáltatásnév szerepkör-hozzárendelésének olvasása |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Szolgáltatásnévhez rendelt szerepkör-hozzárendelések olvasása |
> | microsoft.directory/servicePrincipals/standard/read | Szolgáltatásnév alapvető tulajdonságainak olvasása |
> | microsoft.directory/servicePrincipals/memberOf/read | A szolgáltatásnév csoporttagságának olvasása |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Delegált engedélyengedélyek olvasása a szolgáltatásnévhez |
> | microsoft.directory/servicePrincipals/owners/read | Szolgáltatásnév tulajdonosának olvasása |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Szolgáltatásnév tulajdonában lévő objektumok olvasása |
> | microsoft.directory/servicePrincipals/policies/read | Szolgáltatásnév-házirendek olvasása |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Szolgáltatásnév szerepkör-hozzárendelésének frissítése |
> | microsoft.directory/servicePrincipals/audience/update | A szolgáltatásnév célközönség-tulajdonságainak frissítése |
> | microsoft.directory/servicePrincipals/authentication/update | Szolgáltatásnév hitelesítési tulajdonságainak frissítése |
> | microsoft.directory/servicePrincipals/basic/update | Egyszerű szolgáltatásnév alaptulajdonságának frissítése |
> | microsoft.directory/servicePrincipals/credentials/update | Szolgáltatásnév hitelesítő adatainak frissítése |
> | microsoft.directory/servicePrincipals/owners/update | Szolgáltatásnév-tulajdonosok frissítése |
> | microsoft.directory/servicePrincipals/permissions/update | Szolgáltatásnév-engedélyek frissítése |
> | microsoft.directory/servicePrincipals/policies/update | Szolgáltatásnév-szabályzatok frissítése |
> | microsoft.directory/servicePrincipals/tag/update | A szolgáltatásnév címketulajdonságának frissítése |

## <a name="directory-writers"></a>Címtárírók

Az ebben a szerepkörben található felhasználók olvashatják és frissítheti a felhasználók, csoportok és szolgáltatásnév alapvető információit. Ezt a szerepkört csak olyan alkalmazásokhoz rendelje hozzá, amelyek nem támogatják a [Hozzájárulási keretrendszert.](../develop/quickstart-register-app.md) Nem rendelhető hozzá felhasználókhoz.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Terméklicencek hozzárendelése csoportokhoz csoportalapú licenceléshez |
> | microsoft.directory/groups/create | Csoportok létrehozása a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása csoportalapú licenceléshez |
> | microsoft.directory/groups/basic/update | A csoportok alapszintű tulajdonságainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/classification/update | A csoportok besorolási tulajdonságának frissítése a szerepkör által hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/dynamicMembershipRule/update | Csoportok dinamikus tagsági szabályának frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/groupType/update | Csoport groupType tulajdonságának frissítése |
> | microsoft.directory/groups/members/update | Csoportok tagjainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/onPremWriteBack/update | Frissítse Azure Active Directory csoportokat, hogy a rendszer visszaírható legyen a helyszíni Azure AD Connect |
> | microsoft.directory/groups/owners/update | Csoportok tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/settings/update | Csoportok beállításainak frissítése |
> | microsoft.directory/groups/visibility/update | Csoportok láthatósági tulajdonságának frissítése |
> | microsoft.directory/groupSettings/create | Csoportbeállítások létrehozása |
> | microsoft.directory/groupSettings/delete | Csoportbeállítások törlése |
> | microsoft.directory/groupSettings/basic/update | Alapszintű tulajdonságok frissítése a csoportbeállításokban |
> | microsoft.directory/oAuth2PermissionGrants/create | OAuth 2.0-engedélyengedélyek létrehozása |
> | microsoft.directory/oAuth2PermissionGrants/basic/update | OAuth 2.0-engedélyengedélyek frissítése |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Titkos kulcsok és hitelesítő adatok alkalmazás-kiépítési adatainak kezelése |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Alkalmazás-kiépítési szinkronizálási feladatok elindításakor, újraindítása és szüneteltetése |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Alkalmazás-kiépítési szinkronizálási feladatok és séma létrehozása és kezelése |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Szolgáltatásnév közvetlen hozzáférésének megadása egy csoport adataihoz |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Szolgáltatásnév szerepkör-hozzárendelésének frissítése |
> | microsoft.directory/users/assignLicense | Felhasználói licencek kezelése |
> | microsoft.directory/users/create | Felhasználók hozzáadása |
> | microsoft.directory/users/disable | Felhasználók letiltása |
> | microsoft.directory/users/enable | Felhasználók engedélyezése |
> | microsoft.directory/users/invalidateAllRefreshTokens | A felhasználói frissítési jogkivonatok érvénytelenítésének kényszerítése a kijelentkezésben |
> | microsoft.directory/users/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása felhasználók számára |
> | microsoft.directory/users/basic/update | Alapszintű tulajdonságok frissítése a felhasználókon |
> | microsoft.directory/users/manager/update | Frissítéskezelő felhasználók számára |
> | microsoft.directory/users/userPrincipalName/update | A felhasználók egyszerű felhasználónevének frissítése |

## <a name="domain-name-administrator"></a>Tartománynév-rendszergazda

Az ezzel a szerepkört használó felhasználók kezelhetik (olvashatják, hozzáadhatják, ellenőrizhetik, frissítheti és törölhetik) a tartományneveket. A felhasználókkal, csoportokkal és alkalmazásokkal kapcsolatos címtáradatokat is olvashatják, mivel ezek az objektumok tartományi függőségekkel rendelkeznek. A helyszíni környezetek esetében az ezzel a szerepkörhöz tartozó felhasználók konfigurálni tudnak tartományneveket az összevonáshoz, hogy a társított felhasználók hitelesítése mindig a helyszínen történik. Ezek a felhasználók ezután egyszeri bejelentkezéssel bejelentkeznek az Azure AD-alapú szolgáltatásokba a helyszíni jelszavukat használva. Az összevonási beállításokat szinkronizálni kell a Azure AD Connect, hogy a felhasználók a hozzáférések kezeléséhez is Azure AD Connect.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/domains/allProperties/allTasks | Tartományok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |

## <a name="dynamics-365-administrator"></a>Dynamics 365-rendszergazda

Az ezzel a szerepkörsel rendelkező felhasználók globális engedélyekkel rendelkezik a Microsoft Dynamics 365 Online-ban, ha a szolgáltatás jelen van, valamint a támogatási jegyek kezelése és a szolgáltatás állapotának figyelése. További információ: [Az Azure AD-szervezet](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)kezelése a szolgáltatás-rendszergazdai szerepkör használatával.

> [!NOTE]
> A Microsoft Graph API és az Azure AD PowerShell ezt a szerepkört "Dynamics 365-szolgáltatás-rendszergazdaként" azonosítja. Ez a "Dynamics 365-rendszergazda" a [Azure Portal.](https://portal.azure.com)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.dynamics365/allEntities/allTasks | A Dynamics 365 minden aspektusának kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="exchange-administrator"></a>Exchange-rendszergazda

Az ezzel a szerepkörsel rendelkező felhasználók globális engedélyekkel rendelkezik a Microsoft Exchange Online-ban, ha a szolgáltatás jelen van. Emellett képes létrehozni és kezelni az összes Microsoft 365, kezelni a támogatási jegyeket és figyelni a szolgáltatás állapotát. További információ: [Microsoft 365 rendszergazdai szerepkörök.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> A Microsoft Graph API és az Azure AD PowerShell ezt a szerepkört "Exchange-szolgáltatás-rendszergazdaként" azonosítja. Ez az "Exchange-rendszergazda" a [Azure Portal.](https://portal.azure.com) Ez "Exchange Online-rendszergazda" az [Exchange felügyeleti központban.](https://go.microsoft.com/fwlink/p/?LinkID=529144)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | Csoport rejtett tagjainak olvasása |
> | microsoft.directory/groups.unified/create | Szerepkör Microsoft 365 hozzárendelhető csoportok kizárásával hozzon létre csoportokat |
> | microsoft.directory/groups.unified/delete | Szerepkör Microsoft 365 hozzárendelhető csoportok kizárásával Microsoft 365 csoport törlése |
> | microsoft.directory/groups.unified/restore | Új Microsoft 365 visszaállítása |
> | microsoft.directory/groups.unified/basic/update | Alapszintű tulajdonságok frissítése Microsoft 365 csoportokon szerepkör-hozzárendelésre használható csoportok kizárásával |
> | microsoft.directory/groups.unified/members/update | Csoporttagok Microsoft 365 szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.unified/owners/update | A szerepkörhöz Microsoft 365 csoportok tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Az Exchange Online összes aspektusának kezelése |
> | microsoft.office365.network/performance/allProperties/read | A hálózati teljesítmény összes tulajdonságának olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 használati jelentések olvasása |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="exchange-recipient-administrator"></a>Exchange-címzett rendszergazdája

Az ezzel a szerepkörű felhasználók olvasási hozzáféréssel és írási hozzáféréssel rendelkezik a címzettek attribútumaihoz az Exchange Online-ban. További információ: [Exchange-címzettek.](/exchange/recipients/recipients)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.exchange/allRecipients/allProperties/allTasks | Az összes címzett létrehozása és törlése, valamint a címzettek összes tulajdonságának olvasása és frissítése az Exchange Online-ban |
> | microsoft.office365.exchange/messageTracking/allProperties/allTasks | Az üzenetkövetés összes feladatának kezelése az Exchange Online-ban |
> | microsoft.office365.exchange/migration/allProperties/allTasks | Az Exchange Online címzettjeinek migrálásával kapcsolatos összes feladat kezelése |

## <a name="external-id-user-flow-administrator"></a>Külső azonosító felhasználói folyamat rendszergazdája

Az ezzel a szerepkörű felhasználók létrehozhatnak és kezelhet felhasználói folyamatokat (más néven "beépített" szabályzatokat) a Azure Portal. Ezek a felhasználók testreszabhatják a HTML-/CSS-/JavaScript-tartalmakat, módosíthatják az MFA-követelményeket, jogcímeket választhatnak a jogkivonatban, kezelhetik az API-összekötőket, és konfigurálhatják a munkamenet-beállításokat az Azure AD-szervezet összes felhasználói folyamatához. Ez a szerepkör azonban nem teszi lehetővé a felhasználói adatok áttekintését vagy a szervezeti sémában szereplő attribútumok módosításait. A Identity Experience Framework szabályzatok (más néven egyéni szabályzatok) módosításai szintén kívül vannak ennek a szerepkörnek a hatókörében.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/b2cUserFlow/allProperties/allTasks | Felhasználói attribútumok olvasása és konfigurálása a Azure Active Directory B2C |

## <a name="external-id-user-flow-attribute-administrator"></a>Külső azonosító felhasználói folyamatának attribútum-rendszergazdája

Az ezzel a szerepkörű felhasználók az Azure AD-szervezetben az összes felhasználói folyamat számára elérhető egyéni attribútumokat adhatnak hozzá vagy törölnek. Ennek megfelelően az ezzel a szerepkörhöz kapcsolódó felhasználók módosíthatják vagy új elemeket adhatnak hozzá a végfelhasználói sémához, hatással lehetnek az összes felhasználói folyamat viselkedésére, és közvetett módon változásokat eredményezhetnek a végfelhasználóktól kért adatokban, és végső soron jogcímként küldve el őket az alkalmazásoknak. Ez a szerepkör nem szerkesztheti a felhasználói folyamatokat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/b2cUserAttribute/allProperties/allTasks | Egyéni szabályzatok olvasása és konfigurálása a Azure Active Directory B2C |

## <a name="external-identity-provider-administrator"></a>Külső identitásszolgáltató rendszergazdája

Ez a rendszergazda kezeli az Azure AD-szervezetek és a külső identitásszolgáltatók közötti összevonást. Ezzel a szerepkörvel a felhasználók új identitásszolgáltatókat adhatnak hozzá, és konfigurálhatja az összes elérhető beállítást (például a hitelesítési útvonalat, a szolgáltatásazonosítót és a hozzárendelt kulcstárolókat). Ez a felhasználó engedélyezheti, hogy az Azure AD-szervezet megbízhat a külső identitásszolgáltatóktól származó hitelesítésben. A végfelhasználói élményre gyakorolt hatás a szervezet típusától függ:

* Azure AD-szervezetek alkalmazottaknak és partnereknek: Az összevonás (például a Gmailhez) adása azonnal hatással lesz az összes még be nem váltott vendégmeghívásra. Lásd: [A Google hozzáadása identitásszolgáltatóként B2B vendégfelhasználók számára.](../external-identities/google-federation.md)
* Azure Active Directory B2C: Az összevonás hozzáadása (például a Facebookkal vagy egy másik Azure AD-szervezettel) nem befolyásolja azonnal a végfelhasználói folyamatokat, amíg az identitásszolgáltató hozzá nem lett adva lehetőségként egy felhasználói folyamathoz (más néven beépített szabályzathoz). Erre [egy példát a Microsoft-fiók konfigurálása identitásszolgáltatóként.](../../active-directory-b2c/identity-provider-microsoft-account.md) A felhasználói folyamatok változásához a "B2C felhasználói folyamat rendszergazdájának" korlátozott szerepköre szükséges.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/identityProviders/allProperties/allTasks | Identitásszolgáltatók olvasása és konfigurálása a Azure Active Directory B2C |

## <a name="global-administrator"></a>Globális rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók hozzáférhetnek az Azure Active Directory összes felügyeleti funkcióhoz, valamint olyan szolgáltatásokhoz, amelyek Azure Active Directory-identitásokat (például Microsoft 365 Biztonsági központ, Microsoft 365 Megfelelőségi központ, Exchange Online, SharePoint Online és Skype Vállalati online verzió) használnak. Emellett a globális rendszergazdák megemelheti a [jogosultságszintjüket](../../role-based-access-control/elevate-access-global-admin.md) az összes Azure-előfizetés és felügyeleti csoport kezeléséhez. Ez lehetővé teszi, hogy a globális rendszergazdák teljes hozzáférést kapnak az összes Azure-erőforráshoz a megfelelő Azure AD-bérlő használatával. Az Azure AD-szervezetre bejelentkező személy globális rendszergazdává válik. A vállalatnál több globális rendszergazda is lehet. A globális rendszergazdák bármely felhasználó és az összes többi rendszergazda jelszavát alaphelyzetbe állíthatják.

> [!NOTE]
> Ajánlott eljárásként a Microsoft azt javasolja, hogy rendelje hozzá a globális rendszergazda szerepkört ötnél kevesebb felhasználóhoz a szervezetben. További információkért lásd az [Azure AD-szerepkörök ajánlott eljárásait.](best-practices.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | Hozzáférési felülvizsgálatok létrehozása és törlése, valamint a hozzáférési felülvizsgálatok összes tulajdonságának olvasása és frissítése az Azure AD-ban |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Felügyeleti egységek létrehozása és kezelése (beleértve a tagokat is) |
> | microsoft.directory/applications/allProperties/allTasks | Alkalmazások létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/applications/synchronization/standard/read | Az alkalmazásobjektumhoz társított kiépítési beállítások olvasása |
> | microsoft.directory/applicationTemplates/példányos | Katalógusalkalmazások példányának létrehozása alkalmazássablonból |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | AppRoleAssignments létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/auditLogs/allProperties/read | Az auditnaplók összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Az engedélyezési házirendek összes aspektusának kezelése |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metaadatok és -kulcs olvasása az eszközökön |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése a Microsoft Cloud App Security |
> | microsoft.directory/connectors/create | Alkalmazásproxy-összekötők létrehozása |
> | microsoft.directory/connectors/allProperties/read | Az alkalmazásproxy-összekötők összes tulajdonságának olvasása |
> | microsoft.directory/connectorGroups/create | Alkalmazásproxy-összekötő csoportok létrehozása |
> | microsoft.directory/connectorGroups/delete | Alkalmazásproxy-összekötők csoportjainak törlése |
> | microsoft.directory/connectorGroups/allProperties/read | Az alkalmazásproxy-összekötő csoportok összes tulajdonságának olvasása |
> | microsoft.directory/connectorGroups/allProperties/update | Az alkalmazásproxy-összekötő csoportok összes tulajdonságának frissítése |
> | microsoft.directory/contacts/allProperties/allTasks | Kapcsolattartók létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/contracts/allProperties/allTasks | Partnerszerződések létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/devices/allProperties/allTasks | Eszközök létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/deviceManagementPolicies/standard/read | Általános tulajdonságok olvasása az eszközkezelési alkalmazás-szabályzatokkal |
> | microsoft.directory/deviceManagementPolicies/basic/update | Az eszközkezelési alkalmazás-szabályzatok alapvető tulajdonságainak frissítése |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Általános tulajdonságok olvasása az eszközregisztrációs szabályzatokkal |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Az eszközregisztrációs szabályzatok alapszintű tulajdonságainak frissítése |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Címtárszerepk szerepkörök létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Azure AD-szerepkörsablonok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/domains/allProperties/allTasks | Tartományok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Erőforrások létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure AD-jogosultságkezelésben |
> | microsoft.directory/groups/allProperties/allTasks | Csoportok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/groupsAssignableToRoles/create | Szerepkörhöz hozzárendelhető csoportok létrehozása |
> | microsoft.directory/groupsAssignableToRoles/delete | Szerepkörhöz hozzárendelhető csoportok törlése |
> | microsoft.directory/groupsAssignableToRoles/restore | Szerepkör által hozzárendelhető csoportok visszaállítása |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Szerepkör által hozzárendelhető csoportok frissítése |
> | microsoft.directory/groupSettings/allProperties/allTasks | Csoportbeállítások létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/groupSettingTemplates/allProperties/allTasks | Csoportbeállítás-sablonok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/identityProtection/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése a Azure AD Identity Protection |
> | microsoft.directory/loginOrganizationBranding/allProperties/allTasks | LoginTenantBranding létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0-engedélyengedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/organization/allProperties/allTasks | Szervezetek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/policies/allProperties/allTasks | Szabályzatok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/conditionalAccessPolicies/allProperties/allTasks | A feltételes hozzáférési szabályzatok összes tulajdonságának kezelése |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Az összes erőforrás olvasása a Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | A kiépítési naplók összes tulajdonságának olvasása |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Szerepkör-hozzárendelések létrehozása és törlése, valamint az összes szerepkör-hozzárendelési tulajdonság olvasása és frissítése |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Szerepkör-definíciók létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/serviceAction/activateService | Végrehajthatja egy szolgáltatás "szolgáltatás aktiválása" műveletét |
> | microsoft.directory/serviceAction/disableDirectoryFeature | Végrehajthatja a "címtárszolgáltatás letiltása" szolgáltatás műveletet |
> | microsoft.directory/serviceAction/enableDirectoryFeature | Végrehajthatja a "címtárszolgáltatás engedélyezése" szolgáltatás műveletet |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Végrehajthatja a getAvailableExtentionProperties szolgáltatás műveletet |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | Szolgáltatásnév létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Hozzájárulás megadása bármely alkalmazáshoz való engedélyhez |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Szolgáltatásnév közvetlen hozzáférésének megadása egy csoport adataihoz |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | A szolgáltatásnévhez társított kiépítési beállítások olvasása |
> | microsoft.directory/signInReports/allProperties/read | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/subscribedSkus/allProperties/allTasks | Előfizetések vásárlása és kezelése, valamint előfizetések törlése |
> | microsoft.directory/users/allProperties/allTasks | Felhasználók létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/permissionGrantPolicies/create | Engedélyengedély-megadó szabályzatok létrehozása |
> | microsoft.directory/permissionGrantPolicies/delete | Engedélyengedély-megadó szabályzatok törlése |
> | microsoft.directory/permissionGrantPolicies/standard/read | Az engedély-megadó szabályzatok szabványos tulajdonságainak olvasása |
> | microsoft.directory/permissionGrantPolicies/basic/update | Az engedély-megadó szabályzatok alapvető tulajdonságainak frissítése |
> | microsoft.directory/servicePrincipalCreationPolicies/create | Szolgáltatásnév-létrehozási szabályzatok létrehozása |
> | microsoft.directory/servicePrincipalCreationPolicies/delete | Szolgáltatásnév-létrehozási szabályzatok törlése |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | Szolgáltatásnév-létrehozási szabályzatok szabványos tulajdonságainak olvasása |
> | microsoft.directory/servicePrincipalCreationPolicies/basic/update | Szolgáltatásnév-létrehozási szabályzatok alapvető tulajdonságainak frissítése |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Az Azure Advanced Threat Protection minden aspektusának kezelése |
> | microsoft.azure.informationProtection/allEntities/allTasks | Az alkalmazás minden aspektusának Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.commerce.billing/allEntities/allTasks | Az Office 365 számlázásának minden aspektusának kezelése |
> | microsoft.dynamics365/allEntities/allTasks | A Dynamics 365 minden aspektusának kezelése |
> | microsoft.flow/allEntities/allTasks | A Microsoft összes aspektusának kezelése Power Automate |
> | microsoft.intune/allEntities/allTasks | Az alkalmazás minden aspektusának Microsoft Intune |
> | microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 Compliance Manager összes aspektusának kezelése |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Az alkalmazás minden aspektusának Asztali elemzés |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Az Exchange Online összes aspektusának kezelése |
> | microsoft.office365.lockbox/allEntities/allTasks | Az alkalmazás minden aspektusának Ügyfélszéf |
> | microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a Üzenetközpont a Microsoft 365 Felügyeleti központ a biztonsági üzenetek kivételével |
> | microsoft.office365.messageCenter/securityMessages/read | Biztonsági üzenetek olvasása a Üzenetközpont a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.network/performance/allProperties/read | Az összes hálózati teljesítménytulajdonság olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.protectionCenter/allEntities/allProperties/allTasks | A biztonsági és megfelelőségi központok minden aspektusának kezelése |
> | microsoft.office365.search/content/manage | Tartalom létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft Search szolgáltatásban |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése az Microsoft 365 Biztonsági és megfelelőségi központban |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.sharePoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése a SharePointban |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió összes aspektusának kezelése |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 használati jelentések olvasása |
> | microsoft.office365.userCommunication/allEntities/allTasks | Az új üzenetek láthatóságának olvasása és frissítése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.powerApps/allEntities/allTasks | Az alkalmazás minden aspektusának Power Apps |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Az alkalmazás minden aspektusának Power BI |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | A Microsoft Defender minden aspektusának kezelése végponthoz |

## <a name="global-reader"></a>Globális olvasó

Az ebben a szerepkörben lévő felhasználók olvashatják a beállításokat és a rendszergazdai információkat Microsoft 365 szolgáltatások között, de nem tudnak felügyeleti műveleteket. A globális olvasó a globális rendszergazda írásban használható megfelelője. Globális rendszergazda helyett globális olvasó hozzárendelése tervezéshez, naplózáshoz vagy vizsgálatokhoz. A globális olvasó és más korlátozott rendszergazdai szerepkörök, például az Exchange-rendszergazda együttes használatával megkönnyítheti a munkát a globális rendszergazdai szerepkör hozzárendelése nélkül. A globális olvasó a Microsoft 365 Felügyeleti központ, az Exchange felügyeleti központtal, a SharePoint felügyeleti központtal, a Teams felügyeleti központtal, a Security Centerrel, a Megfelelőségi központtal, az Azure AD felügyeleti központtal és a Eszközkezelés felügyeleti központtal működik.

> [!NOTE]
> A globális olvasói szerepkörnek jelenleg van néhány korlátja –
>
>- [OneDrive felügyeleti központ](https://admin.onedrive.com/) – A OneDrive felügyeleti központ nem támogatja a globális olvasói szerepkört
>- [M365 Felügyeleti központ](https://admin.microsoft.com/Adminportal/Home#/homepage) – A globális olvasó nem tudja olvasni az integrált alkalmazásokat. Az Integrált alkalmazások  lap nem  található az M365 Felügyeleti központ bal oldali panelének Beállítások lapján.
>- [Office Security & Compliance Center](https://sip.protection.office.com/homepage) – A globális olvasó nem olvashatja az SCC-auditnaplókat, nem tud tartalomkeresést vagy biztonsági pontszámot látni.
>- [Teams felügyeleti központ](https://admin.teams.microsoft.com) – A globális olvasó nem tudja olvasni a Teams-életciklust, az elemzési & **jelentéseket,** **az IP-telefoneszközök** kezelését és az **alkalmazáskatalógust.** 
>- [Privileged Access Management (PAM) nem](/office365/securitycompliance/privileged-access-management-overview) támogatja a globális olvasói szerepkört.
>- [Azure Information Protection –](/azure/information-protection/what-is-information-protection) A globális olvasó [](/azure/information-protection/reports-aip) csak központi jelentéskészítéshez támogatott, és ha az Azure AD-szervezet nem az egységes címkézési [platformon van.](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
>
> Ezek a funkciók jelenleg fejlesztés alatt állnak.
>

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/applications/applicationProxy/read | Az alkalmazásproxy összes tulajdonságának olvasása |
> | microsoft.directory/applications/synchronization/standard/read | Az alkalmazásobjektumhoz társított kiépítési beállítások olvasása |
> | microsoft.directory/auditLogs/allProperties/read | Az auditnaplók összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metaadatok és -kulcs olvasása az eszközökön |
> | microsoft.directory/connectors/allProperties/read | Az alkalmazásproxy-összekötők összes tulajdonságának olvasása |
> | microsoft.directory/connectorGroups/allProperties/read | Az alkalmazásproxy-összekötő csoportok összes tulajdonságának olvasása |
> | microsoft.directory/entitlementManagement/allProperties/read | Az Azure AD-jogosultságkezelés összes tulajdonságának olvasása |
> | microsoft.directory/deviceManagementPolicies/standard/read | Általános tulajdonságok olvasása az eszközkezelési alkalmazás-szabályzatokkal |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Általános tulajdonságok olvasása az eszközregisztrációs szabályzatokkal |
> | microsoft.directory/groups/hiddenMembers/read | Csoport rejtett tagjainak olvasása |
> | microsoft.directory/policies/standard/read | Alapszintű tulajdonságok olvasása a szabályzatokkal |
> | microsoft.directory/policies/owners/read | Szabályzatok tulajdonosainak olvasása |
> | microsoft.directory/policies/policyAppliedTo/read | A policies.policyAppliedTo tulajdonság olvasása |
> | microsoft.directory/conditionalAccessPolicies/standard/read | A szabályzatok feltételes hozzáférésének olvasása |
> | microsoft.directory/conditionalAccessPolicies/owners/read | A feltételes hozzáférési szabályzatok tulajdonosainak olvasása |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Olvassa el a feltételes hozzáférési szabályzatok "alkalmazott" tulajdonságát |
> | microsoft.directory/provisioningLogs/allProperties/read | Kiépítési naplók összes tulajdonságának olvasása |
> | microsoft.directory/servicePrincipals/authentication/read | A szolgáltatásnév hitelesítési tulajdonságainak olvasása |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | A szolgáltatásnévhez társított kiépítési beállítások olvasása |
> | microsoft.directory/signInReports/allProperties/read | A bejelentkezési jelentések minden tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/users/strongAuthentication/read | A felhasználók erős hitelesítési tulajdonságának olvasása |
> | microsoft.commerce.billing/allEntities/read | Az Office 365-számlázás összes erőforrásának olvasása |
> | microsoft.office365.exchange/allEntities/standard/read | Az Exchange Online összes erőforrásának olvasása |
> | microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a Üzenetközpont a Microsoft 365 Felügyeleti központ a biztonsági üzenetek kivételével |
> | microsoft.office365.messageCenter/securityMessages/read | Biztonsági üzenetek olvasása Üzenetközpont a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.network/performance/allProperties/read | Az összes hálózati teljesítménytulajdonság olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.protectionCenter/allEntities/allProperties/read | A Biztonsági és megfelelőségi központokban található összes tulajdonság olvasása |
> | microsoft.office365.securityComplianceCenter/allEntities/read | Szabványos tulajdonságok olvasása a Microsoft 365 Biztonsági és megfelelőségi központban |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 használati jelentések olvasása |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="groups-administrator"></a>Csoport-rendszergazda

Az ebben a szerepkörben a felhasználók csoportokat hozhatnak létre és kezelhet, valamint olyan beállításokat, mint az elnevezési és elévülési szabályzatok. Fontos tudni, hogy a felhasználók ehhez a szerepkörhöz való hozzárendelése lehetővé teszi számukra, hogy a szervezet minden csoportját különböző számítási feladatokban ,például a Teamsben, a SharePointban vagy a Yammerben kezelhessék az Outlook mellett. A felhasználó emellett kezelheti a különböző csoportbeállításokat a különböző felügyeleti portálok, például a Microsoft Felügyeleti központ, a Azure Portal, valamint a számítási feladatokra vonatkozó beállítások, például a Teams és a SharePoint felügyeleti központok között.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Terméklicencek hozzárendelése csoportokhoz csoportalapú licenceléshez |
> | microsoft.directory/groups/create | Csoportok létrehozása a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/delete | Csoportok törlése a szerepkörhöz hozzárendelhető csoport kivételével |
> | microsoft.directory/groups/hiddenMembers/read | Csoport rejtett tagjainak olvasása |
> | microsoft.directory/groups/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása csoportalapú licenceléshez |
> | microsoft.directory/groups/restore | Törölt csoportok visszaállítása |
> | microsoft.directory/groups/basic/update | A csoportok alapszintű tulajdonságainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/classification/update | A csoportok besorolási tulajdonságának frissítése a szerepkör által hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/dynamicMembershipRule/update | Csoportok dinamikus tagsági szabályának frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/groupType/update | Csoport groupType tulajdonságának frissítése |
> | microsoft.directory/groups/members/update | Csoportok tagjainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/onPremWriteBack/update | Frissítse Azure Active Directory csoportokat, hogy a rendszer visszaírható legyen a helyszíni Azure AD Connect |
> | microsoft.directory/groups/owners/update | Csoportok tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/settings/update | Csoportok beállításainak frissítése |
> | microsoft.directory/groups/visibility/update | Csoportok láthatósági tulajdonságának frissítése |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Közvetlen hozzáférés megadása egy szolgáltatásnévnek egy csoport adataihoz |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="guest-inviter"></a>Vendég meghívója

Az ebben a szerepkörben a felhasználók kezelhetik Azure Active Directory B2B vendégfelhasználói meghívókat, ha a **Tagok** meghívhat felhasználói meghívót beállítás nemre van állítva. További információ a B2B-együttműködésről: [Tudnivalók az Azure AD B2B-együttműködésről.](../external-identities/what-is-b2b.md) Nem tartalmaz más engedélyeket.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/users/inviteGuest | Vendégfelhasználók meghívása |
> | microsoft.directory/users/standard/read | Alapszintű tulajdonságok olvasása a felhasználókon |
> | microsoft.directory/users/appRoleAssignments/read | Felhasználók alkalmazásszerepkre vonatkozó szerepkör-hozzárendelésének olvasása |
> | microsoft.directory/users/directReports/read | A felhasználók közvetlen jelentésének olvasása |
> | microsoft.directory/users/manager/read | A felhasználók olvasáskezelője |
> | microsoft.directory/users/memberOf/read | Felhasználók csoporttagságának olvasása |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Delegált engedélyengedélyek olvasása a felhasználók számára |
> | microsoft.directory/users/ownedDevices/read | Felhasználók saját eszközeinek olvasása |
> | microsoft.directory/users/ownedObjects/read | Felhasználók tulajdonában lévő objektumok olvasása |
> | microsoft.directory/users/registeredDevices/read | Felhasználók regisztrált eszközeinek olvasása |

## <a name="helpdesk-administrator"></a>Helpdesk Administrator

Az ezzel a szerepkörsel kapcsolatos felhasználók módosíthatják a jelszavakat, érvényteleníthet frissítési jogkivonatokat, kezelhetik a szolgáltatáskéréseket, és figyelheti a szolgáltatás állapotát. A frissítési jogkivonat érvénytelenítésének kényszerítése arra, hogy a felhasználó újra bejelentkezik. Attól függ, hogy a rendszergazda alaphelyzetbe állíthatja-e egy felhasználó jelszavát és érvényteleníthet-e frissítési jogkivonatokat, attól függ, hogy a felhasználó milyen szerepkörhöz van hozzárendelve. Azon szerepkörök listáját, amelyekhez a rendszergazda új jelszót állíthat vissza és érvényteleníthet frissítési jogkivonatokat, lásd: Jelszó-visszaállítási [engedélyek.](#password-reset-permissions)

> [!IMPORTANT]
> Az ezzel a szerepkörű felhasználók megváltoztathatják azon felhasználók jelszavát, akik bizalmas vagy privát adatokhoz, vagy kritikus konfigurációhoz férhetnek hozzá a felhasználók Azure Active Directory. A felhasználó jelszavának módosítása azt jelentheti, hogy feltételezni lehet a felhasználó identitását és engedélyét. Például:
>
>- Az alkalmazásregisztráció és a vállalati alkalmazástulajdonosok, akik kezelhetik a sajátjukhoz használt alkalmazások hitelesítő adatait. Előfordulhat, hogy ezek az alkalmazások emelt szintű engedélyekkel rendelkezik az Azure AD-ban, és máshol nem adhatók meg a támogatási rendszergazdáknak. Ezen az útvonalon az adminisztrátor felteheti az alkalmazás tulajdonosának identitását, majd az alkalmazás hitelesítő adatainak frissítésével tovább felteheti egy kiemelt alkalmazás identitását.
>- Azure-előfizetések tulajdonosai, akik bizalmas vagy privát adatokhoz vagy kritikus konfigurációkhoz férhetnek hozzá az Azure-ban.
>- Biztonsági csoport és Microsoft 365 csoporttulajdonosok, akik kezelhetik a csoporttagságot. Ezek a csoportok hozzáférést adhatnak bizalmas vagy privát adatokhoz, vagy kritikus konfigurációkhoz az Azure AD-ban és máshol.
>- Rendszergazdák az Azure AD-n kívüli más szolgáltatásokban, például az Exchange Online-ban, az Office Biztonsági és megfelelőségi központban és az emberi erőforrások rendszerében.
>- Nem rendszergazdák, például vezetők, jogi személyek és emberi erőforrások alkalmazottai, akik bizalmas vagy személyes adatokhoz férhetnek hozzá.

A felügyeleti egységek lehetővé teszi a rendszergazdai engedélyek delegálását a felhasználók egy részegységére, illetve a házirendek a felhasználók egy részére [való alkalmazását.](administrative-units.md)

Ezt a szerepkört korábban "Jelszó-rendszergazda" szerepkörnek hívták a [Azure Portal.](https://portal.azure.com/) Az Azure AD-beli "Támogatási adminisztrátor" név most már megegyezik az Azure AD PowerShellben és a Microsoft Graph API-ban.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metaadatok és -kulcs olvasása az eszközökön |
> | microsoft.directory/users/invalidateAllRefreshTokens | Kijelentkezés kényszerítése a felhasználói frissítési jogkivonatok érvénytelenítésének használatával |
> | microsoft.directory/users/password/update | Jelszavak visszaállítása az összes felhasználó számára |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="hybrid-identity-administrator"></a>Hibrid identitás-rendszergazda

Az ebben a szerepkörben a felhasználók létrehozhatják, kezelhetik és üzembe helyezhetik a kiépítési konfiguráció beállítását az AD-ről az Azure AD-be a felhőalapú kiépítés, valamint az Azure AD Connect és összevonási beállítások használatával. A felhasználók ezzel a szerepkörvel is hibaelhárítást és figyelheti a naplókat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/applications/create | Minden típusú alkalmazás létrehozása |
> | microsoft.directory/applications/delete | Az összes alkalmazástípus törlése |
> | microsoft.directory/applications/appRoles/update | Az appRoles tulajdonság frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/audience/update | Az alkalmazások célközönség-tulajdonságának frissítése |
> | microsoft.directory/applications/authentication/update | A hitelesítés frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/basic/update | Az alkalmazások alapvető tulajdonságainak frissítése |
> | microsoft.directory/applications/credentials/update | Alkalmazás hitelesítő adatainak frissítése |
> | microsoft.directory/applications/owners/update | Alkalmazások tulajdonosainak frissítése |
> | microsoft.directory/applications/permissions/update | A felfedett engedélyek és a szükséges engedélyek frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/policies/update | Alkalmazások szabályzatának frissítése |
> | microsoft.directory/applications/synchronization/standard/read | Az alkalmazásobjektumhoz társított kiépítési beállítások olvasása |
> | microsoft.directory/applicationTemplates/példány | Katalógusalkalmazások példányos létrehozása alkalmazássablonból |
> | microsoft.directory/auditLogs/allProperties/read | Az auditnaplók összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/cloudProvisioning/allProperties/allTasks | Olvassa el és konfigurálja az Azure AD Cloud Provisioning Service összes tulajdonságát. |
> | microsoft.directory/domains/allProperties/read | Tartományok összes tulajdonságának olvasása |
> | microsoft.directory/domains/federation/update | Tartományok összevonási tulajdonságának frissítése |
> | microsoft.directory/organization/dirSync/update | A szervezeti címtár-szinkronizálási tulajdonság frissítése |
> | microsoft.directory/provisioningLogs/allProperties/read | A kiépítési naplók összes tulajdonságának olvasása |
> | microsoft.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása |
> | microsoft.directory/servicePrincipals/delete | Szolgáltatásnév törlése |
> | microsoft.directory/servicePrincipals/disable | Szolgáltatásnév letiltása |
> | microsoft.directory/servicePrincipals/enable | Szolgáltatásnevek engedélyezése |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Titkos kulcsok és hitelesítő adatok alkalmazás-kiépítési adatainak kezelése |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Alkalmazás-kiépítési szinkronizálási feladatok elindításakor, újraindítása és szüneteltetése |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Alkalmazás-kiépítési szinkronizálási feladatok és séma létrehozása és kezelése |
> | microsoft.directory/servicePrincipals/audience/update | A szolgáltatásnév célközönség-tulajdonságainak frissítése |
> | microsoft.directory/servicePrincipals/authentication/update | Szolgáltatásnév hitelesítési tulajdonságainak frissítése |
> | microsoft.directory/servicePrincipals/basic/update | Egyszerű szolgáltatásnév alaptulajdonságának frissítése |
> | microsoft.directory/servicePrincipals/credentials/update | Szolgáltatásnév hitelesítő adatainak frissítése |
> | microsoft.directory/servicePrincipals/owners/update | Szolgáltatásnév-tulajdonosok frissítése |
> | microsoft.directory/servicePrincipals/permissions/update | Szolgáltatásnév-engedélyek frissítése |
> | microsoft.directory/servicePrincipals/policies/update | Szolgáltatásnév-szabályzatok frissítése |
> | microsoft.directory/servicePrincipals/tag/update | A szolgáltatásnév címketulajdonságának frissítése |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | A szolgáltatásnévhez társított kiépítési beállítások olvasása |
> | microsoft.directory/signInReports/allProperties/read | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a Üzenetközpont a Microsoft 365 Felügyeleti központ a biztonsági üzenetek kivételével |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="insights-administrator"></a>Insights-rendszergazda

Az ebben a szerepkörben található felhasználók az [M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521)alkalmazás összes felügyeleti képességét elérhetik. Ez a szerepkör képes olvasni a címtáradatokat, figyelni a szolgáltatás állapotát, a fájltámogatási jegyeket, és elérni az Insights rendszergazdai beállításait.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.insights/allEntities/allTasks | Az Insights-alkalmazás összes aspektusának kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="insights-business-leader"></a>Insights – Üzleti vezető

Az ebben a szerepkörben lévő felhasználók az [M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521)alkalmazással férhetnek hozzá irányítópultokhoz és elemzésekhez. Ebbe beletartozik az összes irányítópulthoz való teljes hozzáférés, valamint a bemutatott elemzések és adatfeltárási funkciók. Az ebben a szerepkörben található felhasználók nem férhetnek hozzá a termékkonfigurációs beállításokhoz, amely az Insights-rendszergazdai szerepkör feladata.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.insights/reports/read | Jelentések és irányítópultok megtekintése az Insights-alkalmazásban |
> | microsoft.insights/programs/update | Programok üzembe helyezése és kezelése az Insights-alkalmazásban |

## <a name="intune-administrator"></a>Intune-rendszergazda

Az ezzel a szerepkörsel rendelkező felhasználók globális engedélyekkel Microsoft Intune online szolgáltatáson belül, amikor a szolgáltatás jelen van. Emellett ez a szerepkör lehetővé teszi a felhasználók és eszközök kezelését a szabályzatok társításához, valamint csoportok létrehozásához és kezeléséhez. További információ: [Szerepköralapú felügyelet vezérlése (RBAC) a Microsoft Intune.](/intune/role-based-access-control)

Ez a szerepkör az összes biztonsági csoportot létrehozhatja és kezelheti. Az Intune-rendszergazda azonban nem rendelkezik rendszergazdai jogosultságokkal az Office-csoportokhoz. Ez azt jelenti, hogy a rendszergazda nem frissítheti a szervezet összes Office-csoportának tulajdonosait vagy tagságát. Kezelheti azonban a létrehozott Office-csoportot, amely a végfelhasználói jogosultságai részeként jön létre. Így a létrehozott Office-csoportokat (és nem biztonsági csoportokat) bele kell számítani a 250-es kvótába.

> [!NOTE]
> A Microsoft Graph API és az Azure AD PowerShell ezt a szerepkört "Intune-szolgáltatás-rendszergazdaként" azonosítja. Ez az "Intune-rendszergazda" a [Azure Portal.](https://portal.azure.com)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metaadatok és -kulcs olvasása az eszközökön |
> | microsoft.directory/contacts/create | Kapcsolattartók létrehozása |
> | microsoft.directory/contacts/delete | Kapcsolattartók törlése |
> | microsoft.directory/contacts/basic/update | A kapcsolattartók alapszintű tulajdonságainak frissítése |
> | microsoft.directory/devices/create | Eszközök létrehozása (regisztrálás az Azure AD-ban) |
> | microsoft.directory/devices/delete | Eszközök törlése az Azure AD-ból |
> | microsoft.directory/devices/disable | Eszközök letiltása az Azure AD-ban |
> | microsoft.directory/devices/enable | Eszközök engedélyezése az Azure AD-ban |
> | microsoft.directory/devices/basic/update | Alapszintű tulajdonságok frissítése az eszközökön |
> | microsoft.directory/devices/extensionAttributes/update | Frissítse az devices.extensionAttributes tulajdonság összes értékét |
> | microsoft.directory/devices/registeredOwners/update | Regisztrált eszközök tulajdonosainak frissítése |
> | microsoft.directory/devices/registeredUsers/update | Regisztrált eszközök felhasználóinak frissítése |
> | microsoft.directory/deviceManagementPolicies/standard/read | Az eszközkezelési alkalmazás-házirendek szabványos tulajdonságainak olvasása |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Az eszközregisztrációs szabályzatok szabványos tulajdonságainak olvasása |
> | microsoft.directory/groups/hiddenMembers/read | Csoport rejtett tagjainak olvasása |
> | microsoft.directory/groups.security/create | Biztonsági csoportok létrehozása szerepkör által hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.security/delete | Biztonsági csoportok törlése szerepkör által hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.security/basic/update | A biztonsági csoportok alapszintű tulajdonságainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.security/classification/update | A biztonsági csoportok besorolási tulajdonságának frissítése szerepkör által hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | A biztonsági csoportok dynamicMembershipRule tulajdonságának frissítése a szerepkör által hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.security/members/update | Biztonsági csoportok tagjainak frissítése szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.security/owners/update | A biztonsági csoportok tulajdonosainak frissítése szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.security/visibility/update | A biztonsági csoportok láthatósági tulajdonságának frissítése a szerepkör által hozzárendelhető csoportok kizárásával |
> | microsoft.directory/users/basic/update | Alapszintű tulajdonságok frissítése a felhasználókon |
> | microsoft.directory/users/manager/update | Frissítéskezelő felhasználók számára |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.intune/allEntities/allTasks | A Microsoft Intune |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="kaizala-administrator"></a>Kaizala-rendszergazda

Az ezzel a szerepkörsel rendelkező felhasználók globális engedélyekkel rendelkezik a beállítások kezeléséhez a Microsoft Kaizalán belül, amikor a szolgáltatás elérhető, valamint a támogatási jegyek kezelése és a szolgáltatás állapotának figyelése. Emellett a felhasználó hozzáférhet a kaizala bevezetésével és & a Kaizala szervezeti tagjai által használt jelentésekkel és a Kaizala-műveletekkel létrehozott üzleti jelentésekkel kapcsolatos jelentésekhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="knowledge-administrator"></a>Tudás-rendszergazda

Az ebben a szerepkörben található felhasználók teljes körű hozzáféréssel rendelkezik az összes tudás-, tanulási és intelligens funkcióbeállításhoz a Microsoft 365 Felügyeleti központ. Általános ismeretekkel rendelkezik a termékcsomagról és a licencelés részleteiről, és a hozzáférés vezérlése a feladata. A tudás-rendszergazda tartalmakat, például témaköröket, betűszókat és tanulási erőforrásokat hozhat létre és kezelhet. Emellett ezek a felhasználók tartalomközpontokat hozhatnak létre, figyelheti a szolgáltatás állapotát, és szolgáltatáskéréseket hozhatnak létre.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/groups.security/create | Biztonsági csoportok létrehozása szerepkör által hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.security/createAsOwner | A szerepkörhöz hozzárendelhető csoportok kizárásával hozza létre a biztonsági csoportokat, és a rendszer első tulajdonosként hozzáadja a létrehozót |
> | microsoft.directory/groups.security/delete | Biztonsági csoportok törlése szerepkör által hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.security/basic/update | A biztonsági csoportok alapvető tulajdonságainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.security/members/update | Biztonsági csoportok tagjainak frissítése szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.security/owners/update | A biztonsági csoportok tulajdonosainak frissítése szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | Olvassa el és frissítse a tartalomértekértés összes tulajdonságát a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | A tudáshálózat összes tulajdonságának olvasása és frissítése a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.protectionCenter/sensitivityLabels/allProperties/read | A bizalmasság címkék minden tulajdonságának olvasása a Biztonsági és megfelelőségi központokban |
> | microsoft.office365.sharePoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése a SharePointban |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="license-administrator"></a>Licenc-rendszergazda

Az ebben a szerepkörben található felhasználók licenc-hozzárendeléseket adhatnak hozzá, távolíthatnak el és frissíthetnek felhasználókhoz, csoportokhoz (csoportalapú licencelés használatával), és kezelhetik a felhasználók használati helyét. A szerepkör nem biztosít lehetőséget előfizetések vásárlására vagy kezelésére, csoportok létrehozására és kezelésére, valamint felhasználók létrehozására és kezelésére a használati helyen kívül. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Terméklicencek hozzárendelése csoportokhoz csoportalapú licenceléshez |
> | microsoft.directory/groups/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása csoportalapú licenceléshez |
> | microsoft.directory/users/assignLicense | Felhasználói licencek kezelése |
> | microsoft.directory/users/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása felhasználók számára |
> | microsoft.directory/users/usageLocation/update | Felhasználók használati helyének frissítése |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="message-center-privacy-reader"></a>Üzenetközpont adatvédelmi olvasó

Az ebben a szerepkörben a felhasználók a felhasználói csoport összes értesítését Üzenetközpont, beleértve az adatvédelmi üzeneteket is. Üzenetközpont adatvédelmi olvasó e-mailes értesítéseket kap, beleértve az adatvédelemmel kapcsolatos értesítéseket is, és leiratkozhatnak a Üzenetközpont beállítások használatával. Csak a globális rendszergazda és az Üzenetközpont adatvédelmi olvasó olvashatja az adatvédelmi üzeneteket. Ez a szerepkör a csoportok, tartományok és előfizetések megtekintésének képességét is tartalmazza. Ez a szerepkör nem rendelkezik engedéllyel a szolgáltatáskérések megtekintéséhez, létrehozásához és kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a Üzenetközpont a Microsoft 365 Felügyeleti központ a biztonsági üzenetek kivételével |
> | microsoft.office365.messageCenter/securityMessages/read | Biztonsági üzenetek olvasása Üzenetközpont a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="message-center-reader"></a>Üzenetközpont Olvasó

Az ebben a szerepkörben található felhasználók figyelhetik az üzenetközpont értesítéseit és állapotfrissítéseit a szervezetük számára olyan konfigurált szolgáltatásokon, mint az Exchange, az Intune és a Microsoft Teams. [](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) Üzenetközpont az olvasók hetente kapják meg a bejegyzések és frissítések kivonatát, és megoszthatják az üzenetközponti bejegyzéseket a Microsoft 365. Az Azure AD-ban a szerepkörhöz rendelt felhasználók csak olvasási hozzáféréssel fognak hozzáférni az Azure AD-szolgáltatásokhoz, például a felhasználókhoz és a csoportokhoz. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a Üzenetközpont a Microsoft 365 Felügyeleti központ a biztonsági üzenetek kivételével |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="modern-commerce-user"></a>Modern kereskedelmi felhasználó

Ne használja. Ezt a szerepkört a rendszer automatikusan hozzárendeli a Commerce-től, és semmilyen más használatra nem szánt és nem támogatott. A részleteket alább találja.

A Modern kereskedelmi felhasználói szerepkör engedélyt ad bizonyos felhasználóknak a Microsoft 365 Felügyeleti központ és a **kezdőlap,** a számlázás és a támogatás bal oldali navigációs **bejegyzéseinek áttekintésére.** Az ezeken a területeken elérhető tartalmakat a felhasználókhoz rendelt kereskedelmi [szerepkörök](../../cost-management-billing/manage/understand-mca-roles.md) vezérlik a saját maguknak vagy a szervezetnek vásárolt termékek kezeléséhez. Ide tartozhatnak például a számlák fizetése, vagy a számlázási fiókokhoz és számlázási profilokhoz való hozzáférés.

A Modern kereskedelmi felhasználói szerepkörű felhasználók általában rendszergazdai engedélyekkel rendelkezik más Microsoft vásárlási rendszerekben, de nem használnak globális rendszergazdai vagy számlázási adminisztrátor-szerepköröket a felügyeleti központ eléréséhez.

**Mikor van hozzárendelve a Modern kereskedelmi felhasználói szerepkör?**

* **Önkiszolgáló** vásárlás a Microsoft 365 Felügyeleti központ – Az önkiszolgáló vásárlás lehetővé teszi a felhasználóknak, hogy új termékeket vásárolnak meg vagy regisztrálnak rájuk. Ezeket a termékeket a felügyeleti központban lehet kezelni. Az önkiszolgáló vásárlást elfelhasználó felhasználók a kereskedelmi rendszerben és a Modern kereskedelmi felhasználói szerepkörben kapják meg a szerepkört, így a felügyeleti központban kezelhetik a vásárlásokat. A rendszergazdák a PowerShellen keresztül blokkolják az önkiszolgáló vásárlásokat (Power BI, Power Apps power [automate-hez).](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell) További információért olvassa el az [önkiszolgáló vásárlással kapcsolatos gyakori kérdéseket](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).
* Vásárlások a **Microsoft** kereskedelmi piacterén – Az önkiszolgáló vásárláshoz hasonlóan, amikor egy felhasználó megvásárol egy terméket vagy szolgáltatást az Microsoft AppSource-tól vagy az Azure Marketplace-tól, a modern kereskedelmi felhasználói szerepkör lesz hozzárendelve, ha nem globális rendszergazdai vagy számlázási rendszergazdai szerepkört kap. Bizonyos esetekben előfordulhat, hogy a felhasználók nem vásárolhatnak ilyen vásárlásokat. További információ: [Microsoft kereskedelmi piactér.](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)
* **A Microsoft javaslatai** – A javaslat a Microsoft formális ajánlata a szervezet számára a Microsoft-termékek és -szolgáltatások vásárlására. Ha a javaslatot elfogadó személy nem rendelkezik globális rendszergazdai vagy számlázási rendszergazdai szerepkörsel az Azure AD-ban, akkor a javaslat befejezéséhez kereskedelmi-specifikus szerepkört és a Modern kereskedelmi felhasználói szerepkört is hozzárendeli a felügyeleti központ eléréséhez. Amikor hozzáférnek a felügyeleti központhoz, csak a kereskedelmi szerepkörük által engedélyezett funkciókat tudják használni.
* **Kereskedelemspecifikus szerepkörök** – Egyes felhasználókhoz kereskedelmi-specifikus szerepkörök vannak hozzárendelve. Ha a felhasználó nem globális vagy számlázási rendszergazda, akkor a Modern kereskedelmi felhasználó szerepkört kap, hogy hozzáfér a felügyeleti központhoz.

Ha a Modern kereskedelmi felhasználó szerepkör nincs hozzárendelve egy felhasználóhoz, elveszíti a hozzáférését a Microsoft 365 Felügyeleti központ. Ha ők maguk vagy a vállalata számára kezelnek termékeket, nem fogják tudni kezelni őket. Ide tartozhat a licencek hozzárendelése, a fizetési módok módosítása, a számlák befizetése vagy az előfizetések kezelésével kapcsolatos egyéb feladatok.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | A számlázás partnertulajdonságának Microsoft 365 olvasása |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | A mennyiségi licencelési szolgáltatási központ minden aspektusának kezelése |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/basic/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="network-administrator"></a>Hálózati rendszergazda

Az ebben a szerepkörben található felhasználók áttekintheti a Microsofttól származó, a felhasználói helyekről származó hálózati telemetriai adatokon alapuló, a szegélyhálózati architektúrára vonatkozó javaslatokat. A hálózati teljesítmény Microsoft 365 nagyvállalati ügyfélhálózati peremhálózati architektúrára támaszkodik, amely általában a felhasználók helyére jellemző. Ez a szerepkör lehetővé teszi a felderített felhasználói helyek szerkesztését és ezen helyek hálózati paramétereinek konfigurációját a továbbfejlesztett telemetriai mérések és tervezési javaslatok elősegítése érdekében

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | A hálózati helyek minden aspektusának kezelése |
> | microsoft.office365.network/performance/allProperties/read | A hálózati teljesítmény összes tulajdonságának olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="office-apps-administrator"></a>Office-alkalmazások rendszergazdája

Az ebben a szerepkörben a felhasználók kezelhetik Microsoft 365 alkalmazások felhőbeállításait. Ez magában foglalja a felhőházi szabályzatok kezelését, az önkiszolgáló letöltéskezelést és az Office-alkalmazásokkal kapcsolatos jelentések megtekintésének képességét. Ez a szerepkör emellett lehetővé teszi a támogatási jegyek kezelését és a szolgáltatás állapotának figyelét a fő felügyeleti központban. Az ehhez a szerepkörhöz rendelt felhasználók az Office-alkalmazások új szolgáltatásainak kommunikációját is kezelhetik.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a Üzenetközpont a Microsoft 365 Felügyeleti központ a biztonsági üzenetek kivételével |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.userCommunication/allEntities/allTasks | Az új üzenetek láthatóságának olvasása és frissítése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="partner-tier1-support"></a>1. szintű partnertámogatás

Ne használja. Ez a szerepkör elavult, és a jövőben el lesz távolítva az Azure AD-ból. Ez a szerepkör a Microsoft kis számú újraértékesítési partnerének, és nem általános használatra szolgál.

> [!IMPORTANT]
> Ez a szerepkör jelszó-visszaállítást és a frissítési jogkivonatok érvénytelenítését teszi lehetővé csak a nem rendszergazdák számára. Ezt a szerepkört nem szabad használni, mivel elavult, és többé nem lesz visszaadva az API-ban.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | Az appRoles tulajdonság frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/audience/update | Az alkalmazások célközönség-tulajdonságának frissítése |
> | microsoft.directory/applications/authentication/update | A hitelesítés frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/basic/update | Az alkalmazások alapvető tulajdonságainak frissítése |
> | microsoft.directory/applications/credentials/update | Alkalmazás hitelesítő adatainak frissítése |
> | microsoft.directory/applications/owners/update | Alkalmazások tulajdonosainak frissítése |
> | microsoft.directory/applications/permissions/update | A felfedett engedélyek és a szükséges engedélyek frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/policies/update | Alkalmazások szabályzatának frissítése |
> | microsoft.directory/contacts/create | Kapcsolattartók létrehozása |
> | microsoft.directory/contacts/delete | Kapcsolattartók törlése |
> | microsoft.directory/contacts/basic/update | A kapcsolattartók alapszintű tulajdonságainak frissítése |
> | microsoft.directory/groups/create | Csoportok létrehozása a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/delete | Csoportok törlése a szerepkörhöz hozzárendelhető csoport kivételével |
> | microsoft.directory/groups/restore | Törölt csoportok visszaállítása |
> | microsoft.directory/groups/members/update | Csoportok tagjainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/owners/update | Csoportok tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0-engedélyengedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Szolgáltatásnév szerepkör-hozzárendelésének frissítése |
> | microsoft.directory/users/assignLicense | Felhasználói licencek kezelése |
> | microsoft.directory/users/create | Felhasználók hozzáadása |
> | microsoft.directory/users/delete | Felhasználók törlése |
> | microsoft.directory/users/disable | Felhasználók letiltása |
> | microsoft.directory/users/enable | Felhasználók engedélyezése |
> | microsoft.directory/users/invalidateAllRefreshTokens | A felhasználói frissítési jogkivonatok érvénytelenítésének kényszerítése a kijelentkezésben |
> | microsoft.directory/users/restore | Törölt felhasználók visszaállítása |
> | microsoft.directory/users/basic/update | Alapszintű tulajdonságok frissítése a felhasználókon |
> | microsoft.directory/users/manager/update | Frissítéskezelő felhasználók számára |
> | microsoft.directory/users/password/update | Jelszavak visszaállítása az összes felhasználó számára |
> | microsoft.directory/users/userPrincipalName/update | A felhasználók egyszerű felhasználónevének frissítése |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="partner-tier2-support"></a>2. szintű partnertámogatás

Ne használja. Ez a szerepkör elavult, és a jövőben el lesz távolítva az Azure AD-ból. Ez a szerepkör kis számú Microsoft-partner számára van szánva, és nem általános használatra szolgál.

> [!IMPORTANT]
> Ez a szerepkör új jelszavakat állíthat vissza, és érvénytelenítheti a frissítési jogkivonatokat az összes nem rendszergazda és rendszergazda számára (beleértve a globális rendszergazdákat is). Ez a szerepkör nem használható, mivel elavult, és többé nem lesz visszaadva az API-ban.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | Az appRoles tulajdonság frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/audience/update | Az alkalmazások célközönség-tulajdonságának frissítése |
> | microsoft.directory/applications/authentication/update | A hitelesítés frissítése az összes alkalmazástípuson |
> | microsoft.directory/applications/basic/update | Az alkalmazások alapvető tulajdonságainak frissítése |
> | microsoft.directory/applications/credentials/update | Alkalmazás hitelesítő adatainak frissítése |
> | microsoft.directory/applications/owners/update | Alkalmazások tulajdonosainak frissítése |
> | microsoft.directory/applications/permissions/update | A felfedett engedélyek és a szükséges engedélyek frissítése minden alkalmazástípus esetében |
> | microsoft.directory/applications/policies/update | Alkalmazások szabályzatának frissítése |
> | microsoft.directory/contacts/create | Kapcsolattartók létrehozása |
> | microsoft.directory/contacts/delete | Kapcsolattartók törlése |
> | microsoft.directory/contacts/basic/update | A kapcsolattartók alapszintű tulajdonságainak frissítése |
> | microsoft.directory/domains/allProperties/allTasks | Tartományok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/groups/create | Csoportok létrehozása a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/delete | Csoportok törlése a szerepkörhöz hozzárendelhető csoport kivételével |
> | microsoft.directory/groups/restore | Törölt csoportok visszaállítása |
> | microsoft.directory/groups/members/update | Csoportok tagjainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/owners/update | Csoportok tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0-engedélyengedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/organization/basic/update | Alapvető tulajdonságok frissítése a szervezetben |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Szerepkör-hozzárendelések létrehozása és törlése, valamint az összes szerepkör-hozzárendelési tulajdonság olvasása és frissítése |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Szerepkör-definíciók létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Szolgáltatásnév szerepkör-hozzárendelésének frissítése |
> | microsoft.directory/subscribedSkus/standard/read | Az előfizetések alapszintű tulajdonságainak olvasása |
> | microsoft.directory/users/assignLicense | Felhasználói licencek kezelése |
> | microsoft.directory/users/create | Felhasználók hozzáadása |
> | microsoft.directory/users/delete | Felhasználók törlése |
> | microsoft.directory/users/disable | Felhasználók letiltása |
> | microsoft.directory/users/enable | Felhasználók engedélyezése |
> | microsoft.directory/users/invalidateAllRefreshTokens | Kijelentkezés kényszerítése a felhasználói frissítési jogkivonatok érvénytelenítésének használatával |
> | microsoft.directory/users/restore | Törölt felhasználók visszaállítása |
> | microsoft.directory/users/basic/update | Alapszintű tulajdonságok frissítése a felhasználókon |
> | microsoft.directory/users/manager/update | Frissítéskezelő felhasználók számára |
> | microsoft.directory/users/password/update | Jelszavak visszaállítása az összes felhasználó számára |
> | microsoft.directory/users/userPrincipalName/update | A felhasználók egyszerű felhasználónevének frissítése |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="password-administrator"></a>Jelszókezelő

Az ezzel a szerepkörű felhasználók csak korlátozottan kezelhetik a jelszavakat. Ez a szerepkör nem teszi lehetővé a szolgáltatáskérések kezelését és a szolgáltatás állapotának figyelését. Attól függ, hogy a jelszó-rendszergazda visszaállíthatja-e a felhasználó jelszavát attól függ, hogy a felhasználó milyen szerepkörhöz van hozzárendelve. Azon szerepkörök listájáért, amelyekhez a jelszó-rendszergazda visszaállíthatja a jelszavakat, lásd: Jelszó-visszaállítási [engedélyek.](#password-reset-permissions)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/users/password/update | Jelszavak visszaállítása az összes felhasználó számára |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="power-bi-administrator"></a>Power BI rendszergazda

Az ezzel a szerepkörsel rendelkező felhasználók globális engedélyekkel Power BI Microsoft-fiókon belül, amikor a szolgáltatás elérhető, valamint a támogatási jegyek kezelése és a szolgáltatás állapotának figyelése. További információ: [Az Power BI szerepkör ismertetése.](/power-bi/service-admin-role)

> [!NOTE]
> A Microsoft Graph API és az Azure AD PowerShell ezt a szerepkört "szolgáltatás-rendszergazdaként" Power BI azonosítja. A következő Power BI "rendszergazda" [Azure Portal.](https://portal.azure.com)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Az alkalmazás minden aspektusának Power BI |

## <a name="power-platform-administrator"></a>Power Platform rendszergazda

Az ebben a szerepkörben a felhasználók a környezetek, a PowerApps, a folyamatok és az adatveszteség-megelőzési szabályzatok összes aspektusát létrehozhatják és kezelhetik. Emellett az ezzel a szerepkörsel felfelhasználók támogatási jegyeket kezelnek, és figyelik a szolgáltatás állapotát.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.dynamics365/allEntities/allTasks | A Dynamics 365 minden aspektusának kezelése |
> | microsoft.flow/allEntities/allTasks | A Microsoft összes aspektusának kezelése Power Automate |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.powerApps/allEntities/allTasks | Az alkalmazás minden aspektusának Power Apps |

## <a name="printer-administrator"></a>Nyomtató-rendszergazda

Az ebben a szerepkörben található felhasználók regisztrálják a nyomtatókat, és a Microsoft Univerzális nyomtatás megoldásban kezelhetik a nyomtatókonfigurációk minden aspektusát, beleértve a Univerzális nyomtatás összekötő beállításait is. Minden delegált nyomtatási engedélyre vonatkozó kérést elfogadjanak. A nyomtató-rendszergazdák a jelentések nyomtatásához is hozzáférhetnek.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Nyomtatók és összekötők létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft Printben |

## <a name="printer-technician"></a>Nyomtató-technikus

Az ezzel a szerepkörsel kapcsolatos felhasználók regisztrálják a nyomtatókat, és kezelhetik a nyomtatók állapotát a Microsoft Univerzális nyomtatás megoldásban. Emellett az összekötők összes adatát is olvashatják. A nyomtatókezelő által nem használható legfontosabb feladat a nyomtatókra és a nyomtatók megosztására vonatkozó felhasználói engedélyek beállítása.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | Az összekötők összes tulajdonságának olvasása a Microsoft Printben |
> | microsoft.azure.print/printers/allProperties/read | A Microsoft Print nyomtatói összes tulajdonságának olvasása |
> | microsoft.azure.print/printers/register | Nyomtatók regisztrálása a Microsoft Printben |
> | microsoft.azure.print/printers/unregister | Nyomtatók regisztrációjának a Microsoft Printben való regisztrációja |
> | microsoft.azure.print/printers/basic/update | A Microsoft Print nyomtatói alapvető tulajdonságainak frissítése |

## <a name="privileged-authentication-administrator"></a>Privileged Authentication Administrator

Az ezzel a szerepkörrel felhozott felhasználók bármely hitelesítési módszert (beleértve a jelszavakat is) bármely felhasználóhoz beállíthatják vagy alaphelyzetbe állíthatják, beleértve a globális rendszergazdákat is. Az emelt szintű hitelesítést használó rendszergazdák kényszerítheti a felhasználókat, hogy regisztrálják újra a meglévő nem jelszóval rendelkező hitelesítő adatokat (például MFA vagy FIDO), és visszavonják az "MFA ne felejtse el az eszközön" adatokat, és a rendszer az összes felhasználó következő bejelentkezésekor kéri az MFA használatát.

A [hitelesítési rendszergazda](#authentication-administrator) szerepkörnek van jogosultsága újraregisztrációt és többtényezős hitelesítést kényszeríteni az általános jogú felhasználók és bizonyos rendszergazdai szerepkörökkel rendelkező felhasználók számára.

A [hitelesítési házirend rendszergazdai](#authentication-policy-administrator) szerepköre engedélyekkel rendelkezik a bérlő hitelesítési módszerére vonatkozó házirend beállításhoz, amely meghatározza, hogy az egyes felhasználók milyen módszereket regisztrálnak és használhatnak.

| Szerepkör | A felhasználók hitelesítési módszereinek kezelése | Felhasználónkénti MFA kezelése | MFA-beállítások kezelése | Hitelesítési módszer szabályzatának kezelése | Jelszóvédelmi szabályzat kezelése |
| ---- | ---- | ---- | ---- | ---- | ---- |
| hitelesítési rendszergazda | Igen egyes felhasználók esetén (lásd fent) | Igen egyes felhasználók esetén (lásd fent) | Nem | Nem | Nem |
| Privileged Authentication Administrator| Igen minden felhasználó számára | Igen minden felhasználó számára | Nem | Nem | Nem |
| Hitelesítési házirend rendszergazdája | Nem | Nem | Igen | Igen | Yes |

> [!IMPORTANT]
> Az ezzel a szerepkörhöz hozzáférő felhasználók módosíthatják azon személyek hitelesítő adatait, akik bizalmas vagy személyes adatokhoz, vagy kritikus konfigurációhoz férhetnek hozzá az Azure Active Directory. A felhasználó hitelesítő adatainak módosítása azt jelentheti, hogy feltételezni lehet a felhasználó identitását és engedélyét. Például:
>
>* Az alkalmazásregisztráció és a vállalati alkalmazástulajdonosok, akik kezelhetik a sajátjukhoz használt alkalmazások hitelesítő adatait. Előfordulhat, hogy ezek az alkalmazások emelt szintű engedélyekkel rendelkezik az Azure AD-ban, és máshol nem kapnak jogosultságot a hitelesítési rendszergazdáknak. Ezen az útvonalon a hitelesítési rendszergazda felteheti az alkalmazás tulajdonosának identitását, majd az alkalmazás hitelesítő adatainak frissítésével tovább feltételezheti egy rendszer-jogosultságú alkalmazás identitását.
>* Azure-előfizetések tulajdonosai, akik bizalmas vagy privát adatokhoz vagy kritikus konfigurációhoz férhetnek hozzá az Azure-ban.
>* Biztonsági csoport és Microsoft 365 csoporttulajdonosok, akik kezelhetik a csoporttagságot. Ezek a csoportok hozzáférést adhatnak az Azure AD-beli és máshol bizalmas vagy privát információkhoz vagy kritikus konfigurációkhoz.
>* Rendszergazdák az Azure AD-n kívüli más szolgáltatásokban, például az Exchange Online-ban, az Office Biztonsági és megfelelőségi központban és az emberi erőforrások rendszerében.
>* Nem rendszergazdák, például vezetők, jogi személyek és emberi erőforrások alkalmazottai, akik bizalmas vagy személyes adatokhoz férnek hozzá.


> [!IMPORTANT]
> Ez a szerepkör jelenleg nem képes felhasználónkénti MFA kezelésére az örökölt MFA felügyeleti portálon. Ugyanezek a függvények az Azure AD [PowerShell-modul Set-MsolUser](/powershell/module/msonline/set-msoluser) parancsmag használatával is elérhetőek.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | A felhasználói frissítési jogkivonatok érvénytelenítésének kényszerítése a kijelentkezésben |
> | microsoft.directory/users/strongAuthentication/update | A felhasználók erős hitelesítési tulajdonságának frissítése |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="privileged-role-administrator"></a>Kiemelt szerepkör rendszergazdája

Az ezzel a szerepkörhöz hozzárendelt felhasználók kezelhetik a szerepkör-hozzárendeléseket a Azure Active Directory és a szerepkör-Azure AD Privileged Identity Management. Létrehozhatnak és kezelhet Azure AD-szerepkörökhöz hozzárendelhető csoportokat. Emellett ez a szerepkör lehetővé teszi a felügyeleti egységek és Privileged Identity Management aspektusainak kezelését.

> [!IMPORTANT]
> Ez a szerepkör lehetővé teszi az összes Azure AD-szerepkör hozzárendelésének kezelését, beleértve a globális rendszergazdai szerepkört is. Ez a szerepkör nem tartalmazza az Azure AD egyéb kiemelt jogosultságait, például a felhasználók létrehozását vagy frissítését. Az ehhez a szerepkörhöz rendelt felhasználók azonban további szerepkörök hozzárendelése által adhatnak maguknak vagy másoknak további jogosultságokat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Felügyeleti egységek létrehozása és kezelése (beleértve a tagokat is) |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | AppRoleAssignments létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Az engedélyezési házirendek minden aspektusának kezelése |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Címtárszerepk szerepkörök létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/groupsAssignableToRoles/create | Szerepkörhöz hozzárendelhető csoportok létrehozása |
> | microsoft.directory/groupsAssignableToRoles/delete | Szerepkörhöz hozzárendelhető csoportok törlése |
> | microsoft.directory/groupsAssignableToRoles/restore | Szerepkör által hozzárendelhető csoportok visszaállítása |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Szerepkörhöz hozzárendelhető csoportok frissítése |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0-engedélyengedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/privilegedIdentityManagement/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése a Privileged Identity Management |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Szerepkör-hozzárendelések létrehozása és törlése, valamint az összes szerepkör-hozzárendelési tulajdonság olvasása és frissítése |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Szerepkör-definíciók létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Szolgáltatásnév szerepkör-hozzárendelésének frissítése |
> | microsoft.directory/servicePrincipals/permissions/update | Szolgáltatásnév-engedélyek frissítése |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Hozzájárulás megadása bármely alkalmazáshoz való engedélyhez |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="reports-reader"></a>Jelentések olvasója

Az ezzel a szerepkörsel kapcsolatos felhasználók megtekinthetik a használati jelentési adatokat és a jelentések irányítópultját a Microsoft 365 Felügyeleti központ és a környezet csomagját a Power BI. Emellett a szerepkör hozzáférést biztosít a bejelentkezési jelentésekhez és tevékenységekhez az Azure AD-ban, valamint az Microsoft Graph Reporting API által visszaadott adatokhoz. A Jelentésolvasó szerepkörhöz hozzárendelt felhasználók csak a releváns használati és alkalmazásbeadási metrikákhoz férhetnek hozzá. Nem rendelkezik rendszergazdai jogosultsággal a beállítások konfiguráláshoz vagy a termékspecifikus felügyeleti központokhoz, például az Exchange-hez való hozzáféréshez. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Az auditnaplók összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/provisioningLogs/allProperties/read | Kiépítési naplók összes tulajdonságának olvasása |
> | microsoft.directory/signInReports/allProperties/read | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.office365.network/performance/allProperties/read | A hálózati teljesítmény összes tulajdonságának olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 használati jelentések olvasása |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="search-administrator"></a>Rendszergazda keresése

Az ebben a szerepkörben található felhasználók teljes hozzáféréssel rendelkezik a Microsoft Search felügyeleti funkcióihoz a Microsoft 365 Felügyeleti központ. Emellett ezek a felhasználók megtekinthetik az üzenetközpontot, figyelheti a szolgáltatás állapotát, és szolgáltatáskéréseket hozhatnak létre.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a Üzenetközpont a Microsoft 365 Felügyeleti központ a biztonsági üzenetek kivételével |
> | microsoft.office365.search/content/manage | Tartalom létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft Search szolgáltatásban |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="search-editor"></a>Keresésszerkesztő

Az ebben a szerepkörben lévő felhasználók tartalmakat hozhatnak létre, kezelnek és törölnek a Microsoft Search szolgáltatásban a Microsoft 365 Felügyeleti központ, beleértve a könyvjelzőket, a Q&As és a helyeket.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a Üzenetközpont a Microsoft 365 Felügyeleti központ a biztonsági üzenetek kivételével |
> | microsoft.office365.search/content/manage | Tartalom létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft Search szolgáltatásban |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="security-administrator"></a>Biztonsági rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók a biztonsággal kapcsolatos funkciók kezeléséhez engedéllyel rendelkezik a Microsoft 365 Biztonsági központ, Azure Active Directory Identity Protection, Azure Active Directory Authentication, Azure Information Protection és Office 365 Security & Compliance Centerben. További információ az Office 365-engedélyekről: Engedélyek a [Security & Compliance Centerben.](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)

In | A(nak)
--- | ---
[Microsoft 365 Biztonsági központ](https://protection.office.com) | Biztonsággal kapcsolatos szabályzatok figyelése Microsoft 365 szolgáltatásokban<br>Biztonsági fenyegetések és riasztások kezelése<br>Jelentések megtekintése
Identity Protection Center | A Biztonsági olvasó szerepkör összes engedélye<br>Ezenkívül az Identity Protection Center összes műveletének végrehajtása, kivéve a jelszavak alaphelyzetbe állítását
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | A Biztonsági olvasó szerepkör összes engedélye<br>**Az** Azure AD szerepkör-hozzárendelései és beállításai nem kezelhetők
[Office 365 Biztonsági & Megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági szabályzatok kezelése<br>Biztonsági fenyegetések megtekintése, kivizsgálása és válaszadás a fenyegetésekre<br>Jelentések megtekintése
Azure Komplex veszélyforrások elleni védelem | Gyanús biztonsági tevékenységek figyelése és válaszadás a tevékenységekre
Windows Defender ATP és EDR | Szerepkörök hozzárendelése<br>Gépcsoportok kezelése<br>Végponti fenyegetésészlelés és automatizált szervizelés konfigurálása<br>Riasztások megtekintése, kivizsgálása és válaszadás a riasztásra
[megkezdése](/intune/role-based-access-control) | Felhasználói, eszköz-, beléptetési, konfigurációs és alkalmazásinformációk megtekintése<br>Nem lehet módosításokat eszközlni az Intune-ban
[Cloud App Security](/cloud-app-security/manage-admins) | Rendszergazdák hozzáadása, szabályzatok és beállítások hozzáadása, naplók feltöltése és cégirányítási műveletek végrehajtása
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Megtekintheti a biztonsági szabályzatokat, megtekintheti a biztonsági államokat, szerkesztheti a biztonsági szabályzatokat, megtekintheti a riasztásokat és javaslatokat, elvetheti a riasztásokat és javaslatokat
[Microsoft 365 szolgáltatás állapota](/office365/enterprise/view-service-health) | A szolgáltatás állapotának Microsoft 365 megtekintése
[Intelligens zárolás](../authentication/howto-password-smart-lockout.md) | Határozza meg a sikertelen bejelentkezési események esetén a zárolások küszöbértékét és időtartamát.
[Jelszóvédelem](../authentication/concept-password-ban-bad.md) | Konfigurálja a tiltott jelszavak egyéni listáját vagy a helyszíni jelszóvédelmet.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/applications/policies/update | Alkalmazások szabályzatának frissítése |
> | microsoft.directory/auditLogs/allProperties/read | Az auditnaplók összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metaadatok és -kulcs olvasása az eszközökön |
> | microsoft.directory/entitlementManagement/allProperties/read | Az Összes tulajdonság olvasása az Azure AD-jogosultságkezelésben |
> | microsoft.directory/identityProtection/allProperties/read | Az összes erőforrás olvasása a Azure AD Identity Protection |
> | microsoft.directory/identityProtection/allProperties/update | Az összes erőforrás frissítése a Azure AD Identity Protection |
> | microsoft.directory/policies/create | Szabályzatok létrehozása az Azure AD-ban |
> | microsoft.directory/policies/delete | Szabályzatok törlése az Azure AD-ban |
> | microsoft.directory/policies/basic/update | A szabályzatok alapszintű tulajdonságainak frissítése |
> | microsoft.directory/policies/owners/update | Szabályzattulajdonosok frissítése |
> | microsoft.directory/policies/tenantDefault/update | Alapértelmezett szervezeti szabályzatok frissítése |
> | microsoft.directory/conditionalAccessPolicies/create | Feltételes hozzáférési szabályzatok létrehozása |
> | microsoft.directory/conditionalAccessPolicies/delete | Feltételes hozzáférési szabályzatok törlése |
> | microsoft.directory/conditionalAccessPolicies/standard/read | A szabályzatok feltételes hozzáférésének olvasása |
> | microsoft.directory/conditionalAccessPolicies/owners/read | A feltételes hozzáférési szabályzatok tulajdonosainak olvasása |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Olvassa el a feltételes hozzáférési szabályzatok "alkalmazott" tulajdonságát |
> | microsoft.directory/conditionalAccessPolicies/basic/update | A feltételes hozzáférési szabályzatok alapszintű tulajdonságainak frissítése |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Feltételes hozzáférési szabályzatok tulajdonosainak frissítése |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | Az alapértelmezett bérlő frissítése a feltételes hozzáférési szabályzatok számára |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Az összes erőforrás olvasása a Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | A kiépítési naplók összes tulajdonságának olvasása |
> | microsoft.directory/servicePrincipals/policies/update | Szolgáltatásnév-szabályzatok frissítése |
> | microsoft.directory/signInReports/allProperties/read | A bejelentkezési jelentések minden tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.protectionCenter/allEntities/standard/read | A Biztonsági és megfelelőségi központokban található összes erőforrás szabványos tulajdonságainak olvasása |
> | microsoft.office365.protectionCenter/allEntities/basic/update | A Biztonsági és megfelelőségi központokban található összes erőforrás alapvető tulajdonságainak frissítése |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Támadási hasznos adatok létrehozása és kezelése a támadásszimulátorban |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Jelentések olvasása a támadásszimulációs válaszokról és a kapcsolódó betanításról |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Támadásszimulációs sablonok létrehozása és kezelése a támadásszimulátorban |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="security-operator"></a>Biztonsági operátor

Az ezzel a szerepkörrel rendelkezik felhasználók kezelhetik a riasztásokat, és globális, csak olvasási hozzáféréssel rendelkezik a biztonsággal kapcsolatos funkciókhoz, beleértve az Microsoft 365 Biztonsági központ, az Azure Active Directory, az Identity Protection, az Privileged Identity Management és az Office 365 Security & Compliance Center összes adatát. További információ az Office 365-engedélyekről: Engedélyek a [Security & Compliance Centerben.](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)

In | A(nak)
--- | ---
[Microsoft 365 Biztonsági központ](https://protection.office.com) | A biztonsági olvasó szerepkör összes engedélye<br>Biztonsági fenyegetésekkel kapcsolatos riasztások megtekintése, kivizsgálása és válaszadás a riasztásra
Azure AD Identity Protection | A biztonsági olvasó szerepkör összes engedélye<br>Emellett lehetőség van az Identity Protection Center összes műveletének elvégzésére, kivéve a jelszavak alaphelyzetbe állítását és a riasztási e-mailek konfigurálását.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | A biztonsági olvasó szerepkör összes engedélye
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | A biztonsági olvasó szerepkör összes engedélye<br>Biztonsági riasztások megtekintése, kivizsgálása és válaszadás a riasztásra
Windows Defender ATP és EDR | A biztonsági olvasó szerepkör összes engedélye<br>Biztonsági riasztások megtekintése, kivizsgálása és válaszadás a riasztásra
[megkezdése](/intune/role-based-access-control) | A biztonsági olvasó szerepkör összes engedélye
[Cloud App Security](/cloud-app-security/manage-admins) | A biztonsági olvasó szerepkör összes engedélye
[Microsoft 365 szolgáltatás állapota](/office365/enterprise/view-service-health) | A szolgáltatás állapotának Microsoft 365 megtekintése

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Az auditnaplók összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése a Microsoft Cloud App Security |
> | microsoft.directory/identityProtection/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése a Azure AD Identity Protection |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Az összes erőforrás olvasása a Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Kiépítési naplók összes tulajdonságának olvasása |
> | microsoft.directory/signInReports/allProperties/read | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Az Azure Advanced Threat Protection minden aspektusának kezelése |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.intune/allEntities/read | Az összes erőforrás olvasása a Microsoft Intune |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése az Microsoft 365 Biztonsági és megfelelőségi központban |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | A Microsoft Defender összes aspektusának kezelése a végponthoz |

## <a name="security-reader"></a>Biztonsági olvasó

Az ezzel a szerepkörrel rendelkezik felhasználók globális, csak olvasási hozzáféréssel rendelkezik a biztonsággal kapcsolatos funkciókhoz, beleértve az Microsoft 365 Biztonsági központ- és Azure Active Directory-, identity Protection- és Privileged Identity Management-adatokat, Azure Active Directory bejelentkezési jelentések és auditnaplók olvasásának képességét, valamint az Office 365 Security & Compliance Centerben. További információ az Office 365-engedélyekről: Engedélyek a [Security & Compliance Centerben.](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)

In | A(nak)
--- | ---
[Microsoft 365 Biztonsági központ](https://protection.office.com) | A biztonsággal kapcsolatos szabályzatok megtekintése Microsoft 365 szolgáltatásokban<br>Biztonsági fenyegetések és riasztások megtekintése<br>Jelentések megtekintése
Identity Protection Center | A biztonsági funkciókkal kapcsolatos összes biztonsági jelentés és beállítási információ olvasása<br><ul><li>Levélszemét-gátló<li>Titkosítás<li>Adatveszteség-megelőzés<li>Kártevőirtó<li>Speciális fenyegetésvédelem<li>Adathalászat elleni védelem<li>Levélforgalom szabályai
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Csak olvasási hozzáféréssel rendelkezik a következő Azure AD Privileged Identity Management: Szabályzatok és jelentések az Azure AD szerepkör-hozzárendelésekkel és biztonsági felülvizsgálatokkal kapcsolatban.<br>**Nem** regisztrálhat Azure AD Privileged Identity Management vagy nem módosíthat. A Privileged Identity Management portálon vagy a PowerShellen keresztül az ebben a szerepkörben szereplő valaki további szerepköröket aktiválhat (például globális rendszergazda vagy kiemelt szerepkörű rendszergazda), ha a felhasználó jogosult a szerepkörre.
[Office 365 Biztonsági & Megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági szabályzatok megtekintése<br>Biztonsági fenyegetések megtekintése és kivizsgálása<br>Jelentések megtekintése
Windows Defender ATP és EDR | Riasztások megtekintése és kivizsgálása. Amikor bekapcsolja a szerepköralapú hozzáférés-vezérlést az Windows Defender ATP-ban, a csak olvasási jogosultsággal rendelkező felhasználók, például az Azure AD biztonsági olvasó szerepkör elveszítik a hozzáférést, amíg hozzá nem rendelik őket egy Windows Defender ATP-szerepkörhöz.
[megkezdése](/intune/role-based-access-control) | Megtekinti a felhasználó, az eszköz, a regisztráció, a konfiguráció és az alkalmazás adatait. Az Intune-ban nem hajthat végre változtatásokat.
[Cloud App Security](/cloud-app-security/manage-admins) | Csak olvasási engedélyekkel rendelkezik, és képes a riasztások kezelésére
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági szabályzatokat, megtekintheti a biztonsági államokat, de nem tud módosításokat tenni
[Microsoft 365 szolgáltatás állapota](/office365/enterprise/view-service-health) | A szolgáltatás állapotának Microsoft 365 megtekintése

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Az auditnaplók összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metaadatok és -kulcs olvasása az eszközökön |
> | microsoft.directory/entitlementManagement/allProperties/read | Az Összes tulajdonság olvasása az Azure AD-jogosultságkezelésben |
> | microsoft.directory/identityProtection/allProperties/read | Az összes erőforrás olvasása a Azure AD Identity Protection |
> | microsoft.directory/policies/standard/read | Alapszintű tulajdonságok olvasása a szabályzatokkal |
> | microsoft.directory/policies/owners/read | Szabályzatok tulajdonosainak olvasása |
> | microsoft.directory/policies/policyAppliedTo/read | A policies.policyAppliedTo tulajdonság olvasása |
> | microsoft.directory/conditionalAccessPolicies/standard/read | A szabályzatok feltételes hozzáférésének olvasása |
> | microsoft.directory/conditionalAccessPolicies/owners/read | A feltételes hozzáférési szabályzatok tulajdonosainak olvasása |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Olvassa el a feltételes hozzáférési szabályzatok "alkalmazott" tulajdonságát |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Az összes erőforrás olvasása a Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Kiépítési naplók összes tulajdonságának olvasása |
> | microsoft.directory/signInReports/allProperties/read | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a kiemelt tulajdonságokat is |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.office365.protectionCenter/allEntities/standard/read | A Biztonsági és megfelelőségi központokban található összes erőforrás szabványos tulajdonságainak olvasása |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/read | A támadási hasznos információ összes tulajdonságának olvasása a támadásszimulátorban |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Jelentések olvasása a támadásszimulációs válaszokról és a kapcsolódó betanításról |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/read | A támadásszimulációs sablonok összes tulajdonságának olvasása a támadásszimulátorban |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="service-support-administrator"></a>Szolgáltatástámogatási rendszergazda

Az ezzel a szerepkörsel kapcsolatos felhasználók támogatási kéréseket nyitnak meg a Microsoftnál az Azure-hoz és Microsoft 365-szolgáltatásokhoz, és a szolgáltatás irányítópultját és üzenetközpontját a Azure Portal [és](https://portal.azure.com) [Microsoft 365 Felügyeleti központ.](https://admin.microsoft.com) További információ: [Tudnivalók a rendszergazdai szerepkörökről.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> Ezt a szerepkört korábban "Szolgáltatás-rendszergazda" szerepkörnek hívták a Azure Portal [és](https://portal.azure.com) [Microsoft 365 Felügyeleti központ.](https://admin.microsoft.com) A nevet "Szolgáltatástámogatási rendszergazda" névre neveztünk át, hogy igazodni tudjuk az Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell meglévő nevéhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.network/performance/allProperties/read | A hálózati teljesítmény összes tulajdonságának olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="sharepoint-administrator"></a>SharePoint-rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók globális engedélyekkel rendelkezik a Microsoft SharePoint Online-ban, ha a szolgáltatás jelen van, valamint lehetőség van az összes Microsoft 365-csoport létrehozására és kezelésére, a támogatási jegyek kezelésére és a szolgáltatás állapotának figyelére. További információ: [Tudnivalók a rendszergazdai szerepkörökről.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> A Microsoft Graph API és az Azure AD PowerShell ezt a szerepkört "SharePoint-szolgáltatás-rendszergazdaként" azonosítja. Ez a "SharePoint-rendszergazda" a [Azure Portal.](https://portal.azure.com)

> [!NOTE]
> Ez a szerepkör hatókörrel rendelkező engedélyeket is biztosít a Microsoft Graph API for Microsoft Intune számára, ami lehetővé teszi a SharePoint- és OneDrive-erőforrásokhoz kapcsolódó szabályzatok kezelését és konfigurálást.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/groups.unified/create | Szerepkör Microsoft 365 hozzárendelhető csoportok kizárásával hozzon létre új csoportokat |
> | microsoft.directory/groups.unified/delete | Szerepkör Microsoft 365 hozzárendelhető csoportok kizárásával törölje a csoportokat |
> | microsoft.directory/groups.unified/restore | Csoport Microsoft 365 visszaállítása |
> | microsoft.directory/groups.unified/basic/update | Alapszintű tulajdonságok frissítése Microsoft 365 csoportokon szerepkör-hozzárendelésre használható csoportok kizárásával |
> | microsoft.directory/groups.unified/members/update | Csoporttagok frissítése Microsoft 365 szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.unified/owners/update | A szerepkör-Microsoft 365 csoport tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.network/performance/allProperties/read | Az összes hálózati teljesítménytulajdonság olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.sharePoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése a SharePointban |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 használati jelentések olvasása |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="skype-for-business-administrator"></a>Skype Vállalati verzió rendszergazdája

Az ezzel a szerepkörsel rendelkező felhasználók globális engedélyekkel rendelkezik a Microsoft Skype Vállalati verzión belül, ha a szolgáltatás jelen van, valamint a Skype-specifikus felhasználói attribútumokat kezelik a Azure Active Directory. Emellett ez a szerepkör lehetővé teszi a támogatási jegyek kezelését és a szolgáltatás állapotának figyelése, valamint a Teams és a Skype Vállalati verzió felügyeleti központhoz való hozzáférést. A fióknak a Teamshez is licenccel kell lennie, vagy nem futtathat Teams PowerShell-parancsmagokat. További [információ: A Skype](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) Vállalati verzió rendszergazdai szerepkörének és a Teams licencelési információinak a Skype Vállalati verzió és [a Microsoft Teams bővítmény licenceléssel kapcsolatos témaköre.](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> A Microsoft Graph API és az Azure AD PowerShell ezt a szerepkört "Lync-szolgáltatás-rendszergazdaként" azonosítja. Ez a "Skype Vállalati rendszergazda" a [Azure Portal.](https://portal.azure.com/)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió minden aspektusának kezelése |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 használati jelentések olvasása |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="teams-administrator"></a>Teams-rendszergazda

Az ebben a szerepkörben található felhasználók a Microsoft Teams számítási feladatának minden aspektusát kezelhetik a Microsoft Teams & Skype Vállalati verzió felügyeleti központján és a megfelelő PowerShell-modulokon keresztül. Ide tartozik többek között a telefonos funkciókhoz, az üzenetkezeléshez, az értekezletekhez és magukhoz a csapatokhoz kapcsolódó felügyeleti eszközök. Ez a szerepkör emellett lehetővé teszi az összes csoport létrehozására és kezelésére Microsoft 365, a támogatási jegyek kezelését és a szolgáltatás állapotának figyelése.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | Csoport rejtett tagjainak olvasása |
> | microsoft.directory/groups.unified/create | Szerepkör Microsoft 365 hozzárendelhető csoportok kizárásával hozzon létre új csoportokat |
> | microsoft.directory/groups.unified/delete | Szerepkör Microsoft 365 hozzárendelhető csoportok kizárásával törölje a csoportokat |
> | microsoft.directory/groups.unified/restore | Csoport Microsoft 365 visszaállítása |
> | microsoft.directory/groups.unified/basic/update | Alapszintű tulajdonságok frissítése Microsoft 365 csoportokon a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.unified/members/update | Csoporttagok Microsoft 365 szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.directory/groups.unified/owners/update | A szerepkörhöz Microsoft 365 csoportok tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Szolgáltatásnév közvetlen hozzáférésének megadása egy csoport adataihoz |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.network/performance/allProperties/read | A hálózati teljesítmény összes tulajdonságának olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió minden aspektusának kezelése |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések Microsoft 365 kezelése |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 használati jelentések olvasása |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.teams/allEntities/allProperties/allTasks | Az összes erőforrás kezelése a Teamsben |

## <a name="teams-communications-administrator"></a>Teams kommunikációs adminisztrátor

Az ebbe a szerepkörbe tartozó felhasználók kezelhetik a Microsoft Teams számítási feladat hangalapú hozzáféréssel és telefonos & kapcsolatos aspektusait. Ide tartoznak a telefonszám-hozzárendeléshez, hang- és értekezlet-szabályzatok kezeléséhez szükséges eszközök, valamint a híváselemzési eszközkészlethez való teljes hozzáférés.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és konfigurálja Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió összes aspektusának kezelése |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 használati jelentések olvasása |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.teams/callQuality/allProperties/read | Az összes adat olvasása a Hívásminőségi irányítópulton (CQD) |
> | microsoft.teams/meetings/allProperties/allTasks | Értekezletek kezelése, beleértve a szabályzatok, konfigurációk és konferenciahidak kezelését |
> | microsoft.teams/voice/allProperties/allTasks | Hang kezelése, beleértve a hívási szabályzatokat, valamint a telefonszámleltárt és -hozzárendelést |

## <a name="teams-communications-support-engineer"></a>Teams kommunikációs támogatási szakember

Az ebben a szerepkörben található felhasználók a Microsoft Teamsben és a Skype Vállalati verzióban & kommunikációs problémákat a Microsoft Teams és a Skype Vállalati verzió felügyeleti központban található & hibaelhárítási eszközeivel hárítják el. Az ebben a szerepkörben szereplő felhasználók az összes érintett résztvevő teljes hívásrekord-információit megtekinthetik. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió minden aspektusának kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.teams/callQuality/allProperties/read | Az összes adat olvasása a Hívásminőségi irányítópulton (CQD) |

## <a name="teams-communications-support-specialist"></a>A Teams kommunikációs támogatási szakembere

Az ebben a szerepkörben található felhasználók a Microsoft Teamsben és a Skype Vállalati verzióban & kommunikációs problémákat a Microsoft Teams és a Skype Vállalati verzió felügyeleti központban található & hibaelhárítási eszközeivel hárítják el. Az ebben a szerepkörben a felhasználók csak az adott felhasználó által megtekintett felhasználó hívásában tudják megtekinteni a felhasználói adatokat. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió minden aspektusának kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.teams/callQuality/standard/read | Alapszintű adatok olvasása a Call Quality (CQD) irányítópulton |

## <a name="teams-devices-administrator"></a>Teams-eszközök rendszergazdája

Az ezzel a szerepkörsel rendelkező felhasználók [a Teams felügyeleti központban](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) kezelhetik a Teams-tanúsítvánnyal rendelkező eszközöket. Ez a szerepkör lehetővé teszi az összes eszköz egyetlen pillantással való megtekintését, valamint az eszközök keresését és szűrését. A felhasználó ellenőrizheti az egyes eszközök adatait, beleértve a bejelentkezett fiókot, az eszköz modelljét és modelljét. A felhasználó módosíthatja az eszköz beállításait, és frissítheti a szoftververziókat. Ez a szerepkör nem biztosít engedélyeket a Teams-tevékenységek ellenőrzéséhez és az eszköz minőségének ellenőrzéséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.teams/devices/standard/read | A Teams-tanúsítvánnyal rendelkező eszközök összes aspektusának kezelése, beleértve a konfigurációs szabályzatokat is |

## <a name="usage-summary-reports-reader"></a>Használati adatokat összegző jelentések olvasója

Az ezzel a szerepkörhöz tartozó felhasználók hozzáférhetnek a bérlői szintű összesített adatokhoz és a kapcsolódó elemzésekhez az Microsoft 365 Felügyeleti központban a használati és termelékenységi pontszám alapján, de nem férhetnek hozzá a felhasználói szintű adatokhoz és elemzésekhez. A Microsoft 365 felügyeleti központban megkülönböztetjük a bérlői szintű összesített adatokat és a felhasználói szintű adatokat. Ez a szerepkör egy további védelmi réteget biztosít az egyéni, felhasználóazonosításra alkalmas adatokhoz, amelyeket az ügyfelek és a jogi csapatok is kértek.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.office365.network/performance/allProperties/read | Az összes hálózati teljesítménytulajdonság olvasása a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.usageReports/allEntities/standard/read | Bérlőszintű összesített Office 365-használati jelentések olvasása |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="user-administrator"></a>Felhasználói rendszergazda

Az ezzel a szerepkörvel rendelkező felhasználók létrehozhatnak felhasználókat, és bizonyos korlátozásokkal kezelhetik a felhasználók összes aspektusát (lásd a táblázatot), és frissítheti a jelszó-elévülési szabályzatokat. Emellett az ezzel a szerepkörsel felhozott felhasználók az összes csoportot létrehozhatják és kezelhetik. Ez a szerepkör a felhasználói nézetek létrehozására és kezelésére, a támogatási jegyek kezelésére és a szolgáltatás állapotának figyelére is lehetőséget nyújt. A felhasználói rendszergazdák nem rendelkeznek engedéllyel a legtöbb rendszergazdai szerepkör felhasználói tulajdonságainak kezeléséhez. Az ezzel a szerepkörsel rendelkező felhasználó nem rendelkezik engedélyekkel az MFA kezeléséhez. A korlátozás alól kivételt képező szerepkörök az alábbi táblázatban vannak felsorolva.

| Felhasználói rendszergazdai engedély | Jegyzetek |
| --- | --- |
| Felhasználók és csoportok létrehozása<br/>Felhasználói nézetek létrehozása és kezelése<br/>Office-támogatási jegyek kezelése<br/>Jelszó-elévülési szabályzatok frissítése |  |
| Licencek kezelése<br/>Az egyszerű felhasználónév kivételével az összes felhasználói tulajdonság kezelése | Az összes felhasználóra vonatkozik, beleértve az összes rendszergazdát is |
| Törlés és visszaállítás<br/>Letiltás és engedélyezés<br/>Az összes felhasználói tulajdonság kezelése, beleértve az egyszerű felhasználónevet is<br/>FiDO-eszközkulcsok frissítése | Olyan felhasználókra vonatkozik, akik nem rendszergazdák, vagy az alábbi szerepkörök bármelyikében:<ul><li>Helpdesk Administrator</li><li>Szerepkört nem használó felhasználó</li><li>Felhasználói rendszergazda</li></ul> |
| Frissítési jogkivonatok érvénytelenítése<br/>Új jelszó létrehozása | Azon szerepkörök listáját, amelyekhez a felhasználói rendszergazda visszaállíthatja a jelszavát, és érvénytelenítheti a frissítési jogkivonatokat, lásd: Új jelszó [kérhető le.](#password-reset-permissions) |

> [!IMPORTANT]
> Az ezzel a szerepkört használó felhasználók megváltoztathatják azon személyek jelszavát, akik bizalmas vagy privát adatokhoz, vagy kritikus konfigurációhoz férhetnek hozzá a felhasználók Azure Active Directory. A felhasználó jelszavának módosítása azt jelentheti, hogy feltételezni lehet a felhasználó identitását és engedélyét. Például:
>
>- Alkalmazásregisztrációk és vállalati alkalmazások tulajdonosai, akik kezelhetik a tulajdonában áll alkalmazások hitelesítő adatait. Előfordulhat, hogy ezek az alkalmazások emelt szintű engedélyekkel rendelkezik az Azure AD-ban, és máshol nem adhatók meg a felhasználói rendszergazdáknak. Ezen az útvonalon a felhasználói rendszergazda feltételezheti az alkalmazás tulajdonosának identitását, majd az alkalmazás hitelesítő adatainak frissítésével tovább feltételezheti egy rendszer-jogosultságú alkalmazás identitását.
>- Azure-előfizetések tulajdonosai, akik bizalmas vagy privát adatokhoz vagy kritikus konfigurációkhoz férhetnek hozzá az Azure-ban.
>- Biztonsági csoport és Microsoft 365 csoporttulajdonosok, akik kezelhetik a csoporttagságot. Ezek a csoportok hozzáférést adhatnak az Azure AD-beli és máshol bizalmas vagy privát információkhoz vagy kritikus konfigurációkhoz.
>- Rendszergazdák az Azure AD-n kívüli más szolgáltatásokban, például az Exchange Online-ban, az Office Biztonsági és megfelelőségi központban és az emberi erőforrások rendszerében.
>- Nem rendszergazdák, például vezetők, jogi személyek és emberi erőforrások alkalmazottai, akik bizalmas vagy személyes adatokhoz férnek hozzá.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | microsoft.directory/appRoleAssignments/create | Alkalmazásszerepk szerepkör-hozzárendelések létrehozása |
> | microsoft.directory/appRoleAssignments/delete | Alkalmazásszerepk szerepkör-hozzárendelések törlése |
> | microsoft.directory/appRoleAssignments/basic/update | Az alkalmazás-szerepkör-hozzárendelések alapvető tulajdonságainak frissítése |
> | microsoft.directory/contacts/create | Kapcsolattartók létrehozása |
> | microsoft.directory/contacts/delete | Kapcsolattartók törlése |
> | microsoft.directory/contacts/basic/update | A kapcsolattartók alapszintű tulajdonságainak frissítése |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Erőforrások létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure AD-jogosultságkezelésben |
> | microsoft.directory/groups/assignLicense | Terméklicencek hozzárendelése csoportokhoz csoportalapú licenceléshez |
> | microsoft.directory/groups/create | Csoportok létrehozása a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/delete | Csoportok törlése a szerepkörhöz hozzárendelhető csoport kivételével |
> | microsoft.directory/groups/hiddenMembers/read | Csoport rejtett tagjainak olvasása |
> | microsoft.directory/groups/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása csoportalapú licenceléshez |
> | microsoft.directory/groups/restore | Törölt csoportok visszaállítása |
> | microsoft.directory/groups/basic/update | A csoportok alapszintű tulajdonságainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/classification/update | A csoportok besorolási tulajdonságának frissítése a szerepkör által hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/dynamicMembershipRule/update | Csoportok dinamikus tagsági szabályának frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/groupType/update | Csoport groupType tulajdonságának frissítése |
> | microsoft.directory/groups/members/update | Csoportok tagjainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/onPremWriteBack/update | Frissítse Azure Active Directory, hogy a rendszer visszaírja őket a helyszíni környezetbe a Azure AD Connect |
> | microsoft.directory/groups/owners/update | Csoportok tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kivételével |
> | microsoft.directory/groups/settings/update | Csoportok beállításainak frissítése |
> | microsoft.directory/groups/visibility/update | Csoportok láthatósági tulajdonságának frissítése |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0-engedélyengedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Szolgáltatásnév szerepkör-hozzárendelésének frissítése |
> | microsoft.directory/users/assignLicense | Felhasználói licencek kezelése |
> | microsoft.directory/users/create | Felhasználók hozzáadása |
> | microsoft.directory/users/delete | Felhasználók törlése |
> | microsoft.directory/users/disable | Felhasználók letiltása |
> | microsoft.directory/users/enable | Felhasználók engedélyezése |
> | microsoft.directory/users/inviteGuest | Vendégfelhasználók meghívása |
> | microsoft.directory/users/invalidateAllRefreshTokens | Kijelentkezés kényszerítése a felhasználói frissítési jogkivonatok érvénytelenítésének használatával |
> | microsoft.directory/users/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása felhasználók számára |
> | microsoft.directory/users/restore | Törölt felhasználók visszaállítása |
> | microsoft.directory/users/basic/update | Alapszintű tulajdonságok frissítése a felhasználókon |
> | microsoft.directory/users/manager/update | Frissítéskezelő felhasználók számára |
> | microsoft.directory/users/password/update | Jelszavak visszaállítása az összes felhasználó számára |
> | microsoft.directory/users/userPrincipalName/update | A felhasználók egyszerű felhasználónevének frissítése |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Olvasás és konfigurálás Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Jegy létrehozása és Azure-támogatás kezelése |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el és Service Health a Microsoft 365 Felügyeleti központ |
> | microsoft.office365.supportTickets/allEntities/allTasks | Szolgáltatáskérések létrehozása Microsoft 365 kezelése |
> | microsoft.office365.webPortal/allEntities/standard/read | Alapszintű tulajdonságok olvasása a Microsoft 365 Felügyeleti központ |

## <a name="how-to-understand-role-permissions"></a>A szerepkör-engedélyek

Az engedélyek sémája lazán követi a következő REST-Microsoft Graph:

`<namespace>/<entity>/<propertySet>/<action>`

Például:

`microsoft.directory/applications/credentials/update`

| Engedély elem | Description |
| --- | --- |
| névtér | Olyan termék vagy szolgáltatás, amely elérhetővé teszi a feladatot, és a előtaggal van bevésve. `microsoft` Például az Azure AD összes feladata a `microsoft.directory` névteret használja. |
| Entitás | A szolgáltatás által a szolgáltatás által a Microsoft Graph. Az Azure AD például elérhetővé teszi a felhasználót és a csoportokat, a OneNote megjegyzéseket, az Exchange pedig postaládákat és naptárakat. A névtérben az összes entitást meg kell adni egy `allEntities` speciális kulcsszóval. Ezt gyakran használják olyan szerepkörökben, amelyek egy teljes termékhez biztosítják a hozzáférést. |
| propertySet (tulajdonságkészlet) | Annak az entitásnak a konkrét tulajdonságai vagy aspektusa, amelyhez hozzáférést kap. A például lehetővé teszi a válasz URL-cím, a kijelentkezés URL-címének és az implicit folyamattulajdonság olvasását az `microsoft.directory/applications/authentication/read` Azure AD-beli alkalmazásobjektumon.<ul><li>`allProperties` az entitás összes tulajdonságát kijelöli, a kiemelt tulajdonságokat is beleértve.</li><li>`standard` kijelöli a gyakori tulajdonságokat, de kizárja a művelethez kapcsolódó kiemelt `read` tulajdonságokat. A például lehetővé teszi az olyan szabványos tulajdonságok olvasását, mint a nyilvános telefonszám és az e-mail-cím, de a többtényezős hitelesítéshez használt másodlagos magánhálózati telefonszám vagy `microsoft.directory/user/standard/read` e-mail-cím nem.</li><li>`basic` kijelöli a gyakori tulajdonságokat, de kizárja a művelethez kapcsolódó kiemelt `update` tulajdonságokat. Az olvasható tulajdonságok halmaza más lehet, mint a frissíthet. Ezért vannak és `standard` `basic` kulcsszavak, amelyek ezt tükrözik.</li></ul> |
| action | Megadott művelet, általában létrehozás, olvasás, frissítés vagy törlés (CRUD). A fenti képességek (létrehozás, olvasás, frissítés és törlés) megadásához egy speciális kulcsszó `allTasks` is elérhető. |

## <a name="deprecated-roles"></a>Elavult szerepkörök

Az alábbi szerepköröket nem szabad használni. Ezek elavultak, és a jövőben el lesznek távolítva az Azure AD-ból.

* AdHoc licenc-rendszergazda
* Eszközhöz való csatlakozás
* Eszközkezelők
* Eszközfelhasználók
* E-mailben ellenőrzött felhasználó létrehozója
* Postaláda-rendszergazda
* Munkahelyi eszközhöz való csatlakozás

## <a name="roles-not-shown-in-the-portal"></a>A portálon nem megjelenő szerepkörök

Nem minden, a PowerShell vagy MS Graph API által visszaadott szerepkör látható a Azure Portal. Az alábbi táblázat ezeket a különbségeket rendezi.

API neve | Azure Portal neve | Jegyzetek
-------- | ------------------- | -------------
Eszközhöz való csatlakozás | Elavult | [Elavult szerepkörök dokumentációja](#deprecated-roles)
Eszközkezelők | Elavult | [Elavult szerepkörök dokumentációja](#deprecated-roles)
Eszközfelhasználók | Elavult | [Elavult szerepkörök dokumentációja](#deprecated-roles)
Címtár-szinkronizálási fiókok | Nem jelenik meg, mert nem szabad használni | [Címtár-szinkronizálási fiókok dokumentációja](#directory-synchronization-accounts)
Vendégfelhasználó | Nem jelenik meg, mert nem használható | NA
1. szintű partnertámogatás | Nem jelenik meg, mert nem szabad használni | [1. szintű partner támogatási dokumentációja](#partner-tier1-support)
2. szintű partnertámogatás | Nem jelenik meg, mert nem szabad használni | [A Partner Tier2 támogatási dokumentációja](#partner-tier2-support)
Korlátozott vendégfelhasználó | Nem jelenik meg, mert nem használható | NA
Felhasználó | Nem jelenik meg, mert nem használható | NA
Munkahelyi eszközhöz való csatlakozás | Elavult | [Elavult szerepkörök dokumentációja](#deprecated-roles)

## <a name="password-reset-permissions"></a>Jelszó-visszaállítási engedélyek

Az oszlopfejlécek azokat a szerepköröket képviselik, amelyek új jelszavakat állíthatnak vissza. A tábla sorai tartalmazzák a szerepköröket, amelyekhez a jelszavuk visszaállítható.

A jelszó visszaállítható | Jelszókezelő | Helpdesk Admin | Hitelesítési rendszergazda | Felhasználói rendszergazda | Privileged Authentication Admin | Globális rendszergazda
------ | ------ | ------ | ------ | ------ | ------ | ------
Hitelesítési rendszergazda | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Könyvtárolvasók | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Globális rendszergazda | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Csoportok rendszergazdája | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Vendég meghívója | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Helpdesk Admin | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Üzenetközpont Olvasó | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Jelszókezelő | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Privileged Authentication Admin | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Kiemelt szerepkör rendszergazdája | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Jelentések olvasója | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Felhasználó (nincs rendszergazdai szerepkör) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Felhasználói rendszergazda | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Használati adatokat összegző jelentések olvasója | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* A globális rendszergazda nem távolíthatja el a saját globális rendszergazdai hozzárendelését. Ez megakadályozza azt a helyzetet, amikor egy szervezet 0 globális rendszergazdával rendelkezik.

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkörök hozzárendelése csoportokhoz](groups-assign-role.md)
- [A különböző szerepkörök ismertetése](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Azure-előfizetés rendszergazdai szerepkörének felhasználóhoz rendelése](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
