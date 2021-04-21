---
title: Azure AD-alkalmazás & szolgáltatásnév létrehozása a portálon
titleSuffix: Microsoft identity platform
description: Hozzon létre egy Azure Active Directory alkalmazást & szolgáltatásnévvel az erőforrásokhoz való hozzáférés kezeléséhez szerepköralapú hozzáférés-vezérléssel a Azure Resource Manager.
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
ms.openlocfilehash: 3ccc340727a437b3b1e953ea5e742ecdf7f21d40
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814081"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Útmutató: Az erőforrásokhoz hozzáférő Azure AD-alkalmazás és -szolgáltatásnév létrehozása a portálon

Ez a cikk bemutatja, hogyan hozhat létre a szerepköralapú hozzáférés-vezérléssel használható új Azure Active Directory-alkalmazást (Azure AD) és egyszerű szolgáltatást. Ha olyan alkalmazásokkal, üzemeltetett szolgáltatásokkal vagy automatizált eszközökkel rendelkezik, amelyeknek erőforrásokhoz kell hozzáférniük vagy módosítaniuk kell őket, identitást hozhat létre az alkalmazáshoz. Ezt az identitást szolgáltatásnévnek nevezzük. Az erőforrásokhoz való hozzáférést a szolgáltatásnévhez rendelt szerepkörök korlátozzák, így Ön szabályozhatja, hogy mely erőforrásokhoz és milyen szinten lehet hozzáférni. Biztonsági okokból az automatizált eszközök esetében minden esetben ajánlott a szolgáltatásnevek használata a felhasználói identitással való bejelentkezés helyett.

Ez a cikk bemutatja, hogyan hozhatja létre a szolgáltatásnév a portálon a Azure Portal. Egybérlős alkalmazásra összpontosít, ahol az alkalmazásnak csak egy szervezeten belül kell futnia. Az egybérlős alkalmazásokat általában a szervezeten belül futó üzletági alkalmazásokhoz használják.  A [szolgáltatásnév Azure PowerShell is használható.](howto-authenticate-service-principal-powershell.md)

> [!IMPORTANT]
> Szolgáltatásnév létrehozása helyett érdemes lehet felügyelt identitásokat használni az Azure-erőforrásokhoz az alkalmazásidentitáshoz. Ha a kód olyan szolgáltatáson fut, amely támogatja a felügyelt identitásokat, és hozzáfér az Azure AD-hitelesítést támogató erőforrásokhoz, a felügyelt identitások a jobb lehetőségek az Ön számára. Ha többet szeretne megtudni az Azure-erőforrások felügyelt identitásiról, beleértve azokat a szolgáltatásokat, amelyek jelenleg támogatják, lásd: Mi [az Azure-erőforrások felügyelt identitása?](../managed-identities-azure-resources/overview.md).

## <a name="app-registration-app-objects-and-service-principals"></a>Alkalmazásregisztráció, alkalmazásobjektumok és szolgáltatásnév
A szolgáltatásnév nem hozható létre közvetlenül a Azure Portal.  Amikor az alkalmazásokat a Azure Portal regisztrálja, a rendszer automatikusan létrehoz egy alkalmazásobjektumot és egy egyszerű szolgáltatást a kezdőkönyvtárban vagy a bérlőben.  További információ az alkalmazásregisztráció, az alkalmazásobjektumok és a szolgáltatásnév közötti kapcsolatról: Alkalmazás- és [szolgáltatásnév-objektumok](app-objects-and-service-principals.md)a Azure Active Directory.

## <a name="permissions-required-for-registering-an-app"></a>Az alkalmazás regisztrálásához szükséges engedélyek

Megfelelő engedélyekkel kell rendelkeznie ahhoz, hogy egy alkalmazást regisztrálhat az Azure AD-bérlőben, és hozzárendelje az alkalmazáshoz egy szerepkört az Azure-előfizetésében.

### <a name="check-azure-ad-permissions"></a>Azure AD-engedélyek ellenőrzése

1. Válassza a **Azure Active Directory** lehetőséget.
1. Jegyezze fel a szerepkörét. Ha Felhasználói **szerepkört** tölt be, meg kell győződnie arról, hogy a nem rendszergazda felhasználók is regisztrálhatják az alkalmazásokat.

   ![Keresse meg a szerepkörét. Ha Ön felhasználó, győződjön meg arról, hogy a nem rendszergazdai jogosultságú felhasználók regisztrálják az alkalmazásokat](./media/howto-create-service-principal-portal/view-user-info.png)

1. A bal oldali panelen válassza a **Felhasználói beállítások lehetőséget.**
1. Ellenőrizze a **Alkalmazásregisztrációk** beállítást. Ezt az értéket csak rendszergazda állíthatja be. Ha az Igen **,** akkor az Azure AD-bérlő bármely felhasználója regisztrálhat egy alkalmazást.

Ha az alkalmazásregisztrációk beállítása **Nem,** csak a rendszergazdai szerepkörben rendelkező felhasználók regisztrálhatnak ilyen típusú alkalmazásokat. Az [elérhető rendszergazdai](../roles/permissions-reference.md#all-roles) szerepkörökről és az egyes szerepkörökhöz adott Azure AD-engedélyekről az Azure AD beépített szerepköreit lásd: Az Azure AD beépített szerepkörei. Ha a fiókja felhasználói szerepkörhöz van rendelve, de az alkalmazásregisztrációs beállítás csak rendszergazdai felhasználókra van korlátozva, kérje meg a rendszergazdát, hogy rendeljen hozzá egy olyan rendszergazdai szerepkört, amely az alkalmazásregisztrációk összes aspektusát létrehozhatja és kezelheti, vagy engedélyezze a felhasználóknak az alkalmazások regisztrálását.

### <a name="check-azure-subscription-permissions"></a>Azure-előfizetés engedélyeinek ellenőrzése

Az Azure-előfizetésben a fióknak hozzáféréssel kell lennie egy szerepkör `Microsoft.Authorization/*/Write` AD-alkalmazáshoz való hozzárendeléséhez. Ezt a műveletet a [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) szerepkör vagy a [Felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör végezheti el. Ha a fiókja Közreműködő szerepkörhöz van **rendelve,** nem rendelkezik megfelelő engedéllyel. Hibaüzenetet kap, amikor megpróbál hozzárendelni egy szerepkört a szolgáltatásnévhez.

Az előfizetési engedélyek ellenőrzése:

1. Keresse meg és válassza az **Előfizetések** lehetőséget, vagy válassza az **Előfizetések** lehetőséget a **Kezdőlapon.**

   ![Keresés](./media/howto-create-service-principal-portal/select-subscription.png)

1. Válassza ki azt az előfizetést, amelyben a szolgáltatásnév létre lesz hozva.

   ![Előfizetés kiválasztása hozzárendeléshez](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetést, válassza a globális **előfizetések szűrőt.** Győződjön meg arról, hogy a kívánt előfizetés ki van jelölve a portálon.

1. Válassza **a Saját engedélyek lehetőséget.** Ezután válassza a Kattintson ide az előfizetés teljes **hozzáférési részleteinek megtekintéséhez lehetőséget.**

   ![Válassza ki azt az előfizetést, amelyben a szolgáltatásnév létre lesz hozva](./media/howto-create-service-principal-portal/view-details.png)

1. Válassza **a Megtekintés** a Szerepkör-hozzárendelésekben lehetőséget a hozzárendelt szerepkörök megtekintéséhez, és állapítsa meg, hogy rendelkezik-e megfelelő engedélyekkel egy szerepkör AD-alkalmazáshoz való hozzárendeléséhez.  Ha nem, kérje meg az előfizetés rendszergazdáját, hogy adja hozzá a Felhasználói hozzáférés rendszergazdája szerepkörhöz. Az alábbi képen a felhasználóhoz a Tulajdonos szerepkör van hozzárendelve, ami azt jelenti, hogy a felhasználó megfelelő engedélyekkel rendelkezik.

   ![Ez a példa azt mutatja be, hogy a felhasználóhoz a Tulajdonos szerepkör van hozzárendelve](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Alkalmazás regisztrálása az Azure AD-ban és szolgáltatásnév létrehozása

Ássunk rögtön az identitás létrehozásához. Ha problémát okoz, ellenőrizze [](#permissions-required-for-registering-an-app) a szükséges engedélyeket, hogy a fiókja létre tudja-e hozni az identitást.

1. Jelentkezzen be az Azure-fiókjába a <a href="https://portal.azure.com/" target="_blank">Azure Portal.</a>
1. Válassza a **Azure Active Directory** lehetőséget.
1. Válassza az **Alkalmazásregisztrációk** lehetőséget.
1. Válassza **az Új regisztráció lehetőséget.**
1. Nevezze el az alkalmazást. Válasszon egy támogatott fióktípust, amely meghatározza, hogy ki használhatja az alkalmazást. Az **Átirányítási URI alatt** válassza a **Web** lehetőséget a létrehozni kívánt alkalmazás típusaként. Adja meg azt az URI-t, ahová a hozzáférési jogkivonatot elküldi. Natív alkalmazáshoz nem hozhat létre [hitelesítő adatokat.](../manage-apps/application-proxy-configure-native-client-application.md) Ez a típus nem használható automatizált alkalmazásokhoz. Az értékek beállítása után válassza a Regisztráció **lehetőséget.**

   ![Adja meg az alkalmazás nevét](./media/howto-create-service-principal-portal/create-app.png)

Létrehozta az Azure AD-alkalmazást és -szolgáltatásnévt.

> [!NOTE]
> Több alkalmazást is regisztrálhat ugyanazokkal a névvel az Azure AD-ban, de az alkalmazásoknak eltérő alkalmazás- (ügyfél-) ügyfélszámítógép-névvel kell rendelkezőknek lennie.

## <a name="assign-a-role-to-the-application"></a>Szerepkör hozzárendelése az alkalmazáshoz

Az előfizetés erőforrásainak eléréséhez hozzá kell rendelnie egy szerepkört az alkalmazáshoz. Döntse el, hogy melyik szerepkör rendelkezik a megfelelő engedélyekkel az alkalmazáshoz. Az elérhető szerepkörökkel kapcsolatos további információkért lásd az [Azure beépített szerepköreit.](../../role-based-access-control/built-in-roles.md)

A hatókört az előfizetés, az erőforráscsoport vagy az erőforrás szintjén állíthatja be. Az engedélyek a hatókör alacsonyabb szintjeire öröklődik. Ha például hozzáad egy  alkalmazást egy erőforráscsoport Olvasó szerepkörhöz, az azt jelenti, hogy be tudja olvasni az erőforráscsoportot és az abban található erőforrásokat.

1. A Azure Portal válassza ki azt a hatókörszintet, amelyhez hozzá szeretné rendelni az alkalmazást. Ha például egy szerepkört szeretne hozzárendelni az előfizetés hatókörében, keresse meg és válassza az Előfizetések **lehetőséget,** vagy válassza az **Előfizetések** lehetőséget a **Kezdőlapon.**

   ![Hozzárendelhet például egy szerepkört az előfizetés hatókörében](./media/howto-create-service-principal-portal/select-subscription.png)

1. Válassza ki azt az előfizetést, amelyhez hozzá szeretne rendelni egy alkalmazást.

   ![Előfizetés kiválasztása hozzárendeléshez](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Ha nem látja a keresett előfizetést, válassza a globális **előfizetések szűrőt.** Győződjön meg arról, hogy a kívánt előfizetés ki van jelölve a portálon.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.
1. Válassza ki az alkalmazáshoz hozzárendelni kívánt szerepkört. Ha például engedélyezni szeretné, hogy az alkalmazás  olyan műveleteket hajtson végre, mint például az **újraindítás,** a **példányok** újraindítása és leállítása, válassza a **Közreműködő szerepkört.**  További információ az elérhető [szerepkörökről](../../role-based-access-control/built-in-roles.md) Alapértelmezés szerint az Azure AD-alkalmazások nem jelennek meg az elérhető lehetőségek között. Az alkalmazás megkeresheti, ha rákeres a névre, és kiválasztja.

   ![Válassza ki az alkalmazáshoz hozzárendelni kívánt szerepkört](./media/howto-create-service-principal-portal/select-role.png)

1. A **szerepkör hozzárendelésének** befejezéséhez válassza a Mentés lehetőséget. Az alkalmazás megjelenik a hatókörhöz tartozó szerepkörrel bíró felhasználók listájában.

A szolgáltatásnév be van állítva. Elkezdheti használni a szkriptek vagy alkalmazások futtatásához. A szolgáltatásnév (engedélyek, a felhasználó által megadott engedélyek, a hozzájárulással rendelkező felhasználók, az engedélyek áttekintése, a bejelentkezési adatok stb.) kezeléséhez lásd: Vállalati **alkalmazások.**

A következő szakasz bemutatja, hogyan szerez be olyan értékeket, amelyekre szükség van a programozott bejelentkezéshez.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>Bérlő- és alkalmazásazonosító-értékek lekérte a bejelentkezéshez

Ha programozott módon jelentkezik be, adja át a bérlőazonosítót a hitelesítési kéréssel és az alkalmazásazonosítóval együtt.  Emellett tanúsítványra vagy hitelesítési kulcsra is szüksége lesz (ezt a következő szakaszban ismertetjük). Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. Válassza a **Azure Active Directory** lehetőséget.
1. Az **Alkalmazásregisztrációk** az Azure AD-ban, válassza ki az alkalmazást.
1. Másolja ki a Címtár (bérlő) azonosítóját, és tárolja az alkalmazás kódjában.

    ![Másolja ki a címtárat (bérlőazonosítót), és tárolja az alkalmazás kódjában](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    A címtár (bérlő) azonosítója az alapértelmezett könyvtáráttekintő oldalon is megtalálható.

1. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában.

   ![Az alkalmazás (ügyfél) azonosítójának másolása](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>Hitelesítés: Két lehetőség

A szolgáltatásnévhez kétféle hitelesítés érhető el: jelszóalapú hitelesítés (alkalmazás titkos kulcs) és tanúsítványalapú hitelesítés. *Javasoljuk a tanúsítvány használatát,* de létrehozhat egy titkos alkalmazástitkot is.

### <a name="option-1-upload-a-certificate"></a>1. lehetőség: Tanúsítvány feltöltése

Meglévő tanúsítványt is használhat, ha rendelkezik ilyen tanúsítvánnyal.  Önaírt tanúsítványt is létrehozhat tesztelési *célokra.* Önaírt tanúsítvány létrehozásához nyissa meg a PowerShellt, és futtassa a [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) parancsot a következő paraméterekkel a tanúsítvány létrehozásához a számítógép felhasználói tanúsítványtárolójában:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exportálja ezt a tanúsítványt egy fájlba a Felhasználói tanúsítvány [kezelése](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC beépülő modullal, amely elérhető a Windows Vezérlőpult.

1. A **Start menüben** válassza a **Futtatás** lehetőséget, majd írja be a **certmgr.msc parancsot.**

   Megjelenik az aktuális felhasználó Tanúsítványkezelő eszköze.

1. A tanúsítványok megtekintéséhez a bal oldali ablaktáblán a **Tanúsítványok – Aktuális felhasználó** alatt bontsa ki a Személyes **címtárat.**
1. Kattintson a jobb gombbal a létrehozott tanúsítványra, és válassza a **Minden feladat->exportálása lehetőséget.**
1. Kövesse a Tanúsítványexport varázsló lépéseit.  Ne exportálja a titkos kulcsot, és ne exportálja a következőbe: . CER-fájl.

A tanúsítvány feltöltése:

1. Válassza a **Azure Active Directory** lehetőséget.
1. Az **Alkalmazásregisztrációk** az Azure AD-ban válassza ki az alkalmazást.
1. Válassza **a Tanúsítványok & titkos kulcsok lehetőséget.**
1. Válassza **a Tanúsítvány feltöltése** lehetőséget, és válassza ki a tanúsítványt (egy meglévő tanúsítványt vagy az exportált önaírt tanúsítványt).

    ![Válassza a Tanúsítvány feltöltése lehetőséget, és válassza ki a hozzáadni kívánt tanúsítványt](./media/howto-create-service-principal-portal/upload-cert.png)

1. Válassza a **Hozzáadás** lehetőséget.

Miután regisztrálta a tanúsítványt az alkalmazással az alkalmazásregisztrációs portálon, engedélyezze az ügyfélalkalmazás kódjának a tanúsítvány használatát.

### <a name="option-2-create-a-new-application-secret"></a>2. lehetőség: Új titkos alkalmazás létrehozása

Ha úgy dönt, hogy nem használ tanúsítványt, létrehozhat egy új titkos alkalmazást.

1. Válassza a **Azure Active Directory** lehetőséget.
1. Az **Alkalmazásregisztrációk** az Azure AD-ban, válassza ki az alkalmazást.
1. Válassza **a Tanúsítványok & titkos kulcsok lehetőséget.**
1. Válassza **az Ügyfél titkos kulcsok -> Új titkos ügyfélkomit. lehetőséget.**
1. Adja meg a titkos adatokat és az időtartamot. Ha végzett, válassza a **Hozzáadás lehetőséget.**

   Az ügyfél titkos értékének mentése után megjelenik a titkos ügyfél titkos értéke. Másolja ezt az értéket, mert később nem fogja tudni lekérni a kulcsot. Meg kell adnia a kulcs értékét az alkalmazásazonosítóval, hogy az alkalmazásként jelentkezzen be. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

   ![Másolja ki a titkos adatokat, mert ezt később nem tudja lekérni](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Erőforrás-hozzáférési szabályzatok konfigurálása
Ne feledje, hogy előfordulhat, hogy további engedélyeket kell konfigurálnia az olyan erőforrásokhoz, amelyekhez az alkalmazásnak hozzá kell férni. Frissítenie kell például [a kulcstartó](../../key-vault/general/security-features.md#privileged-access) hozzáférési szabályzatait is, hogy hozzáférést biztosítson az alkalmazásnak a kulcsokhoz, titkos kulcsokhoz vagy tanúsítványokhoz.

1. A <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>nyissa meg a kulcstartót, és válassza a **Hozzáférési szabályzatok lehetőséget.**
1. Válassza **a Hozzáférési szabályzat hozzáadása lehetőséget,** majd válassza ki az alkalmazásnak meg adni kívánt kulcsot, titkos kulcsot és tanúsítványengedélyeket.  Válassza ki a korábban létrehozott egyszerű szolgáltatást.
1. A **hozzáférési szabályzat** hozzáadásához válassza a Hozzáadás, majd a **Mentés** lehetőséget a módosítások véglegesítéshez.
    ![Hozzáférési szabályzat hozzáadása](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, [hogyan használhatja Azure PowerShell szolgáltatásnév létrehozására.](howto-authenticate-service-principal-powershell.md)
* További információ a biztonsági szabályzatok megadásáról: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC).](../../role-based-access-control/role-assignments-portal.md)
* A felhasználók számára adható vagy elutasítható elérhető műveletek listáját az Erőforrás-szolgáltatói műveletek Azure Resource Manager [meg.](../../role-based-access-control/resource-provider-operations.md)
* Az alkalmazásregisztrációk alkalmazásregisztrációk használatával való használatával kapcsolatos **Microsoft Graph** az [Applications](/graph/api/resources/application) API-referenciában.
