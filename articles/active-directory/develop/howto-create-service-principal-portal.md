---
title: Azure AD-alkalmazás & egyszerű szolgáltatásnév létrehozása a portálon
titleSuffix: Microsoft identity platform
description: Hozzon létre egy új Azure Active Directory alkalmazás & egyszerű szolgáltatásnév használatával az erőforrásokhoz való hozzáférés kezeléséhez a Azure Resource Manager-ben szerepköralapú hozzáférés-vezérléssel.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: 8a4a4153261b93b3b17641e8561962c274570bd0
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104212"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Útmutató: Az erőforrásokhoz hozzáférő Azure AD-alkalmazás és -szolgáltatásnév létrehozása a portálon

Ez a cikk bemutatja, hogyan hozhat létre egy új Azure Active Directory (Azure AD) alkalmazást és egyszerű szolgáltatást, amely a szerepköralapú hozzáférés-vezérléssel használható. Ha olyan alkalmazásokkal, üzemeltetett szolgáltatásokkal vagy automatizált eszközökkel rendelkezik, amelyeknek szüksége van az erőforrások elérésére vagy módosítására, létrehozhat egy identitást az alkalmazáshoz. Ezt az identitást szolgáltatásnévnek nevezzük. Az erőforrásokhoz való hozzáférést az egyszerű szolgáltatáshoz rendelt szerepkörök korlátozzák, így szabályozhatja, hogy mely erőforrások érhetők el és milyen szinten. Biztonsági okokból az automatizált eszközök esetében minden esetben ajánlott a szolgáltatásnevek használata a felhasználói identitással való bejelentkezés helyett.

Ez a cikk bemutatja, hogyan hozhatja létre az egyszerű szolgáltatásnevet a Azure Portalban a portál használatával. Egy egybérlős alkalmazásra koncentrál, amelyben az alkalmazás csak egy szervezeten belül fut. Általában egybérlős alkalmazásokat használ a szervezeten belül futó üzletági alkalmazásokhoz.  [A Azure PowerShell használatával is létrehozhat egy egyszerű szolgáltatást](howto-authenticate-service-principal-powershell.md).

> [!IMPORTANT]
> Egyszerű szolgáltatásnév létrehozása helyett érdemes felügyelt identitásokat használni az Azure-erőforrásokhoz az alkalmazás identitásához. Ha a kód olyan szolgáltatáson fut, amely támogatja a felügyelt identitásokat, és hozzáfér az Azure AD-hitelesítést támogató erőforrásokhoz, a felügyelt identitások jobb megoldást biztosítanak Önnek. Ha többet szeretne megtudni az Azure-erőforrások felügyelt identitásáról, beleértve a jelenleg támogatott szolgáltatásokat, tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](../managed-identities-azure-resources/overview.md)című témakört.

## <a name="app-registration-app-objects-and-service-principals"></a>Alkalmazások regisztrálása, alkalmazás-objektumok és egyszerű szolgáltatások
A Azure Portal használatával nem lehet közvetlenül létrehozni egyszerű szolgáltatásnevet.  Amikor az Azure Portalon keresztül regisztrálja az alkalmazást, a rendszer automatikusan létrehoz egy Application objektumot és egy egyszerű szolgáltatásnevet a saját kezdőkönyvtár vagy bérlője számára.  További információ az alkalmazás regisztrációja, az alkalmazásobjektumok és az egyszerű szolgáltatások közötti kapcsolatról [: alkalmazások és egyszerű szolgáltatások objektumainak](app-objects-and-service-principals.md)olvasása Azure Active Directory.

## <a name="permissions-required-for-registering-an-app"></a>Az alkalmazások regisztrálásához szükséges engedélyek

Megfelelő engedélyekkel kell rendelkeznie az alkalmazások Azure AD-Bérlővel való regisztrálásához, és az alkalmazáshoz az Azure-előfizetéshez tartozó szerepkört kell rendelni.

### <a name="check-azure-ad-permissions"></a>Azure AD-engedélyek keresése

1. Válassza a **Azure Active Directory** lehetőséget.
1. Jegyezze fel a szerepkört. Ha rendelkezik a **felhasználói** szerepkörrel, meg kell győződnie arról, hogy a nem rendszergazdák regisztrálhatják az alkalmazásokat.

   ![Keresse meg a szerepkört. Ha Ön felhasználó, győződjön meg arról, hogy a nem rendszergazdák regisztrálhatnak alkalmazásokat](./media/howto-create-service-principal-portal/view-user-info.png)

1. A bal oldali ablaktáblán válassza a **felhasználói beállítások** lehetőséget.
1. Keresse meg a **Alkalmazásregisztrációk** beállítást. Ezt az értéket csak rendszergazda állíthatja be. Ha az **Igen** értékre van állítva, akkor az Azure ad-bérlő bármelyik felhasználója regisztrálhat egy alkalmazást.

Ha az alkalmazás regisztrációja **nem** értékre van állítva, akkor csak a rendszergazdai szerepkörrel rendelkező felhasználók regisztrálhatják az ilyen típusú alkalmazásokat. A rendelkezésre álló rendszergazdai szerepkörökről és az egyes szerepkörökhöz megadott Azure AD-engedélyekről az [elérhető szerepkörök](../roles/permissions-reference.md#available-roles) és [szerepkör-engedélyek](../roles/permissions-reference.md#role-permissions) című szakaszban talál további információt. Ha a fiókja hozzá van rendelve a felhasználói szerepkörhöz, de az alkalmazás regisztrációs beállítása a rendszergazda felhasználókra korlátozódik, kérje meg a rendszergazdát, hogy rendeljen hozzá egy olyan rendszergazdai szerepkört, amely az alkalmazások regisztrálásának minden aspektusát létrehozhatja és kezelheti, illetve lehetővé teheti a felhasználók számára, hogy regisztrálják az alkalmazásokat.

### <a name="check-azure-subscription-permissions"></a>Azure-előfizetési engedélyek keresése

Az Azure-előfizetésében a fióknak `Microsoft.Authorization/*/Write` hozzáféréssel kell rendelkeznie ahhoz, hogy szerepkört rendeljen egy ad-alkalmazáshoz. Ezt a műveletet a [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) szerepkör vagy a [Felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör végezheti el. Ha a fiókja hozzá van rendelve a **közreműködő** szerepkörhöz, nincs megfelelő engedélye. Hibaüzenet jelenik meg, amikor az egyszerű szolgáltatás szerepkört rendeli hozzá.

Az előfizetési engedélyek ellenőrzését:

1. Keresse meg és válassza ki az **előfizetéseket**, vagy válassza az **előfizetések** lehetőséget a **kezdőlapon** .

   ![Keresés](./media/howto-create-service-principal-portal/select-subscription.png)

1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni az egyszerű szolgáltatásnevet.

   ![Előfizetés kiválasztása hozzárendeléshez](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetést, válassza a **globális előfizetések szűrőt**. Győződjön meg arról, hogy a portálon a kívánt előfizetés van kiválasztva.

1. Válassza **a saját engedélyek** lehetőséget. Ezután **kattintson ide az előfizetés teljes hozzáférés részleteinek megtekintéséhez**.

   ![Válassza ki azt az előfizetést, amelyben létre kívánja hozni a szolgáltatásnevet a következőben:](./media/howto-create-service-principal-portal/view-details.png)

1. Válassza ki a **szerepkör-hozzárendelések** **nézet** elemet a hozzárendelt szerepkörök megtekintéséhez, és állapítsa meg, hogy rendelkezik-e megfelelő engedélyekkel ahhoz, hogy szerepkört rendeljen egy ad-alkalmazáshoz. Ha nem, kérje meg az előfizetés rendszergazdáját, hogy vegye fel Önt a felhasználói hozzáférés rendszergazdai szerepkörbe. A következő ábrán a felhasználó hozzárendeli a tulajdonosi szerepkört, ami azt jelenti, hogy a felhasználó rendelkezik a megfelelő engedélyekkel.

   ![Ez a példa azt mutatja be, hogy a felhasználó hozzá van rendelve a tulajdonosi szerepkörhöz](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Alkalmazás regisztrálása az Azure AD-ben és egyszerű szolgáltatásnév létrehozása

Ugorjon egyenesen az identitás létrehozásához. Ha probléma lép fel, ellenőrizze a [szükséges engedélyeket](#permissions-required-for-registering-an-app) annak biztosításához, hogy a fiókja létre tudja hozni az identitást.

1. Jelentkezzen be az Azure-fiókjába a <a href="https://portal.azure.com/" target="_blank">Azure Portalon</a>keresztül.
1. Válassza a **Azure Active Directory** lehetőséget.
1. Válassza az **Alkalmazásregisztrációk** lehetőséget.
1. Válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás nevét. Válasszon egy támogatott számlatípust, amely meghatározza, hogy kik használhatják az alkalmazást. Az **átirányítási URI** területen válassza a **web** lehetőséget a létrehozni kívánt alkalmazás típusához. Adja meg azt az URI-t, ahová a hozzáférési tokent elküldi. [Natív alkalmazás](../manage-apps/application-proxy-configure-native-client-application.md)hitelesítő adatai nem hozhatók létre. Az adott típus nem használható automatikus alkalmazáshoz. Az értékek beállítása után válassza a **regisztráció** lehetőséget.

   ![Adja meg az alkalmazás nevét](./media/howto-create-service-principal-portal/create-app.png)

Létrehozott egy Azure AD-alkalmazást és egy egyszerű szolgáltatásnevet.

## <a name="assign-a-role-to-the-application"></a>Szerepkör társítása az alkalmazáshoz

Az előfizetéshez tartozó erőforrások eléréséhez hozzá kell rendelnie egy szerepkört az alkalmazáshoz. Döntse el, melyik szerepkör kínálja a megfelelő engedélyeket az alkalmazáshoz. Az elérhető szerepkörökről az [Azure beépített szerepköreivel](../../role-based-access-control/built-in-roles.md)foglalkozó témakörben olvashat bővebben.

Megadhatja a hatókört az előfizetés, az erőforráscsoport vagy az erőforrás szintjén. Az engedélyek a hatókör alacsonyabb szintjein vannak örökölve. Ha például hozzáad egy alkalmazást az erőforráscsoport *olvasó* szerepköréhez, az azt jelenti, hogy elolvashatja az erőforráscsoportot és a benne található összes erőforrást.

1. A Azure Portal válassza ki azt a hatóköri szintet, amelyhez az alkalmazást hozzá szeretné rendelni. Ha például egy szerepkört szeretne hozzárendelni az előfizetés hatóköréhez, keresse meg és válassza ki az **előfizetések** elemet, vagy válassza az **előfizetések** lehetőséget a **kezdőlapon** .

   ![Például rendeljen hozzá egy szerepkört az előfizetés hatókörében](./media/howto-create-service-principal-portal/select-subscription.png)

1. Válassza ki azt az előfizetést, amelyhez hozzá kívánja rendelni az alkalmazást.

   ![Előfizetés kiválasztása hozzárendeléshez](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetést, válassza a **globális előfizetések szűrőt**. Győződjön meg arról, hogy a portálon a kívánt előfizetés van kiválasztva.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.
1. Válassza ki az alkalmazáshoz hozzárendelni kívánt szerepkört. Ha például engedélyezni szeretné, hogy az alkalmazás olyan műveleteket hajtson végre, mint például az **Újraindítás**, a példányok **elindítása** és **leállítása** , válassza ki a **közreműködő** szerepkört.  További információ az [elérhető szerepkörökről](../../role-based-access-control/built-in-roles.md) alapértelmezés szerint az Azure ad-alkalmazások nem jelennek meg az elérhető lehetőségek között. Az alkalmazás megkereséséhez keresse meg a nevet, és jelölje ki.

   ![Válassza ki az alkalmazáshoz hozzárendelni kívánt szerepkört](./media/howto-create-service-principal-portal/select-role.png)

1. A szerepkör hozzárendelésének befejezéséhez kattintson a **Mentés** gombra. Az alkalmazás az adott hatókörhöz tartozó szerepkörrel rendelkező felhasználók listájában jelenik meg.

Az egyszerű szolgáltatásnév be van állítva. Elkezdheti használni a parancsfájlok vagy alkalmazások futtatását. A szolgáltatásnév (engedélyek, felhasználó által küldött engedélyek) kezeléséhez tekintse meg, hogy mely felhasználók járult hozzá, tekintse át az engedélyeket, tekintse meg a bejelentkezéssel kapcsolatos információkat, és további információt a **vállalati alkalmazásokban**.

A következő szakasz bemutatja, hogyan kérheti le a programozott módon történő bejelentkezéshez szükséges értékeket.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>Bérlői és alkalmazás-azonosító értékek beolvasása a bejelentkezéshez

Ha programozott módon jelentkezik be, adja át a bérlő AZONOSÍTÓját a hitelesítési kérelemmel és az alkalmazás-AZONOSÍTÓval.  Szüksége lesz egy tanúsítványra vagy egy hitelesítési kulcsra is (a következő szakaszban leírtak szerint). Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. Válassza a **Azure Active Directory** lehetőséget.
1. Az Azure AD-ban **Alkalmazásregisztrációk** válassza ki az alkalmazást.
1. Másolja a címtár-(bérlői) azonosítót, és tárolja azt az alkalmazás kódjában.

    ![Másolja a könyvtárat (bérlői azonosítót), és tárolja azt az alkalmazás kódjában.](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    A címtár (bérlő) azonosítója az alapértelmezett címtár-Áttekintés lapon is megtalálható.

1. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában.

   ![Az alkalmazás (ügyfél) AZONOSÍTÓjának másolása](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>Hitelesítés: két lehetőség

Az egyszerű szolgáltatásokhoz kétféle hitelesítés érhető el: jelszó alapú hitelesítés (alkalmazás titkos kódja) és tanúsítványalapú hitelesítés. *Javasoljuk, hogy használjon tanúsítványokat*, de létrehozhat egy alkalmazás-titkot is.

### <a name="option-1-upload-a-certificate"></a>1. lehetőség: tanúsítvány feltöltése

Ha van ilyen, használhat meglévő tanúsítványt is.  Lehetőség van arra is, hogy önaláírt tanúsítványt *csak tesztelési célra* hozzon létre. Önaláírt tanúsítvány létrehozásához nyissa meg a PowerShellt, és futtassa a [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) parancsot a következő paraméterekkel a tanúsítvány létrehozásához a számítógép felhasználói tanúsítványtárolójában:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exportálja a tanúsítványt egy fájlba a Windows Vezérlőpultján elérhető [felhasználói tanúsítvány kezelése](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC beépülő modul használatával.

1. Válassza a **Start** menü **Futtatás** elemét, majd írja be a **certmgr. msc parancsot**.

   Megjelenik az aktuális felhasználóhoz tartozó tanúsítványkezelő eszköz.

1. A tanúsítványok megtekintéséhez a bal oldali ablaktábla **tanúsítványok-aktuális felhasználó** területén bontsa ki a **személyes** könyvtárat.
1. Kattintson a jobb gombbal a létrehozott tanúsítványra, válassza a **minden feladat – >exportálás** lehetőséget.
1. Kövesse a tanúsítvány exportálása varázslót.  Ne exportálja a titkos kulcsot, és exportálja a-ba. CER-fájl.

A tanúsítvány feltöltése:

1. Válassza a **Azure Active Directory** lehetőséget.
1. Az Azure AD-ban **Alkalmazásregisztrációk** válassza ki az alkalmazást.
1. Válassza ki a **tanúsítványok & Secrets** elemet.
1. Válassza a **tanúsítvány feltöltése** lehetőséget, és válassza ki a tanúsítványt (egy meglévő tanúsítványt vagy az exportált önaláírt tanúsítványt).

    ![Válassza a tanúsítvány feltöltése lehetőséget, és válassza ki a hozzáadni kívánt elemet](./media/howto-create-service-principal-portal/upload-cert.png)

1. Válassza a **Hozzáadás** lehetőséget.

Miután regisztrálta a tanúsítványt az alkalmazással az alkalmazás regisztrációs portálján, engedélyezze az ügyfélalkalmazás kódját a tanúsítvány használatához.

### <a name="option-2-create-a-new-application-secret"></a>2. lehetőség: új alkalmazás titkos kódjának létrehozása

Ha úgy dönt, hogy nem használ tanúsítványt, létrehozhat egy új alkalmazás-titkot.

1. Válassza a **Azure Active Directory** lehetőséget.
1. Az Azure AD-ban **Alkalmazásregisztrációk** válassza ki az alkalmazást.
1. Válassza ki a **tanúsítványok & Secrets** elemet.
1. Válassza ki az **ügyfél titkai-> új ügyfél titkát**.
1. Adja meg a titok leírását és időtartamát. Ha elkészült, válassza a **Hozzáadás** lehetőséget.

   Az ügyfél titkos kulcsának mentése után megjelenik az ügyfél titkos kulcsának értéke. Másolja ezt az értéket, mert később nem fogja tudni lekérni a kulcsot. Adja meg a kulcs értékét az alkalmazás-AZONOSÍTÓval, és jelentkezzen be alkalmazásként. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

   ![Másolja a titkos értéket, mert később nem lehet beolvasni](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Hozzáférési szabályzatok konfigurálása az erőforrásokon
Ne feledje, hogy további engedélyeket kell konfigurálnia az alkalmazás számára szükséges erőforrásokhoz. A [Key Vault hozzáférési házirendjeit is frissítenie](../../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) kell, hogy az alkalmazás hozzáférjen a kulcsokhoz, titkokhoz vagy tanúsítványokhoz.

1. A <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>navigáljon a kulcstartóhoz, és válassza a **hozzáférési szabályzatok** lehetőséget.
1. Válassza a **hozzáférési házirend hozzáadása** lehetőséget, majd válassza ki az alkalmazáshoz használni kívánt kulcs-, titkos és tanúsítvány-engedélyeket.  Válassza ki a korábban létrehozott szolgáltatásnevet.
1. A hozzáférési szabályzat hozzáadásához válassza a **Hozzáadás** lehetőséget, majd a **Mentés** gombra kattintva véglegesítse a módosításokat.
    ![Hozzáférési szabályzat hozzáadása](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [hozhat létre egyszerű szolgáltatásnevet a Azure PowerShell használatával](howto-authenticate-service-principal-powershell.md).
* A biztonsági szabályzatok megadásával kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* A felhasználók számára megadható vagy megtagadható elérhető műveletek listáját itt tekintheti meg: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../../role-based-access-control/resource-provider-operations.md).
* További információ az alkalmazás-regisztrációk **Microsoft Graph** használatával történő használatáról: [alkalmazások](/graph/api/resources/application) API-referenciája.
