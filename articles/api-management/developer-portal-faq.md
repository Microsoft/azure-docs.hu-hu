---
title: Fejlesztői portál – Gyakori kérdések
titleSuffix: Azure API Management
description: Gyakori kérdések a fejlesztői portálról a API Management. A fejlesztői portál egy testreszabható webhely, ahol az API-felhasználók felfedezhetik az API-kat.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: b4413bc53cf5c86c311d049046db790582737ca4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741680"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>API Management fejlesztői portál – gyakori kérdések

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>Mi történik, ha olyan funkciókra van szükségem, amelyek nem támogatottak a portálon?

Megnyithat egy funkciókérést a [GitHub-adattárban,](https://github.com/Azure/api-management-developer-portal) vagy megvalósíthatja a [hiányzó funkciókat.](developer-portal-implement-widgets.md) További információ a fejlesztői [portálok kiegészítőségével kapcsolatban.](api-management-howto-developer-portal.md#managed-vs-self-hosted)


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Lehet több fejlesztői portál egy API Management szolgáltatásban?

Egy felügyelt portállal és több saját üzemeltetett portállal is lehet. Az összes portál tartalma ugyanabban a API Management van tárolva, így azok azonosak lesznek. Ha meg szeretné különböztetni a portálok megjelenését és funkcióit, saját egyéni widgetekkel is futtathatja őket, amelyek dinamikusan szabják testre az oldalakat futásidőben, például az URL-cím alapján.

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Támogatja a portál Azure Resource Manager sablonokat és/vagy kompatibilis a API Management DevOps Resource Kittel?

Nem.

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Menti a portál tartalmát a biztonsági mentési/visszaállítási funkcióval a API Management?

Nem.

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Engedélyeznem kell további virtuális hálózati kapcsolatot a felügyelt portál függőségei számára?

A legtöbb esetben – nem.

Ha a API Management szolgáltatás belső virtuális hálózaton található, a fejlesztői portál csak a hálózaton belülről érhető el. A felügyeleti végpont gazdagépnevének a portál felügyeleti felületéhez való hozzáféréshez használt gépről a szolgáltatás belső VIP-nevére kell feloldva lennie. Győződjön meg arról, hogy a felügyeleti végpont regisztrálva van a DNS-ben. Hibás konfiguráció esetén a következő hibaüzenet jelenik meg: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Ha az API Management-szolgáltatás egy belső virtuális hálózatban található, és az Application Gateway-ból fér hozzá az internetről, engedélyezze a fejlesztői portálhoz és a felügyeleti végpontjaihoz való API Management. Előfordulhat, hogy le kell tiltania Web Application Firewall szabályokat. További [részletekért tekintse meg ezt](api-management-howto-integrate-internal-vnet-appgateway.md) a dokumentációs cikket.

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Hozzárendeltam egy egyéni API Management tartományhoz, és a közzétett portál nem működik

A tartomány frissítése után újra [](api-management-howto-developer-portal-customize.md#publish) közzé kell tennie a portált, hogy a módosítások életbe lépnek.

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Hozzáadtam egy identitásszolgáltatót, de nem látom a portálon

Miután konfigurált egy identitásszolgáltatót (például Azure AD, Azure AD B2C), [](api-management-howto-developer-portal-customize.md#publish) újból közzé kell tennie a portált, hogy a módosítások életbe lépnek. Győződjön meg arról, hogy a fejlesztői portál oldalain szerepel az OAuth-gombok widget.

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>Beállítottom a delegálást, de a portál nem használja

A delegálás beállítása után [](api-management-howto-developer-portal-customize.md#publish) újra közzé kell tennie a portált, hogy a módosítások életbe lépnek.

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>A többi API Management konfigurációs módosítás nem lett propagálva a fejlesztői portálon

A konfiguráció legtöbb változásának (például a virtuális hálózatnak, a bejelentkezésnek és a termék feltételeinek) a portált [kell újra közzétenné.](api-management-howto-developer-portal-customize.md#publish)

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> CORS-hibaüzenetet kapok az interaktív konzol használata során

Az interaktív konzol ügyféloldali API-kérést készít a böngészőből. A CORS-probléma megoldásához adja [hozzá a CORS-szabályzatot](api-management-cross-domain-policies.md#CORS) az API(k)hoz.

A CORS-szabályzat állapotát a portál  áttekintési szakaszában ellenőrizheti a API Management szolgáltatásban a Azure Portal. A figyelmeztető mező hiányzó vagy helytelenül konfigurált szabályzatot jelez.

> [!NOTE]
> 
> A rendszer csak egy CORS-szabályzatot hajt végre. Ha több CORS-szabályzatot adott meg (például az API és a minden API szintjén), előfordulhat, hogy az interaktív konzol nem a várt módon működik.

![A CORS-szabályzat állapotának ellenőrzését bemutató képernyőkép.](media/developer-portal-faq/cors-azure-portal.png)

A CORS-szabályzat automatikus alkalmazása a **CORS engedélyezése gombra kattintva.**

A CORS manuálisan is engedélyezhető.

1. Válassza a **Manuális alkalmazás a** globális szinten hivatkozást a létrehozott szabályzatkódhoz.
2. Lépjen a **minden API-ra** a API Management API-k szakaszában a Azure Portal. 
3. Válassza ki **</>** az ikont a **Bejövő feldolgozás szakaszban.**
4. Szúrja be a szabályzatot **<inbound>** az XML-fájl szakaszba. Győződjön meg **<origin>** arról, hogy az érték megegyezik a fejlesztői portál tartományával.

> [!NOTE]
> 
> Ha a CORS-szabályzatot az API(k) hatókör helyett a Termék hatókörben alkalmazza, és az API fejlécen keresztüli előfizetői kulcsos hitelesítést használ, a konzol nem fog működni.
>
> A böngésző automatikusan ki `OPTIONS` ad egy HTTP-kérést, amely nem tartalmaz előfizetési kulcsot tartalmazó fejlécet. A hiányzó előfizetői API Management nem társíthatja a hívást termékkel, ezért nem tudja alkalmazni `OPTIONS` a CORS-szabályzatot.
>
> Áthidaló megoldásként átadhatja az előfizetői kulcsot egy lekérdezési paraméterben.

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>Mi a CORS-proxy funkció, és mikor érdemes használni?

Válassza a **CORS-proxy használata** lehetőséget az API-művelet részletei vezérlő konfigurációjában az interaktív konzol API-hívásait a portál háttérkiszolgálóján keresztül az API Management szolgáltatásban. Ebben a konfigurációban már nem kell CORS-szabályzatot alkalmaznia az API-kra, és nincs szükség az átjáróvégponthoz való csatlakozásra a helyi gépről. Ha az API-kat egy saját maga által üzemeltetett átjárón keresztül teszi elérhetővé, vagy ha a szolgáltatás egy virtuális hálózatban található, szükség van az API Management háttérszolgáltatása és az átjáró között való kapcsolatra. Ha a saját maga által üzemeltetett portált használja, adja meg a portál háttérvégpontját a konfigurációs fájlokban található `backendUrl` beállítással. Ellenkező esetben a saját üzemeltetett portál nem fogja tudni a háttérszolgáltatás helyét.

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Milyen engedélyekre van szükségem a fejlesztői portál szerkesztéséhez?

Ha a portál felügyeleti módban való megnyitásakor hibaüzenet jelenik meg, előfordulhat, hogy nem rendelkezik a szükséges `Oops. Something went wrong. Please try again later.` engedélyekkel (Azure RBAC).

Az örökölt portálok a szolgáltatás hatókörében () megkövetelték az engedélyt ahhoz, hogy a felhasználói rendszergazda hozzáférjen a `Microsoft.ApiManagement/service/getssotoken/action` `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` portálhoz. Az új portálnak a hatókörben kell `Microsoft.ApiManagement/service/users/token/action` engedélyt `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` kérnie.

A következő PowerShell-szkript használatával létrehozhat egy szerepkört a szükséges engedéllyel. Ne felejtse el módosítani a `<subscription-id>` paramétert. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
A szerepkört a létrehozása után bármely felhasználó számára meg lehet adni a Access Control **(IAM)** Azure Portal szakaszában. Ha ezt a szerepkört hozzárendeli egy felhasználóhoz, az engedély a szolgáltatás hatókörében lesz hozzárendelve. A felhasználó sas-jogkivonatokat hozhat létre a szolgáltatás *bármely* felhasználója nevében. Ezt a szerepkört legalább a szolgáltatás rendszergazdájához kell rendelni. A következő PowerShell-parancs bemutatja, hogyan rendelheti hozzá a szerepkört a legalacsonyabb hatókörű felhasználókhoz, hogy elkerülje a szükségtelen engedélyek `user1` megadását a felhasználónak: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Miután a felhasználó megadta az engedélyeket, ki kell jelentkeznie, majd újra be kell jelentkeznie a Azure Portal, hogy az új engedélyek életbe lépnek.

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>A hibaüzenet `Unable to start the portal. See if settings are specified correctly (...)` jelenik meg

Ez a hiba akkor jelenik meg, ha a `GET` hívása `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` meghiúsul. A hívást a böngészőből a portál felügyeleti felülete bocsátotta ki.

Ha a API Management szolgáltatás virtuális hálózatban található, tekintse meg a virtuális hálózatok [kapcsolati kérdését.](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies)

A hívási hibát egy TLS/SSL-tanúsítvány is okozhatja, amely egy egyéni tartományhoz van rendelve, és a böngésző nem megbízhatónak. Ennek érdekében eltávolíthatja a felügyeleti végpont egyéni tartományát, API Management a rendszer vissza fog menni az alapértelmezett végpontra egy megbízható tanúsítvánnyal.

## <a name="whats-the-browser-support-for-the-portal"></a>Milyen böngészőtámogatást biztosít a portál?

| Böngésző                     | Támogatott       |
|-----------------------------|-----------------|
| Apple Safari                | Igen<sup>1</sup> |
| Google Chrome               | Igen<sup>1</sup> |
| Microsoft Edge              | Igen<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Igen<sup>1</sup> |

 <small><sup>1</sup> A két legújabb éles verzióban támogatott.</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>A saját üzemeltetett portál helyi fejlesztése már nem működik

Ha a fejlesztői portál helyi verziója nem tud adatokat menteni vagy lekérni a tárfiókból vagy API Management-példányból, előfordulhat, hogy az SAS-jogkivonatok lejártak. Ezt új jogkivonatok generálása oldhatja meg. Útmutatásért tekintse meg a fejlesztői portál saját [gazdagépként való használatával kapcsolatban oktatóanyagot.](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings)

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>Hogyan távolítható el a fejlesztői portál saját szolgáltatáshoz API Management tartalma?

Adja meg a szükséges paramétereket a fejlesztői portál GitHub-adattárában található szkriptben, és `scripts.v3/cleanup.bat` futtassa a [](https://github.com/Azure/api-management-developer-portal)szkriptet

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>Hogyan engedélyezni az egyszeri bejelentkezéses (SSO) hitelesítést a saját üzemeltetett fejlesztői portálon?

Más hitelesítési módszerek mellett a fejlesztői portál támogatja az egyszeri bejelentkezést (SSO). Ezzel a módszerrel való hitelesítéshez a lekérdezési paraméterben a jogkivonattal kell hívnia a `/signin-sso` metódust:

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>Felhasználói jogkivonatok létrehozása
Felhasználóspecifikus *jogkivonatokat* (beleértve a rendszergazdai jogkivonatokat is) az alkalmazás Megosztott hozzáférési jogkivonat lekért műveletének [használatával API Management REST API.](/rest/api/apimanagement/apimanagementrest/api-management-rest) [](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken)

> [!NOTE]
> A jogkivonatnak URL-kódolásúnak kell lennie.

## <a name="next-steps"></a>Következő lépések

További információ az új fejlesztői portálról:

- [A felügyelt fejlesztői portál elérése és testreszabása](api-management-howto-developer-portal-customize.md)
- [A portál saját üzemeltetett verziójának beállítása](developer-portal-self-host.md)
- [Saját widget implementálja](developer-portal-implement-widgets.md)

Tallózás más erőforrások között:

- [GitHub-adattár a forráskódmal](https://github.com/Azure/api-management-developer-portal)

