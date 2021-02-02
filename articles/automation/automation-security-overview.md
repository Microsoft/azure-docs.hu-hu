---
title: Azure Automation fiók hitelesítésének áttekintése
description: Ez a cikk áttekintést nyújt Azure Automation fiók hitelesítéséről.
keywords: automation-biztonság, automation biztonságossá tétele; automation-hitelesítés
services: automation
ms.subservice: process-automation
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: e3ed14a0845d90a1b9b951dd5175acf6233f8718
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254999"
---
# <a name="automation-account-authentication-overview"></a>Az Automation-fiókok hitelesítésének áttekintése

Az Azure Automation lehetővé teszi a feladatok automatizálását az Azure erőforrásain, továbbá olyan felhőszolgáltatókkal, mint az Amazon webszolgáltatások (AWS). A runbookok segítségével automatizálhatja a feladatokat, vagy egy hibrid Runbook-feldolgozót, ha üzleti vagy működési folyamatai vannak az Azure-on kívüli felügyeletre. Ezeknek a környezeteknek a használata megköveteli az erőforrások biztonságos elérését a minimálisan szükséges jogokkal.

Ez a cikk a Azure Automation által támogatott hitelesítési forgatókönyveket ismerteti, és elmagyarázza, hogyan kezdheti el az első lépéseket a felügyelni kívánt környezet vagy környezetek alapján.

## <a name="automation-account"></a>Automation-fiók

Amikor először indítja el az Azure Automationt, legalább egy Automation-fiókot létre kell hoznia. Az Automation-fiókok lehetővé teszik, hogy elkülönítse az Automation-erőforrásokat, a runbookok, az eszközöket és a konfigurációkat más fiókok erőforrásaiból. Az Automation-fiókok segítségével külön logikai környezetekre vagy delegált felelősségekre különítheti el az erőforrásokat. Használhat például egy fiókot fejlesztéshez, egy másikat az üzemi használatra, egy harmadikat pedig a helyszíni környezethez. Vagy egy Automation-fiókkal is elvégezheti az operációs rendszer frissítéseinek kezelését az összes gépen a [Update Management](update-management/overview.md)használatával. 

Az Azure Automation-fiók különbözik a Microsoft-fiókjától vagy az Azure-előfizetésében létrehozott fiókoktól. Az Automation-fiók létrehozásának bevezetését az [Automation-fiók létrehozása](automation-quickstart-create-account.md)című témakörben tekintheti meg.

## <a name="automation-resources"></a>Automation-erőforrások

Az egyes Automation-fiókok automatizálási erőforrásai egyetlen Azure-régióhoz vannak társítva, de a fiók az Azure-előfizetésében lévő összes erőforrást képes kezelni. Az Automation-fiókok különböző régiókban való létrehozásának fő oka az, ha olyan házirendekkel rendelkezik, amelyek megkövetelik, hogy az egyes régiókban az adatforrások és az erőforrások el legyenek különítve.

A Azure Resource Manager és a Azure Automation PowerShell-parancsmagjai segítségével létrehozott összes feladatnak az Azure-ban Azure Active Directory (Azure AD) szervezeti identitású hitelesítő adatokon alapuló hitelesítéssel kell hitelesítenie az Azure-ban.

## <a name="run-as-accounts"></a>Futtató fiókok

A Azure Automation futtató fiókok biztosítják a klasszikus üzemi modellben üzembe helyezett Azure Resource Manager erőforrások vagy erőforrások felügyeletének hitelesítését. A Azure Automationben kétféle futtató fiók létezik:

* Azure-beli futtató fiók: lehetővé teszi az Azure-erőforrások kezelését az Azure-hoz készült Azure Resource Manager üzembe helyezési és felügyeleti szolgáltatás alapján.
* Klasszikus Azure-beli futtató fiók: lehetővé teszi a klasszikus Azure-erőforrások kezelését a klasszikus üzembe helyezési modell alapján.

Ha többet szeretne megtudni a Azure Resource Manager és a klasszikus üzemi modellekről, tekintse meg a [Resource Manager és a klasszikus üzembe helyezés](../azure-resource-manager/management/deployment-models.md)című témakört.

>[!NOTE]
>A Azure Cloud Solution Provider (CSP) előfizetések csak a Azure Resource Manager modellt támogatják. A nem Azure Resource Manager szolgáltatások nem érhetők el a programban. Ha CSP-előfizetést használ, a klasszikus Azure-beli futtató fiók nem jön létre, de létrejön az Azure-beli futtató fiók. A CSP-előfizetésekkel kapcsolatos további tudnivalókért tekintse meg a [CSP-előfizetésekben elérhető szolgáltatások](/azure/cloud-solution-provider/overview/azure-csp-available-services)című témakört.

Amikor létrehoz egy Automation-fiókot, a futtató fiók alapértelmezés szerint egy időben jön létre. Ha úgy döntött, hogy nem hozza létre az Automation-fiókkal együtt, akkor azt egy későbbi időpontban is létrehozhatja. A klasszikus Azure-beli futtató fiók nem kötelező, és külön jön létre, ha klasszikus erőforrásokat kell kezelnie.

### <a name="run-as-account"></a>Futtató fiók

A futtató fiók létrehozásakor a következő feladatokat hajtja végre:

* Létrehoz egy önaláírt tanúsítvánnyal rendelkező Azure AD-alkalmazást, létrehoz egy egyszerű szolgáltatásfiókot az alkalmazáshoz az Azure AD-ben, és hozzárendeli a [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkört a fiókhoz a jelenlegi előfizetésében. A tanúsítvány beállításait az [olvasóra](../role-based-access-control/built-in-roles.md#reader) vagy bármely más szerepkörre módosíthatja. További információk: [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).

* Létrehoz egy nevű Automation-tanúsítványt `AzureRunAsCertificate` a megadott Automation-fiókban. A tanúsítvány objektuma tartalmazza az Azure AD-alkalmazás által használt tanúsítvány titkos kulcsát.

* Létrehoz egy nevű Automation-összekötő eszközt `AzureRunAsConnection` a megadott Automation-fiókban. A szolgáltatás tartalmazza az alkalmazás AZONOSÍTÓját, a bérlő AZONOSÍTÓját, az előfizetés AZONOSÍTÓját és a tanúsítvány ujjlenyomatát.

### <a name="azure-classic-run-as-account"></a>Klasszikus Azure-beli futtató fiók

Klasszikus Azure-beli futtató fiók létrehozásakor a következő feladatokat hajtja végre:

> [!NOTE]
> Ilyen típusú futtató fiók létrehozásához vagy megújításához az előfizetés egyik társ-rendszergazda tagjának kell lennie.

* Létrehoz egy felügyeleti tanúsítványt az előfizetésben.

* Létrehoz egy nevű Automation-tanúsítványt `AzureClassicRunAsCertificate` a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza a felügyeleti tanúsítvány által használt titkos tanúsítványkulcsot.

* Létrehoz egy nevű Automation-összekötő eszközt `AzureClassicRunAsConnection` a megadott Automation-fiókban. A szolgáltatás tartalmazza az előfizetés nevét, az előfizetés AZONOSÍTÓját és a tanúsítvány-eszköz nevét.

## <a name="service-principal-for-run-as-account"></a>Egyszerű szolgáltatásnév a futtató fiókhoz

A futtató fiókhoz tartozó egyszerű szolgáltatásnév nem rendelkezik az Azure AD alapértelmezett olvasási engedélyeivel. Ha engedélyeket szeretne adni az Azure AD olvasásához vagy kezeléséhez, meg kell adnia az engedélyeket az egyszerű szolgáltatáshoz az **API-engedélyek** alatt. További információért lásd: [engedélyek hozzáadása a webes API-hoz való hozzáféréshez](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Futtató fiók engedélyei

Ez a szakasz a normál futtató fiókok és a klasszikus futtató fiókok engedélyeit határozza meg.

* Futtató fiók létrehozásához vagy frissítéséhez az alkalmazás rendszergazdája Azure Active Directory és az előfizetés tulajdonosa az összes feladatot elvégezheti.
* Klasszikus futtató fiókok konfigurálásához vagy megújításához az előfizetés szintjén kell lennie a társ-rendszergazda szerepkörnek. A klasszikus előfizetési engedélyekkel kapcsolatos további tudnivalókért tekintse meg a [klasszikus Azure-előfizetés rendszergazdái](../role-based-access-control/classic-administrators.md#add-a-co-administrator)című témakört.

Az alábbi táblázat a feladatok elkülönítését mutatja be, a megfelelő parancsmagot és engedélyeket, valamint a szükséges engedélyek listáját:

|Feladat|Parancsmag  |Minimális engedélyek  |Az engedélyek beállítása|
|---|---------|---------|---|
|Azure AD-alkalmazás létrehozása|[Új – AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Alkalmazás fejlesztői szerepköre<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja > |
|Adjon hozzá egy hitelesítő adatot az alkalmazáshoz.|[Új – AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja >|
|Azure AD-szolgáltatásnév létrehozása és beszerzése|[Új – AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja >|
|Az Azure-szerepkör kiosztása vagy beszerzése a megadott rendszerbiztonsági tag számára|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Felhasználói hozzáférés a rendszergazdához vagy a tulajdonoshoz, vagy a következő engedélyekkel rendelkezik:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Előfizetés](../role-based-access-control/role-assignments-portal.md)</br>Home >-előfizetések > \<subscription name\> -Access Control (iam)|
|Automation-tanúsítvány létrehozása vagy eltávolítása|[Új – AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Közreműködő az erőforráscsoporthoz         |Automation-fiók erőforráscsoport|
|Automation-kapcsolatok létrehozása vagy eltávolítása|[Új – AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Közreműködő az erőforráscsoporthoz |Automation-fiók erőforráscsoport|

<sup>1</sup> az Azure ad-bérlőben nem rendszergazda felhasználók [regisztrálhatnak ad-alkalmazásokat](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) , ha az Azure ad-bérlő **felhasználóinak az alkalmazások regisztrálása** lehetőség a **felhasználói beállítások** lapon az **Igen** értékre van állítva. Ha az alkalmazás regisztrációs beállítása **nem**, a műveletet végrehajtó felhasználónak a táblázatban megadott módon kell szerepelnie.

Ha nem tagja az előfizetés Active Directory példányának, mielőtt hozzáadja az előfizetés globális rendszergazdai szerepköréhez, vendégként lesz hozzáadva. Ebben az esetben `You do not have permissions to create…` figyelmeztetést kap az **Automation-fiók hozzáadása** oldalon.

Annak ellenőrzése, hogy a hibaüzenetet előállító helyzet kijavítása megtörtént-e:

1. A Azure Portal Azure Active Directory ablaktábláján válassza a **felhasználók és csoportok** lehetőséget.
2. Válassza **a minden felhasználó** lehetőséget.
3. Válassza ki a nevét, majd válassza a **profil** lehetőséget.
4. Győződjön meg arról, hogy a felhasználó profiljában a **felhasználó típusa** attribútum értéke nincs beállítva **vendégként**.

## <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés

A szerepköralapú hozzáférés-vezérlés Azure Resource Manager segítségével engedélyezheti az Azure AD felhasználói fiókjának és futtató fiókjának engedélyezett műveleteit, valamint hitelesítheti az egyszerű szolgáltatást. Az Automation-engedélyek kezelésére használt modell fejlesztésére vonatkozó további információkért olvassa el [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md) című cikket.

Ha szigorú biztonsági ellenőrzésekkel rendelkezik az engedélyek hozzárendeléséhez az erőforráscsoportok területen, a futtató fiók tagságát hozzá kell rendelnie az erőforráscsoport **közreműködői** szerepköréhez.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook-hitelesítés hibrid Runbook-feldolgozóval

Az adatközpontban vagy más felhőalapú környezetekben, például az AWS-ben működő hibrid Runbook-feldolgozón futó runbookok nem használhatja ugyanazt a módszert, amelyet általában az Azure-erőforrásokhoz történő runbookok-hitelesítéshez használ. Ennek oka az, hogy azok az erőforrások az Azure-on kívül futnak, és emiatt az Automation szolgáltatásban meghatározott saját biztonsági hitelesítő adataikra van szükség a helyileg elérhető erőforrásokhoz történő hitelesítéshez. A runbook-feldolgozókkal való runbook-hitelesítéssel kapcsolatos további információkért lásd: [Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md).

Az Azure-beli virtuális gépeken hibrid Runbook-feldolgozókat használó runbookok esetében az Azure-erőforrások hitelesítéséhez a futtató fiókok helyett [felügyelt identitásokkal rendelkező Runbook-hitelesítést](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) használhat.

## <a name="next-steps"></a>Következő lépések

* Ha Automation-fiókot szeretne létrehozni a Azure Portalből, tekintse meg az [önálló Azure Automation fiók létrehozása](automation-create-standalone-account.md)című témakört.
* Ha sablon használatával szeretné létrehozni a fiókját, tekintse meg [az Automation-fiók létrehozása Azure Resource Manager sablonnal](quickstart-create-automation-account-template.md)című témakört.
* Amazon Web Services használatával történő hitelesítéssel kapcsolatban lásd: [Runbookok hitelesítése Amazon Web Servicessal](automation-config-aws-account.md).