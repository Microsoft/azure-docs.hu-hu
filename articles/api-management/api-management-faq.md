---
title: Azure API Management – gyakori | Microsoft Docs
description: Ismerje meg az Azure-ban gyakran feltett kérdésekre (GYIK), mintákra és ajánlott eljárásokra adott API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b6a138317d0cc2e10e893d1969f9d5452064d8f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813739"
---
# <a name="azure-api-management-faqs"></a>Azure API Management – gyakori kérdések
Választ kaphat az Azure-előfizetések gyakori kérdéseire, mintáira és ajánlott API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Gyakori kérdések
* [Mit jelent, ha egy funkció előzetes verzióban érhető el?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hogyan védhetem meg a kapcsolatot az API Management-átjáró és a háttérszolgáltatások között?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Hogyan másolhatom át az API Management-szolgáltatáspéldányomat új példányra?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Kezelhetem a API Management programozott módon?](#can-i-manage-my-api-management-instance-programmatically)
* [Hogyan adhatok hozzá felhasználót a rendszergazdák csoportjához?](#how-do-i-add-a-user-to-the-administrators-group)
* [Miért nem érhető el a szabályzatszerkesztőben a hozzáadni kívánt szabályzat?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hogyan több környezetet is beállít egyetlen API-ban?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Használhatom a SOAP-t API Management?](#can-i-use-soap-with-api-management)
* [Konfigurálható OAuth 2.0-hitelesítési kiszolgáló AD FS biztonsággal?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Milyen útválasztási módszert API Management több földrajzi helyre történő üzembe helyezések során?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Használhatok egy Azure Resource Manager-sablont egy API Management szolgáltatáspéldány létrehozásához?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Használhatok önaírt TLS-/SSL-tanúsítványt a háttérkiszolgálóhoz?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Miért kapok hitelesítési hibát, amikor GIT-adattárat próbálok klónozni?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Működik API Management a Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Miért van szükség dedikált alhálózatra az Resource Manager virtuális hálózatokban, amikor API Management üzembe helyezünk bennük?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Mi a minimálisan szükséges alhálózatméret a virtuális API Management való üzembe helyezésekor?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Áthelyezhetek egy API Management szolgáltatást az egyik előfizetésből egy másikba?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Vannak korlátozások az API-k importálásával kapcsolatban, vagy ismert problémákat okozhatnak?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Mit jelent, ha egy funkció előzetes verzióban érhető el?
Ha egy funkció előzetes verzióban érhető el, az azt jelenti, hogy aktívan kérünk visszajelzést arról, hogyan működik a funkció Az Ön számára. Az előzetes verzióban elérhető funkciók funkciójuk működőképes, de lehetséges, hogy az ügyfelek visszajelzésére válaszul a legfrissebb változtatást fogjuk végrehajtani. Azt javasoljuk, hogy ne támaszkodjon az éles környezetben előzetes verzióban elérhető funkciókra.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hogyan védhetem meg a kapcsolatot az API Management-átjáró és a háttérszolgáltatások között?
A hálózati átjáró és a háttérszolgáltatások API Management biztosíthatja a kapcsolatot. A következőket teheti:

* Használjon alapszintű HTTP-hitelesítést. További információ: Az első [API importálása és közzététele.](import-and-publish.md)
* Használja a TLS kölcsönös hitelesítést a Háttérszolgáltatások biztonságossá tevésének biztosítása ügyfél-tanúsítványos hitelesítéssel az [Azure API Management.](api-management-howto-mutual-certificates.md)
* HASZNÁLJON IP-szűrést a háttérszolgáltatáson. A szolgáltatás minden API Management a használat alapú csomag kivételével az átjáró IP-címe állandó marad, az IP-dokumentációs cikkben ismertetett néhány [kikötéssel.](api-management-howto-ip-addresses.md)
* Csatlakoztassa a API Management-példányt egy Azure-Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hogyan másolhatom át az API Management-szolgáltatáspéldányomat új példányra?
Több lehetőség is rendelkezésre áll, ha egy API Management példányt szeretne átmásolni egy új példányra. A következőket teheti:

* Használja a biztonsági mentési és visszaállítási függvényt a API Management. További információ: Vészhelyreállítás megvalósítása szolgáltatás biztonsági mentésének és visszaállításának használatával [az Azure API Management.](api-management-howto-disaster-recovery-backup-restore.md)
* Hozza létre saját biztonsági mentési és visszaállítási szolgáltatását a [API Management REST API.](/rest/api/apimanagement/) A REST API mentheti és visszaállíthatja az entitásokat a kívánt szolgáltatáspéldányból.
* Töltse le a szolgáltatás konfigurációját a Git használatával, majd töltse fel egy új példányra. További információkért lásd: How to save and configure your API Management service configuration by using Git (A szolgáltatás konfigurációjának mentése és konfigurálása [a Git használatával).](api-management-configuration-repository-git.md)

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kezelhetem a API Management programozott módon?
Igen, programozott módon API Management a következővel:

* A [API Management REST API](/rest/api/apimanagement/).
* A [Microsoft Azure ApiManagement Service Management Library SDK.](https://aka.ms/apimsdk)
* A [szolgáltatástelepítési](/powershell/module/wds) [és szolgáltatáskezelési](/powershell/azure/servicemanagement/overview) PowerShell-parancsmagok.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hogyan adhatok hozzá felhasználót a rendszergazdák csoportjához?
A rendszergazdák csoportjai nem módosítható rendszercsoportok. Az Azure-előfizetések rendszergazdái ennek a csoportnak a tagjai. Ehhez a csoporthoz nem adhat hozzá felhasználót. További információ: Csoportok létrehozása és használata fejlesztői fiókok kezeléséhez API Management [Azure-ban.](./api-management-howto-create-groups.md)

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Miért nem érhető el a szabályzatszerkesztőben a hozzáadni kívánt szabályzat?
Ha a hozzáadni kívánt szabályzat halványan vagy árnyékoltan jelenik meg a szabályzatszerkesztőben, győződjön meg arról, hogy a szabályzat megfelelő hatókörében van. Minden szabályzati utasítás adott hatókörökben és szabályzatszakaszban való használatra lett kialakítva. Egy szabályzat szabályzatszakaszának és hatókörének áttekintését a szabályzatok Használat szakaszában, a szabályzatok API Management [meg.](./api-management-policies.md)

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hogyan több környezetet is beállít egyetlen API-ban?
Több környezet, például egy tesztkörnyezet és egy éles környezet beállításához egyetlen API-ban két lehetőség közül választhat. A következőket teheti:

* Különböző API-k ugyanazon a bérlőn való gazdagépe.
* Ugyanazon API-k különböző bérlőkben való gazdagépe.

### <a name="can-i-use-soap-with-api-management"></a>Használhatom a SOAP-t API Management?
[A SOAP átmenő támogatása](https://azure.microsoft.com/blog/soap-pass-through/) már elérhető. A rendszergazdák importálhatják a SOAP-szolgáltatás WSDL-ét, és az Azure API Management létrehoznak egy SOAP-előtért. A fejlesztői portál dokumentációja, a tesztkonzol, a szabályzatok és az elemzések mind elérhetők a SOAP-szolgáltatásokhoz.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Konfigurálható OAuth 2.0-hitelesítési kiszolgáló AD FS biztonsággal?
Az OAuth 2.0 engedélyezési kiszolgáló Active Directory összevonási szolgáltatások (AD FS) (AD FS) biztonsággal való konfigurálásával kapcsolatos további információkért lásd: [Using ADFS in API Management (Az ADFS](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)használata a API Management).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Milyen útválasztási módszert API Management több földrajzi helyre történő üzembe helyezések során?
API Management a [teljesítményforgalom útválasztási módszerét](../traffic-manager/traffic-manager-routing-methods.md#performance) használja a több földrajzi helyre történő üzembe helyezések során. A bejövő forgalom a legközelebbi API-átjáróhoz lesz irányítva. Ha egy régió offline állapotba kerül, a bejövő forgalom automatikusan a legközelebbi átjáróra lesz irányítva. További információ az útválasztási módszerekről [Traffic Manager módszerekben.](../traffic-manager/traffic-manager-routing-methods.md)

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Használhatok Azure Resource Manager sablont egy API Management szolgáltatáspéldány létrehozásához?
Igen. Lásd az [Azure API Management Service gyorsindítási](https://aka.ms/apimtemplate) sablonokat.

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Használhatok önaírt TLS-/SSL-tanúsítványt a háttérkiszolgálóhoz?
Igen. Ez a PowerShellen keresztül vagy közvetlenül az API-nak való küldésen keresztül érhető el. Ez letiltja a tanúsítványlánc érvényesítését, és lehetővé teszi önaírt vagy privát aláírású tanúsítványok használatát, amikor a API Management kommunikál a háttérszolgáltatásokkal.

#### <a name="powershell-method"></a>PowerShell-metódus ####
Használja az [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (új háttérhez) vagy [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (meglévő háttérhez) PowerShell-parancsmagokat, és állítsa a paramétert a `-SkipCertificateChainValidation` következőre: `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Közvetlen API-frissítési módszer ####
1. [Háttérentitás létrehozása](/rest/api/apimanagement/) a API Management.     
2. Állítsa a **skipCertificateChainValidation tulajdonságot** true **(igaz) értékűre.**     
3. Ha már nem szeretné engedélyezni az önaláírt tanúsítványokat, törölje a Háttér entitást, vagy állítsa a **skipCertificateChainValidation** tulajdonságot **false (hamis) értékűre.**

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Miért jelenik meg hitelesítési hibaüzenet Git-adattár klónozásakor?
Ha Git Hitelesítőadat-kezelő használ, vagy ha a Visual Studio használatával próbál klónozni egy Git-adattárat, előfordulhat, hogy a Windows hitelesítő adatai párbeszédpanel ismert problémába fog belefutni. A párbeszédpanel a jelszó hosszát 127 karakterre korlátozza, és csonkja a Microsoft által létrehozott jelszót. Dolgozunk a jelszó lerövidítésén. Most a Git Bash használatával klónozza a Git-adattárat.

### <a name="does-api-management-work-with-azure-expressroute"></a>Működik API Management a Azure ExpressRoute?
Igen. API Management a Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Miért van szükség dedikált alhálózatra az Resource Manager virtuális hálózatokban, amikor API Management üzembe helyezünk bennük?
A dedikált alhálózati követelmény a API Management abból a tényből származik, hogy a klasszikus (PAAS V1 réteg) üzembe helyezési modellre épül. Bár üzembe helyezhetünk egy virtuális Resource Manager (V2 rétegben), ennek következményei is lehetnek. Az Azure-beli klasszikus üzembe helyezési modell nincs szorosan összepárosulva az Resource Manager-modellel, így ha v2 rétegben hoz létre erőforrást, a V1 réteg nem tud róla, és problémák fordulhatnak elő API Management, például egy hálózati adapterhez már lefoglalt IP-címet próbál használni (amely a V2-re épül).
A klasszikus és az azure-beli Resource Manager különbségekkel kapcsolatos további információkért tekintse meg az üzembe [helyezési modellek közötti különbséget.](../azure-resource-manager/management/deployment-models.md)

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Mi a minimálisan szükséges alhálózatméret a virtuális API Management való üzembe helyezésekor?
A virtuális gép üzembe helyezéséhez szükséges minimális API Management [/29](../virtual-network/virtual-networks-faq.md#configuration), amely az Azure által támogatott minimális alhálózatméret.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Áthelyezhetek egy API Management szolgáltatást az egyik előfizetésből egy másikba?
Igen. További információ: Erőforrások áthelyezése új [erőforráscsoportba vagy előfizetésbe.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Vannak korlátozások az API-k importálásával kapcsolatban, vagy ismert problémákat okozhatnak?
[Az](api-management-api-import-restrictions.md) Open API(Swagger), a WSDL és a WADL formátumok ismert problémái és korlátozásai.
