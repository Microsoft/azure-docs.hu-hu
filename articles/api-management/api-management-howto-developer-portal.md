---
title: Az Azure fejlesztői portáljának áttekintése API Management
titleSuffix: Azure API Management
description: Ismerkedjen meg a fejlesztői portálon API Management – egy testreszabható webhely, ahol az API-felhasználók megismerhetik az API-kat.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 30487218fc95be75d22b5a9ea5a6dbc224ffd025
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93074797"
---
# <a name="overview-of-the-developer-portal"></a>A fejlesztői portál áttekintése

A fejlesztői portál egy automatikusan létrehozott, teljesen testreszabható webhely, amely az API-k dokumentációját tartalmazza. Ahol az API-felhasználók felfedezhetik az API-kat, megtudhatják, hogyan használhatják őket, hozzáférést igényelhetnek, és kipróbálhatja őket.

Ez a cikk a API Management fejlesztői portál saját üzemeltetésű és felügyelt verziói közötti különbségeket ismerteti. Emellett választ ad a gyakran ismételt kérdésekre is.

![API Management fejlesztői portál](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Áttelepítés az örökölt portálról

> [!IMPORTANT]
> A régi fejlesztői portál elavult, és csak a biztonsági frissítéseket fogja kapni. Továbbra is használhatja, a szokásos módon, a 2023 októberében való kivonulás után, amikor az összes API Management szolgáltatásból el lesz távolítva.

Az új fejlesztői portálra való áttelepítést a [dedikált dokumentációs cikk](developer-portal-deprecated-migration.md)ismerteti.

## <a name="customization-and-styling"></a>Testreszabás és formázás

A fejlesztői portál testre szabható, és a beépített, fogd és vidd stílusú vizualizációs szerkesztővel végezhető el. További részletekért tekintse meg [ezt az oktatóanyagot](api-management-howto-developer-portal-customize.md) .

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Bővíthetőség

A API Management szolgáltatás beépített, mindig naprakész, **felügyelt** fejlesztői portált tartalmaz. A Azure Portal felületéről is elérheti.

Ha olyan egyéni logikával kell kiterjesztenie, amely nem támogatott, akkor módosítható a kód. A portál kód a [GitHub-tárházban érhető el][1]. Megvalósíthat például egy olyan új widgetet, amely integrálható egy külső gyártótól származó támogatási rendszerrel. Új funkciók megvalósításakor az alábbi lehetőségek közül választhat:

- Az eredményül kapott portált a API Management szolgáltatáson kívül is **üzemeltetheti** . Ha önálló üzemeltetést végez a portálon, annak karbantartója lesz, és Ön felelős a frissítésért. Az Azure támogatási szolgálata csak a saját üzemeltetésű portálok alapszintű beállítására korlátozódik, ahogyan azt az [adattár wiki szakasza][2]is dokumentálja.
- Nyisson meg egy lekéréses kérelmet a API Management csapatának, hogy egyesítse az új funkciókat a **felügyelt** portálon.

A bővíthetőséggel kapcsolatos részletekért és utasításokért tekintse meg a [GitHub-tárházat][1] és [a widget megvalósításával kapcsolatos oktatóanyagokat][3]. A [felügyelt portál testreszabására vonatkozó oktatóanyag](api-management-howto-developer-portal-customize.md) végigvezeti a portál felügyeleti paneljén, amely a felügyelt és a **saját** **üzemeltetésű** verziók esetében gyakori.

## <a name="frequently-asked-questions"></a><a name="faq"></a> Gyakori kérdések

Ebben a szakaszban a fejlesztői portál általános jellegű kérdéseit választjuk. A saját üzemeltetésű verzióra vonatkozó kérdésekért tekintse meg a [GitHub-adattár wiki szakaszát](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Hogyan lehet áttérni a portál előzetes verziójáról?

Amikor először indította el a fejlesztői portál előzetes verzióját, kiépítte az alapértelmezett tartalom előzetes verzióját a API Management szolgáltatásban. Az általánosan elérhető verzióban az alapértelmezett tartalom jelentősen módosult. Az alapértelmezett tartalom előzetes verziója például nem tartalmazza a OAuth gombokat a bejelentkezési lapokon, az API-k megjelenítéséhez különböző widgeteket használ, és a fejlesztői portál oldalai strukturálása terén korlátozott képességekre támaszkodik. Annak ellenére, hogy a tartalomban különbségek vannak, a portál motorja (beleértve az alapul szolgáló widgeteket is) automatikusan frissül minden alkalommal, amikor közzéteszi a fejlesztői portált.

Ha a portál előzetes verzióján alapuló, nagy mértékben testre szabott, továbbra is használhatja azt, és az új widgeteket manuálisan helyezheti el a portál oldalain. Ellenkező esetben javasoljuk, hogy cserélje le a portál tartalmát az új alapértelmezett tartalomra.

A felügyelt portál tartalmának alaphelyzetbe állításához válassza a **tartalom alaphelyzetbe állítása** lehetőséget az **Operations (műveletek** ) menü szakaszban. Ez a művelet eltávolítja a portál összes tartalmát, és új alapértelmezett tartalmat fog kiépíteni. A fejlesztői portál minden testreszabását és módosítását elveszítjük. **Ez a művelet nem vonható vissza**.

![Portál tartalmának visszaállítása](media/api-management-howto-developer-portal/reset-content.png)

Ha a saját üzemeltetésű verziót használja, `scripts.v2/cleanup.bat` a GitHub-adattárból futtassa a és a `scripts.v2/generate.bat` szkripteket, és távolítsa el a meglévő tartalmakat, és hozzon létre új tartalmat. Győződjön meg róla, hogy a legújabb kiadásra frissíti a portál kódját a GitHub-adattárból.

Ha először a 2019-es általános elérhetőségi bejelentés után fér hozzá a portálhoz, akkor az új alapértelmezett tartalmat már be kell állítani, és nincs szükség további műveletre.

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>A portálon nem támogatottak a szükséges funkciók

A [GitHub-tárházban][1] megnyithat egy szolgáltatási kérelmet, vagy [saját maga is megvalósíthatja a hiányzó funkciókat][3]. További részletekért tekintse meg a fenti **bővíthetőségi** szakaszt.

### <a name="how-can-i-automate-portal-deployments"></a><a id="automate"></a> Hogyan automatizálható a portál üzembe helyezése?

A REST API keresztül programozott módon érheti el és kezelheti a fejlesztői portál tartalmát, függetlenül attól, hogy felügyelt vagy saját üzemeltetésű verziót használ-e.

Az API-t [a GitHub-adattár wiki szakasza ismerteti][2]. A portál tartalmának a környezetek közötti áttelepítésének automatizálására is használható – például egy tesztkörnyezetben az éles környezetbe. Erről a folyamatról a GitHubról szóló [cikkben](https://aka.ms/apimdocs/migrateportal) olvashat bővebben.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Hogyan a felügyelt rendszerről a saját üzemeltetésű verzióra?

Tekintse [meg a githubon a fejlesztői portál adattárának wiki szakaszának][2]részletes cikkét.

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Használhatok több fejlesztői portált egy API Management szolgáltatásban?

Egy felügyelt portált és több saját üzemeltetésű portált is használhat. Az összes portál tartalmát ugyanabban a API Management szolgáltatásban tárolja a rendszer, így azok azonosak lesznek. Ha szeretné megkülönböztetni a portálok megjelenését és funkcióit, saját maga is üzemeltetheti azokat a saját egyéni widgetekkel, amelyek dinamikusan testreszabják a futásidejű oldalakat, például az URL-cím alapján.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Támogatja a portál a Azure Resource Manager sablonokat, és/vagy kompatibilis-e a API Management DevOps Resource Kit-vel?

Nem.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>A portál tartalma a API Management biztonsági mentési/helyreállítási funkcióival lett mentve?

Nem.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Engedélyezni kell további VNet-kapcsolatot a felügyelt portál függőségeihez?

A legtöbb esetben – nem.

Ha a API Management szolgáltatás belső VNet található, a fejlesztői portál csak a hálózaton belülről érhető el. A felügyeleti végpont állomásneve fel kell oldania a szolgáltatás belső VIP-címére a portál felügyeleti felületének elérésére használt gépről. Győződjön meg arról, hogy a felügyeleti végpont regisztrálva van a DNS-ben. Helytelen konfiguráció esetén a következő hibaüzenet jelenik meg: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Ha a API Management szolgáltatás belső VNet van, és az internetről Application Gateway keresztül fér hozzá, ügyeljen arra, hogy engedélyezze a kapcsolatot a fejlesztői portálon és a API Management felügyeleti végpontján. Előfordulhat, hogy le kell tiltania a webalkalmazási tűzfalszabályok szabályait. További részletekért tekintse meg [ezt a dokumentációt](api-management-howto-integrate-internal-vnet-appgateway.md) .

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Egyéni API Management tartományt rendeltem hozzá, és a közzétett portál nem működik

A tartomány frissítése után újra közzé kell tennie [a portált](api-management-howto-developer-portal-customize.md#publish) , hogy a módosítások életbe lépnek.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Felvettem egy identitás-szolgáltatót, és nem látom a portálon

Miután konfigurált egy identitás-szolgáltatót (például az Azure AD-t, Azure AD B2C), újra [közzé kell tennie a portált](api-management-howto-developer-portal-customize.md#publish) , hogy a módosítások életbe lépnek. Győződjön meg arról, hogy a fejlesztői portál lapjai tartalmazzák a OAuth gombok Widgetet.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Beállítottam a delegálást, és a portál nem használja

A delegálás beállítása után újra közzé kell tennie [a portált](api-management-howto-developer-portal-customize.md#publish) , hogy a módosítások életbe lépnek.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>A többi API Management konfiguráció módosítása nem lett propagálva a fejlesztői portálon

A legtöbb konfigurációs módosítás (például a VNet, a bejelentkezés, a termékek használati feltételei) újra közzé kell tennie [a portált](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> CORS hibaüzenetet kapok az interaktív konzol használatakor

Az interaktív konzol ügyféloldali API-kérést tesz elérhetővé a böngészőből. Javítsa ki a CORS-problémát úgy, hogy hozzáad [egy CORS-szabályzatot](api-management-cross-domain-policies.md#CORS) az API (ok) hoz.

A CORS szabályzat állapotát a Azure Portal API Management szolgáltatásának **portál áttekintés** szakaszában tekintheti meg. A figyelmeztetési mező egy hiányzó vagy helytelenül konfigurált szabályzatot jelez.

![Képernyőkép, amely bemutatja, hol ellenőrizhető a CORS-szabályzat állapota.](media/api-management-howto-developer-portal/cors-azure-portal.png)

A CORS házirend automatikus alkalmazása a **CORS engedélyezése** gombra kattintva.

A CORS is engedélyezheti manuálisan is.

1. Jelölje be a **manuális alkalmazása a globális szinten** hivatkozásra a generált szabályzat megjelenítéséhez.
2. A Azure Portal a API Management szolgáltatás **API** -k szakaszában navigáljon az **összes API** -hoz.
3. Válassza ki az **</>** ikont a **bejövő feldolgozás** szakaszban.
4. Szúrja be a szabályzatot az **<inbound>** XML-fájl szakaszában. Győződjön meg arról, hogy az **<origin>** érték megegyezik a fejlesztői portál tartományával.

> [!NOTE]
> 
> Ha a CORS szabályzatot a termék hatókörében alkalmazza, az API-k hatóköre helyett, és az API az előfizetés-kulcs hitelesítését használja egy fejlécen keresztül, a konzol nem fog működni.
>
> A böngésző automatikusan kiadja a beállítások HTTP-kérését, amely nem tartalmaz az előfizetési kulccsal rendelkező fejlécet. A hiányzó előfizetési kulcs miatt API Management nem tudja hozzárendelni a beállításokat a termékhez, így nem tudja alkalmazni a CORS házirendet.
>
> Megkerülő megoldásként átadhatja az előfizetési kulcsot egy lekérdezési paraméterben.

> [!NOTE]
> 
> Csak egy CORS házirend kerül végrehajtásra. Ha több CORS-házirendet (például az API-t és az All-API-kat) adott meg, előfordulhat, hogy az interaktív konzol nem a várt módon fog működni.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Milyen engedélyekre van szükségem a fejlesztői portál szerkesztéséhez?

Ha úgy látja a `Oops. Something went wrong. Please try again later.` hibát, amikor a portált felügyeleti módban nyitja meg, előfordulhat, hogy nem rendelkezik a szükséges engedélyekkel (Azure RBAC).

Az örökölt portáloknak a `Microsoft.ApiManagement/service/getssotoken/action` szolgáltatás hatókörében () kell megadniuk az engedélyt `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` ahhoz, hogy a felhasználói rendszergazda hozzáférjen a portálokhoz. Az új portálon engedély szükséges a `Microsoft.ApiManagement/service/users/token/action` hatókörben `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

A következő PowerShell-parancsfájl használatával létrehozhat egy szerepkört a szükséges engedélyekkel. Ne felejtse el módosítani a `<subscription-id>` paramétert. 

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
 
A szerepkör létrehozása után bármely felhasználó számára megadható a Azure Portal **Access Control (iam)** szakasza. Ha ezt a szerepkört hozzárendeli a felhasználóhoz, az engedélyt a szolgáltatás hatókörében kell kiosztania. A felhasználó a szolgáltatás *bármely* felhasználója nevében képes sas-jogkivonatokat előállítani. Legalább ezt a szerepkört a szolgáltatás rendszergazdájához kell rendelni. A következő PowerShell-parancs bemutatja, hogyan rendelhető hozzá a szerepkör a `user1` legalacsonyabb hatókörű felhasználóhoz, hogy ne kelljen felesleges engedélyeket biztosítani a felhasználónak: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Miután megadta az engedélyeket egy felhasználó számára, a felhasználónak ki kell jelentkeznie, majd újra be kell jelentkeznie a Azure Portal az új engedélyek érvénybe léptetéséhez.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>`Unable to start the portal. See if settings are specified correctly (...)`Hibaüzenetet látok

Ez a hiba akkor jelenik `GET` meg, ha `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` sikertelen hívás történik. A hívást a böngésző a portál felügyeleti felülete adja ki.

Ha a API Management szolgáltatás VNet található – tekintse meg a fenti VNet-kapcsolat kérdését.

A hívási hibát a TLS/SSL-tanúsítvány is okozhatja, amely egy egyéni tartományhoz van rendelve, és a böngésző nem tartja megbízhatónak. Enyhítő megoldásként eltávolíthatja a felügyeleti végpont egyéni tartományát – API Management a megbízható tanúsítvánnyal rendelkező alapértelmezett végpontra kerül vissza.

### <a name="whats-the-browser-support-for-the-portal"></a>Milyen böngésző-támogatást nyújt a portál?

| Böngésző                     | Támogatott       |
|-----------------------------|-----------------|
| Apple Safari                | Igen<sup>1</sup> |
| Google Chrome               | Igen<sup>1</sup> |
| Microsoft Edge              | Igen<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Igen<sup>1</sup> |

 <small><sup>1</sup> támogatott a két legújabb üzemi verzióban.</small>

## <a name="next-steps"></a>Következő lépések

További információ az új fejlesztői portálról:

- [A felügyelt fejlesztői portál elérése és testreszabása](api-management-howto-developer-portal-customize.md)
- [A portál saját üzemeltetésű verziójának beállítása][2]
- [Saját widget implementálása][3]

Egyéb erőforrások tallózása:

- [GitHub-adattár a forráskódtal][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
