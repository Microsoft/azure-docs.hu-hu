---
title: Beépített Azure AD-szerepkörök – Azure Active Directory
description: Ismerteti a Azure Active Directory beépített szerepköreit és engedélyeit.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 02/17/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65b91e3dff3ef412dad8bbe57383a9dbf8c7765
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032245"
---
# <a name="azure-ad-built-in-roles"></a>Beépített Azure AD-szerepkörök

A Azure Active Directory (Azure AD) használatával korlátozott rendszergazdákat jelölhet ki a kevésbé Kiemelt szerepkörökben lévő Identity Tasks-feladatok kezelésére. A rendszergazdák olyan célokra rendelhetők hozzá, mint a felhasználók hozzáadása vagy módosítása, a rendszergazdai szerepkörök hozzárendelése, a felhasználói jelszavak alaphelyzetbe állítása, a felhasználói licencek kezelése és a tartománynevek kezelése. Az [alapértelmezett felhasználói engedélyeket](../fundamentals/users-default-permissions.md) csak az Azure ad felhasználói beállításaiban lehet módosítani.

## <a name="limit-use-of-global-administrator"></a>Globális rendszergazda használatának korlátozása

A globális rendszergazdai szerepkörhöz hozzárendelt felhasználók elolvashatják és módosíthatják az Azure AD-szervezet minden felügyeleti beállítását. Alapértelmezés szerint, amikor egy felhasználó regisztrál a Microsoft Cloud Service szolgáltatásra, létrejön egy Azure AD-bérlő, és a felhasználó tagja lesz a globális rendszergazdák szerepkörnek. Ha egy meglévő bérlőhöz ad hozzá egy előfizetést, nincs hozzárendelve a globális rendszergazdai szerepkörhöz. Csak a globális rendszergazdák és a Kiemelt szerepkörű rendszergazdák delegálják a rendszergazdai szerepköröket. Az üzleti kockázat csökkentése érdekében javasoljuk, hogy ezt a szerepkört a szervezete legkevesebb lehetséges személyéhez rendelje.

Ajánlott eljárásként Azt javasoljuk, hogy a szerepkört a szervezetnél kevesebb mint öt személyhez rendelje hozzá. Ha több mint öt adminisztrátora van hozzárendelve a globális rendszergazdai szerepkörhöz a szervezetében, a következő módokon csökkentheti annak használatát.

### <a name="find-the-role-you-need"></a>Keresse meg a szükséges szerepkört

Ha nem szeretné megkeresni a sok szerepkörből álló listáról származó szerepkört, az Azure AD a szerepkör-kategóriák alapján jeleníti meg a szerepkörök részhalmazait. Tekintse meg az [Azure ad-szerepkörök és-rendszergazdák](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) új **típusú** szűrőjét, hogy csak a kiválasztott típus szerepkörei jelenjenek meg.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Már létezik olyan szerepkör, amely nem létezett a globális rendszergazdai szerepkör hozzárendelésekor

Lehetséges, hogy egy szerepkört vagy szerepkört adtak hozzá az Azure AD-hez, amely olyan részletesebb engedélyeket biztosít, amelyek nem voltak elérhetők, amikor a felhasználók a globális rendszergazdához emeltek. Az idő múlásával további szerepköröket vezetünk be, amelyek csak a globális rendszergazdai szerepkörrel ellátott feladatokat hajtják végre. Ezek a következő [szerepkörökben](#all-roles)láthatók.

## <a name="assign-or-remove-administrator-roles"></a>Rendszergazdai szerepkörök kiosztása vagy eltávolítása

Ha meg szeretné tudni, hogyan rendelhet hozzá rendszergazdai szerepköröket egy felhasználóhoz Azure Active Directoryban, tekintse meg [a rendszergazdai szerepkörök megtekintése és társítása a Azure Active Directory](manage-roles-portal.md).

> [!Note]
> Ha rendelkezik egy prémium szintű Azure AD-licenccel, és Ön már Privileged Identity Management (PIM) felhasználó, az összes szerepkör-felügyeleti feladat elvégzése a Privilege Identity Management szolgáltatásban történik, nem az Azure AD-ben.
>
> ![A PIM-ben felügyelt Azure AD-szerepkörök olyan felhasználók számára, akik már használják a PIM-t, és prémium P2 licenccel rendelkeznek](./media/permissions-reference/pim-manages-roles-for-p2.png)

## <a name="all-roles"></a>Minden szerepkör

> [!div class="mx-tableFixed"]
> | Szerepkör | Leírás | Sablon azonosítója |
> | --- | --- | --- |
> | [Alkalmazás-rendszergazda](#application-administrator) | Az alkalmazás-regisztrációk és a vállalati alkalmazások minden aspektusát létrehozhatja és kezelheti. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Alkalmazásfejlesztő](#application-developer) | A "felhasználók regisztrálhatnak alkalmazásokat" beállítástól független alkalmazás-regisztrációkat hozhat létre. | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Támadási hasznos adatok szerzője](#attack-payload-author) | Olyan támadási hasznos adatokat hozhat létre, amelyeket a rendszergazda később indíthat el. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Támadás szimulációjának rendszergazdája](#attack-simulation-administrator) | A a támadási szimulációs kampányok minden aspektusát képes létrehozni és kezelni. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Hitelesítés rendszergazdája](#authentication-administrator) | A nem rendszergazdai jogú felhasználók számára is elérhetővé teheti a hitelesítési módszerre vonatkozó információk megtekintését, beállítását és visszaállítását. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Hitelesítési házirend rendszergazdája](#authentication-policy-administrator) | A a hitelesítési módszerek és a jelszavas védelem házirendjeinek minden aspektusát létrehozhatja és kezelheti. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Azure AD-hez csatlakoztatott eszköz helyi rendszergazdája](#azure-ad-joined-device-local-administrator) | Az ehhez a szerepkörhöz hozzárendelt felhasználók hozzá lesznek adva a helyi Rendszergazdák csoporthoz az Azure AD-hez csatlakoztatott eszközökön. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps-rendszergazda](#azure-devops-administrator) | Az Azure DevOps szervezeti házirendjét és beállításait tudja kezelni. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure Information Protection rendszergazda](#azure-information-protection-administrator) | A Azure Information Protection termék összes aspektusát képes kezelni. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [B2C IEF kulcskészlet rendszergazdája](#b2c-ief-keyset-administrator) | Kezelheti az összevonási és titkosítási titkokat az Identity Experience Framework (IEF) szolgáltatásban. | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [B2C IEF házirend rendszergazdája](#b2c-ief-policy-administrator) | Megbízhatósági keretrendszer-szabályzatokat hozhat létre és kezelhet az Identity Experience Framework (IEF) alkalmazásban. | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Számlázási rendszergazda](#billing-administrator) | Olyan általános számlázási feladatokat hajthat végre, mint például a fizetési adatok frissítése. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Felhőalkalmazás-rendszergazda](#cloud-application-administrator) | Az alkalmazás-regisztráció és a vállalati alkalmazások minden aspektusát létrehozhatja és kezelheti az alkalmazásproxy kivételével. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Felhőalapú eszköz rendszergazdája](#cloud-device-administrator) | Korlátozott hozzáférés az eszközök az Azure AD-ben való kezeléséhez. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Megfelelőségi rendszergazda](#compliance-administrator) | Képes olvasni és kezelni a megfelelőségi konfigurációt és jelentéseket az Azure AD-ben és a Microsoft 365ban. | 17315797-102d-40b4-93e0-432062caca18 |
> | [Megfelelőségi adatkezelő](#compliance-data-administrator) | A megfelelőségi tartalom létrehozása és kezelése. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Feltételes hozzáférésű rendszergazda](#conditional-access-administrator) | Kezelheti a feltételes hozzáférési képességeket. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Ügyfél-kulcstároló hozzáférési jóváhagyója](#customer-lockbox-access-approver) | Jóváhagyhatja a Microsoft támogatási kérelmeit az ügyfél szervezeti adatkezeléséhez. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Asztali elemzési rendszergazda](#desktop-analytics-administrator) | Elérheti és kezelheti az asztali felügyeleti eszközöket és szolgáltatásokat. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Directory-olvasók](#directory-readers) | Az alapvető címtáradatok olvasása. Az alkalmazások és a vendégek számára a címtár olvasási hozzáférésének engedélyezése gyakran történik. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Címtár-szinkronizálási fiókok](#directory-synchronization-accounts) | Csak Azure AD Connect szolgáltatás használja. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Címtár-írók](#directory-writers) | Az alapszintű címtáradatok olvasása és írása. Az alkalmazásokhoz való hozzáférés biztosítása nem a felhasználók számára ajánlott. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Tartománynév-rendszergazda](#domain-name-administrator) | A a felhőben és a helyszínen is kezelheti a tartományneveket. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365-rendszergazda](#dynamics-365-administrator) | A a Dynamics 365 termék összes aspektusát képes kezelni. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Exchange-rendszergazda](#exchange-administrator) | Az Exchange termék összes aspektusát képes kezelni. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Külső azonosító – felhasználói folyamat rendszergazdája](#external-id-user-flow-administrator) | A a felhasználói folyamatok összes aspektusát képes létrehozni és kezelni. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Külső azonosító felhasználói flow-attribútumának rendszergazdája](#external-id-user-flow-attribute-administrator) | Az az összes felhasználói folyamat számára elérhető attribútum-sémát is létrehozhatja és kezelheti. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Külső identitás-szolgáltató rendszergazdája](#external-identity-provider-administrator) | Konfigurálhatja az identitás-szolgáltatókat közvetlen összevonás esetén. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Globális rendszergazda](#global-administrator) | Az Azure ad-identitásokat használó Azure AD-és Microsoft-szolgáltatások összes aspektusát képes kezelni. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Globális olvasó](#global-reader) | Mindent megtudhat, amit a globális rendszergazda tud, de nem frissít semmit. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Csoportok rendszergazdája](#groups-administrator) | Ennek a szerepkörnek a tagjai csoportokat hozhatnak létre/kezelhetnek, csoportokat hozhatnak létre/kezelhetnek, például elnevezési és lejárati szabályzatokat, valamint csoportok tevékenység-és naplózási jelentéseket tekinthetnek | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Vendég meghívója](#guest-inviter) | Meghívhatja a vendég felhasználókat a "tagok hívhatják meg a vendégeket" beállítástól függetlenül. | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Segélyszolgálat rendszergazdája](#helpdesk-administrator) | Alaphelyzetbe állíthatja a nem rendszergazdák és az ügyfélszolgálati rendszergazdák jelszavát. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Hibrid identitás-rendszergazda](#hybrid-identity-administrator) | Az AD-t az Azure AD Felhőbeli üzembe helyezési és összevonási beállításainak kezelésére képes kezelni. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Az Áttekintés rendszergazdája](#insights-administrator) | Rendszergazdai hozzáféréssel rendelkezik a Microsoft 365 bepillantást biztosító alkalmazásban. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Üzleti vezető](#insights-business-leader) | Megtekintheti és megoszthatja az irányítópultokat és az ismereteket az M365-bepillantást használó alkalmazással. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune-rendszergazda](#intune-administrator) | Az Intune-termék minden aspektusát képes kezelni. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala-rendszergazda](#kaizala-administrator) | Kezelheti a Microsoft Kaizala beállításait. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Licenc rendszergazdája](#license-administrator) | A a felhasználókra és a csoportokra vonatkozó licenceket is képes kezelni. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [A Message Center adatvédelmi olvasója](#message-center-privacy-reader) | Csak az Office 365 Message Center biztonsági üzeneteinek és frissítéseinek olvasására van lehetőség. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Üzenetközpont-olvasó](#message-center-reader) | A szervezete üzenetei és frissítései csak az Office 365 Message Centerben olvashatók. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Modern kereskedelmi felhasználó](#modern-commerce-user) | Kezelheti a vállalat, a részleg vagy a csapat kereskedelmi vásárlásait. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Hálózati rendszergazda](#network-administrator) | A felügyelheti a hálózati telephelyeket, és áttekintheti a vállalati hálózat kialakításával kapcsolatos Microsoft 365 szoftvereket. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Office-alkalmazások rendszergazdája](#office-apps-administrator) | Kezelheti az Office-alkalmazások Cloud Services szolgáltatásait, beleértve a házirendek és beállítások kezelését, valamint a lehetőség kiválasztását, kiválasztását és közzétételét, valamint a "Újdonságok" funkciót a végfelhasználó eszközeihez. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [Partneri Tier1-támogatás](#partner-tier1-support) | Nem használható – általános használatra nem ajánlott. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [Partneri szint-támogatás](#partner-tier2-support) | Nem használható – általános használatra nem ajánlott. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Jelszó-rendszergazda](#password-administrator) | Alaphelyzetbe állíthatja a nem rendszergazdák és a jelszó-rendszergazdák jelszavát. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI rendszergazda](#power-bi-administrator) | A Power BI termék összes aspektusát képes kezelni. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Energiagazdálkodási platform rendszergazdája](#power-platform-administrator) | A a Microsoft Dynamics 365, a PowerApps és a Microsoft Flow összes aspektusát képes létrehozni és kezelni. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Nyomtató rendszergazdája](#printer-administrator) | A a nyomtatók és a nyomtató-összekötők összes aspektusát képes kezelni. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Nyomtató technikusa](#printer-technician) | Regisztrálhatja és törölheti a nyomtatókat, és frissítheti a nyomtató állapotát. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Kiemelt jogosultságú hitelesítés rendszergazdája](#privileged-authentication-administrator) | Bármely felhasználó (rendszergazda vagy nem rendszergazda) számára elérhetővé teheti a hitelesítési módszerre vonatkozó információk megtekintését, beállítását és alaphelyzetbe állítását. | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Kiemelt szerepkörű rendszergazda](#privileged-role-administrator) | Felügyelheti a szerepkör-hozzárendeléseket az Azure AD-ben, és a Privileged Identity Management összes aspektusát. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Jelentések olvasója](#reports-reader) | Beolvashatja a bejelentkezési és a naplózási jelentéseket. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Keresés a Rendszergazdában](#search-administrator) | A a Microsoft keresési beállításainak minden aspektusát létrehozhatja és kezelheti. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Keresési szerkesztő](#search-editor) | Létrehozhat és kezelhet olyan szerkesztési tartalmakat, mint a könyvjelzők, a Q és a, a helyszínek, az alaprajz. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Biztonsági rendszergazda](#security-administrator) | A biztonsági információk és jelentések beolvasása, valamint a konfiguráció kezelése az Azure AD-ben és az Office 365-ben. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Biztonsági operátor](#security-operator) | Biztonsági események létrehozása és kezelése. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Biztonsági olvasó](#security-reader) | A biztonsági információkat és jelentéseket az Azure AD-ben és az Office 365-ben is elolvashatja. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Szolgáltatás-támogatási rendszergazda](#service-support-administrator) | A szolgáltatás állapotával kapcsolatos információk beolvasása és a támogatási jegyek kezelése. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [SharePoint-rendszergazda](#sharepoint-administrator) | A a SharePoint szolgáltatás összes aspektusát képes kezelni. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Skype vállalati verzió-rendszergazda](#skype-for-business-administrator) | A a Skype vállalati verziójának minden aspektusát képes kezelni. | 75941009-915a-4869-abe7-691bff18279e |
> | [Csapatok rendszergazdája](#teams-administrator) | Felügyelheti a Microsoft Teams szolgáltatást. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Csapatok kommunikációs rendszergazdája](#teams-communications-administrator) | A a Microsoft Teams szolgáltatás hívási és értekezlet-szolgáltatásainak kezelésére képes. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Csapat kommunikációs támogató mérnök](#teams-communications-support-engineer) | A speciális eszközöket használó csapatokon belüli kommunikációs problémák megoldására is képes. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [A Teams Communications támogatási szakértője](#teams-communications-support-specialist) | Az alapszintű eszközöket használó csapatokon belüli kommunikációs problémák elhárítására használható. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Csapatok eszközeinek rendszergazdája](#teams-devices-administrator) | Felügyelettel kapcsolatos feladatokat hajthat végre a csapatok által hitelesített eszközökön. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Használati összesítő jelentések olvasója](#usage-summary-reports-reader) | Csak a bérlői szintű összesítéseket láthatja Microsoft 365 használati elemzési és termelékenységi pontszámban. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Felhasználói rendszergazda](#user-administrator) | Kezelheti a felhasználók és a csoportok minden aspektusát, beleértve a korlátozott rendszergazdák jelszavainak alaphelyzetbe állítását is. | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>alkalmazás-rendszergazda

Az ebben a szerepkörben lévő felhasználók a vállalati alkalmazások, az alkalmazások regisztrációi és az alkalmazásproxy-beállítások összes aspektusát hozhatják létre és kezelhetik. Vegye figyelembe, hogy az ehhez a szerepkörhöz hozzárendelt felhasználók nem lesznek hozzáadva tulajdonosként új alkalmazás-regisztrációk vagy vállalati alkalmazások létrehozásakor.

Ez a szerepkör a delegált engedélyek és az alkalmazás engedélyeinek megadását is lehetővé teszi, a Microsoft Graph és az Azure AD Graph alkalmazás engedélyeinek kivételével.

> [!IMPORTANT]
> Ez a kivétel azt jelenti, hogy továbbra is beleegyezik az alkalmazások engedélyeibe _más_ alkalmazásokhoz (például nem Microsoft alkalmazások vagy a regisztrált alkalmazások esetében). Továbbra is _kérheti_ ezeket az engedélyeket az alkalmazás regisztrálásának részeként, de _megadhatja_ , hogy ezek az engedélyek magasabb jogosultsági szintű rendszergazda, például globális rendszergazda számára szükségesek.
>
>Ez a szerepkör lehetővé teszi az alkalmazás hitelesítő adatainak kezelését. Az ehhez a szerepkörhöz hozzárendelt felhasználók hitelesítő adatokat adhatnak hozzá egy alkalmazáshoz, és ezekkel a hitelesítő adatokkal megszemélyesítheti az alkalmazás identitását. Ha az alkalmazás identitása hozzáférést kapott egy erőforráshoz, például felhasználói vagy más objektumok létrehozására vagy frissítésére, akkor a szerepkörhöz hozzárendelt felhasználó elvégezheti ezeket a műveleteket az alkalmazás megszemélyesítése közben. Előfordulhat, hogy az alkalmazás identitásának megszemélyesítése magasabb szintű jogosultságot jelent, mint amit a felhasználó a szerepkör-hozzárendelésein keresztül el tud végezni. Fontos tisztában lenni azzal, hogy egy felhasználó az alkalmazás-rendszergazdai szerepkörhöz való hozzárendelésével megszemélyesítheti az alkalmazás identitását.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/alkalmazások/létrehozás | Összes típusú alkalmazás létrehozása |
> | Microsoft. Directory/alkalmazások/törlés | Összes típusú alkalmazás törlése |
> | Microsoft. Directory/alkalmazások/applicationProxy/olvasás | Az összes alkalmazásproxy tulajdonságainak olvasása |
> | Microsoft. Directory/alkalmazások/applicationProxy/Update | Az összes alkalmazásproxy tulajdonságainak frissítése |
> | Microsoft. Directory/alkalmazások/applicationProxyAuthentication/Update | Alkalmazásproxy-hitelesítés tulajdonságainak frissítése |
> | Microsoft. Directory/alkalmazások/applicationProxySslCertificate/Update | Alkalmazásproxy egyéni tartományának frissítése |
> | Microsoft. Directory/alkalmazások/applicationProxyUrlSettings/Update | Alkalmazásproxy belső és külső URL-címeinek frissítése |
> | Microsoft. Directory/alkalmazások/appRoles/Update | A appRoles tulajdonság frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/célközönség/frissítés | Az alkalmazások célközönség tulajdonságának frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítés/frissítés | Hitelesítés frissítése minden típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/alapszintű/frissítés | Alkalmazások alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés | Alkalmazás hitelesítő adatainak frissítése |
> | Microsoft. Directory/alkalmazások/tulajdonosok/frissítés | Alkalmazások tulajdonosainak frissítése |
> | Microsoft. Directory/alkalmazások/engedélyek/frissítés | A kitett engedélyek és a szükséges engedélyek frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/házirendek/frissítés | Alkalmazások szabályzatának frissítése |
> | Microsoft. Directory/alkalmazások/ellenőrzés/frissítés | Applicationsverification tulajdonság frissítése |
> | Microsoft. Directory/alkalmazások/szinkronizálás/standard/olvasás | Az alkalmazásobjektum-objektumhoz társított kiépítési beállítások olvasása |
> | Microsoft. Directory/applicationTemplates/példány | Katalógusbeli alkalmazások példányainak megnyitása alkalmazás-sablonokból |
> | Microsoft. Directory/auditLogs/allProperties/READ | A naplók összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/összekötők/létrehozás | Alkalmazásproxy-összekötők létrehozása |
> | Microsoft. Directory/összekötők/allProperties/olvasás | Az alkalmazásproxy-összekötők összes tulajdonságának olvasása |
> | Microsoft. Directory/connectorGroups/Create | Alkalmazásproxy-összekötő csoportok létrehozása |
> | Microsoft. Directory/connectorGroups/delete | Alkalmazásproxy-összekötő csoportok törlése |
> | Microsoft. Directory/connectorGroups/allProperties/READ | Az Application proxy Connector-csoportok összes tulajdonságának olvasása |
> | Microsoft. Directory/connectorGroups/allProperties/Update | Az Application proxy Connector-csoportok összes tulajdonságának frissítése |
> | Microsoft. Directory/Oauth2permissiongrant objektumok/allProperties/allTasks | OAuth 2,0 engedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/applicationPolicies/Create | Alkalmazás-házirendek létrehozása |
> | Microsoft. Directory/applicationPolicies/delete | Alkalmazás-házirendek törlése |
> | Microsoft. Directory/applicationPolicies/standard/olvasás | Az alkalmazás-házirendek szabványos tulajdonságainak olvasása |
> | Microsoft. Directory/applicationPolicies/tulajdonosok/olvasás | Az alkalmazás-szabályzatok tulajdonosának beolvasása |
> | Microsoft. Directory/applicationPolicies/policyAppliedTo/READ | Az objektumok listájára alkalmazott Használati szabályzatok olvasása |
> | Microsoft. Directory/applicationPolicies/Basic/Update | Az alkalmazás-házirendek szabványos tulajdonságainak frissítése |
> | Microsoft. Directory/applicationPolicies/owners/Update | Az alkalmazás-házirendek tulajdonosi tulajdonságának frissítése |
> | Microsoft. Directory/provisioningLogs/allProperties/READ | A kiépítési naplók összes tulajdonságának olvasása |
> | Microsoft. Directory/servicePrincipals/Create | Szolgáltatásnevek létrehozása |
> | Microsoft. Directory/servicePrincipals/delete | Egyszerű szolgáltatások törlése |
> | Microsoft. Directory/servicePrincipals/letiltás | Egyszerű szolgáltatások letiltása |
> | Microsoft. Directory/servicePrincipals/Enable | Szolgáltatásnevek engedélyezése |
> | Microsoft. Directory/servicePrincipals/getPasswordSingleSignOnCredentials | A jelszó egyszeri bejelentkezési hitelesítő adatainak kezelése a szolgáltatás rendszerbiztonsági szolgáltatásainál |
> | Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage | Alkalmazás-kiépítési titkok és hitelesítő adatok kezelése |
> | Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Alkalmazás-kiépítési szinkronizálás feladatok elindítása, újraindítása és szüneteltetése |
> | Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | Alkalmazás-kiépítési szinkronizálás-feladatok és-séma létrehozása és kezelése |
> | Microsoft. Directory/servicePrincipals/managePasswordSingleSignOnCredentials | Jelszó egyszeri bejelentkezési hitelesítő adatainak beolvasása az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Application-admin | Az alkalmazás engedélyeinek és a delegált engedélyeknek a jóváhagyása bármely felhasználó vagy minden felhasználó nevében, a Microsoft Graph és az Azure AD Graph alkalmazás engedélyeinek kivételével |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Egyszerű szolgáltatás szerepkör-hozzárendeléseinek frissítése |
> | Microsoft. Directory/servicePrincipals/hallgatóság/frissítés | A célközönség tulajdonságainak frissítése az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/hitelesítés/frissítés | Az egyszerű szolgáltatások hitelesítési tulajdonságainak frissítése |
> | Microsoft. Directory/servicePrincipals/Basic/Update | Egyszerű tulajdonságok frissítése az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/hitelesítő adatok/frissítés | Egyszerű szolgáltatásnév hitelesítő adatainak frissítése |
> | Microsoft. Directory/servicePrincipals/owners/Update | Egyszerű szolgáltatásnév tulajdonosainak frissítése |
> | Microsoft. Directory/servicePrincipals/engedélyek/frissítés | Egyszerű szolgáltatásnév engedélyeinek frissítése |
> | Microsoft. Directory/servicePrincipals/házirendek/frissítés | Az egyszerű szolgáltatások szabályzatának frissítése |
> | Microsoft. Directory/servicePrincipals/tag/frissítés | Az egyszerű szolgáltatásnév címkézési tulajdonságának frissítése |
> | Microsoft. Directory/servicePrincipals/szinkronizálás/standard/olvasás | Az egyszerű szolgáltatáshoz társított kiépítési beállítások olvasása |
> | Microsoft. Directory/signInReports/allProperties/READ | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="application-developer"></a>Alkalmazásfejlesztő

Az ebben a szerepkörben lévő felhasználók létrehozhatnak alkalmazás-regisztrációkat, ha a "felhasználók regisztrálhatnak alkalmazásokat" beállítás a nem értékre van állítva. Ez a szerepkör lehetővé teszi az engedély megadását a saját nevében, amikor a "felhasználók beleegyeznek az alkalmazásokkal a vállalati adatok eléréséhez" beállítás értéke nem. Az ehhez a szerepkörhöz hozzárendelt felhasználók új alkalmazás-regisztrációk vagy vállalati alkalmazások létrehozásakor tulajdonosként lesznek hozzáadva.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/alkalmazások/createAsOwner | Hozzon létre minden típusú alkalmazást, és adja hozzá a létrehozót az első tulajdonosként. |
> | Microsoft. Directory/appRoleAssignments/createAsOwner | Alkalmazás-szerepkör-hozzárendelések létrehozása a létrehozóval az első tulajdonosként |
> | Microsoft. Directory/Oauth2permissiongrant objektumok/createAsOwner | OAuth 2,0 engedélyek létrehozása a létrehozóval az első tulajdonosként |
> | Microsoft. Directory/servicePrincipals/createAsOwner | Egyszerű szolgáltatás létrehozása a létrehozóval az első tulajdonosként |

## <a name="attack-payload-author"></a>Támadási hasznos adatok szerzője

Az ebben a szerepkörben lévő felhasználók támadási hasznos adatokat hozhatnak létre, de valójában nem indítják el vagy nem ütemezhetik azokat. A támadási adatok ezután a bérlő minden olyan rendszergazdája számára elérhetők lesznek, akik a szimuláció létrehozásához használhatják őket.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Office 365. protectionCenter/attackSimulator/hasznos adat/allProperties/allTasks | Támadási hasznos adatok létrehozása és kezelése a támadási szimulátorban |
> | Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/READ | A támadási szimulációval, a válaszokkal és a kapcsolódó képzésekkel kapcsolatos jelentések olvasása |

## <a name="attack-simulation-administrator"></a>Támadás szimulációjának rendszergazdája

Az ebben a szerepkörben lévő felhasználók létrehozhatják és kezelhetik a támadási szimulációk létrehozásával, a szimuláció indításával/ütemezésével, valamint a Szimulációs eredmények áttekintésével kapcsolatos szempontokat. Ennek a szerepkörnek a tagjai hozzáférnek a bérlő összes szimulációja számára.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Office 365. protectionCenter/attackSimulator/hasznos adat/allProperties/allTasks | Támadási hasznos adatok létrehozása és kezelése a támadási szimulátorban |
> | Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/READ | A támadási szimulációval, a válaszokkal és a kapcsolódó képzésekkel kapcsolatos jelentések olvasása |
> | Microsoft. Office 365. protectionCenter/attackSimulator/szimulációs/allProperties/allTasks | Támadási szimulációs sablonok létrehozása és kezelése a támadási szimulátorban |

## <a name="authentication-administrator"></a>Hitelesítés rendszergazdája

Az ezzel a szerepkörrel rendelkező felhasználók beállíthat vagy Visszaállíthatnak bármely hitelesítési módszert (beleértve a jelszavakat is) a nem rendszergazdák és néhány szerepkör számára. A hitelesítő rendszergazdák megkövetelhetik, hogy a nem rendszergazdák vagy bizonyos szerepkörökhöz rendelt felhasználók újra regisztráljanak a meglévő, nem jelszóval nem rendelkező hitelesítő adatokkal (például MFA vagy...), és visszavonhatók az MFA-t az **eszközön**, amely a következő bejelentkezéskor az MFA-t kéri. A hitelesítési rendszergazda által a hitelesítési módszerek olvasására vagy frissítésére szolgáló szerepkörök listáját lásd: [jelszó-visszaállítási engedélyek](#password-reset-permissions).

Az emelt [szintű hitelesítés rendszergazdai](#privileged-authentication-administrator) szerepköre jogosult az újbóli regisztrációra és a többtényezős hitelesítésre az összes felhasználó számára.

A [hitelesítési házirend rendszergazdai](#authentication-policy-administrator) szerepköre rendelkezik engedélyekkel a bérlő hitelesítési módszer házirendjének beállításához, amely meghatározza, hogy az egyes felhasználók hogyan regisztrálhatnak és használhatnak.

| Szerepkör | A felhasználó hitelesítési módszereinek kezelése | Felhasználónkénti MFA kezelése | MFA-beállítások kezelése | Hitelesítési módszer házirendjének kezelése | Jelszavas védelmi szabályzat kezelése |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Hitelesítés rendszergazdája | Igen, egyes felhasználók számára (lásd fent) | Igen, egyes felhasználók számára (lásd fent) | Nem | Nem | Nem |
| Kiemelt jogosultságú hitelesítés rendszergazdája| Igen minden felhasználó számára | Igen minden felhasználó számára | Nem | Nem | Nem |
| Hitelesítési házirend rendszergazdája | Nem |Nem | Igen | Igen | Igen |

> [!IMPORTANT]
> Az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják azoknak a személyeknek a hitelesítő adatait, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz, illetve a Azure Active Directoryon belüli és kívüli kritikus konfigurációhoz. A felhasználók hitelesítő adatainak módosítása azt jelentheti, hogy a felhasználó identitását és engedélyeit feltételezi. Például:
>
>* Az alkalmazás regisztrálása és a vállalati alkalmazások tulajdonosai, akik kezelhetik a saját alkalmazások hitelesítő adatait. Ezek az alkalmazások jogosultsági szintű engedélyekkel rendelkezhetnek az Azure AD-ben, és másutt nem kapják meg a hitelesítési rendszergazdákat. Ezen az elérési úton a hitelesítési rendszergazda feltételezheti az alkalmazás tulajdonosának identitását, majd az alkalmazás hitelesítő adatainak frissítésével továbbra is feltételezheti a Kiemelt alkalmazások identitását.
>* Az Azure-előfizetések tulajdonosai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz vagy az Azure-beli kritikus konfigurációhoz.
>* Biztonsági csoport és Microsoft 365 csoport tulajdonosai, akik kezelhetik a csoporttagság kezelését. Ezek a csoportok hozzáférést biztosíthatnak a bizalmas vagy magánjellegű információkhoz, illetve a kritikus konfigurációhoz az Azure AD-ben és máshol.
>* Az Azure AD-n kívül más szolgáltatásokban, például az Exchange Online-ban, az Office biztonsági és megfelelőségi központban és a humán erőforrás-rendszerekben található rendszergazdák.
>* Nem rendszergazdák, például vezetők, jogi tanácsadás és emberi erőforrások alkalmazottai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz.

> [!IMPORTANT]
> Ez a szerepkör jelenleg nem képes a felhasználónkénti MFA kezelésére az örökölt MFA felügyeleti portálon. Ugyanezek a függvények a [set-MsolUser](/powershell/module/msonline/set-msoluser) PARANCSMAGOT Azure ad PowerShell-modul használatával végezhetők el.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | A felhasználói frissítési tokenek érvénytelenítésével kényszerítheti ki a kijelentkezést |
> | Microsoft. Directory/felhasználók/strongAuthentication/Update | A felhasználók erős hitelesítési tulajdonságának frissítése |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="authentication-policy-administrator"></a>Hitelesítési házirend rendszergazdája

Az ezzel a szerepkörrel rendelkező felhasználók konfigurálhatják a hitelesítési módszerek házirendjét, a bérlői szintű MFA-beállításokat és a jelszavas védelemre vonatkozó házirendet. Ez a szerepkör engedélyt ad a jelszavas védelem beállításainak kezelésére: az intelligens zárolási konfigurációk és az egyéni tiltott jelszavak listájának frissítése.

A [hitelesítési rendszergazda](#authentication-administrator) és az emelt [szintű hitelesítés rendszergazdai](#privileged-authentication-administrator) szerepkörei engedéllyel rendelkeznek a regisztrált hitelesítési módszerek kezeléséhez a felhasználókon, és az újbóli regisztrációt és a többtényezős hitelesítést is kényszerítheti az összes felhasználó számára.

| Szerepkör | A felhasználó hitelesítési módszereinek kezelése | Felhasználónkénti MFA kezelése | MFA-beállítások kezelése | Hitelesítési módszer házirendjének kezelése | Jelszavas védelmi szabályzat kezelése |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Hitelesítés rendszergazdája | Igen, egyes felhasználók számára (lásd fent) | Igen, egyes felhasználók számára (lásd fent) | Nem | Nem | Nem |
| Kiemelt jogosultságú hitelesítés rendszergazdája| Igen minden felhasználó számára | Igen minden felhasználó számára | Nem | Nem | Nem |
| Hitelesítési házirend rendszergazdája | Nem | Nem | Igen | Igen | Igen |

> [!IMPORTANT]
> Ez a szerepkör jelenleg nem képes kezelni az MFA-beállításokat az örökölt MFA felügyeleti portálon.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/szervezet/strongAuthentication/Update | Szervezet erős hitelesítési tulajdonságainak frissítése |
> | Microsoft. Directory/userCredentialPolicies/Create | Hitelesítő szabályzatok létrehozása a felhasználók számára |
> | Microsoft. Directory/userCredentialPolicies/delete | Felhasználói hitelesítő szabályzatok törlése |
> | Microsoft. Directory/userCredentialPolicies/standard/olvasás | A hitelesítő adatokra vonatkozó szabályzatok általános tulajdonságainak olvasása a felhasználók számára |
> | Microsoft. Directory/userCredentialPolicies/tulajdonosok/olvasás | Felhasználói hitelesítő szabályzatok tulajdonosának beolvasása |
> | Microsoft. Directory/userCredentialPolicies/policyAppliedTo/READ | A Policy. appliesTo navigációs hivatkozásának olvasása |
> | Microsoft. Directory/userCredentialPolicies/Basic/Update | Alapszintű szabályzatok frissítése a felhasználók számára |
> | Microsoft. Directory/userCredentialPolicies/owners/Update | A felhasználói hitelesítő szabályzatok tulajdonosának frissítése |
> | Microsoft. Directory/userCredentialPolicies/tenantDefault/Update | A Policy. isOrganizationDefault tulajdonság frissítése |

## <a name="azure-ad-joined-device-local-administrator"></a>Azure AD-hez csatlakoztatott eszköz helyi rendszergazdája

Ez a szerepkör csak az [eszközbeállítások](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)további helyi rendszergazdája számára érhető el. Az ezzel a szerepkörrel rendelkező felhasználók a Azure Active Directoryhoz csatlakozó összes Windows 10-es eszközön a helyi számítógép-Rendszergazdák lesznek. Nem tudja kezelni az eszközök objektumait Azure Active Directoryban.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/groupSettings/standard/olvasás | Alapszintű tulajdonságok olvasása a csoport beállításainál |
> | Microsoft. Directory/groupSettingTemplates/standard/olvasás | Alapszintű tulajdonságok olvasása a csoport beállítási sablonjain |

## <a name="azure-devops-administrator"></a>Azure DevOps-rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik az Azure DevOps-szabályzatot, hogy az új Azure DevOps-szervezet létrehozása konfigurálható felhasználók vagy csoportok számára legyen korlátozva. A szerepkör felhasználói a vállalat Azure AD-szervezete által támogatott bármely Azure DevOps-szervezeten keresztül kezelhetik ezt a szabályzatot. Ez a szerepkör nem biztosít más Azure DevOps-specifikus engedélyeket (például a Project Collection-rendszergazdákat) a vállalat Azure AD-szervezete által támogatott bármely Azure DevOps-szervezeten belül.

Az összes Enterprise Azure DevOps-szabályzatot a felhasználók kezelhetik a szerepkörben.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. devOps/allEntities/allTasks | Az Azure DevOps olvasása és konfigurálása |

## <a name="azure-information-protection-administrator"></a>Azure Information Protection rendszergazda

Az ehhez a szerepkörhöz tartozó felhasználók minden engedéllyel rendelkeznek a Azure Information Protection szolgáltatásban. Ez a szerepkör lehetővé teszi a címkék konfigurálását a Azure Information Protection házirend számára, a védelmi sablonok kezelését és a védelem aktiválását. Ez a szerepkör nem biztosít engedélyeket az Identity Protection Center, a Privileged Identity Management, a monitor Microsoft 365 Service Health vagy az Office 365 biztonsági & megfelelőségi központjában.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. informationProtection/allEntities/allTasks | Azure Information Protection összes aspektusának kezelése |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="b2c-ief-keyset-administrator"></a>B2C IEF kulcskészlet rendszergazdája

A felhasználók létrehozhatják és kezelhetik a jogkivonat-titkosításhoz, a jogkivonat-aláírásokhoz és a jogcím titkosításához és visszafejtéséhez szükséges szabályzatokat és titkos kulcsokat. Ha új kulcsokat ad hozzá a meglévő kulcstárolóhoz, ez a korlátozott rendszergazda szükség szerint átválthatja a titkokat a meglévő alkalmazások befolyásolása nélkül. A felhasználók a létrehozásuk után is láthatják a titkok teljes tartalmát és a lejárat dátumát.

> [!IMPORTANT]
> Ez egy bizalmas szerepkör. A kulcskészlet rendszergazdai szerepkörét körültekintően kell naplózni, és körültekintően kell kiosztani az üzem előtti és az éles környezetben.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Az engedélyezési házirendek összes tulajdonságának olvasása és frissítése |

## <a name="b2c-ief-policy-administrator"></a>B2C IEF házirend rendszergazdája

Az ebben a szerepkörben lévő felhasználók létrehozhatnak, olvashatnak, frissíthetnek és törölhetnek minden egyéni szabályzatot a Azure AD B2Cban, így teljes körűen szabályozható a megfelelő Azure AD B2C-szervezet identitás-ellenőrzési keretrendszere. A szabályzatok szerkesztésével a felhasználó közvetlen kapcsolatot létesíthet külső identitás-szolgáltatókkal, módosíthatja a címtár-sémát, módosíthatja az összes felhasználó felé irányuló tartalmat (HTML, CSS, JavaScript), megváltoztathatja a hitelesítést, új felhasználókat hozhat létre, felhasználói adatokat küldhet külső rendszereknek, beleértve a teljes áttelepítést, és szerkesztheti az összes felhasználói adatot, beleértve a jelszavakat és a telefonszámokat Ezzel ellentétben ez a szerepkör nem változtathatja meg a titkosítási kulcsokat, vagy szerkesztheti a szervezeten belüli összevonáshoz használt titkokat.

> [!IMPORTANT]
> A B2 IEF szabályzat rendszergazdája rendkívül kényes szerepkör, amelyet az éles környezetben működő szervezeteknek nagyon korlátozott mértékben kell kiosztaniuk. Ezeknek a felhasználóknak a tevékenységeit alaposan meg kell vizsgálni, különösen az éles környezetben működő szervezeteknek.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/b2cTrustFrameworkPolicy/allProperties/allTasks | A kulcs-készletek olvasása és konfigurálása Azure Active Directory B2C |

## <a name="billing-administrator"></a>Számlázási adminisztrátor

Lebonyolítja a vásárlásokat, kezeli az előfizetéseket és a támogatási jegyeket, valamint figyeli a szolgáltatás állapotát.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/szervezet/alapszintű/frissítés | Alapszintű tulajdonságok frissítése a szervezeten belül |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Commerce. Billing/allEntities/allTasks | Az Office 365-számlázás összes aspektusának kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="cloud-application-administrator"></a>Felhőalkalmazás-rendszergazda

Az ebben a szerepkörben lévő felhasználók ugyanazok az engedélyek, mint az alkalmazás-rendszergazda szerepkör, az alkalmazásproxy felügyeletének lehetősége nélkül. Ez a szerepkör lehetővé teszi a vállalati alkalmazások és az alkalmazások regisztrálásának minden aspektusának létrehozását és felügyeletét. Az ehhez a szerepkörhöz hozzárendelt felhasználók nem lesznek hozzáadva tulajdonosként új alkalmazás-regisztrációk vagy vállalati alkalmazások létrehozásakor.

Ez a szerepkör a delegált engedélyek és az alkalmazás engedélyeinek megadását is lehetővé teszi, a Microsoft Graph és az Azure AD Graph alkalmazás engedélyeinek kivételével.

> [!IMPORTANT]
> Ez a kivétel azt jelenti, hogy továbbra is beleegyezik az alkalmazások engedélyeibe _más_ alkalmazásokhoz (például nem Microsoft alkalmazások vagy a regisztrált alkalmazások esetében). Továbbra is _kérheti_ ezeket az engedélyeket az alkalmazás regisztrálásának részeként, de _megadhatja_ , hogy ezek az engedélyek magasabb jogosultsági szintű rendszergazda, például globális rendszergazda számára szükségesek.
>
>Ez a szerepkör lehetővé teszi az alkalmazás hitelesítő adatainak kezelését. Az ehhez a szerepkörhöz hozzárendelt felhasználók hitelesítő adatokat adhatnak hozzá egy alkalmazáshoz, és ezekkel a hitelesítő adatokkal megszemélyesítheti az alkalmazás identitását. Ha az alkalmazás identitása hozzáférést kapott egy erőforráshoz, például felhasználói vagy más objektumok létrehozására vagy frissítésére, akkor a szerepkörhöz hozzárendelt felhasználó elvégezheti ezeket a műveleteket az alkalmazás megszemélyesítése közben. Előfordulhat, hogy az alkalmazás identitásának megszemélyesítése magasabb szintű jogosultságot jelent, mint amit a felhasználó a szerepkör-hozzárendelésein keresztül el tud végezni. Fontos tisztában lenni azzal, hogy egy felhasználó az alkalmazás-rendszergazdai szerepkörhöz való hozzárendelésével megszemélyesítheti az alkalmazás identitását.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/alkalmazások/létrehozás | Összes típusú alkalmazás létrehozása |
> | Microsoft. Directory/alkalmazások/törlés | Összes típusú alkalmazás törlése |
> | Microsoft. Directory/alkalmazások/appRoles/Update | A appRoles tulajdonság frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/célközönség/frissítés | Az alkalmazások célközönség tulajdonságának frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítés/frissítés | Hitelesítés frissítése minden típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/alapszintű/frissítés | Alkalmazások alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés | Alkalmazás hitelesítő adatainak frissítése |
> | Microsoft. Directory/alkalmazások/tulajdonosok/frissítés | Alkalmazások tulajdonosainak frissítése |
> | Microsoft. Directory/alkalmazások/engedélyek/frissítés | A kitett engedélyek és a szükséges engedélyek frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/házirendek/frissítés | Alkalmazások szabályzatának frissítése |
> | Microsoft. Directory/alkalmazások/ellenőrzés/frissítés | Applicationsverification tulajdonság frissítése |
> | Microsoft. Directory/alkalmazások/szinkronizálás/standard/olvasás | Az alkalmazásobjektum-objektumhoz társított kiépítési beállítások olvasása |
> | Microsoft. Directory/applicationTemplates/példány | Katalógusbeli alkalmazások példányainak megnyitása alkalmazás-sablonokból |
> | Microsoft. Directory/auditLogs/allProperties/READ | A naplók összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/Oauth2permissiongrant objektumok/allProperties/allTasks | OAuth 2,0 engedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/applicationPolicies/Create | Alkalmazás-házirendek létrehozása |
> | Microsoft. Directory/applicationPolicies/delete | Alkalmazás-házirendek törlése |
> | Microsoft. Directory/applicationPolicies/standard/olvasás | Az alkalmazás-házirendek szabványos tulajdonságainak olvasása |
> | Microsoft. Directory/applicationPolicies/tulajdonosok/olvasás | Az alkalmazás-szabályzatok tulajdonosának beolvasása |
> | Microsoft. Directory/applicationPolicies/policyAppliedTo/READ | Az objektumok listájára alkalmazott Használati szabályzatok olvasása |
> | Microsoft. Directory/applicationPolicies/Basic/Update | Az alkalmazás-házirendek szabványos tulajdonságainak frissítése |
> | Microsoft. Directory/applicationPolicies/owners/Update | Az alkalmazás-házirendek tulajdonosi tulajdonságának frissítése |
> | Microsoft. Directory/provisioningLogs/allProperties/READ | A kiépítési naplók összes tulajdonságának olvasása |
> | Microsoft. Directory/servicePrincipals/Create | Szolgáltatásnevek létrehozása |
> | Microsoft. Directory/servicePrincipals/delete | Egyszerű szolgáltatások törlése |
> | Microsoft. Directory/servicePrincipals/letiltás | Egyszerű szolgáltatások letiltása |
> | Microsoft. Directory/servicePrincipals/Enable | Szolgáltatásnevek engedélyezése |
> | Microsoft. Directory/servicePrincipals/getPasswordSingleSignOnCredentials | A jelszó egyszeri bejelentkezési hitelesítő adatainak kezelése a szolgáltatás rendszerbiztonsági szolgáltatásainál |
> | Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage | Alkalmazás-kiépítési titkok és hitelesítő adatok kezelése |
> | Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Alkalmazás-kiépítési szinkronizálás feladatok elindítása, újraindítása és szüneteltetése |
> | Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | Alkalmazás-kiépítési szinkronizálás-feladatok és-séma létrehozása és kezelése |
> | Microsoft. Directory/servicePrincipals/managePasswordSingleSignOnCredentials | Jelszó egyszeri bejelentkezési hitelesítő adatainak beolvasása az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Application-admin | Az alkalmazás engedélyeinek és a delegált engedélyeknek a jóváhagyása bármely felhasználó vagy minden felhasználó nevében, a Microsoft Graph és az Azure AD Graph alkalmazás engedélyeinek kivételével  |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Egyszerű szolgáltatás szerepkör-hozzárendeléseinek frissítése |
> | Microsoft. Directory/servicePrincipals/hallgatóság/frissítés | A célközönség tulajdonságainak frissítése az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/hitelesítés/frissítés | Az egyszerű szolgáltatások hitelesítési tulajdonságainak frissítése |
> | Microsoft. Directory/servicePrincipals/Basic/Update | Egyszerű tulajdonságok frissítése az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/hitelesítő adatok/frissítés | Egyszerű szolgáltatásnév hitelesítő adatainak frissítése |
> | Microsoft. Directory/servicePrincipals/owners/Update | Egyszerű szolgáltatásnév tulajdonosainak frissítése |
> | Microsoft. Directory/servicePrincipals/engedélyek/frissítés | Egyszerű szolgáltatásnév engedélyeinek frissítése |
> | Microsoft. Directory/servicePrincipals/házirendek/frissítés | Az egyszerű szolgáltatások szabályzatának frissítése |
> | Microsoft. Directory/servicePrincipals/tag/frissítés | Az egyszerű szolgáltatásnév címkézési tulajdonságának frissítése |
> | Microsoft. Directory/servicePrincipals/szinkronizálás/standard/olvasás | Az egyszerű szolgáltatáshoz társított kiépítési beállítások olvasása |
> | Microsoft. Directory/signInReports/allProperties/READ | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="cloud-device-administrator"></a>Felhőalapú eszköz rendszergazdája

A szerepkörben lévő felhasználók engedélyezheti, letilthatja és törölheti az eszközöket az Azure AD-ben, és beolvashatja a Windows 10 BitLocker-kulcsokat (ha vannak) a Azure Portal. A szerepkör nem biztosít engedélyeket az eszköz egyéb tulajdonságainak kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/READ | A naplók összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/bitlockerKeys/kulcs/olvasás | BitLocker-metaadatok és-kulcs olvasása eszközökön |
> | Microsoft. Directory/eszközök/törlés | Eszközök törlése az Azure AD-ből |
> | Microsoft. Directory/eszközök/letiltás | Eszközök letiltása az Azure AD-ben |
> | Microsoft. Directory/eszközök/engedélyezés | Eszközök engedélyezése az Azure AD-ben |
> | Microsoft. Directory/eszközök/extensionAttributes/Update | A Devices. extensionAttributes tulajdonság összes értékének frissítése |
> | Microsoft. Directory/deviceManagementPolicies/standard/olvasás | Általános tulajdonságok olvasása az Eszközkezelő alkalmazás házirendjein |
> | Microsoft. Directory/deviceManagementPolicies/Basic/Update | Az alapszintű tulajdonságok frissítése az eszközkezelés alkalmazás-házirendjein |
> | Microsoft. Directory/deviceRegistrationPolicy/standard/olvasás | Szabványos tulajdonságok olvasása az eszköz regisztrációs házirendjeiben |
> | Microsoft. Directory/deviceRegistrationPolicy/Basic/Update | Az eszköz regisztrációs házirendjeinek alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/signInReports/allProperties/READ | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |

## <a name="compliance-administrator"></a>Megfelelőségi rendszergazda

Az ehhez a szerepkörhöz tartozó felhasználók jogosultak a megfelelőséggel kapcsolatos szolgáltatások kezelésére a Microsoft 365 megfelelőségi központban, Microsoft 365 felügyeleti központban, az Azure-ban és az Office 365 biztonsági & megfelelőségi központban. A engedményesek az Exchange felügyeleti központban és a Teams & a Skype vállalati felügyeleti központokban is kezelheti az összes funkciót, és támogatási jegyeket hozhat létre az Azure-hoz és a Microsoft 365hoz. További információ a [Microsoft 365 rendszergazdai szerepkörökről](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)című témakörben található.

In | Elvégezhető
----- | ----------
[Microsoft 365 megfelelőségi központ](https://protection.office.com) | A szervezet adatainak védelme és kezelése Microsoft 365 szolgáltatások között<br>Megfelelőségi riasztások kezelése
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | A szervezet szabályozási megfelelőségi tevékenységeinek nyomon követése, kiosztása és ellenőrzése
[Office 365 biztonsági & megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Az adatszabályozás kezelése<br>Jogi és adatvizsgálati műveletek végrehajtása<br>Az adattulajdonosi kérelem kezelése<br><br>Ez a szerepkör ugyanazokkal az engedélyekkel rendelkezik, mint a [megfelelőségi rendszergazda RoleGroup](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) az Office 365 Security & a megfelelőségi központ szerepköralapú hozzáférés-vezérlése.
[megkezdése](/intune/role-based-access-control) | Az összes Intune-naplózási adatbázis megtekintése
[Cloud App Security](/cloud-app-security/manage-admins) | Csak olvasási jogosultsággal rendelkezik, és képes kezelni a riasztásokat<br>Házirendeket hozhat létre és módosíthat, valamint engedélyezheti a fájlok irányítási műveleteit<br>Megtekintheti az összes beépített jelentést adatkezelés

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Directory/entitlementManagement/allProperties/READ | Az Azure AD-jogosultságok kezelésének összes tulajdonságának olvasása |
> | Microsoft. Office 365. complianceManager/allEntities/allTasks | Az Office 365 megfelelőség-kezelő összes aspektusának kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="compliance-data-administrator"></a>Megfelelőségi adatkezelő

Az ehhez a szerepkörhöz tartozó felhasználók rendelkeznek a Microsoft 365 megfelelőségi központban, Microsoft 365 felügyeleti központban és az Azure-ban lévő adatkövetési jogosultságokkal. A felhasználók az Exchange felügyeleti központban, a megfelelőség-kezelőben és a Teams & Skype vállalati felügyeleti központban is nyomon követhetik a megfelelőségi és támogatási jegyeket az Azure-hoz és Microsoft 365hoz. [Ez a dokumentáció](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) részletesen ismerteti a megfelelőségi rendszergazda és a megfelelőségi adatok rendszergazdája közötti különbségeket.

In | Elvégezhető
----- | ----------
[Microsoft 365 megfelelőségi központ](https://protection.office.com) | Megfelelőséggel kapcsolatos szabályzatok monitorozása Microsoft 365 szolgáltatások között<br>Megfelelőségi riasztások kezelése
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | A szervezet szabályozási megfelelőségi tevékenységeinek nyomon követése, kiosztása és ellenőrzése
[Office 365 biztonsági & megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Az adatszabályozás kezelése<br>Jogi és adatvizsgálati műveletek végrehajtása<br>Az adattulajdonosi kérelem kezelése<br><br>Ez a szerepkör ugyanazokkal az engedélyekkel rendelkezik, mint a [megfelelőségi adat rendszergazdai RoleGroup](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) az Office 365 Security & a megfelelőségi központ szerepköralapú hozzáférés-vezérlése.
[megkezdése](/intune/role-based-access-control) | Az összes Intune-naplózási adatbázis megtekintése
[Cloud App Security](/cloud-app-security/manage-admins) | Csak olvasási jogosultsággal rendelkezik, és képes kezelni a riasztásokat<br>Házirendeket hozhat létre és módosíthat, valamint engedélyezheti a fájlok irányítási műveleteit<br>Megtekintheti az összes beépített jelentést adatkezelés

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése Microsoft Cloud App Security |
> | Microsoft. Azure. informationProtection/allEntities/allTasks | Azure Information Protection összes aspektusának kezelése |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. complianceManager/allEntities/allTasks | Az Office 365 megfelelőség-kezelő összes aspektusának kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="conditional-access-administrator"></a>Feltételes hozzáférésű rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik Azure Active Directory feltételes hozzáférési beállításokat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/conditionalAccessPolicies/Create | Feltételes hozzáférési szabályzatok létrehozása |
> | Microsoft. Directory/conditionalAccessPolicies/delete | Feltételes hozzáférési szabályzatok törlése |
> | Microsoft. Directory/conditionalAccessPolicies/standard/olvasás | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/tulajdonosok/olvasás | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/READ | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/Basic/Update | Feltételes hozzáférési szabályzatok alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/conditionalAccessPolicies/owners/Update | A policies. conditionalAccess tulajdonság frissítése |
> | Microsoft. Directory/conditionalAccessPolicies/tenantDefault/Update | A policies. conditionalAccess tulajdonság frissítése |
> | Microsoft. Directory/crossTenantAccessPolicies/Create | Több-bérlős hozzáférési szabályzatok létrehozása |
> | Microsoft. Directory/crossTenantAccessPolicies/delete | Több-bérlős hozzáférési szabályzatok törlése |
> | Microsoft. Directory/crossTenantAccessPolicies/standard/olvasás | A több-bérlős hozzáférési szabályzatok alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/crossTenantAccessPolicies/tulajdonosok/olvasás | A több-bérlős hozzáférési szabályzatok tulajdonosainak olvasása |
> | Microsoft. Directory/crossTenantAccessPolicies/policyAppliedTo/READ | A több-bérlős hozzáférési házirendek policyAppliedTo tulajdonságának olvasása |
> | Microsoft. Directory/crossTenantAccessPolicies/Basic/Update | A több-bérlős hozzáférési szabályzatok alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/crossTenantAccessPolicies/owners/Update | A több-bérlős hozzáférési házirendek tulajdonosainak frissítése |
> | Microsoft. Directory/crossTenantAccessPolicies/tenantDefault/Update | Az alapértelmezett bérlő frissítése a több-bérlős hozzáférési házirendek esetében |

## <a name="customer-lockbox-access-approver"></a>Ügyfél-kulcstároló hozzáférési jóváhagyója

A szervezet [Ügyfélszéf-kérelmeit](/office365/admin/manage/customer-lockbox-requests) kezeli. E-mail-értesítéseket kapnak Ügyfélszéf kérelmekről, és a Microsoft 365 felügyeleti központban hagyhatják jóvá és tagadhatják meg a kérelmeket. Emellett be-vagy kikapcsolhatja a Ügyfélszéf funkciót. Csak a globális rendszergazdák állíthatják alaphelyzetbe a szerepkörhöz rendelt személyek jelszavát.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Office 365. kulcstároló/allEntities/allTasks | Ügyfélszéf összes aspektusának kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="desktop-analytics-administrator"></a>Asztali elemzési rendszergazda

Az ebben a szerepkörben lévő felhasználók kezelhetik az asztali elemzési és az Office testreszabási & házirend-szolgáltatásait. Az asztali elemzések esetében ez magában foglalja az eszközök leltárának megtekintését, a központi telepítési tervek létrehozását, a központi telepítés és az állapot megtekintését. Az Office testreszabása & házirend-szolgáltatás esetében ez a szerepkör lehetővé teszi a felhasználóknak az Office-házirendek kezelését.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. desktopAnalytics/allEntities/allTasks | Az asztali elemzések összes aspektusának kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="directory-readers"></a>Directory-olvasók

Az ebben a szerepkörben lévő felhasználók beolvashatják az alapszintű címtár-információkat. A következő szerepkört kell használni:

* A vendég felhasználói számára az olvasási hozzáférés megadása helyett egy adott készletet adjon meg.
* A nem rendszergazdai jogosultságú felhasználók megadott készletének megadása Azure Portal ha "az Azure AD-portálhoz való hozzáférés korlátozása csak a rendszergazdák számára" beállítás értéke "yes".
* Egyszerű szolgáltatásokhoz való hozzáférés biztosítása a címtár. Read. All nem lehetőség.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/Administrativeunit/standard/olvasás | Alapszintű tulajdonságok olvasása a felügyeleti egységeken |
> | Microsoft. Directory/Administrativeunit/tagok/olvasás | Felügyeleti egységek tagjainak olvasása |
> | Microsoft. Directory/alkalmazások/standard/olvasás | Alkalmazások általános tulajdonságainak olvasása |
> | Microsoft. Directory/alkalmazások/tulajdonosok/olvasás | Alkalmazások tulajdonosainak olvasása |
> | Microsoft. Directory/alkalmazások/házirendek/olvasás | Alkalmazások szabályzatának olvasása |
> | Microsoft. Directory/Contacts/standard/olvasás | A névjegyek alapszintű tulajdonságainak olvasása az Azure AD-ben |
> | Microsoft. Directory/Contacts/memberOf/READ | A csoporttagság beolvasása az Azure AD összes ügyfeléhez |
> | Microsoft. Directory/szerződések/standard/olvasás | A partneri szerződések alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/eszközök/standard/olvasás | Alapszintű tulajdonságok olvasása az eszközökön |
> | Microsoft. Directory/eszközök/memberOf/olvasás | Eszközök tagságának olvasása |
> | Microsoft. Directory/eszközök/registeredOwners/olvasás | Eszközök regisztrált tulajdonosainak olvasása |
> | Microsoft. Directory/eszközök/registeredUsers/olvasás | Eszközök regisztrált felhasználóinak olvasása |
> | Microsoft. Directory/directoryRoles/standard/olvasás | Alapszintű tulajdonságok frissítése az Azure AD-szerepkörökben |
> | Microsoft. Directory/directoryRoles/eligibleMembers/READ | Az Azure AD-szerepkörök jogosult tagjainak beolvasása |
> | Microsoft. Directory/directoryRoles/tagok/olvasás | Az Azure AD-szerepkörök összes tagjának olvasása |
> | Microsoft. Directory/tartományok/standard/olvasás | Alapszintű tulajdonságok olvasása tartományokban |
> | Microsoft. Directory/csoportok/standard/olvasás | A csoportok alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/csoportok/appRoleAssignments/olvasás | Csoportok alkalmazás-szerepkör-hozzárendeléseinek beolvasása |
> | Microsoft. Directory/csoportok/memberOf/olvasás | Azon csoportok olvasása, amelyekben egy csoport tagja az Azure AD-ben |
> | Microsoft. Directory/csoportok/tagok/olvasás | Csoportok tagjainak olvasása |
> | Microsoft. Directory/csoportok/tulajdonosok/olvasás | Csoportok tulajdonosainak olvasása |
> | Microsoft. Directory/csoportok/beállítások/olvasás | Csoportok beállításainak olvasása |
> | Microsoft. Directory/groupSettings/standard/olvasás | Alapszintű tulajdonságok olvasása a csoport beállításainál |
> | Microsoft. Directory/groupSettingTemplates/standard/olvasás | Alapszintű tulajdonságok olvasása a csoport beállítási sablonjain |
> | Microsoft. Directory/Oauth2permissiongrant objektumok/standard/olvasás | Alapszintű tulajdonságok olvasása a OAuth 2,0 engedélyekkel |
> | Microsoft. Directory/szervezet/standard/olvasás | Alapszintű tulajdonságok olvasása egy szervezeten belül |
> | Microsoft. Directory/szervezet/trustedCAsForPasswordlessAuth/olvasás | Megbízható hitelesítésszolgáltatók olvasása a jelszóval nem rendelkező hitelesítéshez |
> | Microsoft. Directory/applicationPolicies/standard/olvasás | Az alkalmazás-házirendek szabványos tulajdonságainak olvasása |
> | Microsoft. Directory/roleAssignments/standard/olvasás | A szerepkör-hozzárendelések alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/roleDefinitions/standard/olvasás | A szerepkör-definíciók alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/READ | Egyszerű szolgáltatás szerepkör-hozzárendeléseinek beolvasása |
> | Microsoft. Directory/servicePrincipals/appRoleAssignments/READ | Az egyszerű szolgáltatásokhoz hozzárendelt szerepkör-hozzárendelések olvasása |
> | Microsoft. Directory/servicePrincipals/standard/olvasás | Az egyszerű szolgáltatások alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/servicePrincipals/memberOf/READ | Csoporttagság beolvasása az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/Oauth2permissiongrant objektumok/READ | Delegált engedélyek beolvasása az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/tulajdonosok/olvasás | Az egyszerű szolgáltatásnév tulajdonosának beolvasása |
> | Microsoft. Directory/servicePrincipals/ownedObjects/READ | Az egyszerű szolgáltatásnév tulajdonosi objektumainak beolvasása |
> | Microsoft. Directory/servicePrincipals/házirendek/olvasás | Egyszerű szolgáltatásnév-szabályzatok beolvasása |
> | Microsoft. Directory/subscribedSkus/standard/olvasás | Előfizetések alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/felhasználók/standard/olvasás | Alapszintű tulajdonságok olvasása a felhasználóknál |
> | Microsoft. Directory/felhasználók/appRoleAssignments/olvasás | Alkalmazások szerepkör-hozzárendeléseinek beolvasása |
> | Microsoft. Directory/felhasználók/directReports/olvasás | A közvetlen jelentések beolvasása a felhasználók számára |
> | Microsoft. Directory/felhasználók/kezelő/olvasás | Felhasználók beolvasása |
> | Microsoft. Directory/felhasználók/memberOf/olvasás | A felhasználók csoport tagságának beolvasása |
> | Microsoft. Directory/felhasználók/Oauth2permissiongrant objektumok/olvasás | Delegált engedélyek beolvasása a felhasználók számára |
> | Microsoft. Directory/felhasználók/ownedDevices/olvasás | A felhasználók tulajdonában lévő eszközök beolvasása |
> | Microsoft. Directory/felhasználók/ownedObjects/olvasás | A felhasználók tulajdonában lévő objektumok beolvasása |
> | Microsoft. Directory/felhasználók/registeredDevices/olvasás | Felhasználók regisztrált eszközeinek olvasása |

## <a name="directory-synchronization-accounts"></a>Címtár-szinkronizálási fiókok

Ne használja. Ezt a szerepkört a rendszer automatikusan hozzárendeli a Azure AD Connect szolgáltatáshoz, és semmilyen más használatra nem javasolt vagy nem támogatott.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/alkalmazások/létrehozás | Összes típusú alkalmazás létrehozása |
> | Microsoft. Directory/alkalmazások/törlés | Összes típusú alkalmazás törlése |
> | Microsoft. Directory/alkalmazások/appRoles/Update | A appRoles tulajdonság frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/célközönség/frissítés | Az alkalmazások célközönség tulajdonságának frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítés/frissítés | Hitelesítés frissítése minden típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/alapszintű/frissítés | Alkalmazások alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés | Alkalmazás hitelesítő adatainak frissítése |
> | Microsoft. Directory/alkalmazások/tulajdonosok/frissítés | Alkalmazások tulajdonosainak frissítése |
> | Microsoft. Directory/alkalmazások/engedélyek/frissítés | A kitett engedélyek és a szükséges engedélyek frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/házirendek/frissítés | Alkalmazások szabályzatának frissítése |
> | Microsoft. Directory/szervezet/rSync/Update | A szervezeti címtár-szinkronizálási tulajdonság frissítése |
> | Microsoft. Directory/házirendek/létrehozás | Szabályzatok létrehozása az Azure AD-ben |
> | Microsoft. Directory/házirendek/törlés | Szabályzatok törlése az Azure AD-ben |
> | Microsoft. Directory/házirendek/standard/olvasás | A szabályzatok alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/házirendek/tulajdonosok/olvasás | Szabályzatok tulajdonosának olvasása |
> | Microsoft. Directory/házirendek/policyAppliedTo/olvasás | A policies. policyAppliedTo tulajdonság olvasása |
> | Microsoft. Directory/házirendek/alapszintű/frissítés | Szabályzatok alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/házirendek/tulajdonosok/frissítés | Szabályzatok tulajdonosának frissítése |
> | Microsoft. Directory/házirendek/tenantDefault/Update | Alapértelmezett szervezeti házirendek frissítése |
> | Microsoft. Directory/servicePrincipals/Create | Szolgáltatásnevek létrehozása |
> | Microsoft. Directory/servicePrincipals/delete | Egyszerű szolgáltatások törlése |
> | Microsoft. Directory/servicePrincipals/Enable | Szolgáltatásnevek engedélyezése |
> | Microsoft. Directory/servicePrincipals/letiltás | Egyszerű szolgáltatások letiltása |
> | Microsoft. Directory/servicePrincipals/getPasswordSingleSignOnCredentials | A jelszó egyszeri bejelentkezési hitelesítő adatainak kezelése a szolgáltatás rendszerbiztonsági szolgáltatásainál |
> | Microsoft. Directory/servicePrincipals/managePasswordSingleSignOnCredentials | Jelszó egyszeri bejelentkezési hitelesítő adatainak beolvasása az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/READ | Egyszerű szolgáltatás szerepkör-hozzárendeléseinek beolvasása |
> | Microsoft. Directory/servicePrincipals/appRoleAssignments/READ | Az egyszerű szolgáltatásokhoz hozzárendelt szerepkör-hozzárendelések olvasása |
> | Microsoft. Directory/servicePrincipals/standard/olvasás | Az egyszerű szolgáltatások alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/servicePrincipals/memberOf/READ | Csoporttagság beolvasása az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/Oauth2permissiongrant objektumok/READ | Delegált engedélyek beolvasása az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/tulajdonosok/olvasás | Az egyszerű szolgáltatásnév tulajdonosának beolvasása |
> | Microsoft. Directory/servicePrincipals/ownedObjects/READ | Az egyszerű szolgáltatásnév tulajdonosi objektumainak beolvasása |
> | Microsoft. Directory/servicePrincipals/házirendek/olvasás | Egyszerű szolgáltatásnév-szabályzatok beolvasása |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Egyszerű szolgáltatás szerepkör-hozzárendeléseinek frissítése |
> | Microsoft. Directory/servicePrincipals/hallgatóság/frissítés | A célközönség tulajdonságainak frissítése az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/hitelesítés/frissítés | Az egyszerű szolgáltatások hitelesítési tulajdonságainak frissítése |
> | Microsoft. Directory/servicePrincipals/Basic/Update | Egyszerű tulajdonságok frissítése az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/hitelesítő adatok/frissítés | Egyszerű szolgáltatásnév hitelesítő adatainak frissítése |
> | Microsoft. Directory/servicePrincipals/owners/Update | Egyszerű szolgáltatásnév tulajdonosainak frissítése |
> | Microsoft. Directory/servicePrincipals/engedélyek/frissítés | Egyszerű szolgáltatásnév engedélyeinek frissítése |
> | Microsoft. Directory/servicePrincipals/házirendek/frissítés | Az egyszerű szolgáltatások szabályzatának frissítése |
> | Microsoft. Directory/servicePrincipals/tag/frissítés | Az egyszerű szolgáltatásnév címkézési tulajdonságának frissítése |

## <a name="directory-writers"></a>Címtár-írók

A szerepkör felhasználói a felhasználók, csoportok és egyszerű szolgáltatások alapvető információit tudják olvasni és frissíteni. Ezt a szerepkört csak olyan alkalmazásokhoz rendelheti hozzá, amelyek nem támogatják az [engedélyezési keretrendszert](../develop/quickstart-register-app.md). Nem rendelhető hozzá egyetlen felhasználóhoz sem.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/csoportok/assignLicense | Licencek kiosztása csoportok számára csoport alapú licenceléshez |
> | Microsoft. Directory/csoportok/létrehozás | Csoportok létrehozása a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása csoport alapú licenceléshez |
> | Microsoft. Directory/csoportok/alapszintű/frissítés | A csoportok alapszintű tulajdonságainak frissítése, a szerepkörhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/besorolás/frissítés | A csoportok besorolási tulajdonságának frissítése, a szerepkörhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/dynamicMembershipRule/Update | A dinamikus tagsági szabályok frissítése a szerepkörökhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/groupType/Update | Egy csoport groupType tulajdonságának frissítése |
> | Microsoft. Directory/csoportok/tagok/frissítés | Csoportok tagjainak frissítése, a szerepkörhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/onPremWriteBack/Update | Azure AD-csoportok frissítése a helyi környezetbe való visszaíráshoz |
> | Microsoft. Directory/csoportok/tulajdonosok/frissítés | Csoportok tulajdonosainak frissítése, a szerepkörhöz hozzárendelhető csoportok kizárása |
> | Microsoft. Directory/csoportok/beállítások/frissítés | Csoportok beállításainak frissítése |
> | Microsoft. Directory/csoportok/láthatóság/frissítés | A csoportok láthatósági tulajdonságának frissítése |
> | Microsoft. Directory/groupSettings/Create | Csoportbeállítások létrehozása |
> | Microsoft. Directory/groupSettings/delete | Csoportbeállítások törlése |
> | Microsoft. Directory/groupSettings/Basic/Update | Alapszintű tulajdonságok frissítése a csoport beállításainál |
> | Microsoft. Directory/Oauth2permissiongrant objektumok/Create | OAuth 2,0 engedélyek létrehozása |
> | Microsoft. Directory/Oauth2permissiongrant objektumok/Basic/Update | OAuth 2,0 engedélyek frissítése |
> | Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage | Alkalmazás-kiépítési titkok és hitelesítő adatok kezelése |
> | Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Alkalmazás-kiépítési szinkronizálás feladatok elindítása, újraindítása és szüneteltetése |
> | Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | Alkalmazás-kiépítési szinkronizálás-feladatok és-séma létrehozása és kezelése |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-all-Application-permissions | A szolgáltatás közvetlen hozzáférésének biztosítása a pricipal |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Egyszerű szolgáltatás szerepkör-hozzárendeléseinek frissítése |
> | Microsoft. Directory/felhasználók/assignLicense | Felhasználói licencek kezelése |
> | Microsoft. Directory/felhasználók/létrehozás | Felhasználók hozzáadása |
> | Microsoft. Directory/felhasználók/letiltás | Felhasználók letiltása |
> | Microsoft. Directory/felhasználók/engedélyezés | Felhasználók engedélyezése |
> | Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | A felhasználói frissítési tokenek érvénytelenítésével kényszerítheti ki a kijelentkezést |
> | Microsoft. Directory/felhasználók/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása a felhasználók számára |
> | Microsoft. Directory/felhasználók/alapszintű/frissítés | A felhasználók alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/felhasználók/kezelő/frissítés | Frissítési kezelő felhasználók számára |
> | Microsoft. Directory/felhasználók/userPrincipalName/Update | Felhasználó egyszerű nevének frissítése |

## <a name="domain-name-administrator"></a>Tartománynév-rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a tartományneveket (olvasás, Hozzáadás, ellenőrzés, frissítés és törlés). Emellett a felhasználókra, csoportokra és alkalmazásokra vonatkozó címtáradatokat is elolvashatnak, mivel ezek az objektumok tartományi függőségekkel rendelkeznek. A helyszíni környezetekben az ezzel a szerepkörrel rendelkező felhasználók konfigurálhatják az összevonáshoz tartozó tartományneveket, hogy a társított felhasználók mindig a helyszínen legyenek hitelesítve. Ezek a felhasználók ezután egyszeri bejelentkezéssel jelentkezhetnek be az Azure AD-alapú szolgáltatásba a helyszíni jelszavuk használatával. Az összevonási beállításokat Azure AD Connect használatával kell szinkronizálni, így a felhasználóknak a Azure AD Connect kezelésére is jogosultsággal kell rendelkezniük.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/tartományok/allProperties/allTasks | Tartományok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |

## <a name="dynamics-365-administrator"></a>Dynamics 365-rendszergazda

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Dynamics 365 online-on belül, ha a szolgáltatás megtalálható, valamint a támogatási jegyek felügyeletének és a szolgáltatás állapotának figyelésének lehetősége. További információ: [a szolgáltatás-rendszergazdai szerepkör használata az Azure ad-szervezet kezeléséhez](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "Dynamics 365 szolgáltatás-rendszergazdaként" azonosítható. A [Azure Portal](https://portal.azure.com)a "Dynamics 365 Administrator".

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. dynamics365/allEntities/allTasks | A Dynamics 365 összes aspektusának kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="exchange-administrator"></a>Exchange-rendszergazda

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Exchange Online-ban, ha a szolgáltatás jelen van. Emellett képes az összes Microsoft 365-csoport létrehozására és felügyeletére, a támogatási jegyek kezelésére és a szolgáltatás állapotának figyelésére. További információ a [Microsoft 365 rendszergazdai szerepkörökről](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "Exchange szolgáltatás-rendszergazdaként" azonosítható. A [Azure Portal](https://portal.azure.com)az "Exchange Administrator". Az [Exchange felügyeleti központban](https://go.microsoft.com/fwlink/p/?LinkID=529144)az "Exchange Online Administrator".

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/csoportok/hiddenMembers/olvasás | Csoport rejtett tagjainak olvasása |
> | Microsoft. Directory/csoportok. egyesített/létrehozás | Szerepkörökhöz hozzárendelhető csoportok kizárásával Microsoft 365 csoportok létrehozása |
> | Microsoft. Directory/groups. Unified/delete | Szerepkör-hozzárendelhető csoportok kizárása Microsoft 365 csoportok törlése |
> | Microsoft. Directory/groups. Unified/Restore | Microsoft 365 csoportok visszaállítása |
> | Microsoft. Directory/csoportok. Unified/Basic/Update | Microsoft 365 csoportok alapszintű tulajdonságainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok. Unified/tagok/frissítés | Microsoft 365 csoportok tagjainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/groups. Unified/owners/Update | Microsoft 365 csoportok tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. Exchange/allEntities/allTasks | Az Exchange Online összes aspektusának kezelése |
> | Microsoft. Office 365. Network/Performance/allProperties/READ | Az összes hálózati teljesítmény tulajdonságainak olvasása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/READ | Office 365-használati jelentések olvasása |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="external-id-user-flow-administrator"></a>Külső azonosító – felhasználói folyamat rendszergazdája

Az ezzel a szerepkörrel rendelkező felhasználók felhasználói folyamatokat hozhatnak létre és kezelhetnek (más néven "beépített" szabályzatokat) a Azure Portalban. Ezek a felhasználók testre szabhatják a HTML/CSS/JavaScript tartalmakat, módosíthatják az MFA követelményeit, kiválaszthatják a jogcímek jogcímeit, kezelhetik az API-összekötőket, és konfigurálhatják a munkamenet-beállításokat az Azure AD-szervezet összes felhasználói Másfelől azonban ez a szerepkör nem teszi lehetővé a felhasználói adat áttekintését, illetve a szervezeti sémában foglalt attribútumok módosítását. Az Identity Experience Framework-szabályzatok (más néven egyéni szabályzatok) változásai a szerepkör hatókörén kívül esnek.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/b2cUserFlow/allProperties/allTasks | Felhasználói attribútumok olvasása és konfigurálása a Azure Active Directory B2Cban |

## <a name="external-id-user-flow-attribute-administrator"></a>Külső azonosító felhasználói flow-attribútumának rendszergazdája

Az ezzel a szerepkörrel rendelkező felhasználók hozzáadhatnak vagy törölhetnek egyéni attribútumokat az Azure AD-szervezet összes felhasználói folyamata számára. Így az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják vagy hozzáadhatják az új elemeket a végfelhasználói sémához, és befolyásolhatják az összes felhasználói folyamat viselkedését, és közvetetten a végfelhasználók számára megkérhetik az adatokat, és végső soron az alkalmazások számára is elküldhetik azt. Ez a szerepkör nem szerkesztheti a felhasználói folyamatokat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/b2cUserAttribute/allProperties/allTasks | Egyéni szabályzatok olvasása és konfigurálása Azure Active Directory B2C |

## <a name="external-identity-provider-administrator"></a>Külső identitás-szolgáltató rendszergazdája

Ez a rendszergazda kezeli az Azure AD-szervezetek és a külső identitás-szolgáltatók közötti összevonást. Ezzel a szerepkörrel a felhasználók új identitás-szolgáltatókat adhatnak hozzá, és konfigurálhatják az összes rendelkezésre álló beállítást (például a hitelesítési útvonalat, a szolgáltatás AZONOSÍTÓját, a hozzárendelt kulcstárolókat). Ez a felhasználó engedélyezheti az Azure AD-szervezet számára, hogy megbízzon a külső identitás-szolgáltatóktól érkező hitelesítésekben. A végfelhasználói élményekre gyakorolt hatás a szervezet típusától függ:

* Azure AD-szervezetek alkalmazottak és partnerek számára: az összevonások (például a Gmailtel együtt) azonnali hatással lesznek az összes olyan vendég meghívóra, amely még nincs beváltva. Lásd: [a Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói](../external-identities/google-federation.md)számára.
* Azure Active Directory B2C szervezetek: az összevonás (például a Facebook vagy egy másik Azure AD-szervezet) hozzáadása nem befolyásolja azonnal a végfelhasználói folyamatokat, amíg az identitás-szolgáltató hozzá nem adódik a felhasználói folyamatokban (más néven beépített szabályzathoz). Lásd: [Microsoft-fiók konfigurálása identitás-szolgáltatóként](../../active-directory-b2c/identity-provider-microsoft-account.md) egy példához. A felhasználói folyamatok módosításához a "B2C felhasználói folyamat rendszergazdája" korlátozott szerepkörre van szükség.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/identityProviders/allProperties/allTasks | Identitás-szolgáltatók olvasása és konfigurálása Azure Active Directory B2C |

## <a name="global-administrator"></a>Globális rendszergazda

Az ehhez a szerepkörhöz tartozó felhasználók hozzáférhetnek a Azure Active Directory összes felügyeleti funkciójához, valamint olyan szolgáltatásokhoz, amelyek olyan Azure Active Directory identitásokat használnak, mint például a Microsoft 365 Security Center, a Microsoft 365 megfelelőségi központ, az Exchange Online, a SharePoint Online és a Skype vállalati online verzió. A globális rendszergazdák továbbá az Azure-előfizetések és-felügyeleti csoportok kezeléséhez is [hozzáférhetnek](../../role-based-access-control/elevate-access-global-admin.md) . Ez lehetővé teszi, hogy a globális rendszergazdák teljes hozzáférést kapjanak az összes Azure-erőforráshoz a megfelelő Azure AD-bérlő használatával. Az Azure AD-szervezetre feliratkozik személy globális rendszergazda lesz. A vállalatnál több globális rendszergazda is lehet. A globális rendszergazdák alaphelyzetbe állíthatják bármely felhasználó és az összes többi rendszergazda jelszavát.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/Administrativeunit/allProperties/allTasks | Felügyeleti egységek létrehozása és kezelése (beleértve a tagokat) |
> | Microsoft. Directory/alkalmazások/allProperties/allTasks | Alkalmazások létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/alkalmazások/szinkronizálás/standard/olvasás | Az alkalmazásobjektum-objektumhoz társított kiépítési beállítások olvasása |
> | Microsoft. Directory/applicationTemplates/példány | Katalógusbeli alkalmazások példányainak megnyitása alkalmazás-sablonokból |
> | Microsoft. Directory/appRoleAssignments/allProperties/allTasks | AppRoleAssignments létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/auditLogs/allProperties/READ | A naplók összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/authorizationPolicy/allProperties/allTasks | Az engedélyezési házirendek összes aspektusának kezelése |
> | Microsoft. Directory/bitlockerKeys/kulcs/olvasás | BitLocker-metaadatok és-kulcs olvasása eszközökön |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése Microsoft Cloud App Security |
> | Microsoft. Directory/összekötők/létrehozás | Alkalmazásproxy-összekötők létrehozása |
> | Microsoft. Directory/összekötők/allProperties/olvasás | Az alkalmazásproxy-összekötők összes tulajdonságának olvasása |
> | Microsoft. Directory/connectorGroups/Create | Alkalmazásproxy-összekötő csoportok létrehozása |
> | Microsoft. Directory/connectorGroups/delete | Alkalmazásproxy-összekötő csoportok törlése |
> | Microsoft. Directory/connectorGroups/allProperties/READ | Az Application proxy Connector-csoportok összes tulajdonságának olvasása |
> | Microsoft. Directory/connectorGroups/allProperties/Update | Az Application proxy Connector-csoportok összes tulajdonságának frissítése |
> | Microsoft. Directory/Contacts/allProperties/allTasks | Névjegyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/szerződések/allProperties/allTasks | Partneri szerződések létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/eszközök/allProperties/allTasks | Eszközök létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/deviceManagementPolicies/standard/olvasás | Általános tulajdonságok olvasása az Eszközkezelő alkalmazás házirendjein |
> | Microsoft. Directory/deviceManagementPolicies/Basic/Update | Az alapszintű tulajdonságok frissítése az eszközkezelés alkalmazás-házirendjein |
> | Microsoft. Directory/deviceRegistrationPolicy/standard/olvasás | Szabványos tulajdonságok olvasása az eszköz regisztrációs házirendjeiben |
> | Microsoft. Directory/deviceRegistrationPolicy/Basic/Update | Az eszköz regisztrációs házirendjeinek alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/directoryRoles/allProperties/allTasks | Címtárbeli szerepkörök létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/directoryRoleTemplates/allProperties/allTasks | Azure AD-szerepkörök sablonjainak létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/tartományok/allProperties/allTasks | Tartományok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/entitlementManagement/allProperties/allTasks | Erőforrások létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure AD-jogosultságok kezelésében |
> | Microsoft. Directory/csoportok/allProperties/allTasks | Csoportok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/groupsAssignableToRoles/allProperties/Update | A isAssignableToRole tulajdonsággal rendelkező csoportok frissítése True értékre |
> | Microsoft. Directory/groupsAssignableToRoles/Create | IsAssignableToRole tulajdonsággal rendelkező csoportok létrehozása True értékre |
> | Microsoft. Directory/groupsAssignableToRoles/delete | A isAssignableToRole tulajdonsággal rendelkező csoportok törlése igaz értékre |
> | Microsoft. Directory/groupSettings/allProperties/allTasks | Csoportházirend-beállítások létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/groupSettingTemplates/allProperties/allTasks | Csoport-beállítási sablonok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/identityProtection/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése Azure AD Identity Protection |
> | Microsoft. Directory/loginOrganizationBranding/allProperties/allTasks | LoginTenantBranding létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/Oauth2permissiongrant objektumok/allProperties/allTasks | OAuth 2,0 engedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/szervezet/allProperties/allTasks | Szervezetek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/házirendek/allProperties/allTasks | Szabályzatok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/conditionalAccessPolicies/allProperties/allTasks | A feltételes hozzáférési szabályzatok összes tulajdonságának kezelése |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/READ | Privileged Identity Management összes erőforrásának olvasása |
> | Microsoft. Directory/provisioningLogs/allProperties/READ | A kiépítési naplók összes tulajdonságának olvasása |
> | Microsoft. Directory/roleAssignments/allProperties/allTasks | Szerepkör-hozzárendelések létrehozása és törlése, valamint az összes szerepkör-hozzárendelési tulajdonság olvasása és frissítése |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Szerepkör-definíciók létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/serviceAction/activateService | Elvégezhető a szolgáltatás "szolgáltatás aktiválása" művelete |
> | Microsoft. Directory/serviceAction/disableDirectoryFeature | Elvégezhető a "címtár-szolgáltatás letiltása" szolgáltatás művelete |
> | Microsoft. Directory/serviceAction/enableDirectoryFeature | Elvégezhető a "címtár-szolgáltatás engedélyezése" szolgáltatás művelete |
> | Microsoft. Directory/serviceAction/getAvailableExtentionProperties | Végrehajthatja a Getavailableextentionproperties szolgáltatás műveletét |
> | Microsoft. Directory/servicePrincipals/allProperties/allTasks | Egyszerű szolgáltatásnév létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Company-admin | Hozzájárulás engedélyezése bármely alkalmazáshoz bármely engedélyhez |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-all-Application-permissions | A szolgáltatás közvetlen hozzáférésének biztosítása a pricipal  |
> | Microsoft. Directory/servicePrincipals/szinkronizálás/standard/olvasás | Az egyszerű szolgáltatáshoz társított kiépítési beállítások olvasása |
> | Microsoft. Directory/signInReports/allProperties/READ | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/subscribedSkus/allProperties/allTasks | Előfizetések vásárlása és kezelése, előfizetések törlése |
> | Microsoft. Directory/felhasználók/allProperties/allTasks | Felhasználók létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/permissionGrantPolicies/Create | Engedély-engedélyezési házirendek létrehozása |
> | Microsoft. Directory/permissionGrantPolicies/delete | Engedély-engedélyezési szabályzatok törlése |
> | Microsoft. Directory/permissionGrantPolicies/standard/olvasás | Engedély-engedélyezési házirendek szabványos tulajdonságainak olvasása |
> | Microsoft. Directory/permissionGrantPolicies/Basic/Update | Engedély-engedélyezési szabályzatok alapszintű tulajdonságainak frissítése |
> | Microsoft. Azure. advancedThreatProtection/allEntities/allTasks | Az Azure komplex veszélyforrások elleni védelem összes aspektusának kezelése |
> | Microsoft. Azure. informationProtection/allEntities/allTasks | Azure Information Protection összes aspektusának kezelése |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Commerce. Billing/allEntities/allTasks | Az Office 365-számlázás összes aspektusának kezelése |
> | Microsoft. dynamics365/allEntities/allTasks | A Dynamics 365 összes aspektusának kezelése |
> | Microsoft. flow/allEntities/allTasks | A Microsoft Power automatizálás minden aspektusának kezelése |
> | Microsoft. Intune/allEntities/allTasks | Microsoft Intune összes aspektusának kezelése |
> | Microsoft. Office 365. complianceManager/allEntities/allTasks | Az Office 365 megfelelőség-kezelő összes aspektusának kezelése |
> | Microsoft. Office 365. desktopAnalytics/allEntities/allTasks | Az asztali elemzések összes aspektusának kezelése |
> | Microsoft. Office 365. Exchange/allEntities/allTasks | Az Exchange Online összes aspektusának kezelése |
> | Microsoft. Office 365. kulcstároló/allEntities/allTasks | Ügyfélszéf összes aspektusának kezelése |
> | Microsoft. Office 365. messageCenter/messages/READ | Üzenetek olvasása az üzenetsor-központban a Microsoft 365 felügyeleti központban, a biztonsági üzenetek kivételével |
> | Microsoft. Office 365. messageCenter/securityMessages/READ | Az üzenetközpont biztonsági üzeneteinek beolvasása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. protectionCenter/allEntities/allProperties/allTasks | Az Office 365 Protection Center összes aspektusának kezelése |
> | Microsoft. Office 365. Search/Content/Manage | Tartalom létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft Search szolgáltatásban |
> | Microsoft. Office 365. securityComplianceCenter/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a Microsoft 365 biztonsági és megfelelőségi központban |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. sharePoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a SharePointban |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | A Skype vállalati online verzió összes aspektusának kezelése |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/READ | Office 365-használati jelentések olvasása |
> | Microsoft. Office 365. userCommunication/allEntities/allTasks | Az új üzenetek láthatóságának olvasása és frissítése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |
> | Microsoft. powerApps/allEntities/allTasks | A Power apps összes aspektusának kezelése |
> | Microsoft. powerApps. powerBI/allEntities/allTasks | Power BI összes aspektusának kezelése |
> | Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/allTasks | A Microsoft Defender és a végpont összes aspektusának kezelése |

## <a name="global-reader"></a>Globális olvasó

Az ebben a szerepkörben lévő felhasználók beolvashatják a beállításokat és a felügyeleti információkat Microsoft 365 szolgáltatások között, de nem végezhetnek felügyeleti műveleteket. A globális olvasó a globális rendszergazda csak olvasási jogosultsággal rendelkező partnere. Globális olvasót rendelhet a globális rendszergazda helyett a tervezéshez, a naplózáshoz és a vizsgálatokhoz. A globális olvasót más korlátozott rendszergazdai szerepkörökkel, például az Exchange Administrator szolgáltatással kombinálva könnyebben dolgozhat a globális rendszergazdai szerepkör kiosztása nélkül. A globális olvasó együttműködik Microsoft 365 felügyeleti központtal, az Exchange felügyeleti központtal, a SharePoint felügyeleti központtal, a Teams felügyeleti központtal, a Security centerrel, a megfelelőségi központtal, az Azure Active Directory felügyeleti központtal

> [!NOTE]
> A globális olvasói szerepkörnek jelenleg néhány korlátozása van –
>
>- [OneDrive felügyeleti központ](https://admin.onedrive.com/) – a OneDrive felügyeleti központ nem támogatja a globális olvasó szerepkört
>- [M365 felügyeleti központ](https://admin.microsoft.com/Adminportal/Home#/homepage) – a globális olvasó nem tudja beolvasni az ügyfél kulcstároló-kérelmeit. A M365 felügyeleti központ bal oldali ablaktáblájában nem találja a **Customer kulcstároló-kérelmek** fület a **támogatás** területen.
>- [Office biztonsági & megfelelőségi központ](https://sip.protection.office.com/homepage) – a globális olvasó nem tudja olvasni az SCC-naplókat, a tartalom keresését vagy a biztonságos pontszám megtekintését.
>- [Teams felügyeleti központ](https://admin.teams.microsoft.com) – a globális olvasó nem tudja beolvasni a **csapatok életciklusát**, az **elemzési & jelentéseket**, az **IP Phone-eszközök kezelését** és az **alkalmazás-katalógust**
>- A [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) nem támogatja a globális olvasó szerepkört.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) – a globális olvasó csak a [központi jelentéskészítés](/azure/information-protection/reports-aip) esetén támogatott, és ha az Azure ad-szervezet nem az [egyesített címkézési platformon](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)van.
>
> Ezek a funkciók jelenleg fejlesztés alatt állnak.
>

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/alkalmazások/applicationProxy/olvasás | Az összes alkalmazásproxy tulajdonságainak olvasása |
> | Microsoft. Directory/alkalmazások/szinkronizálás/standard/olvasás | Az alkalmazásobjektum-objektumhoz társított kiépítési beállítások olvasása |
> | Microsoft. Directory/auditLogs/allProperties/READ | A naplók összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/bitlockerKeys/kulcs/olvasás | BitLocker-metaadatok és-kulcs olvasása eszközökön |
> | Microsoft. Directory/összekötők/allProperties/olvasás | Az alkalmazásproxy-összekötők összes tulajdonságának olvasása |
> | Microsoft. Directory/connectorGroups/allProperties/READ | Az Application proxy Connector-csoportok összes tulajdonságának olvasása |
> | Microsoft. Directory/entitlementManagement/allProperties/READ | Az Azure AD-jogosultságok kezelésének összes tulajdonságának olvasása |
> | Microsoft. Directory/deviceManagementPolicies/standard/olvasás | Általános tulajdonságok olvasása az Eszközkezelő alkalmazás házirendjein |
> | Microsoft. Directory/deviceRegistrationPolicy/standard/olvasás | Szabványos tulajdonságok olvasása az eszköz regisztrációs házirendjeiben |
> | Microsoft. Directory/csoportok/hiddenMembers/olvasás | Csoport rejtett tagjainak olvasása |
> | Microsoft. Directory/házirendek/standard/olvasás | A szabályzatok alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/házirendek/tulajdonosok/olvasás | Szabályzatok tulajdonosának olvasása |
> | Microsoft. Directory/házirendek/policyAppliedTo/olvasás | A policies. policyAppliedTo tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/standard/olvasás | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/tulajdonosok/olvasás | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/READ | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/provisioningLogs/allProperties/READ | A kiépítési naplók összes tulajdonságának olvasása |
> | Microsoft. Directory/servicePrincipals/hitelesítés/olvasás | Az egyszerű szolgáltatások hitelesítési tulajdonságainak olvasása |
> | Microsoft. Directory/servicePrincipals/szinkronizálás/standard/olvasás | Az egyszerű szolgáltatáshoz társított kiépítési beállítások olvasása |
> | Microsoft. Directory/signInReports/allProperties/READ | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/felhasználók/strongAuthentication/olvasás | A felhasználók erős hitelesítési tulajdonságának beolvasása |
> | Microsoft. Commerce. számlázás/allEntities/olvasás | Az Office 365-számlázás összes erőforrásának olvasása |
> | Microsoft. Office 365. Exchange/allEntities/READ | Az Exchange Online összes erőforrásának olvasása |
> | Microsoft. Office 365. messageCenter/messages/READ | Üzenetek olvasása az üzenetsor-központban a Microsoft 365 felügyeleti központban, a biztonsági üzenetek kivételével |
> | Microsoft. Office 365. messageCenter/securityMessages/READ | Az üzenetközpont biztonsági üzeneteinek beolvasása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. Network/Performance/allProperties/READ | Az összes hálózati teljesítmény tulajdonságainak olvasása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. protectionCenter/allEntities/allProperties/READ | Az Office 365 Protection Center összes aspektusának olvasása |
> | Microsoft. Office 365. securityComplianceCenter/allEntities/READ | Általános tulajdonságok olvasása Microsoft 365 biztonsági és megfelelőségi központban |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/READ | Office 365-használati jelentések olvasása |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="groups-administrator"></a>Csoportok rendszergazdája

A szerepkör felhasználói létrehozhatnak és kezelhetnek csoportokat és azok beállításait, például az elnevezési és lejárati házirendeket. Fontos tisztában lenni azzal, hogy egy felhasználó ehhez a szerepkörhöz rendelése lehetővé teszi a szervezet összes csoportjának kezelését a különböző számítási feladatokban, például a Teams, a SharePoint és az Outlook Yammer. Emellett a felhasználók a különböző csoportok beállításait is kezelhetik különböző felügyeleti portálokon, például a Microsoft felügyeleti központban, a Azure Portalon, valamint a munkaterhelések, például a csapatok és a SharePoint-felügyeleti központok között.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/csoportok/assignLicense | Licencek kiosztása csoportok számára csoport alapú licenceléshez |
> | Microsoft. Directory/csoportok/létrehozás | Csoportok létrehozása a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok/törlés | Csoportok törlése, a szerepkörhöz hozzárendelhető csoport kizárásával |
> | Microsoft. Directory/csoportok/hiddenMembers/olvasás | Csoport rejtett tagjainak olvasása |
> | Microsoft. Directory/csoportok/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása csoport alapú licenceléshez |
> | Microsoft. Directory/csoportok/visszaállítás | Törölt csoportok visszaállítása |
> | Microsoft. Directory/csoportok/alapszintű/frissítés | A csoportok alapszintű tulajdonságainak frissítése, a szerepkörhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/besorolás/frissítés | A csoportok besorolási tulajdonságának frissítése, a szerepkörhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/dynamicMembershipRule/Update | A dinamikus tagsági szabályok frissítése a szerepkörökhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/groupType/Update | Egy csoport groupType tulajdonságának frissítése |
> | Microsoft. Directory/csoportok/tagok/frissítés | Csoportok tagjainak frissítése, a szerepkörhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/onPremWriteBack/Update | Azure AD-csoportok frissítése a helyi környezetbe való visszaíráshoz |
> | Microsoft. Directory/csoportok/tulajdonosok/frissítés | Csoportok tulajdonosainak frissítése, a szerepkörhöz hozzárendelhető csoportok kizárása |
> | Microsoft. Directory/csoportok/beállítások/frissítés | Csoportok beállításainak frissítése |
> | Microsoft. Directory/csoportok/láthatóság/frissítés | A csoportok láthatósági tulajdonságának frissítése |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-all-Application-permissions | A szolgáltatás közvetlen hozzáférésének biztosítása a pricipal  |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="guest-inviter"></a>Vendég meghívója

Az ebben a szerepkörben lévő felhasználók kezelhetik Azure Active Directory B2B vendég felhasználói meghívókat, amikor a **tagok meghívhatják** a felhasználói beállítást a nem értékre. További információ a B2B-együttműködésről az [Azure ad B2B együttműködésről](../external-identities/what-is-b2b.md). Nem tartalmaz más engedélyeket.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/felhasználók/inviteGuest | Vendégfelhasználók meghívása |
> | Microsoft. Directory/felhasználók/standard/olvasás | Alapszintű tulajdonságok olvasása a felhasználóknál |
> | Microsoft. Directory/felhasználók/appRoleAssignments/olvasás | Alkalmazások szerepkör-hozzárendeléseinek beolvasása |
> | Microsoft. Directory/felhasználók/directReports/olvasás | A közvetlen jelentések beolvasása a felhasználók számára |
> | Microsoft. Directory/felhasználók/kezelő/olvasás | Felhasználók beolvasása |
> | Microsoft. Directory/felhasználók/memberOf/olvasás | A felhasználók csoport tagságának beolvasása |
> | Microsoft. Directory/felhasználók/Oauth2permissiongrant objektumok/olvasás | Delegált engedélyek beolvasása a felhasználók számára |
> | Microsoft. Directory/felhasználók/ownedDevices/olvasás | A felhasználók tulajdonában lévő eszközök beolvasása |
> | Microsoft. Directory/felhasználók/ownedObjects/olvasás | A felhasználók tulajdonában lévő objektumok beolvasása |
> | Microsoft. Directory/felhasználók/registeredDevices/olvasás | Felhasználók regisztrált eszközeinek olvasása |

## <a name="helpdesk-administrator"></a>Segélyszolgálat rendszergazdája

Az ezzel a szerepkörrel rendelkező felhasználók módosíthatják a jelszavakat, érvénytelenítik a frissítési jogkivonatokat, kezelhetik a szolgáltatási kérelmeket, és figyelik a szolgáltatás állapotát A frissítési jogkivonat érvénytelenítése kényszeríti a felhasználót, hogy jelentkezzen be újra. Azt határozza meg, hogy egy segélyszolgálat rendszergazdája visszaállíthatja-e a felhasználó jelszavát, és a frissítési tokenek érvénytelenítése a felhasználó által hozzárendelt szerepkörtől függ. Az ügyfélszolgálati rendszergazda által a frissítési tokenek jelszavának alaphelyzetbe állításához használt szerepkörök listáját lásd: [jelszó-visszaállítási engedélyek](#password-reset-permissions).

> [!IMPORTANT]
> Az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják azokat a személyeket, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz, illetve a Azure Active Directoryon belül és kívül is kritikus konfigurációhoz. A felhasználó jelszavának módosítása azt jelentheti, hogy a felhasználó identitását és engedélyeit feltételezi. Például:
>
>- Az alkalmazás regisztrálása és a vállalati alkalmazások tulajdonosai, akik kezelhetik a saját alkalmazások hitelesítő adatait. Ezek az alkalmazások jogosultsági szintű engedélyekkel rendelkezhetnek az Azure AD-ben, és máshol nem biztosítanak ügyfélszolgálati rendszergazdának. Ezen az elérési úton egy ügyfélszolgálati rendszergazda képes lehet feltételezni az alkalmazás tulajdonosának identitását, majd az alkalmazás hitelesítő adatainak frissítésével tovább feltételezni egy emelt szintű alkalmazás identitását.
>- Azure-előfizetések tulajdonosai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz vagy az Azure-beli kritikus konfigurációhoz.
>- Biztonsági csoport és Microsoft 365 csoport tulajdonosai, akik kezelhetik a csoporttagság kezelését. Ezek a csoportok hozzáférést biztosíthatnak a bizalmas vagy magánjellegű információkhoz, illetve a kritikus konfigurációhoz az Azure AD-ben és máshol.
>- Az Azure AD-n kívül más szolgáltatásokban, például az Exchange Online-ban, az Office biztonsági és megfelelőségi központban és a humán erőforrás-rendszerekben található rendszergazdák.
>- Nem rendszergazdák, például vezetők, jogi tanácsadás és emberi erőforrások alkalmazottai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz.

A rendszergazdai engedélyek delegálása a felhasználók alkészletei között és a házirendek a felhasználók egy részhalmazára való alkalmazása a [felügyeleti egységekkel](administrative-units.md)lehetséges.

Ezt a szerepkört korábban "password Administrator" néven nevezték a [Azure Portal](https://portal.azure.com/). A "segélyszolgálat rendszergazdája" neve az Azure AD-ben már megegyezik a nevével az Azure AD PowerShellben és a Microsoft Graph API-ban.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/bitlockerKeys/kulcs/olvasás | BitLocker-metaadatok és-kulcs olvasása eszközökön |
> | Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | A felhasználói frissítési tokenek érvénytelenítésével kényszerítheti ki a kijelentkezést |
> | Microsoft. Directory/felhasználók/jelszó/frissítés | Az összes felhasználó jelszavának alaphelyzetbe állítása |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="hybrid-identity-administrator"></a>Hibrid identitás-rendszergazda

Az ebben a szerepkörben lévő felhasználók létrehozhatják, kezelhetik és telepíthetik az AD-ből az Azure AD-be történő üzembe helyezési konfigurációt a felhőalapú kiépítés, valamint az összevonási beállítások kezelése révén. A felhasználók ezen szerepkör használatával is elhárítják és megfigyelheti a naplókat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/alkalmazások/létrehozás | Összes típusú alkalmazás létrehozása |
> | Microsoft. Directory/alkalmazások/törlés | Összes típusú alkalmazás törlése |
> | Microsoft. Directory/alkalmazások/appRoles/Update | A appRoles tulajdonság frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/célközönség/frissítés | Az alkalmazások célközönség tulajdonságának frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítés/frissítés | Hitelesítés frissítése minden típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/alapszintű/frissítés | Alkalmazások alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés | Alkalmazás hitelesítő adatainak frissítése |
> | Microsoft. Directory/alkalmazások/tulajdonosok/frissítés | Alkalmazások tulajdonosainak frissítése |
> | Microsoft. Directory/alkalmazások/engedélyek/frissítés | A kitett engedélyek és a szükséges engedélyek frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/házirendek/frissítés | Alkalmazások szabályzatának frissítése |
> | Microsoft. Directory/alkalmazások/szinkronizálás/standard/olvasás | Az alkalmazásobjektum-objektumhoz társított kiépítési beállítások olvasása |
> | Microsoft. Directory/applicationTemplates/példány | Katalógusbeli alkalmazások példányainak megnyitása alkalmazás-sablonokból |
> | Microsoft. Directory/auditLogs/allProperties/READ | A naplók összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/cloudProvisioning/allProperties/allTasks | Az Azure AD Cloud kiépítési szolgáltatás összes tulajdonságának olvasása és konfigurálása. |
> | Microsoft. Directory/tartományok/allProperties/olvasás | A tartományok összes tulajdonságának olvasása |
> | Microsoft. Directory/tartományok/összevonás/frissítés | Tartományok összevonási tulajdonságainak frissítése |
> | Microsoft. Directory/szervezet/rSync/Update | A szervezeti címtár-szinkronizálási tulajdonság frissítése |
> | Microsoft. Directory/provisioningLogs/allProperties/READ | A kiépítési naplók összes tulajdonságának olvasása |
> | Microsoft. Directory/servicePrincipals/Create | Szolgáltatásnevek létrehozása |
> | Microsoft. Directory/servicePrincipals/delete | Egyszerű szolgáltatások törlése |
> | Microsoft. Directory/servicePrincipals/letiltás | Egyszerű szolgáltatások letiltása |
> | Microsoft. Directory/servicePrincipals/Enable | Szolgáltatásnevek engedélyezése |
> | Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage | Alkalmazás-kiépítési titkok és hitelesítő adatok kezelése |
> | Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Alkalmazás-kiépítési szinkronizálás feladatok elindítása, újraindítása és szüneteltetése |
> | Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | Alkalmazás-kiépítési szinkronizálás-feladatok és-séma létrehozása és kezelése |
> | Microsoft. Directory/servicePrincipals/hallgatóság/frissítés | A célközönség tulajdonságainak frissítése az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/hitelesítés/frissítés | Az egyszerű szolgáltatások hitelesítési tulajdonságainak frissítése |
> | Microsoft. Directory/servicePrincipals/Basic/Update | Egyszerű tulajdonságok frissítése az egyszerű szolgáltatásokban |
> | Microsoft. Directory/servicePrincipals/hitelesítő adatok/frissítés | Egyszerű szolgáltatásnév hitelesítő adatainak frissítése |
> | Microsoft. Directory/servicePrincipals/owners/Update | Egyszerű szolgáltatásnév tulajdonosainak frissítése |
> | Microsoft. Directory/servicePrincipals/engedélyek/frissítés | Egyszerű szolgáltatásnév engedélyeinek frissítése |
> | Microsoft. Directory/servicePrincipals/házirendek/frissítés | Az egyszerű szolgáltatások szabályzatának frissítése |
> | Microsoft. Directory/servicePrincipals/tag/frissítés | Az egyszerű szolgáltatásnév címkézési tulajdonságának frissítése |
> | Microsoft. Directory/servicePrincipals/szinkronizálás/standard/olvasás | Az egyszerű szolgáltatáshoz társított kiépítési beállítások olvasása |
> | Microsoft. Directory/signInReports/allProperties/READ | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. messageCenter/messages/READ | Üzenetek olvasása az üzenetsor-központban a Microsoft 365 felügyeleti központban, a biztonsági üzenetek kivételével |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="insights-administrator"></a>Az Áttekintés rendszergazdája

Az ebben a szerepkörben lévő felhasználók a M365-betekintő [alkalmazásban](https://go.microsoft.com/fwlink/?linkid=2129521)hozzáférhetnek a felügyeleti funkciók teljes készletéhez. Ez a szerepkör képes a címtáradatok olvasására, a szolgáltatás állapotának figyelésére, a fájlok támogatására szolgáló jegyek megtekintésére és a rendszergazdai beállítások beszerzésére.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. bepillantások/allEntities/allTasks | Az észlelt alkalmazás összes aspektusának kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="insights-business-leader"></a>Üzleti vezető

Az ebben a szerepkörben lévő felhasználók az M365-betekintő [alkalmazással](https://go.microsoft.com/fwlink/?linkid=2129521)hozzáférhetnek az irányítópultokhoz és az adatellenőrzésekhez. Ebbe beletartozik az összes irányítópulthoz való teljes hozzáférés, valamint az elemzések és az adatfeltárási funkciók. Az ebben a szerepkörben lévő felhasználók nem férhetnek hozzá a termék konfigurációs beállításaihoz, amely az információ-felügyeleti szerepkör felelőssége.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. bepillantások/jelentések/olvasás | Jelentések és irányítópult megtekintése az adatáttekintési alkalmazásban |
> | Microsoft. bepillantások/programok/frissítés | Programok üzembe helyezése és kezelése az adatáttekintési alkalmazásban |

## <a name="intune-administrator"></a>Intune-rendszergazda

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek Microsoft Intune online-ban, ha a szolgáltatás jelen van. Emellett ez a szerepkör lehetővé teszi a felhasználók és eszközök felügyeletét a házirendek társítása, valamint a csoportok létrehozása és kezelése céljából. További információ a [szerepköralapú adminisztrációs vezérlő (RBAC) és a Microsoft Intune](/intune/role-based-access-control).

Ez a szerepkör az összes biztonsági csoportot képes létrehozni és kezelni. Az Intune-rendszergazda azonban nem rendelkezik rendszergazdai jogosultságokkal az Office-csoportokon keresztül. Ez azt jelenti, hogy a rendszergazda nem tudja frissíteni a szervezet összes Office-csoportjának tulajdonosait vagy tagságait. Azonban felügyelheti az általa létrehozott Office-csoportot, amely a végfelhasználói jogosultságok részeként jön létre. Ezért az Ön által létrehozott összes Office-csoportot (nem biztonsági csoportot) a 250-es kvóta alapján kell figyelembe venni.

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "Intune szolgáltatás-rendszergazdaként" van azonosítva. A [Azure Portal](https://portal.azure.com)"Intune-rendszergazda".

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/bitlockerKeys/kulcs/olvasás | BitLocker-metaadatok és-kulcs olvasása eszközökön |
> | Microsoft. Directory/névjegyek/létrehozás | Névjegyek létrehozása |
> | Microsoft. Directory/Contacts/delete | Névjegyek törlése |
> | Microsoft. Directory/Contacts/Basic/Update | A névjegyek alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/eszközök/létrehozás | Eszközök létrehozása (regisztrálás az Azure AD-ben) |
> | Microsoft. Directory/eszközök/törlés | Eszközök törlése az Azure AD-ből |
> | Microsoft. Directory/eszközök/letiltás | Eszközök letiltása az Azure AD-ben |
> | Microsoft. Directory/eszközök/engedélyezés | Eszközök engedélyezése az Azure AD-ben |
> | Microsoft. Directory/eszközök/alapszintű/frissítés | Az eszközök alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/eszközök/extensionAttributes/Update | A Devices. extensionAttributes tulajdonság összes értékének frissítése |
> | Microsoft. Directory/eszközök/registeredOwners/Update | Eszközök regisztrált tulajdonosainak frissítése |
> | Microsoft. Directory/eszközök/registeredUsers/Update | Eszközök regisztrált felhasználóinak frissítése |
> | Microsoft. Directory/deviceManagementPolicies/standard/olvasás | Általános tulajdonságok olvasása az Eszközkezelő alkalmazás házirendjein |
> | Microsoft. Directory/deviceRegistrationPolicy/standard/olvasás | Szabványos tulajdonságok olvasása az eszköz regisztrációs házirendjeiben |
> | Microsoft. Directory/csoportok/hiddenMembers/olvasás | Csoport rejtett tagjainak olvasása |
> | Microsoft. Directory/csoportok. biztonság/létrehozás | Biztonsági csoportok létrehozása a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok. biztonság/törlés | Biztonsági csoportok törlése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/groups. Security/Basic/Update | A biztonsági csoportok alapszintű tulajdonságainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok. biztonság/besorolás/frissítés | A biztonsági csoportok Update besorolási tulajdonsága a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/groups. Security/dynamicMembershipRule/Update | A biztonsági csoportok dynamicMembershipRule tulajdonságának frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/groups. Security/groupType/Update | A biztonsági csoportok csoport típusa tulajdonságának frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok. biztonság/tagok/frissítés | Biztonsági csoportok tagjainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok. biztonság/tulajdonosok/frissítés | Biztonsági csoportok tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok. biztonság/láthatóság/frissítés | A biztonsági csoportok láthatósági tulajdonságának frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/felhasználók/alapszintű/frissítés | A felhasználók alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/felhasználók/kezelő/frissítés | Frissítési kezelő felhasználók számára |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Intune/allEntities/allTasks | Microsoft Intune összes aspektusának kezelése |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="kaizala-administrator"></a>Kaizala-rendszergazda

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a beállítások Microsoft Kaizala való kezeléséhez, ha a szolgáltatás megtalálható, valamint a támogatási jegyek felügyeletének és a szolgáltatás állapotának figyelésének lehetősége. Emellett a felhasználó hozzáférhet a bevezetéshez kapcsolódó jelentésekhez & a Kaizala használatát a Kaizala műveletekkel létrehozott szervezeti tagok és üzleti jelentések segítségével.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="license-administrator"></a>Licenc rendszergazdája

Az ebben a szerepkörben lévő felhasználók hozzáadhatják, eltávolíthatják és frissíthetik a licenc-hozzárendeléseket a felhasználók, csoportok (csoporton alapuló licencelés) használatával, és kezelhetik a felhasználók használati helyét. A szerepkör nem biztosít előfizetéseket, nem hozhat létre és kezelhet csoportokat, illetve nem hozhat létre vagy kezelhet felhasználókat a használat helyén kívül. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/csoportok/assignLicense | Licencek kiosztása csoportok számára csoport alapú licenceléshez |
> | Microsoft. Directory/csoportok/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása csoport alapú licenceléshez |
> | Microsoft. Directory/felhasználók/assignLicense | Felhasználói licencek kezelése |
> | Microsoft. Directory/felhasználók/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása a felhasználók számára |
> | Microsoft. Directory/felhasználók/usageLocation/Update | A felhasználók használati helyének frissítése |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="message-center-privacy-reader"></a>A Message Center adatvédelmi olvasója

Az ebben a szerepkörben lévő felhasználók az üzenetközpont összes értesítését nyomon tudják követni, beleértve az adatvédelmi üzeneteket is. Az üzenetközpont adatvédelmi olvasói e-mail-értesítéseket kapnak, beleértve az adatvédelemmel kapcsolatos értesítőket is, és lefizethetnek az üzenetközpont beállításaival. Az adatvédelmi üzeneteket csak a globális rendszergazda és az üzenetközpont adatvédelmi olvasója tudja olvasni. Emellett ez a szerepkör a csoportok, tartományok és előfizetések megtekintésére is lehetőséget tartalmaz. Ez a szerepkör nem jogosult a szolgáltatási kérelmek megtekintésére, létrehozására és kezelésére.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Office 365. messageCenter/messages/READ | Üzenetek olvasása az üzenetsor-központban a Microsoft 365 felügyeleti központban, a biztonsági üzenetek kivételével |
> | Microsoft. Office 365. messageCenter/securityMessages/READ | Az üzenetközpont biztonsági üzeneteinek beolvasása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="message-center-reader"></a>Üzenetközpont-olvasó

Az ebben a szerepkörben lévő felhasználók megtekinthetik az üzenetek és a tanácsadói állapot frissítéseit az [üzenetközpont](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) számára olyan konfigurált szolgáltatásokban, mint például az Exchange, az Intune és a Microsoft teams. Az üzenetközpont-olvasók hetente küldenek e-mail-kivonatokat a hozzászólások, a frissítések, és megoszthatják az üzenetközpont-bejegyzéseket Microsoft 365ban. Az Azure AD-ben az ehhez a szerepkörhöz hozzárendelt felhasználók csak olvasási hozzáféréssel rendelkeznek az Azure AD-szolgáltatásokhoz, például a felhasználókhoz és a csoportokhoz. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Office 365. messageCenter/messages/READ | Üzenetek olvasása az üzenetsor-központban a Microsoft 365 felügyeleti központban, a biztonsági üzenetek kivételével |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="modern-commerce-user"></a>Modern kereskedelmi felhasználó

Ne használja. Ezt a szerepkört a rendszer automatikusan a kereskedelemből rendeli hozzá, és semmilyen más használatra nem javasolt vagy nem támogatott. A részleteket alább találja.

A modern kereskedelmi felhasználói szerepkör lehetővé teszi, hogy bizonyos felhasználók hozzáférhessenek Microsoft 365 felügyeleti központhoz, és megtekintsék a bal oldali navigációs bejegyzéseket a **Kezdőlap**, a **számlázás** és a **támogatás** számára. Az ezeken a területeken elérhető tartalmakat a felhasználók számára a saját maguk vagy a szervezete számára vásárolt termékek kezeléséhez rendelt [kereskedelmi szerepkörök](../../cost-management-billing/manage/understand-mca-roles.md) szabályozzák. Ilyen lehet például a számlák kifizetése, illetve a számlázási fiókokhoz és a számlázási profilokhoz való hozzáférés.

A modern kereskedelmi felhasználói szerepkörrel rendelkező felhasználók általában rendszergazdai jogosultságokkal rendelkeznek más Microsoft vásárlási rendszerekben, de nem rendelkeznek globális rendszergazdai vagy számlázási rendszergazdai szerepkörökkel a felügyeleti központ eléréséhez.

**Mikor van hozzárendelve a modern kereskedelmi felhasználói szerepkör?**

* **Önkiszolgáló vásárlás a Microsoft 365 felügyeleti központban** – az önkiszolgáló vásárlás lehetővé teszi a felhasználóknak az új termékek kipróbálását a saját maguk általi megvásárlásával vagy regisztrálásával. Ezeket a termékeket a felügyeleti központban kezelheti. Azok a felhasználók, akik önkiszolgáló vásárlást végeznek, egy szerepkört kapnak a kereskedelmi rendszeren, és a modern kereskedelmi felhasználói szerepkört, hogy a vásárlásokat a felügyeleti központban tudják kezelni. A rendszergazdák letilthatják az önkiszolgáló vásárlásokat (Power BI, energiagazdálkodási alkalmazások, automatizálás) a [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell)használatával. További információért olvassa el az [önkiszolgáló vásárlással kapcsolatos gyakori kérdéseket](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).
* **Vásárlások a Microsoft kereskedelmi piactérről** – az önkiszolgáló vásárláshoz hasonlóan, amikor egy felhasználó terméket vagy szolgáltatást vásárol Microsoft AppSource vagy Azure piactéren, a modern kereskedelmi felhasználói szerepkör hozzá lesz rendelve, ha nem rendelkezik globális rendszergazdai vagy számlázási rendszergazdai szerepkörrel. Bizonyos esetekben előfordulhat, hogy a felhasználók nem tudják a vásárlásokat letiltani. További információ: [Microsoft kereskedelmi piactér](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase).
* A Microsoft **javaslatai** – a javaslat a Microsoft hivatalos ajánlata a Microsoft termékeinek és szolgáltatásainak megvásárlására. Ha a javaslatot elfogadó személy nem rendelkezik globális rendszergazdai vagy számlázási rendszergazdai szerepkörrel az Azure AD-ben, akkor a javaslatot és a modern kereskedelmi felhasználói szerepkört a felügyeleti központ eléréséhez is hozzá kell rendelni. Amikor hozzáférnek a felügyeleti központhoz, csak azok a funkciók használhatók, amelyeket a kereskedelmi jellemző szerepköre is jogosult.
* **Commerce-specifikus szerepkörök** – egyes felhasználók kereskedelmi-specifikus szerepköröket kapnak. Ha a felhasználó nem globális vagy számlázási rendszergazda, akkor a modern kereskedelmi felhasználói szerepkört kapják meg, hogy hozzáférhessenek a felügyeleti központhoz.

Ha a modern kereskedelmi felhasználói szerepkör nincs hozzárendelve egy felhasználóhoz, akkor elveszti a hozzáférést Microsoft 365 felügyeleti központhoz. Ha bármilyen terméket kezelnek, akár saját maguk, akár a szervezete számára, nem lesznek képesek a felügyeletre. Ez magában foglalhatja a licencek hozzárendelését, a fizetési módok módosítását, a számlák kifizetését, valamint az előfizetések kezeléséhez szükséges egyéb feladatokat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Commerce. számlázás/partnerek/olvasás | Microsoft 365 számlázás partner tulajdonságának olvasása |
> | Microsoft. Commerce. volumeLicenseServiceCenter/allEntities/allTasks | A mennyiségi licencelési szolgáltatási központ összes aspektusának kezelése |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/alap/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="network-administrator"></a>Hálózati rendszergazda

Az ebben a szerepkörben lévő felhasználók áttekinthetik a Microsoft által a saját felhasználói helyükről érkező hálózati telemetria alapuló hálózati peremhálózati javaslatokat. A Microsoft 365 hálózati teljesítménye a nagyvállalati ügyfél hálózati peremhálózati architektúráján alapul, amely általában a felhasználói helytől függ. Ez a szerepkör lehetővé teszi a felderített felhasználói helyszínek szerkesztését és a hálózati paraméterek konfigurálását az adott helyszíneken a továbbfejlesztett telemetria-mérések és kialakítási javaslatok megkönnyítése érdekében

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Office 365. Network/Locations/allProperties/allTasks | A hálózati telephelyek összes aspektusának kezelése |
> | Microsoft. Office 365. Network/Performance/allProperties/READ | Az összes hálózati teljesítmény tulajdonságainak olvasása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="office-apps-administrator"></a>Office-alkalmazások rendszergazdája

Az ebben a szerepkörben található felhasználók kezelhetik Microsoft 365 alkalmazások Felhőbeli beállításait. Ez magában foglalja a felhőalapú házirendek kezelését, az önkiszolgáló Letöltés felügyeletét, valamint az Office-alkalmazások kapcsolódó jelentésének megtekintését. Ez a szerepkör emellett lehetőséget biztosít a támogatási jegyek kezelésére és a szolgáltatás állapotának figyelésére a fő felügyeleti központban. Az ehhez a szerepkörhöz hozzárendelt felhasználók az Office-alkalmazások új szolgáltatásainak kommunikációját is kezelhetik.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. messageCenter/messages/READ | Üzenetek olvasása az üzenetsor-központban a Microsoft 365 felügyeleti központban, a biztonsági üzenetek kivételével |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. userCommunication/allEntities/allTasks | Az új üzenetek láthatóságának olvasása és frissítése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="partner-tier1-support"></a>Partneri Tier1-támogatás

Ne használja. Ez a szerepkör elavult, és a jövőben el lesz távolítva az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partner általi használatra készült, és nem általános használatra készült.

> [!IMPORTANT]
> Ez a szerepkör alaphelyzetbe állíthatja a jelszavakat, és érvénytelenítheti a frissítési jogkivonatokat csak a nem rendszergazdák számára. Ez a szerepkör nem használható, mert elavult, és a továbbiakban nem lesz visszaadva az API-ban.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/alkalmazások/appRoles/Update | A appRoles tulajdonság frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/célközönség/frissítés | Az alkalmazások célközönség tulajdonságának frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítés/frissítés | Hitelesítés frissítése minden típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/alapszintű/frissítés | Alkalmazások alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés | Alkalmazás hitelesítő adatainak frissítése |
> | Microsoft. Directory/alkalmazások/tulajdonosok/frissítés | Alkalmazások tulajdonosainak frissítése |
> | Microsoft. Directory/alkalmazások/engedélyek/frissítés | A kitett engedélyek és a szükséges engedélyek frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/házirendek/frissítés | Alkalmazások szabályzatának frissítése |
> | Microsoft. Directory/névjegyek/létrehozás | Névjegyek létrehozása |
> | Microsoft. Directory/Contacts/delete | Névjegyek törlése |
> | Microsoft. Directory/Contacts/Basic/Update | A névjegyek alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/csoportok/létrehozás | Csoportok létrehozása a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok/törlés | Csoportok törlése, a szerepkörhöz hozzárendelhető csoport kizárásával |
> | Microsoft. Directory/csoportok/visszaállítás | Törölt csoportok visszaállítása |
> | Microsoft. Directory/csoportok/tagok/frissítés | Csoportok tagjainak frissítése, a szerepkörhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/tulajdonosok/frissítés | Csoportok tulajdonosainak frissítése, a szerepkörhöz hozzárendelhető csoportok kizárása |
> | Microsoft. Directory/Oauth2permissiongrant objektumok/allProperties/allTasks | OAuth 2,0 engedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Egyszerű szolgáltatás szerepkör-hozzárendeléseinek frissítése |
> | Microsoft. Directory/felhasználók/assignLicense | Felhasználói licencek kezelése |
> | Microsoft. Directory/felhasználók/létrehozás | Felhasználók hozzáadása |
> | Microsoft. Directory/felhasználók/törlés | Felhasználók törlése |
> | Microsoft. Directory/felhasználók/letiltás | Felhasználók letiltása |
> | Microsoft. Directory/felhasználók/engedélyezés | Felhasználók engedélyezése |
> | Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | A felhasználói frissítési tokenek érvénytelenítésével kényszerítheti ki a kijelentkezést |
> | Microsoft. Directory/felhasználók/visszaállítás | Törölt felhasználók visszaállítása |
> | Microsoft. Directory/felhasználók/alapszintű/frissítés | A felhasználók alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/felhasználók/kezelő/frissítés | Frissítési kezelő felhasználók számára |
> | Microsoft. Directory/felhasználók/jelszó/frissítés | Az összes felhasználó jelszavának alaphelyzetbe állítása |
> | Microsoft. Directory/felhasználók/userPrincipalName/Update | Felhasználó egyszerű nevének frissítése |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="partner-tier2-support"></a>Partneri szint-támogatás

Ne használja. Ez a szerepkör elavult, és a jövőben el lesz távolítva az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partner általi használatra készült, és nem általános használatra készült.

> [!IMPORTANT]
> Ez a szerepkör alaphelyzetbe állíthatja a jelszavakat, és érvényteleníti az összes nem rendszergazdák és rendszergazdák számára a frissítési jogkivonatokat (beleértve a globális rendszergazdákat is). Ez a szerepkör nem használható, mert elavult, és a továbbiakban nem lesz visszaadva az API-ban.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/alkalmazások/appRoles/Update | A appRoles tulajdonság frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/célközönség/frissítés | Az alkalmazások célközönség tulajdonságának frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítés/frissítés | Hitelesítés frissítése minden típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/alapszintű/frissítés | Alkalmazások alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés | Alkalmazás hitelesítő adatainak frissítése |
> | Microsoft. Directory/alkalmazások/tulajdonosok/frissítés | Alkalmazások tulajdonosainak frissítése |
> | Microsoft. Directory/alkalmazások/engedélyek/frissítés | A kitett engedélyek és a szükséges engedélyek frissítése az összes típusú alkalmazáshoz |
> | Microsoft. Directory/alkalmazások/házirendek/frissítés | Alkalmazások szabályzatának frissítése |
> | Microsoft. Directory/névjegyek/létrehozás | Névjegyek létrehozása |
> | Microsoft. Directory/Contacts/delete | Névjegyek törlése |
> | Microsoft. Directory/Contacts/Basic/Update | A névjegyek alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/tartományok/alap/allTasks | Tartományok létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése |
> | Microsoft. Directory/csoportok/létrehozás | Csoportok létrehozása a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok/törlés | Csoportok törlése, a szerepkörhöz hozzárendelhető csoport kizárásával |
> | Microsoft. Directory/csoportok/visszaállítás | Törölt csoportok visszaállítása |
> | Microsoft. Directory/csoportok/tagok/frissítés | Csoportok tagjainak frissítése, a szerepkörhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/tulajdonosok/frissítés | Csoportok tulajdonosainak frissítése, a szerepkörhöz hozzárendelhető csoportok kizárása |
> | Microsoft. Directory/Oauth2permissiongrant objektumok/allProperties/allTasks | OAuth 2,0 engedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/szervezet/alapszintű/frissítés | Alapszintű tulajdonságok frissítése a szervezeten belül |
> | Microsoft. Directory/roleAssignments/allProperties/allTasks | Szerepkör-hozzárendelések létrehozása és törlése, valamint az összes szerepkör-hozzárendelési tulajdonság olvasása és frissítése |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Szerepkör-definíciók létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Egyszerű szolgáltatás szerepkör-hozzárendeléseinek frissítése |
> | Microsoft. Directory/subscribedSkus/standard/olvasás | Előfizetések alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/felhasználók/assignLicense | Felhasználói licencek kezelése |
> | Microsoft. Directory/felhasználók/létrehozás | Felhasználók hozzáadása |
> | Microsoft. Directory/felhasználók/törlés | Felhasználók törlése |
> | Microsoft. Directory/felhasználók/letiltás | Felhasználók letiltása |
> | Microsoft. Directory/felhasználók/engedélyezés | Felhasználók engedélyezése |
> | Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | A felhasználói frissítési tokenek érvénytelenítésével kényszerítheti ki a kijelentkezést |
> | Microsoft. Directory/felhasználók/visszaállítás | Törölt felhasználók visszaállítása |
> | Microsoft. Directory/felhasználók/alapszintű/frissítés | A felhasználók alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/felhasználók/kezelő/frissítés | Frissítési kezelő felhasználók számára |
> | Microsoft. Directory/felhasználók/jelszó/frissítés | Az összes felhasználó jelszavának alaphelyzetbe állítása |
> | Microsoft. Directory/felhasználók/userPrincipalName/Update | Felhasználó egyszerű nevének frissítése |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="password-administrator"></a>Jelszó-rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók korlátozottan kezelhetik a jelszavakat. Ez a szerepkör nem teszi lehetővé a szolgáltatási kérelmek felügyeletét vagy a szolgáltatás állapotának figyelését. Azt határozza meg, hogy a jelszó-rendszergazda alaphelyzetbe állíthatja-e a felhasználó jelszavát, a felhasználó által hozzárendelt szerepkörtől függ. A jelszó-rendszergazda által a jelszavak alaphelyzetbe állításához használt szerepkörök listáját itt tekintheti meg: [jelszó-visszaállítási engedélyek](#password-reset-permissions).

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/felhasználók/jelszó/frissítés | Az összes felhasználó jelszavának alaphelyzetbe állítása |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="power-bi-administrator"></a>Power BI rendszergazda

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Power BIon belül, ha a szolgáltatás megtalálható, valamint a támogatási jegyek felügyeletének és a szolgáltatás állapotának figyelésének lehetősége. További információ [a Power bi rendszergazdai szerepkör megismeréséhez](/power-bi/service-admin-role).

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "Power BI szolgáltatás-rendszergazda" néven van azonosítva. A [Azure Portal](https://portal.azure.com)"Power bi rendszergazda".

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |
> | Microsoft. powerApps. powerBI/allEntities/allTasks | Power BI összes aspektusának kezelése |

## <a name="power-platform-administrator"></a>Energiagazdálkodási platform rendszergazdája

A szerepkör felhasználói a környezetek, a PowerApps, a folyamatok és az adatveszteség-megelőzési szabályzatok minden aspektusát létrehozhatják és kezelhetik. Emellett az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a támogatási jegyeket, és figyelik a szolgáltatás állapotát.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. dynamics365/allEntities/allTasks | A Dynamics 365 összes aspektusának kezelése |
> | Microsoft. flow/allEntities/allTasks | A Microsoft Power automatizálás minden aspektusának kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |
> | Microsoft. powerApps/allEntities/allTasks | A Power apps összes aspektusának kezelése |

## <a name="printer-administrator"></a>Nyomtató rendszergazdája

Az ebben a szerepkörben lévő felhasználók regisztrálhatják a nyomtatókat, és kezelhetik a Microsoft univerzális nyomtatási megoldás összes nyomtatási konfigurációjának minden aspektusát, beleértve az univerzális nyomtatási összekötő beállításait is. Az összes delegált nyomtatási engedély iránti kérelem beleegyezése. A nyomtató rendszergazdái is hozzáférhetnek a nyomtatási jelentésekhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. Print/allEntities/allProperties/allTasks | Nyomtatók és összekötők létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft Print szolgáltatásban |

## <a name="printer-technician"></a>Nyomtató technikusa

Az ezzel a szerepkörrel rendelkező felhasználók regisztrálhatják a nyomtatókat, és kezelhetik a nyomtató állapotát a Microsoft Universal Print megoldásban. Emellett az összes összekötő adatait is elolvashatják. Legfontosabb feladat, hogy a nyomtató technikusa nem tud felhasználói engedélyeket beállítani a nyomtatók és a nyomtatók megosztásához.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. Print/Connectors/allProperties/READ | Az összekötők összes tulajdonságának olvasása a Microsoft Print szolgáltatásban |
> | Microsoft. Azure. Print/nyomtatók/allProperties/READ | A nyomtatók összes tulajdonságának olvasása a Microsoft Print szolgáltatásban |
> | Microsoft. Azure. Print/nyomtatók/regisztráció | Nyomtatók regisztrálása a Microsoft Print szolgáltatásban |
> | Microsoft. Azure. Print/nyomtatók/Regisztráció törlése | Nyomtatók regisztrációjának törlése a Microsoft Print szolgáltatásban |
> | Microsoft. Azure. Print/nyomtatók/alapszintű/frissítés | A nyomtatók alapszintű tulajdonságainak frissítése a Microsoft Print szolgáltatásban |

## <a name="privileged-authentication-administrator"></a>Kiemelt jogosultságú hitelesítés rendszergazdája

Az ezzel a szerepkörrel rendelkező felhasználók bármely felhasználóhoz (beleértve a jelszavakat is) bármilyen hitelesítési módszert beállíthatnak vagy állíthatnak vissza, beleértve a globális rendszergazdákat. A privilegizált hitelesítő rendszergazdák kényszerítheti a felhasználókat, hogy újra regisztráljanak a meglévő, nem jelszóval nem rendelkező hitelesítő adatokkal (például az MFA vagy a (z)

A [hitelesítési rendszergazdai](#authentication-administrator) szerepkör jogosult az újraregisztrálásra és a többtényezős hitelesítésre az általános jogú felhasználók és a felhasználók számára bizonyos rendszergazdai szerepkörökkel.

A [hitelesítési házirend rendszergazdai](#authentication-policy-administrator) szerepköre rendelkezik engedélyekkel a bérlő hitelesítési módszer házirendjének beállításához, amely meghatározza, hogy az egyes felhasználók hogyan regisztrálhatnak és használhatnak.

| Szerepkör | A felhasználó hitelesítési módszereinek kezelése | Felhasználónkénti MFA kezelése | MFA-beállítások kezelése | Hitelesítési módszer házirendjének kezelése | Jelszavas védelmi szabályzat kezelése |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Hitelesítés rendszergazdája | Igen, egyes felhasználók számára (lásd fent) | Igen, egyes felhasználók számára (lásd fent) | Nem | Nem | Nem |
| Kiemelt jogosultságú hitelesítés rendszergazdája| Igen minden felhasználó számára | Igen minden felhasználó számára | Nem | Nem | Nem |
| Hitelesítési házirend rendszergazdája | Nem | Nem | Igen | Igen | Igen |

> [!IMPORTANT]
> Az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják azoknak a személyeknek a hitelesítő adatait, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz, illetve a Azure Active Directoryon belüli és kívüli kritikus konfigurációhoz. A felhasználók hitelesítő adatainak módosítása azt jelentheti, hogy a felhasználó identitását és engedélyeit feltételezi. Például:
>
>* Az alkalmazás regisztrálása és a vállalati alkalmazások tulajdonosai, akik kezelhetik a saját alkalmazások hitelesítő adatait. Ezek az alkalmazások jogosultsági szintű engedélyekkel rendelkezhetnek az Azure AD-ben, és másutt nem kapják meg a hitelesítési rendszergazdákat. Ezen az elérési úton a hitelesítési rendszergazda feltételezheti az alkalmazás tulajdonosának identitását, majd az alkalmazás hitelesítő adatainak frissítésével továbbra is feltételezheti a Kiemelt alkalmazások identitását.
>* Az Azure-előfizetések tulajdonosai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz vagy az Azure-beli kritikus konfigurációhoz.
>* Biztonsági csoport és Microsoft 365 csoport tulajdonosai, akik kezelhetik a csoporttagság kezelését. Ezek a csoportok hozzáférést biztosíthatnak a bizalmas vagy magánjellegű információkhoz, illetve a kritikus konfigurációhoz az Azure AD-ben és máshol.
>* Az Azure AD-n kívül más szolgáltatásokban, például az Exchange Online-ban, az Office biztonsági és megfelelőségi központban és a humán erőforrás-rendszerekben található rendszergazdák.
>* Nem rendszergazdák, például vezetők, jogi tanácsadás és emberi erőforrások alkalmazottai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz.


> [!IMPORTANT]
> Ez a szerepkör jelenleg nem képes a felhasználónkénti MFA kezelésére az örökölt MFA felügyeleti portálon. Ugyanezek a függvények a [set-MsolUser](https://docs.microsoft.com/powershell/module/msonline/set-msoluser) PARANCSMAGOT Azure ad PowerShell-modul használatával végezhetők el.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | A felhasználói frissítési tokenek érvénytelenítésével kényszerítheti ki a kijelentkezést |
> | Microsoft. Directory/felhasználók/strongAuthentication/Update | A felhasználók erős hitelesítési tulajdonságának frissítése |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="privileged-role-administrator"></a>Kiemelt szerepkörű rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók a szerepkör-hozzárendeléseket kezelhetik Azure Active Directoryban, valamint a Azure AD Privileged Identity Managementon belül is. Létrehozhatnak és kezelhetnek Azure AD-szerepkörökhöz hozzárendelhető csoportokat. Emellett ez a szerepkör lehetővé teszi Privileged Identity Management és felügyeleti egységek valamennyi aspektusának kezelését.

> [!IMPORTANT]
> Ez a szerepkör lehetővé teszi az összes Azure AD-szerepkör hozzárendelésének kezelését, beleértve a globális rendszergazdai szerepkört is. Ez a szerepkör nem tartalmaz más, az Azure AD-ben található, például felhasználók létrehozásához vagy frissítéséhez szükséges jogosultságokkal rendelkező képességeket. Az ehhez a szerepkörhöz hozzárendelt felhasználók azonban további jogosultságokat is biztosíthatnak a további szerepkörök hozzárendelésével.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/Administrativeunit/allProperties/allTasks | Felügyeleti egységek létrehozása és kezelése (beleértve a tagokat) |
> | Microsoft. Directory/appRoleAssignments/allProperties/allTasks | AppRoleAssignments létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/authorizationPolicy/allProperties/allTasks | Az engedélyezési házirendek összes aspektusának kezelése |
> | Microsoft. Directory/directoryRoles/allProperties/allTasks | Címtárbeli szerepkörök létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/groupsAssignableToRoles/allProperties/Update | A isAssignableToRole tulajdonsággal rendelkező csoportok frissítése True értékre |
> | Microsoft. Directory/groupsAssignableToRoles/Create | IsAssignableToRole tulajdonsággal rendelkező csoportok létrehozása True értékre |
> | Microsoft. Directory/groupsAssignableToRoles/delete | A isAssignableToRole tulajdonsággal rendelkező csoportok törlése igaz értékre |
> | Microsoft. Directory/Oauth2permissiongrant objektumok/allProperties/allTasks | OAuth 2,0 engedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése Privileged Identity Management |
> | Microsoft. Directory/roleAssignments/allProperties/allTasks | Szerepkör-hozzárendelések létrehozása és törlése, valamint az összes szerepkör-hozzárendelési tulajdonság olvasása és frissítése |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Szerepkör-definíciók létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Egyszerű szolgáltatás szerepkör-hozzárendeléseinek frissítése |
> | Microsoft. Directory/servicePrincipals/engedélyek/frissítés | Egyszerű szolgáltatásnév engedélyeinek frissítése |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Company-admin | Hozzájárulás engedélyezése bármely alkalmazáshoz bármely engedélyhez |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="reports-reader"></a>Jelentések olvasója

Az ezzel a szerepkörrel rendelkező felhasználók megtekinthetik a használati jelentéskészítési adatokat és a jelentések irányítópultot Microsoft 365 felügyeleti központban, valamint a Power BI bevezetésének környezeti csomagjában. Emellett a szerepkör hozzáférést biztosít a bejelentkezési jelentésekhez és tevékenységekhez az Azure AD-ben, és a Microsoft Graph jelentési API által visszaadott adat. A jelentés-olvasó szerepkörhöz hozzárendelt felhasználó csak a megfelelő használati és bevezetési metrikákat érheti el. Nem rendelkeznek rendszergazdai jogosultsággal a beállítások konfigurálásához, illetve a termékspecifikus felügyeleti központok, például az Exchange eléréséhez. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/READ | A naplók összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/provisioningLogs/allProperties/READ | A kiépítési naplók összes tulajdonságának olvasása |
> | Microsoft. Directory/signInReports/allProperties/READ | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/READ | Office 365-használati jelentések olvasása |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="search-administrator"></a>Keresés a Rendszergazdában

Az ebben a szerepkörben lévő felhasználók teljes hozzáféréssel rendelkeznek a Microsoft 365 felügyeleti központban található összes Microsoft keresési felügyeleti szolgáltatáshoz. Emellett ezek a felhasználók megtekinthetik az üzenetközpont, figyelheti a szolgáltatás állapotát és szolgáltatási kérelmeket hozhatnak létre.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Office 365. messageCenter/messages/READ | Üzenetek olvasása az üzenetsor-központban a Microsoft 365 felügyeleti központban, a biztonsági üzenetek kivételével |
> | Microsoft. Office 365. Search/Content/Manage | Tartalom létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft Search szolgáltatásban |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="search-editor"></a>Keresési szerkesztő

Az ebben a szerepkörben lévő felhasználók létrehozhatnak, kezelhetnek és törölhetnek tartalmakat a Microsoft Search szolgáltatáshoz a Microsoft 365 felügyeleti központban, beleértve a könyvjelzőket, a Q&a-t és a helyet.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Office 365. messageCenter/messages/READ | Üzenetek olvasása az üzenetsor-központban a Microsoft 365 felügyeleti központban, a biztonsági üzenetek kivételével |
> | Microsoft. Office 365. Search/Content/Manage | Tartalom létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft Search szolgáltatásban |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="security-administrator"></a>Biztonsági rendszergazda

Az ehhez a szerepkörhöz tartozó felhasználók a Microsoft 365 Security Center, a Azure Active Directory Identity Protection, a Azure Active Directory Authentication, a Azure Information Protection és az Office 365 Security & megfelelőségi központ biztonsággal kapcsolatos funkcióinak kezelésére jogosultak. Az Office 365 engedélyeivel kapcsolatos további információk [a biztonsági & megfelelőségi központban](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)találhatók.

In | Elvégezhető
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | A biztonsággal kapcsolatos házirendek figyelése Microsoft 365 szolgáltatások között<br>Biztonsági fenyegetések és riasztások kezelése<br>Jelentések megtekintése
Identity Protection-központ | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Emellett az összes Identity Protection Center-művelet elvégzésének lehetősége, kivéve a jelszavak alaphelyzetbe állítását
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Az Azure AD szerepkör-hozzárendelések és-beállítások **nem** kezelhetők
[Office 365 biztonsági & megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági szabályzatok kezelése<br>Biztonsági fenyegetések megtekintése, vizsgálata és reagálás<br>Jelentések megtekintése
Azure Komplex veszélyforrások elleni védelem | Gyanús biztonsági tevékenység figyelése és reagálás
Windows Defender ATP és EDR | Szerepkörök hozzárendelése<br>Számítógépcsoportok kezelése<br>A végponti fenyegetések észlelésének és automatikus szervizelésének konfigurálása<br>Riasztások megtekintése, vizsgálata és válaszadás
[megkezdése](/intune/role-based-access-control) | A felhasználók, az eszközök, a beléptetés, a konfiguráció és az alkalmazás adatainak megtekintése<br>Nem lehet módosítani az Intune-t
[Cloud App Security](/cloud-app-security/manage-admins) | Rendszergazdák hozzáadása, szabályzatok és beállítások hozzáadása, naplók feltöltése és irányítási műveletek végrehajtása
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági szabályzatokat, megtekintheti a riasztásokat és a javaslatokat,
[Microsoft 365 szolgáltatás állapota](/office365/enterprise/view-service-health) | Microsoft 365 szolgáltatások állapotának megtekintése
[Intelligens zárolás](../authentication/howto-password-smart-lockout.md) | A zárolások küszöbértékének és időtartamának megadása, ha a sikertelen bejelentkezési események történnek.
[Jelszavas védelem](../authentication/concept-password-ban-bad.md) | Konfigurálja az egyéni tiltott jelszavak listáját vagy a helyszíni jelszavas védelmet.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/alkalmazások/házirendek/frissítés | Alkalmazások szabályzatának frissítése |
> | Microsoft. Directory/auditLogs/allProperties/READ | A naplók összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/bitlockerKeys/kulcs/olvasás | BitLocker-metaadatok és-kulcs olvasása eszközökön |
> | Microsoft. Directory/entitlementManagement/allProperties/READ | Az Azure AD-jogosultságok kezelésének összes tulajdonságának olvasása |
> | Microsoft. Directory/identityProtection/allProperties/READ | Azure AD Identity Protection összes erőforrásának olvasása |
> | Microsoft. Directory/identityProtection/allProperties/Update | Azure AD Identity Protection összes erőforrásának frissítése |
> | Microsoft. Directory/házirendek/létrehozás | Szabályzatok létrehozása az Azure AD-ben |
> | Microsoft. Directory/házirendek/törlés | Szabályzatok törlése az Azure AD-ben |
> | Microsoft. Directory/házirendek/alapszintű/frissítés | Szabályzatok alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/házirendek/tulajdonosok/frissítés | Szabályzatok tulajdonosának frissítése |
> | Microsoft. Directory/házirendek/tenantDefault/Update | Alapértelmezett szervezeti házirendek frissítése |
> | Microsoft. Directory/conditionalAccessPolicies/Create | Feltételes hozzáférési szabályzatok létrehozása |
> | Microsoft. Directory/conditionalAccessPolicies/delete | Feltételes hozzáférési szabályzatok törlése |
> | Microsoft. Directory/conditionalAccessPolicies/standard/olvasás | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/tulajdonosok/olvasás | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/READ | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/Basic/Update | Feltételes hozzáférési szabályzatok alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/conditionalAccessPolicies/owners/Update | A policies. conditionalAccess tulajdonság frissítése |
> | Microsoft. Directory/conditionalAccessPolicies/tenantDefault/Update | A policies. conditionalAccess tulajdonság frissítése |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/READ | Privileged Identity Management összes erőforrásának olvasása |
> | Microsoft. Directory/provisioningLogs/allProperties/READ | A kiépítési naplók összes tulajdonságának olvasása |
> | Microsoft. Directory/servicePrincipals/házirendek/frissítés | Az egyszerű szolgáltatások szabályzatának frissítése |
> | Microsoft. Directory/signInReports/allProperties/READ | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. protectionCenter/allEntities/standard/READ | Az Office 365 Protection Center összes erőforrásának általános tulajdonságainak olvasása |
> | Microsoft. Office 365. protectionCenter/allEntities/Basic/Update | Az Office 365 Protection Center összes erőforrásának alapszintű tulajdonságainak frissítése |
> | Microsoft. Office 365. protectionCenter/attackSimulator/hasznos adat/allProperties/allTasks | Támadási hasznos adatok létrehozása és kezelése a támadási szimulátorban |
> | Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/READ | A támadási szimulációval, a válaszokkal és a kapcsolódó képzésekkel kapcsolatos jelentések olvasása |
> | Microsoft. Office 365. protectionCenter/attackSimulator/szimulációs/allProperties/allTasks | Támadási szimulációs sablonok létrehozása és kezelése a támadási szimulátorban |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="security-operator"></a>Biztonsági operátor

Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a riasztásokat, és globális olvasási hozzáféréssel rendelkeznek a biztonsággal kapcsolatos funkciókhoz, beleértve a Microsoft 365 Security Center, a Azure Active Directory, az Identity Protection, a Privileged Identity Management és az Office 365 Security & megfelelőségi központ összes adatát. Az Office 365 engedélyeivel kapcsolatos további információk [a biztonsági & megfelelőségi központban](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)találhatók.

In | Elvégezhető
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Biztonsági fenyegetésekkel kapcsolatos riasztások megtekintése, vizsgálata és reagálás
Azure AD Identity Protection | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Emellett az összes Identity Protection Center-művelet elvégzésére is képes, kivéve a jelszavak alaphelyzetbe állítását és a riasztási e-mailek konfigurálását.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | A biztonsági olvasó szerepkörre vonatkozó összes engedély
[Office 365 biztonsági & megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Biztonsági riasztások megtekintése, vizsgálata és reagálás
Windows Defender ATP és EDR | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Biztonsági riasztások megtekintése, vizsgálata és reagálás
[megkezdése](/intune/role-based-access-control) | A biztonsági olvasó szerepkörre vonatkozó összes engedély
[Cloud App Security](/cloud-app-security/manage-admins) | A biztonsági olvasó szerepkörre vonatkozó összes engedély
[Microsoft 365 szolgáltatás állapota](/office365/enterprise/view-service-health) | Microsoft 365 szolgáltatások állapotának megtekintése

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/READ | A naplók összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése Microsoft Cloud App Security |
> | Microsoft. Directory/identityProtection/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése Azure AD Identity Protection |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/READ | Privileged Identity Management összes erőforrásának olvasása |
> | Microsoft. Directory/signInReports/allProperties/READ | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Azure. advancedThreatProtection/allEntities/allTasks | Az Azure komplex veszélyforrások elleni védelem összes aspektusának kezelése |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Intune/allEntities/READ | Microsoft Intune összes erőforrásának olvasása |
> | Microsoft. Office 365. securityComplianceCenter/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a Microsoft 365 biztonsági és megfelelőségi központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/allTasks | A Microsoft Defender és a végpont összes aspektusának kezelése |

## <a name="security-reader"></a>Biztonsági olvasó

Az ehhez a szerepkörhöz tartozó felhasználók globális olvasási hozzáféréssel rendelkeznek a biztonsággal kapcsolatos funkcióhoz, beleértve a Microsoft 365 Security Center, a Azure Active Directory, az Identity Protection, a Privileged Identity Management, valamint a Azure Active Directory bejelentkezési jelentések és naplók olvasását, valamint az Office 365 biztonsági & megfelelőségi központot. Az Office 365 engedélyeivel kapcsolatos további információk [a biztonsági & megfelelőségi központban](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)találhatók.

In | Elvégezhető
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | A biztonsággal kapcsolatos házirendek megtekintése Microsoft 365 szolgáltatások között<br>Biztonsági fenyegetések és riasztások megtekintése<br>Jelentések megtekintése
Identity Protection-központ | A biztonsági funkciókkal kapcsolatos összes biztonsági jelentés és beállítás olvasása<br><ul><li>Levélszemét elleni<li>Titkosítás<li>Adatveszteség-megelőzés<li>Kártevők elleni<li>Speciális fenyegetésvédelem<li>Adathalászat elleni védekezés<li>E-mail-flow-szabályok
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | A csak olvasási hozzáféréssel rendelkezik a Azure AD Privileged Identity Managementban felkészített összes információhoz: szabályzatok és jelentések az Azure AD szerepkör-hozzárendelésekhez és biztonsági felülvizsgálatokhoz.<br>**Nem lehet** regisztrálni a Azure ad Privileged Identity Managementre, és nem végezheti el a módosításokat. A Privileged Identity Management portálon vagy a PowerShellen keresztül a szerepkörhöz tartozó valaki további szerepköröket (például globális rendszergazda vagy Kiemelt szerepkörű rendszergazda) is aktiválhat, ha a felhasználó jogosult rájuk.
[Office 365 biztonsági & megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági szabályzatok megtekintése<br>Biztonsági fenyegetések megtekintése és kivizsgálása<br>Jelentések megtekintése
Windows Defender ATP és EDR | Riasztások megtekintése és kivizsgálása. Ha bekapcsolja a szerepköralapú hozzáférés-vezérlést a Windows Defender ATP-ben, a csak olvasási jogosultsággal rendelkező felhasználók, például az Azure AD biztonsági olvasó szerepkör elvesztik a hozzáférést, amíg hozzá nem rendelnek egy Windows Defender ATP-szerepkörhöz.
[megkezdése](/intune/role-based-access-control) | A felhasználó, az eszköz, a beléptetés, a konfiguráció és az alkalmazás adatainak megtekintése. Az Intune-ban nem hajthat végre változtatásokat.
[Cloud App Security](/cloud-app-security/manage-admins) | Csak olvasási jogosultsággal rendelkezik, és képes kezelni a riasztásokat
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem végezhet módosításokat
[Microsoft 365 szolgáltatás állapota](/office365/enterprise/view-service-health) | Microsoft 365 szolgáltatások állapotának megtekintése

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/READ | A naplók összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Directory/bitlockerKeys/kulcs/olvasás | BitLocker-metaadatok és-kulcs olvasása eszközökön |
> | Microsoft. Directory/entitlementManagement/allProperties/READ | Az Azure AD-jogosultságok kezelésének összes tulajdonságának olvasása |
> | Microsoft. Directory/identityProtection/allProperties/READ | Azure AD Identity Protection összes erőforrásának olvasása |
> | Microsoft. Directory/házirendek/standard/olvasás | A szabályzatok alapszintű tulajdonságainak olvasása |
> | Microsoft. Directory/házirendek/tulajdonosok/olvasás | Szabályzatok tulajdonosának olvasása |
> | Microsoft. Directory/házirendek/policyAppliedTo/olvasás | A policies. policyAppliedTo tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/standard/olvasás | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/tulajdonosok/olvasás | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/READ | A policies. conditionalAccess tulajdonság olvasása |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/READ | Privileged Identity Management összes erőforrásának olvasása |
> | Microsoft. Directory/provisioningLogs/allProperties/READ | A kiépítési naplók összes tulajdonságának olvasása |
> | Microsoft. Directory/signInReports/allProperties/READ | A bejelentkezési jelentések összes tulajdonságának olvasása, beleértve a privilegizált tulajdonságokat is |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Office 365. protectionCenter/allEntities/standard/READ | Az Office 365 Protection Center összes erőforrásának általános tulajdonságainak olvasása |
> | Microsoft. Office 365. protectionCenter/attackSimulator/hasznos adat/allProperties/olvasás | A támadásokkal kapcsolatos adattartalom összes tulajdonságának olvasása a támadási szimulátorban |
> | Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/READ | A támadási szimulációval, a válaszokkal és a kapcsolódó képzésekkel kapcsolatos jelentések olvasása |
> | Microsoft. Office 365. protectionCenter/attackSimulator/szimulációs/allProperties/olvasás | A támadási szimulátorban található Attack szimulációs sablonok összes tulajdonságának olvasása |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="service-support-administrator"></a>Szolgáltatás-támogatási rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók megnyithatják a Microsoft Azure-hoz és a Microsoft 365-szolgáltatásokhoz tartozó támogatási kéréseket, és megtekintik a szolgáltatás irányítópultját és az üzenetközpont szolgáltatást a [Azure Portal](https://portal.azure.com) és [Microsoft 365 felügyeleti központban](https://admin.microsoft.com). További információ a [rendszergazdai szerepkörökről](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Korábban ezt a szerepkört "szolgáltatás-rendszergazdaként" hívták a [Azure Portal](https://portal.azure.com) és [Microsoft 365 felügyeleti központban](https://admin.microsoft.com). Átnevezte a szolgáltatást támogató rendszergazdának, hogy az Microsoft Graph API-ban, az Azure AD Graph API és az Azure AD PowerShellben meglévő.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="sharepoint-administrator"></a>SharePoint-rendszergazda

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft SharePoint Online-ban, ha a szolgáltatás megtalálható, valamint az összes Microsoft 365 csoport létrehozását és felügyeletét, a támogatási jegyek kezelését és a szolgáltatás állapotának figyelését. További információ a [rendszergazdai szerepkörökről](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "SharePoint-szolgáltatás rendszergazdája" lesz azonosítva. A [Azure Portal](https://portal.azure.com)a "SharePoint Administrator".

> [!NOTE]
> Ez a szerepkör hatókörön belüli engedélyeket is biztosít a Microsoft Intune Microsoft Graph API számára, így lehetővé teszi a SharePoint-és OneDrive-erőforrásokkal kapcsolatos házirendek felügyeletét és konfigurálását.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/csoportok. egyesített/létrehozás | Szerepkörökhöz hozzárendelhető csoportok kizárásával Microsoft 365 csoportok létrehozása |
> | Microsoft. Directory/groups. Unified/delete | Szerepkör-hozzárendelhető csoportok kizárása Microsoft 365 csoportok törlése |
> | Microsoft. Directory/groups. Unified/Restore | Microsoft 365 csoportok visszaállítása |
> | Microsoft. Directory/csoportok. Unified/Basic/Update | Microsoft 365 csoportok alapszintű tulajdonságainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok. Unified/tagok/frissítés | Microsoft 365 csoportok tagjainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/groups. Unified/owners/Update | Microsoft 365 csoportok tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. Network/Performance/allProperties/READ | Az összes hálózati teljesítmény tulajdonságainak olvasása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. sharePoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a SharePointban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/READ | Office 365-használati jelentések olvasása |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="skype-for-business-administrator"></a>Skype vállalati verzió-rendszergazda

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Skype vállalati verziójában, ha a szolgáltatás megtalálható, valamint a Skype-specifikus felhasználói attribútumok kezelése a Azure Active Directoryban. Emellett ez a szerepkör lehetővé teszi a támogatási jegyek felügyeletét és a szolgáltatás állapotának figyelését, valamint a csapatok és a Skype vállalati felügyeleti központ elérését. A fióknak a csapatoknak is licenccel kell rendelkeznie, vagy nem futtathat Team PowerShell-parancsmagokat. További információ [a Skype for Business-rendszergazdai szerepkörrel és a](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) Teams licencelési információkkal kapcsolatban a [Skype vállalati verzió és a Microsoft Teams bővítmény licencelése](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing) szolgáltatásban

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "Lync szolgáltatás-rendszergazdaként" azonosítható. A [Azure Portal](https://portal.azure.com/)"Skype vállalati rendszergazda".

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | A Skype vállalati online verzió összes aspektusának kezelése |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/READ | Office 365-használati jelentések olvasása |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="teams-administrator"></a>Csapatok rendszergazdája

A szerepkör felhasználói a Microsoft Teams munkaterhelésének minden aspektusát felügyelhetik a Microsoft csapatainak & a Skype vállalati felügyeleti központ és a megfelelő PowerShell-modulok használatával. Ez többek között a telefonos, üzenetküldési, értekezlet-és a csapatokhoz kapcsolódó felügyeleti eszközökre is kiterjed. Ez a szerepkör emellett lehetőséget biztosít az összes Microsoft 365 csoport létrehozására és felügyeletére, a támogatási jegyek kezelésére és a szolgáltatás állapotának figyelésére.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/csoportok/hiddenMembers/olvasás | Csoport rejtett tagjainak olvasása |
> | Microsoft. Directory/csoportok. egyesített/létrehozás | Szerepkörökhöz hozzárendelhető csoportok kizárásával Microsoft 365 csoportok létrehozása |
> | Microsoft. Directory/groups. Unified/delete | Szerepkör-hozzárendelhető csoportok kizárása Microsoft 365 csoportok törlése |
> | Microsoft. Directory/groups. Unified/Restore | Microsoft 365 csoportok visszaállítása |
> | Microsoft. Directory/csoportok. Unified/Basic/Update | Microsoft 365 csoportok alapszintű tulajdonságainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok. Unified/tagok/frissítés | Microsoft 365 csoportok tagjainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/groups. Unified/owners/Update | Microsoft 365 csoportok tulajdonosainak frissítése a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-all-Application-permissions | A szolgáltatás közvetlen hozzáférésének biztosítása a pricipal  |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. Network/Performance/allProperties/READ | Az összes hálózati teljesítmény tulajdonságainak olvasása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | A Skype vállalati online verzió összes aspektusának kezelése |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/READ | Office 365-használati jelentések olvasása |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |
> | Microsoft. Teams/allEntities/allProperties/allTasks | A csapatok összes erőforrásának kezelése |

## <a name="teams-communications-administrator"></a>Csapatok kommunikációs rendszergazdája

Az ebben a szerepkörben lévő felhasználók kezelhetik a Microsoft Teams munkaterhelésének a hang& telefonos szolgáltatással kapcsolatos aspektusait. Ez magában foglalja a telefonszám-hozzárendelés, a hang-és értekezlet-szabályzatok felügyeleti eszközeit, valamint a Call Analytics eszközkészlet teljes hozzáférését.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | A Skype vállalati online verzió összes aspektusának kezelése |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/READ | Office 365-használati jelentések olvasása |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |
> | Microsoft. Teams/callQuality/allProperties/READ | Az összes érték olvasása a hívás minőségi irányítópultján (CQD) |
> | Microsoft. Teams/találkozók/allProperties/allTasks | Értekezletek kezelése, beleértve a szabályzatokat, a konfigurációkat és a konferencia-hidakat |
> | Microsoft. Teams/Voice/allProperties/allTasks | Hang kezelése, beleértve a Meghívási szabályzatokat és a telefonszámok leltározását és hozzárendelését |

## <a name="teams-communications-support-engineer"></a>Csapat kommunikációs támogató mérnök

Az ebben a szerepkörben lévő felhasználók a Microsoft Teams szolgáltatásban a Skype for Business felügyeleti központban a & Microsoft Teams felhasználói hívás hibaelhárítási eszközeivel végezhetik el a kommunikációval & kapcsolatos problémákat. Az ebben a szerepkörben lévő felhasználók megtekinthetik a teljes hívási rekord adatait az összes érintett résztvevő számára. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | A Skype vállalati online verzió összes aspektusának kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |
> | Microsoft. Teams/callQuality/allProperties/READ | Az összes érték olvasása a hívás minőségi irányítópultján (CQD) |

## <a name="teams-communications-support-specialist"></a>A Teams Communications támogatási szakértője

Az ebben a szerepkörben lévő felhasználók a Microsoft Teams szolgáltatásban a Skype for Business felügyeleti központban a & Microsoft Teams felhasználói hívás hibaelhárítási eszközeivel végezhetik el a kommunikációval & kapcsolatos problémákat. Az ebben a szerepkörben lévő felhasználók csak az adott felhasználóra vonatkozó hívásban tekinthetik meg a felhasználói adatokat. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | A Skype vállalati online verzió összes aspektusának kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |
> | Microsoft. Teams/callQuality/standard/olvasás | Alapszintű adatok olvasása a hívás minőségi irányítópultján (CQD) |

## <a name="teams-devices-administrator"></a>Csapatok eszközeinek rendszergazdája

Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a csapatok [által hitelesített eszközöket](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) a csapatok felügyeleti központjából. Ez a szerepkör lehetővé teszi az összes eszköz megtekintését egyetlen pillantással, az eszközök keresésének és szűrésének lehetőségével. A felhasználó megtekintheti az egyes eszközök adatait, például a bejelentkezett fiókot, az eszköz gyártmányát és modelljét. A felhasználó módosíthatja az eszköz beállításait, és frissítheti a szoftver verzióit. Ez a szerepkör nem biztosít engedélyeket a csapatok ellenőrzéséhez és az eszköz minőségének meghívásához.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |
> | Microsoft. Teams/eszközök/standard/olvasás | A csapat által hitelesített eszközök, például a konfigurációs szabályzatok összes aspektusának kezelése |

## <a name="usage-summary-reports-reader"></a>Használati összesítő jelentések olvasója

Az ezzel a szerepkörrel rendelkező felhasználók hozzáférhetnek a bérlői szintű összesített adatokhoz és a Microsoft 365 felügyeleti központban a használati és termelékenységi pontszámhoz kapcsolódó információkhoz, de nem férhetnek hozzá a felhasználói szint részleteihez vagy elemzésekhez. Microsoft 365 felügyeleti központban a két jelentés esetében különbséget teszünk a bérlői szint összesített adatai és a felhasználói szintek részletei között. Ez a szerepkör egy külön védelmi réteget biztosít az egyes felhasználó által azonosítható adatokhoz, amelyeket mind az ügyfelek, mind a jogi csapatok igényeltek.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Office 365. usageReports/allEntities/standard/READ | Bérlői szintű összesített Office 365 használati jelentések olvasása |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="user-administrator"></a>Felhasználói rendszergazda

Az ezzel a szerepkörrel rendelkező felhasználók létrehozhatnak felhasználókat, és kezelhetik a felhasználók minden aspektusát bizonyos korlátozásokkal (lásd a táblázatot), és frissíthetik a jelszó-elévülési házirendeket. Emellett az ezzel a szerepkörrel rendelkező felhasználók az összes csoportot is létrehozhatják és kezelhetik. Ez a szerepkör magában foglalja a felhasználói nézetek létrehozását és kezelését, a támogatási jegyek kezelését és a szolgáltatás állapotának figyelését is. A felhasználói rendszergazdáknak nincs engedélye arra, hogy a legtöbb rendszergazdai szerepkörbe tartozó felhasználókhoz felhasználói tulajdonságokat kezeljenek. Az ehhez a szerepkörhöz tartozó felhasználó nem rendelkezik az MFA felügyeletéhez szükséges engedélyekkel. A korlátozás alól kivételt képező szerepkörök az alábbi táblázatban láthatók.

| Felhasználói rendszergazdai engedély | Jegyzetek |
| --- | --- |
| Felhasználók és csoportok létrehozása<br/>Felhasználói nézetek létrehozása és kezelése<br/>Office-támogatási jegyek kezelése<br/>Jelszó-elévülési szabályzatok frissítése |  |
| Licencek kezelése<br/>Az összes felhasználói tulajdonság kezelése az egyszerű felhasználónév kivételével | Minden felhasználóra vonatkozik, beleértve az összes rendszergazdát is |
| Törlés és visszaállítás<br/>Letiltás és engedélyezés<br/>Az összes felhasználói tulajdonság kezelése, beleértve az egyszerű felhasználónevet<br/>Eszköz kulcsainak frissítése | Azokra a felhasználókra vonatkozik, akik nem rendszergazdák vagy a következő szerepkörök valamelyikében vannak:<ul><li>Segélyszolgálat rendszergazdája</li><li>Nem szerepkörrel rendelkező felhasználó</li><li>Felhasználói rendszergazda</li></ul> |
| Frissítési tokenek érvénytelenítése<br/>Új jelszó létrehozása | A felhasználói rendszergazda által a frissítési tokenek jelszavának alaphelyzetbe állításához szükséges szerepkörök listáját lásd: jelszó- [visszaállítási engedélyek](#password-reset-permissions). |

> [!IMPORTANT]
> Az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják azokat a személyeket, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz, illetve a Azure Active Directoryon belül és kívül is kritikus konfigurációhoz. A felhasználó jelszavának módosítása azt jelentheti, hogy a felhasználó identitását és engedélyeit feltételezi. Például:
>
>- Az alkalmazás regisztrálása és a vállalati alkalmazások tulajdonosai, akik kezelhetik a saját alkalmazások hitelesítő adatait. Ezek az alkalmazások jogosultsági szintű engedélyekkel rendelkezhetnek az Azure AD-ben, és máshol nem kapnak felhasználói rendszergazdákat. Ezen az elérési úton a felhasználói rendszergazda feltételezheti az alkalmazás tulajdonosának identitását, majd az alkalmazáshoz tartozó hitelesítő adatok frissítésével továbbra is feltételezheti a Kiemelt alkalmazások identitását.
>- Az Azure-előfizetések tulajdonosai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz vagy az Azure-beli kritikus konfigurációhoz.
>- Biztonsági csoport és Microsoft 365 csoport tulajdonosai, akik kezelhetik a csoporttagság kezelését. Ezek a csoportok hozzáférést biztosíthatnak a bizalmas vagy magánjellegű információkhoz, illetve a kritikus konfigurációhoz az Azure AD-ben és máshol.
>- Az Azure AD-n kívül más szolgáltatásokban, például az Exchange Online-ban, az Office biztonsági és megfelelőségi központban és a humán erőforrás-rendszerekben található rendszergazdák.
>- Nem rendszergazdák, például vezetők, jogi tanácsadás és emberi erőforrások alkalmazottai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft. Directory/appRoleAssignments/Create | Alkalmazás-szerepkör-hozzárendelések létrehozása |
> | Microsoft. Directory/appRoleAssignments/delete | Alkalmazás-szerepkör-hozzárendelések törlése |
> | Microsoft. Directory/appRoleAssignments/Basic/Update | Alkalmazás-szerepkör-hozzárendelések alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/névjegyek/létrehozás | Névjegyek létrehozása |
> | Microsoft. Directory/Contacts/delete | Névjegyek törlése |
> | Microsoft. Directory/Contacts/Basic/Update | A névjegyek alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/entitlementManagement/allProperties/allTasks | Erőforrások létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure AD-jogosultságok kezelésében |
> | Microsoft. Directory/csoportok/assignLicense | Licencek kiosztása csoportok számára csoport alapú licenceléshez |
> | Microsoft. Directory/csoportok/létrehozás | Csoportok létrehozása a szerepkörhöz hozzárendelhető csoportok kizárásával |
> | Microsoft. Directory/csoportok/törlés | Csoportok törlése, a szerepkörhöz hozzárendelhető csoport kizárásával |
> | Microsoft. Directory/csoportok/hiddenMembers/olvasás | Csoport rejtett tagjainak olvasása |
> | Microsoft. Directory/csoportok/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása csoport alapú licenceléshez |
> | Microsoft. Directory/csoportok/visszaállítás | Törölt csoportok visszaállítása |
> | Microsoft. Directory/csoportok/alapszintű/frissítés | A csoportok alapszintű tulajdonságainak frissítése, a szerepkörhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/besorolás/frissítés | A csoportok besorolási tulajdonságának frissítése, a szerepkörhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/dynamicMembershipRule/Update | A dinamikus tagsági szabályok frissítése a szerepkörökhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/groupType/Update | Egy csoport groupType tulajdonságának frissítése |
> | Microsoft. Directory/csoportok/tagok/frissítés | Csoportok tagjainak frissítése, a szerepkörhöz hozzárendelhető csoportok kivételével |
> | Microsoft. Directory/csoportok/onPremWriteBack/Update | Azure AD-csoportok frissítése a helyi környezetbe való visszaíráshoz |
> | Microsoft. Directory/csoportok/tulajdonosok/frissítés | Csoportok tulajdonosainak frissítése, a szerepkörhöz hozzárendelhető csoportok kizárása |
> | Microsoft. Directory/csoportok/beállítások/frissítés | Csoportok beállításainak frissítése |
> | Microsoft. Directory/csoportok/láthatóság/frissítés | A csoportok láthatósági tulajdonságának frissítése |
> | Microsoft. Directory/Oauth2permissiongrant objektumok/allProperties/allTasks | OAuth 2,0 engedélyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Egyszerű szolgáltatás szerepkör-hozzárendeléseinek frissítése |
> | Microsoft. Directory/felhasználók/assignLicense | Felhasználói licencek kezelése |
> | Microsoft. Directory/felhasználók/létrehozás | Felhasználók hozzáadása |
> | Microsoft. Directory/felhasználók/törlés | Felhasználók törlése |
> | Microsoft. Directory/felhasználók/letiltás | Felhasználók letiltása |
> | Microsoft. Directory/felhasználók/engedélyezés | Felhasználók engedélyezése |
> | Microsoft. Directory/felhasználók/inviteGuest | Vendégfelhasználók meghívása |
> | Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | A felhasználói frissítési tokenek érvénytelenítésével kényszerítheti ki a kijelentkezést |
> | Microsoft. Directory/felhasználók/reprocessLicenseAssignment | Licenc-hozzárendelések újrafeldolgozása a felhasználók számára |
> | Microsoft. Directory/felhasználók/visszaállítás | Törölt felhasználók visszaállítása |
> | Microsoft. Directory/felhasználók/alapszintű/frissítés | A felhasználók alapszintű tulajdonságainak frissítése |
> | Microsoft. Directory/felhasználók/kezelő/frissítés | Frissítési kezelő felhasználók számára |
> | Microsoft. Directory/felhasználók/jelszó/frissítés | Az összes felhasználó jelszavának alaphelyzetbe állítása |
> | Microsoft. Directory/felhasználók/userPrincipalName/Update | Felhasználó egyszerű nevének frissítése |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Service Health olvasása és konfigurálása a Microsoft 365 felügyeleti központban |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Microsoft 365 szolgáltatási kérelmek létrehozása és kezelése |
> | Microsoft. Office 365. Webportal/allEntities/standard/olvasás | A Microsoft 365 felügyeleti központban található összes erőforrás alapszintű tulajdonságainak olvasása |

## <a name="deprecated-roles"></a>Elavult szerepkörök

A következő szerepkörök nem használhatók. Elavultak, és a későbbiekben el lesznek távolítva az Azure AD-ből.

* Ad hoc licenc rendszergazdája
* Eszköz csatlakoztatása
* Eszközkezelők
* Eszköz felhasználói
* Ellenőrzött e-mailek felhasználói létrehozója
* Postaláda-rendszergazda
* Munkahelyi eszközök csatlakoztatása

## <a name="roles-not-shown-in-the-portal"></a>A portálon nem látható szerepkörök

Nem minden, a PowerShell vagy az MS Graph API által visszaadott szerepkör jelenik meg Azure Portalban. A következő táblázat ezeket a különbségeket rendezi.

API neve | Azure Portal neve | Jegyzetek
-------- | ------------------- | -------------
Eszköz csatlakoztatása | Elavult | [Elavult szerepkörök dokumentációja](#deprecated-roles)
Eszközkezelők | Elavult | [Elavult szerepkörök dokumentációja](#deprecated-roles)
Eszköz felhasználói | Elavult | [Elavult szerepkörök dokumentációja](#deprecated-roles)
Címtár-szinkronizálási fiókok | Nem látható, mert nem használható | [A címtár-szinkronizálási fiókok dokumentációja](#directory-synchronization-accounts)
Vendégfelhasználó | Nem látható, mert nem használható | NA
1. partneri szintű támogatás | Nem látható, mert nem használható | [A partneri Tier1 támogatási dokumentációja](#partner-tier1-support)
2. partneri szintű támogatás | Nem látható, mert nem használható | [A partneri szint támogatási dokumentációja](#partner-tier2-support)
Korlátozott vendég felhasználó | Nem látható, mert nem használható | NA
User | Nem látható, mert nem használható | NA
Munkahelyi eszközök csatlakoztatása | Elavult | [Elavult szerepkörök dokumentációja](#deprecated-roles)

## <a name="password-reset-permissions"></a>Jelszó-visszaállítási engedélyek

Az oszlopfejlécek a jelszavak alaphelyzetbe állítására használható szerepköröket jelölik. A tábla sorai tartalmazzák azokat a szerepköröket, amelyek esetében a jelszó alaphelyzetbe állítható.

A jelszó alaphelyzetbe állítható | Jelszó-rendszergazda | Segélyszolgálat rendszergazdája | Hitelesítési rendszergazda | Felhasználói rendszergazda | Privilegizált hitelesítési rendszergazda | Globális rendszergazda
------ | ------ | ------ | ------ | ------ | ------ | ------
Hitelesítési rendszergazda | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Directory-olvasók | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Globális rendszergazda | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Csoportok rendszergazdája | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Vendég meghívója | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Segélyszolgálat rendszergazdája | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Üzenetközpont-olvasó | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Jelszó-rendszergazda | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Privilegizált hitelesítési rendszergazda | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Kiemelt szerepkörű rendszergazda | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Jelentések olvasója | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Felhasználó (nincs rendszergazdai szerepkör) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Felhasználói rendszergazda | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Használati összesítő jelentések olvasója | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* A globális rendszergazda nem tudja eltávolítani a saját globális rendszergazdai hozzárendelését. Ezzel megelőzhető, hogy egy szervezetnek 0 globális rendszergazdája legyen.

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni arról, hogyan rendeljen hozzá egy felhasználót egy Azure-előfizetés rendszergazdájaként, tekintse meg a következőt: [felhasználó kijelölése Azure-előfizetés rendszergazdájának](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
* Ha többet szeretne megtudni a Microsoft Azure erőforrás-hozzáférés szabályozásáról, tekintse meg [a különböző szerepkörök megismerése](../../role-based-access-control/rbac-and-directory-admin-roles.md) című témakört.
* Az előfizetések és az Azure AD-bérlők közötti kapcsolatról, illetve az előfizetés hozzárendelésével vagy hozzáadásával kapcsolatos információkért lásd: [Azure-előfizetés hozzárendelése vagy hozzáadása a Azure Active Directory bérlőhöz](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
