---
title: Bérlő létrehozása a Windows Virtual Desktopban (klasszikus) – Azure
description: Útmutató a Windows rendszerű virtuális asztali (klasszikus) bérlők beállításához a Azure Active Directoryban.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f41584194f8f5e8afde630405116b8b169cb5656
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91542036"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-classic"></a>Oktatóanyag: bérlő létrehozása a Windows Virtual Desktopban (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat.

A bérlők létrehozása a Windows Virtual Desktopban az első lépés az asztali virtualizálási megoldás felépítése felé. A bérlő egy vagy több gazdagép egy csoportja. Mindegyik gazdagép több munkamenet-gazdagépből áll, és virtuális gépekként fut az Azure-ban, és regisztrálva van a Windows rendszerű virtuális asztali szolgáltatásban. Az egyes címkészlet egy vagy több olyan alkalmazáscsoportből is áll, amelyek a távoli asztal és a távoli alkalmazás erőforrásainak közzétételére szolgálnak a felhasználók számára. A Bérlővel létrehozhatók a gazdagépek, létrehozhatók alkalmazások, felhasználók rendelhetők, és a szolgáltatáson keresztül is csatlakozhatnak.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Active Directory engedélyek megadása a Windows rendszerű virtuális asztali szolgáltatásnak.
> * Rendelje hozzá a TenantCreator-alkalmazás szerepkört a Azure Active Directory bérlő egyik felhasználója számára.
> * Hozzon létre egy Windows rendszerű virtuális asztali bérlőt.

## <a name="what-you-need-to-set-up-a-tenant"></a>Mire van szükség a bérlő beállításához

Mielőtt elkezdené a Windows rendszerű virtuális asztali bérlő beállítását, győződjön meg róla, hogy rendelkezik a következőkkel:

* A [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) bérlő azonosítója a Windows rendszerű virtuális asztali felhasználók számára.
* Globális rendszergazdai fiók a Azure Active Directory bérlőn belül.
   * Ez olyan felhőalapú megoldás-szolgáltatói (CSP) szervezetekre is vonatkozik, amelyek egy Windows rendszerű virtuális asztali bérlőt hoznak létre ügyfeleiknek. Ha egy CSP-szervezetben van, akkor az ügyfél Azure Active Directory példányának globális rendszergazdájaként kell tudnia bejelentkeznie.
   * A rendszergazdai fióknak a Azure Active Directory bérlőtől kell származnia, amelyben létre kívánja hozni a Windows rendszerű virtuális asztali bérlőt. Ez a folyamat nem támogatja Azure Active Directory B2B-(vendég-) fiókokat.
   * A rendszergazdai fióknak munkahelyi vagy iskolai fióknak kell lennie.
* Azure-előfizetés.

Ahhoz, hogy az oktatóanyagban ismertetett folyamat megfelelően működjön, rendelkeznie kell a bérlői AZONOSÍTÓval, a globális rendszergazdai fiókkal és az Azure-előfizetéssel.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Engedélyek megadása a Windows rendszerű virtuális asztal számára

Ha már engedélyezte a Windows rendszerű virtuális asztal számára a Azure Active Directory példányra vonatkozó engedélyeket, ugorja át ezt a szakaszt.

A Windows rendszerű virtuális asztali szolgáltatás engedélyeinek megadása lehetővé teszi, hogy lekérdezze Azure Active Directory felügyeleti és végfelhasználói feladatokhoz.

A szolgáltatás engedélyeinek megadása:

1. Nyisson meg egy böngészőt, és kezdje meg a rendszergazdai jogosultságokat a [Windows rendszerű virtuális asztali kiszolgáló alkalmazásban](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Ha felügyel egy ügyfelet, és rendszergazdai jóváhagyást kell adnia az ügyfél címtárához, adja meg a következő URL-címet a böngészőben, és cserélje le a {bérlő} értéket az ügyfél Azure AD-tartománynevére. Ha például az ügyfél szervezete regisztrálta a contoso.onmicrosoft.com Azure AD-tartománynevet, cserélje le a (z) {bérlő} nevet a contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Jelentkezzen be a Windows rendszerű virtuális asztal engedélyezési lapjára egy globális rendszergazdai fiókkal. Ha például a contoso szervezete volt, a fiókja lehet admin@contoso.com vagy admin@contoso.onmicrosoft.com .
3. Válassza ki az **Elfogadás** lehetőséget.
4. Várjon egy percet, hogy az Azure AD jegyezze fel a beleegyező engedélyt.
5. Nyisson meg egy böngészőt, és kezdje meg a rendszergazdai jogosultságokat a [Windows rendszerű virtuális asztali ügyfélalkalmazás](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)számára.
   >[!NOTE]
   > Ha felügyel egy ügyfelet, és rendszergazdai jóváhagyást kell adnia az ügyfél címtárához, adja meg a következő URL-címet a böngészőben, és cserélje le a {bérlő} értéket az ügyfél Azure AD-tartománynevére. Ha például az ügyfél szervezete regisztrálta a contoso.onmicrosoft.com Azure AD-tartománynevet, cserélje le a (z) {bérlő} nevet a contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Jelentkezzen be a Windows rendszerű virtuális asztal engedélyezési lapjára globális rendszergazdaként, ahogy azt a 2. lépésben elvégezte.
7. Válassza ki az **Elfogadás** lehetőséget.

## <a name="assign-the-tenantcreator-application-role"></a>A TenantCreator-alkalmazás szerepkörének kiosztása

Azure Active Directory felhasználó kiosztása a TenantCreator alkalmazási szerepkör lehetővé teszi, hogy a felhasználó létrehoz egy Windows rendszerű virtuális asztali bérlőt, amely társítva van a Azure Active Directory példányhoz. A TenantCreator szerepkör hozzárendeléséhez globális rendszergazdai fiókot kell használnia.

A TenantCreator-alkalmazás szerepkörének hozzárendeléséhez:

1. A TenantCreator-alkalmazás szerepkörének kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . **Vállalati alkalmazások** keresése és kiválasztása. Ha több Azure Active Directory Bérlővel dolgozik, ajánlott megnyitnia egy privát böngésző-munkamenetet, és az URL-címeket a címsorba másolhatja és beillesztheti.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a nagyvállalati alkalmazások kereséséről a Azure Portal](../media/azure-portal-enterprise-applications.png)

2. A **vállalati alkalmazásokban** keresse meg a **Windows rendszerű virtuális asztal** kifejezést. Ekkor megjelenik a két, az előző szakaszban megadott beleegyezett alkalmazás. A két alkalmazás közül válassza a **Windows virtuális asztal** lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a keresési eredményekről, amikor a "Windowsos virtuális asztal" kifejezést keres a "vállalati alkalmazások" kifejezésben. A "Windows virtuális asztal" nevű alkalmazás ki van emelve.](../media/tenant-enterprise-app.png)

3. Válassza a **Felhasználók és csoportok** elemet. Láthatja, hogy az alkalmazáshoz beleegyezett jogosultsággal rendelkező rendszergazda már szerepel a hozzárendelt **alapértelmezett hozzáférési** szerepkörben. Ez nem elég a Windows rendszerű virtuális asztali bérlő létrehozásához. Kövesse az alábbi utasításokat a **TenantCreator** szerepkör felhasználóhoz való hozzáadásához.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a "Windows Virtual Desktop" vállalati alkalmazás kezeléséhez hozzárendelt felhasználókról és csoportokról. A képernyőképen csak egy hozzárendelés látható, amely az "alapértelmezett hozzáférés" értékre mutat.](../media/tenant-default-access.png)

4. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** lapon válassza a **felhasználók és csoportok** lehetőséget.
5. Keressen egy olyan felhasználói fiókot, amely létrehozza a Windows rendszerű virtuális asztali bérlőt. Az egyszerűség kedvéért ez lehet a globális rendszergazdai fiók.
   - Ha a (z) rendszerhez hasonló Microsoft-identitás-szolgáltatót használ contosoadmin@live.com contosoadmin@outlook.com , előfordulhat, hogy nem tud bejelentkezni a Windows virtuális asztalra. Azt javasoljuk, hogy használjon egy, a tartományra vonatkozó fiókot (például admin@contoso.com vagy admin@contoso.onmicrosoft.com inkább).

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a "TenantCreator" felvenni kívánt felhasználó kiválasztásáról.](../media/tenant-assign-user.png)

   > [!NOTE]
   > Ki kell választania egy felhasználót (vagy egy csoportot, amely tartalmazza a felhasználót) Ebből az Azure Active Directory-példányból származik. Nem választhat vendég (B2B) felhasználót vagy egyszerű szolgáltatásnevet.

6. Válassza ki a felhasználói fiókot, kattintson a **kiválasztás** gombra, majd válassza a **hozzárendelés** lehetőséget.
7. A **Windows rendszerű virtuális asztal – felhasználók és csoportok** lapon ellenőrizze, hogy megjelenik-e egy új bejegyzés a **TenantCreator** szerepkörrel, amely ahhoz a felhasználóhoz van rendelve, aki létrehozza a Windows rendszerű virtuális asztali bérlőt.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a "Windows Virtual Desktop" vállalati alkalmazás kezeléséhez hozzárendelt felhasználókról és csoportokról. A képernyőkép most már tartalmaz egy, a "TenantCreator" szerepkörhöz rendelt felhasználó második bejegyzését.](../media/tenant-tenant-creator-added.png)

Mielőtt továbblépne a Windows rendszerű virtuális asztali bérlő létrehozásához, két információra van szüksége:

   - Az Azure Active Directory bérlői AZONOSÍTÓját (vagy a **CÍMTÁR azonosítóját**)
   - Az Azure-előfizetés azonosítója

A Azure Active Directory bérlői AZONOSÍTÓjának (vagy a **könyvtár azonosítójának**) megkeresése:
1. Ugyanebben a [Azure Portal](https://portal.azure.com) -munkamenetben keresse meg és válassza a **Azure Active Directory** lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![A Azure Portal "Azure Active Directory" keresési eredményeinek képernyőképe. A "szolgáltatások" alatt a keresési eredmények ki vannak emelve.](../media/tenant-search-azure-active-directory.png)

2. Görgessen lefelé, amíg meg nem találja a **tulajdonságokat**, majd válassza ki.
3. Keresse meg a **CÍMTÁR azonosítóját**, majd válassza a vágólap ikont. Illessze be egy praktikus helyre, hogy később a **AadTenantId** értékeként is használhassa.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a Azure Active Directory tulajdonságairól. Az egérmutatót a vágólap ikonja fölé helyezi a másoláshoz és beillesztéshez.](../media/tenant-directory-id.png)

Az Azure-előfizetés AZONOSÍTÓjának megkeresése:
1. Ugyanebben a [Azure Portal](https://portal.azure.com) -munkamenetben keresse meg és válassza ki az **előfizetéseket**.

   > [!div class="mx-imgBorder"]
   > ![A Azure Portal "Azure Active Directory" keresési eredményeinek képernyőképe. A "szolgáltatások" keresési eredménye kiemelve.](../media/tenant-search-subscription.png)

2. Válassza ki azt az Azure-előfizetést, amelyet a Windows rendszerű virtuális asztali szolgáltatás értesítéseinek fogadásához használni kíván.
3. Keresse meg az **előfizetés azonosítóját**, majd vigye a kurzort az érték fölé, amíg meg nem jelenik a vágólap ikonja. Válassza a vágólap ikont, és illessze be egy praktikus helyre, hogy később a **AzureSubscriptionId** értékeként is használhassa.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép az Azure-előfizetés tulajdonságairól. Az egérmutatót a vágólap ikon fölé helyezi a másoláshoz és beillesztéshez az "előfizetés azonosítója" elemnél.](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Windows rendszerű virtuális asztali bérlő létrehozása

Most, hogy megadta a Windows rendszerű virtuális asztali szolgáltatásnak a Azure Active Directory lekérdezésére vonatkozó engedélyeit, és hozzárendelte a TenantCreator szerepkört egy felhasználói fiókhoz, létrehozhat egy Windows rendszerű virtuális asztali bérlőt.

Először [töltse le és importálja a Windows rendszerű virtuális asztali modult](/powershell/windows-virtual-desktop/overview/) a PowerShell-munkamenetben való használathoz, ha még nem tette meg.

Jelentkezzen be a Windows rendszerű virtuális asztalra a TenantCreator felhasználói fiók használatával a következő parancsmaggal:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ezután hozzon létre egy új, a Azure Active Directory bérlőhöz társított Windowsos virtuális asztali bérlőt:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Cserélje le a zárójelben lévő értékeket a szervezete és a bérlője szempontjából releváns értékekre. Az új Windowsos virtuális asztali bérlőhöz választott névnek globálisan egyedinek kell lennie. Tegyük fel például, hogy a contoso szervezet Windows rendszerű virtuális asztali TenantCreator van. A futtatni kívánt parancsmag a következőképpen fog kinézni:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Javasoljuk, hogy rendszergazdai hozzáférést rendeljen hozzá egy második felhasználóhoz abban az esetben, ha a fiókja kizárta magát, vagy ha a vakációra van szüksége, és szeretné, hogy valaki a bérlői rendszergazdaként működjön a távollétében. Ha rendszergazdai hozzáférést szeretne hozzárendelni egy második felhasználóhoz, futtassa a következő parancsmagot a `<TenantName>` és a `<Upn>` helyére a bérlő nevét és a második felhasználó egyszerű felhasználónevét.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Következő lépések
A bérlő létrehozása után létre kell hoznia egy egyszerű szolgáltatásnevet a Azure Active Directoryban, és hozzá kell rendelnie egy szerepkört a Windows rendszerű virtuális asztalon belül. Az egyszerű szolgáltatásnév lehetővé teszi a Windows rendszerű virtuális asztali Azure Marketplace-ajánlat sikeres üzembe helyezését egy gazda készlet létrehozásához. Ha többet szeretne megtudni a gazdagép-készletekről, folytassa a gazdagépek Windows rendszerű virtuális asztali gépen való létrehozásának oktatóanyagával.

> [!div class="nextstepaction"]
> [Szolgáltatásnevek létrehozása és szerepkörök hozzárendelése a PowerShell-lel](create-service-principal-role-powershell.md)
