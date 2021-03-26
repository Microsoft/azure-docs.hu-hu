---
title: Felhőalapú alkalmazások vagy műveletek a feltételes hozzáférési házirendben – Azure Active Directory
description: Mi a felhőalapú alkalmazások vagy műveletek egy Azure AD feltételes hozzáférési szabályzatban
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c8024a2083d09fcbd53a37f0d391c4589748eea
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605076"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Feltételes hozzáférés: felhőalapú alkalmazások vagy műveletek

A felhőalapú alkalmazások vagy műveletek a feltételes hozzáférési szabályzatok kulcsfontosságú jelei. A feltételes hozzáférési házirendek lehetővé teszik a rendszergazdák számára, hogy bizonyos alkalmazásokhoz vagy műveletekhez rendeljenek vezérlőket.

- A rendszergazdák választhatnak a beépített Microsoft-alkalmazásokat és bármely [Azure ad integrált alkalmazást](../manage-apps/what-is-application-management.md) , beleértve a katalógust, a nem katalógust és az [Application proxyn](../manage-apps/what-is-application-proxy.md)keresztül közzétett alkalmazásokat is.
- A rendszergazdák dönthetnek úgy, hogy nem felhőalapú alkalmazáson, hanem felhasználói műveleten alapuló szabályzatot határoznak meg. Az egyetlen támogatott művelet a biztonsági adatok (előzetes verzió) regisztrálása, amely lehetővé teszi, hogy a feltételes hozzáférés a [kombinált biztonsági információk regisztrációs felülete](../authentication/howto-registration-mfa-sspr-combined.md)körüli ellenőrzéseket kényszerítse ki.

![Feltételes hozzáférési szabályzat definiálása és felhőalapú alkalmazások megadása](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft Cloud-alkalmazások

Számos meglévő Microsoft Cloud-alkalmazás szerepel azon alkalmazások listáján, amelyek közül választhat. 

A rendszergazdák feltételes hozzáférési szabályzatot rendelhetnek a Microsoft következő felhőalapú alkalmazásaihoz. Egyes alkalmazások, például az Office 365 és a Microsoft Azure Management több kapcsolódó alárendelt alkalmazást vagy szolgáltatást tartalmaznak. Folyamatosan bővítünk további alkalmazásokat, így az alábbi lista nem teljes, és változhat.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database és az Azure szinapszis Analytics](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights Analitika
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure-kezelés](#microsoft-azure-management)
- Előfizetés-kezelés Microsoft Azure
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control portál
- Microsoft Commerce Tools hitelesítési szolgáltatás
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune regisztráció](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Search a Bingben
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office-Sway
- Outlook Groups
- Power BI szolgáltatás
- Project Online
- Skype Vállalati online verzió
- Virtuális magánhálózat (VPN)
- Windows Defender ATP

A feltételes hozzáféréshez elérhető alkalmazások a bevezetési és érvényesítési folyamaton keresztül mentek keresztül. Ez nem tartalmazza az összes Microsoft-alkalmazást, mivel a háttér-szolgáltatások közül sokan nem azt jelentik, hogy a szabályzatot közvetlenül alkalmazták. Ha egy hiányzó alkalmazást keres, vegye fel a kapcsolatot az adott alkalmazás csapatával, vagy hozzon igénybe egy kérelmet a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=167259).

### <a name="office-365"></a>Office 365

A Microsoft 365 felhőalapú hatékonyságnövelő és együttműködési szolgáltatásokat nyújt, mint például az Exchange, a SharePoint és a Microsoft teams. A Microsoft 365 Cloud Services szorosan integrálva van a zökkenőmentes és együttműködő élmény biztosítása érdekében. Ez az integráció zavart okozhat a házirendek létrehozásakor, mivel egyes alkalmazások, például a Microsoft-csapatok függőségekkel rendelkeznek másokkal, például a SharePoint vagy az Exchange használatával.

Az Office 365-alkalmazás lehetővé teszi, hogy egyszerre lehessen megcélozni ezeket a szolgáltatásokat. Javasoljuk, hogy az új Office 365-alkalmazást az egyes felhőalapú alkalmazások célzása helyett a [szolgáltatás függőségeivel](service-dependencies.md)kapcsolatos problémák elkerülésére használja. Az alkalmazások csoportjának megcélzása segít elkerülni a nem konzisztens házirendek és függőségek miatt felmerülő problémákat.

A rendszergazdák dönthetnek úgy, hogy kizárják az adott alkalmazásokat a szabályzatból, ha az Office 365 alkalmazást kívánják kizárni, és kizárják a házirendben általuk választott konkrét alkalmazásokat.

Az Office 365-ügyfélalkalmazás részét képező legfontosabb alkalmazások:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Microsoft 365 Search Service
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype Vállalati online verzió
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure-kezelés

A Microsoft Azure felügyeleti alkalmazás több mögöttes szolgáltatást is tartalmaz. 

   - Azure Portal
   - Azure Resource Manager szolgáltató
   - Klasszikus üzembe helyezési modell API-k
   - Azure PowerShell
   - Azure CLI
   - Visual Studio-előfizetések felügyeleti portál
   - Azure DevOps
   - Azure Data Factory portál

> [!NOTE]
> A Microsoft Azure felügyeleti alkalmazás a Azure PowerShellra vonatkozik, amely meghívja a Azure Resource Manager API-t. Ez nem vonatkozik az Azure AD PowerShellre, amely meghívja a Microsoft Graph.

## <a name="other-applications"></a>Egyéb alkalmazások

A Microsoft-alkalmazások mellett a rendszergazdák bármilyen Azure AD-beli regisztrált alkalmazást hozzáadhatnak a feltételes hozzáférési házirendekhez. Ezek az alkalmazások a következőket tartalmazhatják: 

- Az [Azure ad Application proxy](../manage-apps/what-is-application-proxy.md) használatával közzétett alkalmazások
- [A gyűjteményből hozzáadott alkalmazások](../manage-apps/add-application-portal.md)
- [A katalógusban nem szereplő egyéni alkalmazások](../manage-apps/view-applications-portal.md)
- [Az App Delivery Controllers és Networks szolgáltatáson keresztül közzétett örökölt alkalmazások](../manage-apps/secure-hybrid-access.md)
- [Jelszó alapú egyszeri bejelentkezést](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md) használó alkalmazások

> [!NOTE]
> Mivel a feltételes hozzáférési házirend olyan szolgáltatáshoz való hozzáférés követelményeit állítja be, amely nem alkalmazható az ügyfél (nyilvános/natív) alkalmazásra. Más szóval a házirend nincs beállítva közvetlenül egy ügyfélen (nyilvános/natív) alkalmazáson, de a rendszer akkor alkalmazza, amikor egy ügyfél meghívja a szolgáltatást. A SharePoint szolgáltatásban beállított házirendek például a SharePoint szolgáltatást hívó ügyfelekre érvényesek. Az Exchange-ben beállított házirend az e-mailek Outlook-ügyfél használatával való elérésére tett kísérletre vonatkozik. Ezért nem érhetők el az ügyfél (nyilvános/natív) alkalmazások a Cloud apps választóban való kiválasztáshoz, és a feltételes hozzáférés beállítás nem érhető el a bérlőben regisztrált ügyfél (nyilvános/natív) alkalmazás Alkalmazásbeállítások esetében. 

## <a name="user-actions"></a>Felhasználói műveletek

A felhasználói műveletek olyan feladatok, amelyeket a felhasználók elvégezhetnek. A feltételes hozzáférés jelenleg két felhasználói műveletet támogat: 

- **Biztonsági információk regisztrálása**: Ez a felhasználói művelet lehetővé teszi, hogy a feltételes hozzáférési szabályzat kényszerítse ki, hogy a rendszer mikor engedélyezze a kombinált regisztrációt a biztonsági adatok regisztrálására. További információt a következő cikkben talál: [kombinált biztonsági információk regisztrálása](../authentication/concept-registration-mfa-sspr-combined.md).

- **Eszközök regisztrálása vagy csatlakoztatása (előzetes verzió)**: Ez a felhasználói művelet lehetővé teszi a rendszergazdák számára a feltételes hozzáférési szabályzat kikötését, amikor a felhasználók [regisztrálják](../devices/concept-azure-ad-register.md) vagy [csatlakoztatják](../devices/concept-azure-ad-join.md) az eszközöket az Azure ad Ennek a felhasználói műveletnek két fő szempontja van: 
   - `Require multi-factor authentication` az egyetlen elérhető hozzáférés-vezérlés ezzel a felhasználói művelettel, és minden más le van tiltva. Ez a korlátozás megakadályozza az olyan hozzáférés-vezérlési ütközéseket, amelyek az Azure AD-eszközök regisztrációja vagy nem alkalmazhatók az Azure AD-eszközök regisztrálására. 
   - Ha a feltételes hozzáférési szabályzat engedélyezve van ezzel a felhasználói művelettel, a nem értékre kell állítania a **Azure Active Directory**  >  **eszköz**  >  **beállításait**  -  `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` .  Ellenkező esetben az ezzel a felhasználói művelettel rendelkező feltételes hozzáférési szabályzatok nem megfelelően lettek kikényszerítve. Az eszköz beállításával kapcsolatos további információkért tekintse meg az [eszközbeállítások konfigurálása](../devices/device-management-azure-portal.md#configure-device-settings)című részt. Ez a felhasználói művelet rugalmasságot biztosít a többtényezős hitelesítés megköveteléséhez egy adott felhasználóhoz és csoporthoz vagy feltételhez tartozó eszközök regisztrálásához vagy csatlakoztatásához ahelyett, hogy a teljes bérlőre kiterjedő szabályzatot kellene használnia az eszközbeállítások során. 
   
## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférés: feltételek](concept-conditional-access-conditions.md)

- [Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)
- [Ügyfélalkalmazás függőségei](service-dependencies.md)
