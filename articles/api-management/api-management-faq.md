---
title: Azure API Management GYIK | Microsoft Docs
description: Ismerje meg az Azure API Management gyakori kérdéseire (GYIK), mintákra és ajánlott eljárásokra adott válaszokat.
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
ms.openlocfilehash: eea3c8525d31a3ca551e9cbc7d21d7dde163b5cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94697985"
---
# <a name="azure-api-management-faqs"></a>Azure API Management – gyakori kérdések
Választ kaphat az Azure API Management gyakori kérdéseire, mintáinak és ajánlott eljárásaira.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Gyakori kérdések
* [Mit jelent, ha egy funkció előzetes verzióban van?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hogyan védhetem meg a kapcsolatot az API Management-átjáró és a háttérszolgáltatások között?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Hogyan másolhatom át az API Management-szolgáltatáspéldányomat új példányra?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Kezelhetem a API Management-példányt programozott módon?](#can-i-manage-my-api-management-instance-programmatically)
* [Hogyan adhatok hozzá felhasználót a rendszergazdák csoportjához?](#how-do-i-add-a-user-to-the-administrators-group)
* [Miért nem érhető el a szabályzat a házirend-szerkesztőben?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hogyan több környezetet is beállíthat egyetlen API-ban?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Használhatom a SOAP-t a API Management?](#can-i-use-soap-with-api-management)
* [Konfigurálható a OAuth 2,0-es engedélyezési kiszolgáló AD FS biztonsággal?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Milyen útválasztási módszert használ a központi telepítések API Management több földrajzi helyhez?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Használhatok Azure Resource Manager sablont egy API Management Service-példány létrehozásához?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Használhatok önaláírt TLS/SSL-tanúsítványt a háttérrendszer számára?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Miért kapok hitelesítési hibát a GIT-tárház klónozásakor?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Működik API Management az Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Miért van szükség egy dedikált alhálózatra a Resource Manager-stílusú virtuális hálózatok, amikor a API Management üzembe helyezése folyamatban van?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Milyen minimális alhálózati méretre van szükség a API Management VNET való telepítésekor?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Áthelyezhetek egy API Management szolgáltatást az egyik előfizetésből egy másikba?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Van korlátozás vagy ismert probléma az API-k importálásakor?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Mit jelent, ha egy funkció előzetes verzióban van?
Ha egy funkció előzetes verzióban érhető el, az azt jelenti, hogy aktívan keresünk visszajelzést arról, hogy a szolgáltatás hogyan működik. Az előzetes verzióban egy funkció működik, de lehetséges, hogy a vásárlói visszajelzésekre adott válaszként feltörhető változást teszünk elérhetővé. Azt javasoljuk, hogy ne függjön az éles környezetben előzetes verzióban elérhető szolgáltatástól.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hogyan védhetem meg a kapcsolatot az API Management-átjáró és a háttérszolgáltatások között?
Több lehetőség áll rendelkezésére a kapcsolat biztonságossá tételéhez a API Management-átjáró és a háttér-szolgáltatások között. A következőket teheti:

* HTTP alapszintű hitelesítés használata. További információ: [az első API importálása és közzététele](import-and-publish.md).
* Használjon TLS kölcsönös hitelesítést a következő témakörben ismertetett módon: a [háttér-szolgáltatások biztonságossá tétele az ügyféltanúsítvány-alapú hitelesítés használatával az Azure API Managementban](api-management-howto-mutual-certificates.md).
* Használjon IP-szűrést a háttér-szolgáltatáson. Az API Management minden olyan szintjében, amely a felhasználási szintet is tartalmazza, az átjáró IP-címe állandó marad, és néhány, [az IP-dokumentációban](api-management-howto-ip-addresses.md)leírt figyelmeztetéssel rendelkezik.
* API Management példányának összekapcsolásához Azure-Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hogyan másolhatom át az API Management-szolgáltatáspéldányomat új példányra?
Több lehetőség is rendelkezésre áll, ha API Management példányt szeretne másolni egy új példányra. A következőket teheti:

* Használja a API Management biztonsági mentési és visszaállítási függvényt. További információ: a vész- [helyreállítás megvalósítása a szolgáltatás biztonsági mentésével és visszaállításával az Azure API Managementban](api-management-howto-disaster-recovery-backup-restore.md).
* Hozza létre saját biztonsági mentési és visszaállítási funkcióját a [API Management REST API](/rest/api/apimanagement/)használatával. A REST API használatával mentse és állítsa vissza az entitásokat a kívánt szolgáltatás-példányból.
* Töltse le a szolgáltatás konfigurációját a git használatával, majd töltse fel egy új példányba. További információ: [a API Management szolgáltatás konfigurációjának mentése és konfigurálása a git használatával](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kezelhetem a API Management-példányt programozott módon?
Igen, API Management programozott módon felügyelheti a használatával:

* A [API Management REST API](/rest/api/apimanagement/).
* A [Microsoft Azure ApiManagement Service Management Library SDK](https://aka.ms/apimsdk).
* A [szolgáltatás központi telepítésével](/powershell/module/wds) és a [Service Management](/powershell/azure/servicemanagement/overview) PowerShell-parancsmagokkal.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hogyan adhatok hozzá felhasználót a rendszergazdák csoportjához?
A rendszergazdák csoport egy nem módosítható Rendszercsoport. Az Azure-előfizetés rendszergazdái tagjai ennek a csoportnak. Ehhez a csoporthoz nem adhat hozzá felhasználót. További információért lásd: [csoportok létrehozása és használata a fejlesztői fiókok kezeléséhez az Azure API Managementban](./api-management-howto-create-groups.md) .

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Miért nem érhető el a szabályzat a házirend-szerkesztőben?
Ha a hozzáadni kívánt szabályzat szürkén vagy árnyékolva jelenik meg a házirend-szerkesztőben, ügyeljen arra, hogy a szabályzat megfelelő hatóköre legyen. Minden házirend-utasítás meghatározott hatókörökben és házirend-szakaszban való használatra készült. A szabályzatok szabályzat-szakaszainak és hatókörének áttekintéséhez tekintse meg a házirend használatáról szóló szakaszt [API Management házirendek](./api-management-policies.md)részben.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hogyan több környezetet is beállíthat egyetlen API-ban?
Több környezet (például egy tesztkörnyezet és egy éles környezet) egyetlen API-ban való beállításához két lehetőség közül választhat. A következőket teheti:

* Különböző API-k üzemeltetése ugyanazon a bérlőn.
* Ugyanazon API-k üzemeltetése különböző bérlők esetében.

### <a name="can-i-use-soap-with-api-management"></a>Használhatom a SOAP-t a API Management?
A [SOAP pass-through](https://azure.microsoft.com/blog/soap-pass-through/) támogatás mostantól elérhető. A rendszergazdák importálhatók a SOAP-szolgáltatáshoz tartozó WSDL-t, az Azure API Management pedig létrehoz egy SOAP-előtérben. A fejlesztői portál dokumentációja, a tesztelési konzol, a szabályzatok és az elemzések mind elérhetők a SOAP-szolgáltatásokhoz.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Konfigurálható a OAuth 2,0-es engedélyezési kiszolgáló AD FS biztonsággal?
Az OAuth 2,0 engedélyezési kiszolgáló Active Directory összevonási szolgáltatások (AD FS) (AD FS) biztonsággal való konfigurálásával kapcsolatos információkért lásd: az [ADFS használata a API Management-ben](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Milyen útválasztási módszert használ a központi telepítések API Management több földrajzi helyhez?
API Management a központi telepítések [teljesítmény-forgalmi útválasztási módszerét](../traffic-manager/traffic-manager-routing-methods.md#performance) használja több földrajzi helyhez. A bejövő forgalom a legközelebbi API-átjáróhoz lesz irányítva. Ha az egyik régió offline állapotba kerül, a rendszer automatikusan átirányítja a bejövő forgalmat a következő legközelebbi átjáróhoz. További információ az útválasztási módszerekről [Traffic Manager útválasztási módszerekben](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Használhatok Azure Resource Manager sablont egy API Management Service-példány létrehozásához?
Igen. Tekintse meg az [Azure API Management Service](https://aka.ms/apimtemplate) Gyorsindítás sablonjait.

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Használhatok önaláírt TLS/SSL-tanúsítványt a háttérrendszer számára?
Igen. Ez a PowerShell használatával vagy az API-hoz való közvetlen beküldéssel végezhető el. Ezzel a beállítással letilthatja a tanúsítványlánc érvényesítését, és lehetővé teszi önaláírt vagy önaláírt tanúsítványok használatát a API Managementról a háttér-szolgáltatásokhoz való kommunikáció során.

#### <a name="powershell-method"></a>PowerShell-módszer ####
Használja az [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (új háttérrendszer) vagy a [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (meglévő háttérrendszer) PowerShell-parancsmagokat, és állítsa a paramétert a következőre: `-SkipCertificateChainValidation` `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Közvetlen API-frissítési módszer ####
1. Hozzon létre egy [háttérbeli](/rest/api/apimanagement/) entitást API Management használatával.     
2. Állítsa a **skipCertificateChainValidation** tulajdonságot **true (igaz**) értékre.     
3. Ha már nem szeretné engedélyezni az önaláírt tanúsítványokat, törölje a háttérbeli entitást, vagy állítsa a **skipCertificateChainValidation** tulajdonságot **hamis** értékre.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Miért jelenik meg hitelesítési hibaüzenet Git-adattár klónozásakor?
Ha a git hitelesítőadat-kezelőt használja, vagy ha a Visual Studióval próbál meg klónozási tárházat használni, előfordulhat, hogy a Windows hitelesítő adatok párbeszédpanel ismert problémába ütközhet. A párbeszédpanel a jelszó hosszát 127 karakterre korlátozza, és levágja a Microsoft által generált jelszót. Dolgozunk a jelszó lerövidítésén. A git-tárház klónozásához használja a git Basht.

### <a name="does-api-management-work-with-azure-expressroute"></a>Működik API Management az Azure ExpressRoute?
Igen. A API Management az Azure ExpressRoute működik.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Miért van szükség egy dedikált alhálózatra a Resource Manager-stílusú virtuális hálózatok, amikor a API Management üzembe helyezése folyamatban van?
A API Management dedikált alhálózat-követelménye a tény, hogy klasszikus (Pásti v1-es) üzembe helyezési modellre épül. Noha üzembe helyezhető egy Resource Manager-VNET (v2-réteg), ennek következményei vannak. Az Azure-beli klasszikus üzemi modell nincs szorosan összekapcsolva a Resource Manager-modellel, így ha a v2-rétegben hoz létre egy erőforrást, a v1-réteg nem tud róla, és problémákat tapasztalhat, például API Management olyan IP-címet próbálok használni, amely már le van foglalva egy hálózati adapterre (a v2-re épül).
Ha többet szeretne megtudni a klasszikus és a Resource Manager-modellekről az Azure-ban, tekintse át az [üzembe helyezési modellek különbségét](../azure-resource-manager/management/deployment-models.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Milyen minimális alhálózati méretre van szükség a API Management VNET való telepítésekor?
Az API Management a [/29](../virtual-network/virtual-networks-faq.md#configuration)telepítéséhez szükséges minimális alhálózati méret, amely az Azure által támogatott minimális alhálózat-méret.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Áthelyezhetek egy API Management szolgáltatást az egyik előfizetésből egy másikba?
Igen. További információ: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Van korlátozás vagy ismert probléma az API-k importálásakor?
[Ismert problémák és korlátozások](api-management-api-import-restrictions.md) az Open API (hencegő), a WSDL és a WADL formátumokhoz.
