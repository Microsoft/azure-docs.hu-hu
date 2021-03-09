---
title: Szerepkörök kiosztása az Azure Nagyvállalati Szerződés egyszerű szolgáltatásnév neveként
description: Ez a cikk segítséget nyújt a szerepkörök egyszerű nevekhez való hozzárendeléséhez a PowerShell és a REST API-k használatával.
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/07/2021
ms.author: banders
ms.openlocfilehash: e7f5370e1e387947d196959fef31043ea8f4d3bd
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508520"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Szerepkörök kiosztása az Azure Nagyvállalati Szerződés egyszerű szolgáltatásnév neveként

A Nagyvállalati Szerződés (EA) regisztrációját az [Azure Enterprise Portalon](https://ea.azure.com/)kezelheti. Létrehozhat különböző szerepköröket a szervezet felügyeletéhez, a költségek megtekintéséhez és előfizetések létrehozásához. Ebből a cikkből megtudhatja, hogyan automatizálhat néhány feladatot a Azure PowerShell és REST API-k használatával az Azure egyszerű neveivel (SPN).

Mielőtt elkezdené, győződjön meg arról, hogy már ismeri a következő cikkeket:

- [Nagyvállalati szerződés szerepkörei](understand-ea-roles.md)
- [Bejelentkezés az Azure PowerShell-lel](/powershell/azure/authenticate-azureps)
- [REST API-k hívása a Poster használatával](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>Az egyszerű szolgáltatás létrehozása és hitelesítése

Ha egyszerű szolgáltatásnév használatával szeretné automatizálni az EA-műveleteket, létre kell hoznia egy Azure Active Directory (Azure AD) alkalmazást. A hitelesítés automatizált módon végezhető el. Olvassa el a következő cikkeket, és végezze el az egyszerű szolgáltatásnév létrehozásához és hitelesítéséhez szükséges lépéseket.

1. [Egyszerű szolgáltatás létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Bérlői és alkalmazás-azonosító értékek beolvasása a bejelentkezéshez](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Íme egy példa az alkalmazás regisztrálását bemutató képernyőképre.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Az alkalmazás regisztrálását bemutató képernyőkép." lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>Az SPN és a bérlő AZONOSÍTÓjának megkeresése

Szüksége lesz az egyszerű szolgáltatásnév és az alkalmazás bérlői AZONOSÍTÓJÁRA is. Az engedély-hozzárendelési műveletekhez szükséges információk a későbbi részekben találhatók.

Az Azure AD alkalmazás bérlői AZONOSÍTÓját az alkalmazás Áttekintés oldalán találja. Ha meg szeretné keresni a Azure Portalban, keresse meg Azure Active Directory és válassza a **vállalati alkalmazások** lehetőséget. Keresse meg az alkalmazást.

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Egy példa vállalati alkalmazást bemutató képernyőkép." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

Válassza ki az alkalmazást. Az alábbi példa az alkalmazás AZONOSÍTÓját és az objektum AZONOSÍTÓját mutatja be.

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="A vállalati alkalmazások alkalmazás-AZONOSÍTÓját és objektumazonosítót ábrázoló képernyőkép." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

A bérlői azonosítót a Microsoft Azure AD Áttekintés lapon találja.

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="A bérlői azonosító megtekintésének helyét bemutató képernyőkép." lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

A résztvevő bérlői AZONOSÍTÓját a rendszer a fő azonosító, SPN és objektumazonosító néven is említi különböző helyszíneken. Az Azure AD-bérlő AZONOSÍTÓjának értéke a következő formátumú GUID azonosítóhoz hasonlít: `11111111-1111-1111-1111-111111111111` .

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>Az egyszerű szolgáltatásnév számára kiosztható engedélyek

A következő lépésekhez engedélyt ad az Azure AD-alkalmazásnak az EA szerepkört használó műveletek végrehajtásához. Csak a következő szerepköröket rendelheti hozzá az SPN-hez. A szerepkör-definíció AZONOSÍTÓját pontosan az ábrán látható módon kell használni a hozzárendelés lépéseiben.

| Szerepkör | Engedélyezett műveletek | Szerepkör-definíció azonosítója |
| --- | --- | --- |
| EnrollmentReader | Megtekintheti a használatot és a díjakat az összes fiók és előfizetés között. Megtekintheti a beléptetéshez kapcsolódó Azure-előfizetést (korábban pénzügyi kötelezettségvállalásnak nevezett) egyenleget. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| DepartmentReader | Töltse le az általuk felügyelt részleg használati adatait. Megtekintheti a részleghez kapcsolódó használati és használati díjakat. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Új előfizetések létrehozása a fiók adott hatókörében. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- A beléptetési olvasó csak a regisztrációs író szerepkörrel rendelkező felhasználó számára rendelhető hozzá egy egyszerű szolgáltatásnév számára.
- A részleg-olvasó csak olyan felhasználó számára rendelhető hozzá egy egyszerű szolgáltatásnév számára, amely regisztrációs író szerepkörrel vagy részleg-író szerepkörrel rendelkezik.
- Egy előfizetés-létrehozó szerepkört csak olyan felhasználó rendelhet hozzá egy SPN-hez, amely a regisztrációs fiók fiókjának tulajdonosa.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>Beléptetési fiók szerepkör-engedélyének kiosztása az egyszerű szolgáltatásnév számára

A [szerepkör-hozzárendelések – Put](/rest/api/billing/2019-10-01-preview/roleassignments/put) REST API cikk olvasása.

A cikk elolvasása közben válassza a **kipróbálás** lehetőséget az SPN használatának megkezdéséhez.

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Képernyőkép a Put (kipróbálás) lehetőségről a Put cikkben." lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

Jelentkezzen be a fiókjával a bérlőbe, amely hozzáféréssel rendelkezik ahhoz a regisztrációhoz, amelyhez hozzáférést szeretne rendelni.

Adja meg a következő paramétereket az API-kérelem részeként.

**billingAccountName**

A paraméter a Számlázási fiók azonosítója. A Azure Portal a Cost Management + számlázási áttekintés oldalon találhatja meg.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="A Számlázási fiók AZONOSÍTÓját ábrázoló képernyőfelvétel." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

A paraméter egy egyedi GUID, amelyet meg kell adnia. A GUID azonosítót a [New-GUID PowerShell-](/powershell/module/microsoft.powershell.utility/new-guid) paranccsal hozhatja meg.

Vagy használhatja az [online GUID/UUID Generator](https://guidgenerator.com/) webhelyét is egyedi GUID létrehozásához.

**API-Version**

Használja a **2019-10-01-Preview** verziót.

A kérelem törzse JSON-kóddal rendelkezik, amelyet használni kell.

Használja a minta kérelem törzsét a [szerepkör-hozzárendelések – Put-példákban](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples).

A JSON részeként három paramétert kell használnia.

| Paraméter | Hol található |
| --- | --- |
| Properties. principalId | Lásd: [az SPN és a bérlő azonosítójának megkeresése](#find-your-spn-and-tenant-id). |
| Properties. principalTenantId | Lásd: [az SPN és a bérlő azonosítójának megkeresése](#find-your-spn-and-tenant-id). |
| Properties. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e" |

A Számlázási fiók neve megegyezik az API-paraméterekben használt paraméterrel. Ez a regisztrációs azonosító, amelyet a nagyvállalati portálon láthat, és Azure Portal.

Figyelje meg, hogy egy `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` EnrollmentReader tartozó számlázási szerepkör definíciójának azonosítója.

A parancs elindításához válassza a **Futtatás** lehetőséget.

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="A példaként szolgáló szerepkör-hozzárendelést bemutató képernyőképen próbálja ki a futtatásra kész példákat." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

A `200 OK` Válasz azt jelzi, hogy az egyszerű szolgáltatásnév hozzáadása sikeres volt.

Most már használhatja az SPN-t (Azure AD alkalmazás az objektumazonosító használatával) az EA API-k automatizált módon való eléréséhez. Az SPN rendelkezik a EnrollmentReader szerepkörrel.

## <a name="assign-the-department-reader-role-to-the-spn"></a>Az osztály-olvasó szerepkör társítása az SPN-hez

Mielőtt elkezdené, olvassa el a [regisztrációs részleg szerepkör-hozzárendelések-Put](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put) REST API cikket.

A cikk elolvasása közben válassza a **kipróbálás** lehetőséget.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Képernyőfelvétel: a beléptetési részleg szerepkör-hozzárendeléseire vonatkozó cikk kipróbálása lehetőségének megjelenítése." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Jelentkezzen be a fiókjával a bérlőbe, amely hozzáféréssel rendelkezik ahhoz a regisztrációhoz, amelyhez hozzáférést szeretne rendelni.

Adja meg a következő paramétereket az API-kérelem részeként.

**billingAccountName**

Ez a Számlázási fiók azonosítója. A Azure Portal a Cost Management + számlázási áttekintés oldalon találhatja meg.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="A Számlázási fiók AZONOSÍTÓját ábrázoló képernyőfelvétel." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

A paraméter egy egyedi GUID, amelyet meg kell adnia. A GUID azonosítót a [New-GUID PowerShell-](/powershell/module/microsoft.powershell.utility/new-guid) paranccsal hozhatja meg.

Vagy használhatja az [online GUID/UUID Generator](https://guidgenerator.com/) webhelyét is egyedi GUID létrehozásához.

**departmentName**

Ez a részleg azonosítója. A Azure Portalban láthatja a részleg azonosítóit. Navigáljon a Cost Management + számlázási > **részlegek** elemre.

Ebben a példában az ACE részleget használtuk. A példához tartozó azonosító a következő: `84819` .

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Képernyőkép egy példa-részleg AZONOSÍTÓjának megjelenítéséről." lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**API-Version**

Használja a **2019-10-01-Preview** verziót.

A kérelem törzse JSON-kóddal rendelkezik, amelyet használni kell.

Használja a mintát a [beléptetési részleg szerepkör-hozzárendelései – Put](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put). A JSON részeként három paramétert kell használnia.

| Paraméter | Hol található |
| --- | --- |
| Properties. principalId | Lásd: [az SPN és a bérlő azonosítójának megkeresése](#find-your-spn-and-tenant-id). |
| Properties. principalTenantId | Lásd: [az SPN és a bérlő azonosítójának megkeresése](#find-your-spn-and-tenant-id). |
| Properties. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a" |

A Számlázási fiók neve megegyezik az API-paraméterekben használt paraméterrel. Ez a regisztrációs azonosító, amelyet a nagyvállalati portálon láthat, és Azure Portal.

A számlázási szerepkör definíciójának azonosítója a `db609904-a47f-4794-9be8-9bd86fbffd8a` részleg olvasója.

A parancs elindításához válassza a **Futtatás** lehetőséget.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Képernyőkép egy példa regisztrációs részleg szerepkör-hozzárendelésekről – a REST kipróbálása a futtatásra kész példákkal." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

A `200 OK` Válasz azt jelzi, hogy az egyszerű szolgáltatásnév hozzáadása sikeres volt.

Most már használhatja az SPN-t (Azure AD alkalmazás az objektumazonosító használatával) az EA API-k automatizált módon való eléréséhez. Az SPN rendelkezik a DepartmentReader szerepkörrel.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>Az előfizetés létrehozói szerepkörének kiosztása az SPN-hez

Olvassa el a [beléptetési fiók szerepkör-hozzárendelések – Put](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) cikket.

Olvasása közben válassza a **kipróbálás** lehetőséget az előfizetés létrehozói SZEREPKÖR az SPN-hez való hozzárendeléséhez.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Képernyőfelvétel: a beléptetési fiók szerepkör-hozzárendelések Put cikkének kipróbálása lehetőség." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Jelentkezzen be a fiókjával a bérlőbe, amely hozzáféréssel rendelkezik ahhoz a regisztrációhoz, amelyhez hozzáférést szeretne rendelni.

Adja meg a következő paramétereket az API-kérelem részeként. Olvassa el a [beléptetési fiók szerepkör-hozzárendelések – Put-URI paraméterek](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters)című cikket.

**billingAccountName**

A paraméter a Számlázási fiók azonosítója. A Azure Portal a Cost Management + számlázási áttekintés oldalon találhatja meg.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="A Számlázási fiók AZONOSÍTÓját ábrázoló képernyőfelvétel." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

A paraméter egy egyedi GUID, amelyet meg kell adnia. A GUID azonosítót a [New-GUID PowerShell-](/powershell/module/microsoft.powershell.utility/new-guid) paranccsal hozhatja meg.

Vagy használhatja az [online GUID/UUID Generator](https://guidgenerator.com/) webhelyét is egyedi GUID létrehozásához.
**enrollmentAccountName**

A paraméter a fiók azonosítója. Keresse meg a fiók nevét a Azure Portal Cost Management + számlázás a beléptetési és részlegi hatókörben.

Ebben a példában a GTM-teszt fiókot használtuk. Az azonosító: `196987` .

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="A fiók AZONOSÍTÓját ábrázoló képernyőfelvétel." lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**API-Version**

Használja a **2019-10-01-Preview** verziót.

A kérelem törzse JSON-kóddal rendelkezik, amelyet használni kell.

Használja a mintát a [beléptetési részleg szerepkör-hozzárendelései – Put-examples](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment).

A JSON részeként három paramétert kell használnia.

| Paraméter | Hol található |
| --- | --- |
| Properties. principalId | Lásd: [az SPN és a bérlő azonosítójának megkeresése](#find-your-spn-and-tenant-id). |
| Properties. principalTenantId | Lásd: [az SPN és a bérlő azonosítójának megkeresése](#find-your-spn-and-tenant-id). |
| Properties. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71" |

A Számlázási fiók neve megegyezik az API-paraméterekben használt paraméterrel. Ez a regisztrációs azonosító, amelyet a nagyvállalati portálon láthat, és Azure Portal.

Az `a0bcee42-bf30-4d1b-926a-48d21664ef71` előfizetés-létrehozó szerepkörhöz tartozó számlázási szerepkör definíciójának azonosítója.

A parancs elindításához válassza a **Futtatás** lehetőséget.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Képernyőfelvétel a beléptetési fiók szerepkör-hozzárendelései – Put cikk" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

A `200 OK` Válasz azt jelzi, hogy az egyszerű szolgáltatásnév hozzáadása sikeresen megtörtént.

Most már használhatja az SPN-t (Azure AD alkalmazás az objektumazonosító használatával) az EA API-k automatizált módon való eléréséhez. Az SPN rendelkezik a SubscriptionCreator szerepkörrel.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure EA Portal felügyeletéről](ea-portal-administration.md).