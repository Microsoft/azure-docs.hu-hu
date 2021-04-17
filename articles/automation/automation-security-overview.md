---
title: Azure Automation fiókhitelesítés áttekintése
description: Ez a cikk áttekintést nyújt a Azure Automation hitelesítésről.
keywords: automation-biztonság, automation biztonságossá tétele; automation-hitelesítés
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 09aab71513b1152924de4eae91a718bad23d1012
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518001"
---
# <a name="azure-automation-account-authentication-overview"></a>Azure Automation fiókhitelesítés áttekintése

Az Azure Automation lehetővé teszi a feladatok automatizálását az Azure erőforrásain, továbbá olyan felhőszolgáltatókkal, mint az Amazon webszolgáltatások (AWS). Runbookok használatával automatizálhatja a feladatokat, vagy egy hibrid runbook-feldolgozót, ha üzleti vagy üzemeltetési folyamatokkal dolgozik az Azure-ban kívül. Ezen környezetek bármelyikében való munkavégzéshez engedélyre van szükség az erőforrások biztonságos eléréséhez a minimálisan szükséges jogosultságokkal.

Ez a cikk a Azure Automation által támogatott hitelesítési forgatókönyveket mutatja be, és bemutatja, hogyan kell az első lépésekhez a felügyelni szükséges környezet vagy környezetek alapján.

## <a name="automation-account"></a>Automation-fiók

Amikor először indítja el az Azure Automationt, legalább egy Automation-fiókot létre kell hoznia. Az Automation-fiókok lehetővé teszik az Automation-erőforrások, runbookok, eszközök és konfigurációk elkülönítését a többi fiók erőforrásaitól. Az Automation-fiókokkal az erőforrásokat különálló logikai környezetekbe vagy delegált felelősségekbe használhatja. Használhat például egy fiókot fejlesztéshez, egy másikat az üzemi használatra, egy harmadikat pedig a helyszíni környezethez. De dedikálhat egy Automation-fiókot is az operációs rendszer frissítésének kezeléséhez az összes gépen a [Update Management.](update-management/overview.md) 

Az Azure Automation-fiók különbözik a Microsoft-fiókjától vagy az Azure-előfizetésében létrehozott fiókoktól. Az Automation-fiókok létrehozásának bemutatásáért lásd: [Automation-fiók létrehozása.](automation-quickstart-create-account.md)

## <a name="automation-resources"></a>Automation-erőforrások

Az egyes Automation-fiókok Automation-erőforrásai egyetlen Azure-régióhoz vannak társítva, de a fiók az Azure-előfizetés összes erőforrását kezelheti. Az Automation-fiókok különböző régiókban való létrehozásának fő oka az, ha olyan szabályzatokkal rendelkezik, amelyek megkövetelik, hogy az adatok és az erőforrások egy adott régióba különítve legyen.

Az Azure Resource Manager és az Azure Automation PowerShell-parancsmagok használatával erőforrásokon létrehozott összes feladatot hitelesíteni kell az Azure-ban Azure Active Directory (Azure AD) szervezeti identitás hitelesítőadat-alapú hitelesítésének használatával.

## <a name="managed-identities-preview"></a>Felügyelt identitások (előzetes verzió)

A felügyelt identitás Azure Active Directory (Azure AD) lehetővé teszi, hogy a runbook könnyedén hozzáférjen más Azure AD-védelem alatt álló erőforrásokhoz. Az identitást az Azure platform kezeli, és nem követeli meg a titkos kulcsok üzembe vagy váltogatása nélkül. További információ az Azure AD-beli felügyelt identitásokkal kapcsolatban: [Azure-erőforrások felügyelt identitása.](/azure/active-directory/managed-identities-azure-resources/overview)

A felügyelt identitások használatának néhány előnye:

- Felügyelt identitások használatával bármely Olyan Azure-szolgáltatásban hitelesíthet, amely támogatja az Azure AD-hitelesítést. Felhőbeli és hibrid feladatokhoz is használhatók. A hibrid feladatok felügyelt identitásokat használhatnak, ha azure-beli vagy nem Azure-beli virtuális gépen futó hibrid runbook-feldolgozón futnak.

- A felügyelt identitások további költségek nélkül használhatók.

- Nem kell megújítani az Automation-run As-fiók által használt tanúsítványt.

- A Runbook kódban nem kell megadnia a runbook kapcsolati objektumát. Az Erőforrásokhoz az Automation-fiók felügyelt identitásával férhet hozzá egy runbookból anélkül, hogy tanúsítványokat, kapcsolatokat, run as-fiókokat stb. hoz létre.

Az Automation-fiókok kétféle identitást kaphatnak:

- A rendszer által hozzárendelt identitás az alkalmazáshoz van kötve, és az alkalmazás törlésekor törlődik. Egy alkalmazásnak csak egy, a rendszer által hozzárendelt identitása lehet.

- A felhasználó által hozzárendelt identitás egy önálló Azure-erőforrás, amely hozzárendelhető az alkalmazáshoz. Egy alkalmazás több felhasználó által hozzárendelt identitással is lehet.

>[!NOTE]
> A felhasználóhoz rendelt identitások még nem támogatottak.

A felügyelt identitások használatával kapcsolatos részletekért lásd: Felügyelt identitás engedélyezése Azure Automation [(előzetes verzió)](enable-managed-identity-for-automation.md).

## <a name="run-as-accounts"></a>Futtató fiókok

A szolgáltatásban a Azure Automation biztosítják a hitelesítést Azure Resource Manager klasszikus üzembe helyezési modellben üzembe helyezett erőforrások és erőforrások kezeléséhez. A fiókban kétféle Azure Automation:

* Azure-beli futtatás fiókja: Lehetővé teszi az Azure-erőforrások kezelését az Azure Azure Resource Manager üzembe helyezési és felügyeleti szolgáltatása alapján.
* Klasszikus Azure-beli futó fiók: Lehetővé teszi a klasszikus Azure-erőforrások kezelését a klasszikus üzembe helyezési modell alapján.

Az üzembe helyezési modellel és a Azure Resource Manager modellel kapcsolatos további információkért lásd: Resource Manager és a klasszikus üzembe [helyezés.](../azure-resource-manager/management/deployment-models.md)

>[!NOTE]
>Azure-felhőszolgáltató (CSP)-előfizetések csak a Azure Resource Manager támogatják. A nem Azure Resource Manager szolgáltatások nem érhetők el a programban. CSP-előfizetés használata esetén a klasszikus Azure-beli futtatás fiókja nem jön létre, hanem az Azure-beli futtatás fiókja. A CSP-előfizetésekkel kapcsolatos további információkért lásd: [Elérhető szolgáltatások CSP-előfizetések esetén.](/azure/cloud-solution-provider/overview/azure-csp-available-services)

Automation-fiók létrehozásakor a rendszer alapértelmezés szerint egyidejűleg hozza létre a fiókot. Ha úgy döntött, hogy nem hozza létre az Automation-fiókkal együtt, később külön is létrehozható. A klasszikus Azure-beli futtatás fiókja nem kötelező, és külön jön létre, ha klasszikus erőforrásokat kell kezelnie.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>Futtató fiók

A létrehozott futó fiók a következő feladatokat végzi el:

* Létrehoz egy Önaírt tanúsítvánnyal bíró Azure AD-alkalmazást, létrehoz egy egyszerű szolgáltatásfiókot az alkalmazáshoz az Azure AD-ban, és hozzárendeli a Közreműködő szerepkört az aktuális előfizetésében lévő fiókhoz. [](../role-based-access-control/built-in-roles.md#contributor) A tanúsítványbeállítást módosíthatja Olvasó [vagy](../role-based-access-control/built-in-roles.md#reader) bármely más szerepkör beállításra. További információk: [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).

* Létrehoz egy nevű Automation-tanúsítványeszközt `AzureRunAsCertificate` a megadott Automation-fiókban. A tanúsítványeszköz tartalmazza az Azure AD-alkalmazás által használt titkos tanúsítványkulcsot.

* Létrehoz egy nevű Automation-kapcsolati eszközt `AzureRunAsConnection` a megadott Automation-fiókban. A kapcsolati eszköz tartalmazza az alkalmazásazonosítót, a bérlőazonosítót, az előfizetés azonosítóját és a tanúsítvány ujjlenyomatát.

### <a name="azure-classic-run-as-account"></a>Klasszikus Azure-beli futtatás fiókja

Klasszikus Azure-beli futó fiók létrehozásakor a következő feladatokat végzi el:

> [!NOTE]
> Az ilyen típusú run as-fiók létrehozásához vagy megújításához az előfizetés társad rendszergazdájának kell lennie.

* Felügyeleti tanúsítványt hoz létre az előfizetésben.

* Létrehoz egy nevű Automation-tanúsítványeszközt `AzureClassicRunAsCertificate` a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza a felügyeleti tanúsítvány által használt titkos tanúsítványkulcsot.

* Létrehoz egy nevű Automation-kapcsolati `AzureClassicRunAsConnection` adateszközt a megadott Automation-fiókban. A kapcsolati eszköz tartalmazza az előfizetés nevét, az előfizetés azonosítóját és a tanúsítványeszköz nevét.

## <a name="service-principal-for-run-as-account"></a>Szolgáltatásnév a futtatott fiókhoz

A futtatott fiók egyszerű szolgáltatása alapértelmezés szerint nem rendelkezik az Azure AD olvasására vonatkozó engedélyekkel. Ha az Azure AD olvasására vagy kezelésére vonatkozó engedélyeket szeretne hozzáadni, az API-engedélyek alatt meg kell adni az engedélyeket a **szolgáltatásnévhez.** További tudnivalókért lásd: Engedélyek hozzáadása a [webes API eléréséhez.](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)

## <a name="run-as-account-permissions"></a><a name="permissions"></a>A futtatás fiókengedélyei

Ez a szakasz a normál és a klasszikus futtatású fiókokhoz is meghatározza az engedélyeket.

* A run as fiók létrehozásához vagy frissítéséhez a alkalmazás-rendszergazda egy Azure Active Directory és egy tulajdonos az előfizetésben az összes feladatot el tudja elvégezni.
* A klasszikus futtatású fiókok konfigurálásához vagy megújításához társadi rendszergazdai szerepkörre van szükség az előfizetés szintjén. A klasszikus előfizetési engedélyekkel kapcsolatos további információkért lásd: [Klasszikus Azure-előfizetés-adminisztrátorok.](../role-based-access-control/classic-administrators.md#add-a-co-administrator)

Abban az esetben, ha a feladatok el vannak különvetve, az alábbi táblázatban a feladatok, az egyenértékű parancsmagok és a szükséges engedélyek listája látható:

|Feladat|Parancsmag  |Minimális engedélyek  |Itt állíthatja be az engedélyeket|
|---|---------|---------|---|
|Azure AD-alkalmazás létrehozása|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | <sup>1. alkalmazásfejlesztői szerepkör</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Az Azure AD > és > kezdőlapja |
|Adjon hozzá egy hitelesítő adatokat az alkalmazáshoz.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Az Azure AD > és > kezdőlapja|
|Azure AD-szolgáltatásnév létrehozása és lekért létrehozása|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Az Azure AD > és > kezdőlapja|
|Azure-szerepkör hozzárendelése vagy lekérte a megadott rendszerbiztonsági taghoz|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Felhasználói hozzáférés rendszergazdája vagy tulajdonosa, vagy a következő engedélyekkel rendelkezik:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Előfizetés](../role-based-access-control/role-assignments-portal.md)</br>Otthoni > előfizetések > \<subscription name\> – Access Control (IAM)|
|Automation-tanúsítvány létrehozása vagy eltávolítása|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Közreműködő az erőforráscsoportban         |Automation-fiók erőforráscsoportja|
|Automation-kapcsolat létrehozása vagy eltávolítása|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Közreműködő az erőforráscsoportban |Automation-fiók erőforráscsoportja|

<sup>1</sup> Az Azure AD-bérlő nem rendszergazda felhasználói regisztrálhat [AD-alkalmazásokat,](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) ha az  Azure AD-bérlő Felhasználók **regisztrálhat** alkalmazásokat beállításának Beállítása **Igen.** Ha az alkalmazásregisztráció beállítása **Nem,** akkor a műveletet végző felhasználónak a táblázatban meghatározottak szerint kell lennie.

Ha nem tagja az előfizetés Active Directory-példányának, mielőtt hozzáadják az előfizetés globális rendszergazdai szerepköréhez, vendégként lesz hozzáadva. Ebben az esetben figyelmeztetés jelenik meg `You do not have permissions to create…` az **Automation-fiók hozzáadása oldalon.**

A hibaüzenetet előállító helyzet megoldásának ellenőrzése:

1. A Azure Active Directory panelen válassza Azure Portal Felhasználók és **csoportok lehetőséget.**
2. Válassza a **Minden felhasználó lehetőséget.**
3. Válassza ki a nevét, majd válassza a **Profil lehetőséget.**
4. Győződjön meg arról,  hogy a felhasználói profilban a Felhasználótípus attribútum értéke nem Vendég értékre **van állítva.**

## <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés

A szerepköralapú hozzáférés-vezérlés Azure Resource Manager az engedélyezett műveleteket egy Azure AD-felhasználói fióknak és egy run as-fióknak, valamint a szolgáltatásnév hitelesítésének érdekében. Az Automation-engedélyek kezelésére használt modell fejlesztésére vonatkozó további információkért olvassa el [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md) című cikket.

Ha szigorú biztonsági vezérlőkkel rendelkezik az erőforráscsoportokban való engedély-hozzárendeléshez,  a futtatás fióktagságot hozzá kell rendelnie a Közreműködő szerepkörhöz az erőforráscsoportban.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook-hitelesítés hibrid runbook-feldolgozóval

Az adatközpontban hibrid runbook-feldolgozón vagy más felhőalapú környezetekben, például az AWS-beli számítástechnikai szolgáltatásokon futó runbookok nem tudják ugyanazt a módszert használni, mint az Azure-erőforrásokban hitelesítő runbookok. Ennek oka az, hogy azok az erőforrások az Azure-on kívül futnak, és emiatt az Automation szolgáltatásban meghatározott saját biztonsági hitelesítő adataikra van szükség a helyileg elérhető erőforrásokhoz történő hitelesítéshez. További információ a Runbook-hitelesítésről Runbook-feldolgozók [használatával: Runbookok futtatása hibrid runbook-feldolgozón.](automation-hrw-run-runbooks.md)

Az Azure-beli virtuális gépeken hibrid runbook-dolgozókat felhasználó runbookok esetében a [Runbook-hitelesítést](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) felügyelt identitásokkal használhatja a run as fiókok helyett az Azure-erőforrásokon való hitelesítéshez.

## <a name="next-steps"></a>Következő lépések

* Automation-fiók létrehozása a Azure Portal: Önálló fiók [Azure Automation létrehozása.](automation-create-standalone-account.md)
* Ha inkább sablon használatával szeretné létrehozni a fiókját, tekintse meg az [Automation-fiók létrehozása sablon Azure Resource Manager használatával Azure Resource Manager.](quickstart-create-automation-account-template.md)
* A forgatókönyvekkel való Amazon Web Services [lásd: Runbookok hitelesítése a Amazon Web Services.](automation-config-aws-account.md)
* Az Azure-erőforrások felügyelt identitásai szolgáltatást támogató Azure-szolgáltatások listájáért lásd [az Azure-erőforrások felügyelt identitásait támogató szolgáltatásokkal](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities) foglalkozó részt.
