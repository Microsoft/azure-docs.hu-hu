---
title: Démonalkalmazás biztonságossá tétele
titleSuffix: Azure Maps
description: A Azure Portal használatával kezelheti a hitelesítést egy megbízható Daemon-alkalmazás konfigurálásához.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2dd04f404330a6c86e2df09da610e16ba9b721f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895647"
---
# <a name="secure-a-daemon-application"></a>Daemon-alkalmazás biztonságossá tétele

Az alábbi útmutató a megbízható és biztonságos környezetben üzemeltetett háttérbeli folyamatokra, időzítőekre és feladatokra szolgál. Ilyenek például az Azure web Jobs, az Azure Function apps, a Windows-szolgáltatások és bármely más megbízható háttérrendszer.

> [!Tip]
> A Microsoft azt javasolja, hogy éles alkalmazásokhoz Azure Active Directory (Azure AD) és Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) implementáljon. A fogalmak áttekintését lásd: [Azure Maps hitelesítés](./azure-maps-authentication.md).

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>Forgatókönyv: megosztott kulcsos hitelesítés

Azure Maps fiók létrehozása után létrejön az elsődleges és a másodlagos kulcs. Javasoljuk, hogy az elsődleges kulcsot használja előfizetési kulcsként, ha [megosztott kulcsos hitelesítést használ a Azure Maps meghívásához](./azure-maps-authentication.md#shared-key-authentication). Másodlagos kulcsot használhat olyan forgatókönyvekben, mint például a kulcsok változásai. További információ: [hitelesítés Azure Mapsban](./azure-maps-authentication.md).

### <a name="securely-store-shared-key"></a>Megosztott kulcs biztonságos tárolása

Az elsődleges és a másodlagos kulcs engedélyezi az összes API-nak a Maps-fiókhoz való engedélyezését. Az alkalmazásoknak biztonságos tárolóban (például Azure Key Vault) kell tárolniuk a kulcsokat. Az alkalmazásnak Azure Key Vault titokként kell lekérnie a megosztott kulcsot, hogy ne tárolja a megosztott kulcsot egyszerű szövegként az alkalmazás konfigurációjában. A Azure Key Vault konfigurálásának megismeréséhez tekintse meg a [Azure Key Vault fejlesztői útmutató](../key-vault/general/developers-guide.md)című témakört.

A folyamat a következő lépésekből áll:

1. Hozzon létre egy Azure Key Vault.
2. Hozzon létre egy Azure AD-egyszerű szolgáltatást az alkalmazás regisztrációja vagy a felügyelt identitás létrehozásával, a létrehozott rendszerbiztonsági tag felelős a Azure Key Vault eléréséhez.
3. Rendelje hozzá az egyszerű szolgáltatás elérését az Azure Key Secrets `Get` engedélyhez.
4. A titkokhoz való hozzáférés ideiglenes kiosztása `Set` a fejlesztőknek.
5. Állítsa be a megosztott kulcsot a Key Vault titkos kulcsban, és hivatkozzon a titkos AZONOSÍTÓra a démon alkalmazás konfigurációjában, és távolítsa el a Secrets `Set` engedélyt.
6. Az Azure AD-hitelesítés implementálása a démon alkalmazásban a megosztott kulcs titkos Azure Key Vaultból való beolvasásához.
7. Hozzon létre Azure Maps REST API kérelmet megosztott kulccsal.

> [!Tip]
> Ha az alkalmazást az Azure-környezetben üzemelteti, egy felügyelt identitást kell megvalósítani, hogy csökkentse a Azure Key Vault-hitelesítéshez szükséges titkos kód kezelésének költségeit és összetettségét. Tekintse meg a következő Azure Key Vault [oktatóanyagot a felügyelt identitáson keresztül történő kapcsolódáshoz](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).

A démon alkalmazás feladata a megosztott kulcs biztonságos tárolóból való beolvasása. A Azure Key Vault megvalósításához az Azure AD-n keresztül történő hitelesítésre van szükség a titok eléréséhez. Ehelyett azt javasoljuk, hogy a megosztott kulcsos hitelesítéssel kapcsolatos további összetettségi és üzemeltetési követelmények eredményeképpen Azure Maps közvetlen Azure AD-hitelesítést.

> [!IMPORTANT]
> A kulcs újragenerálásának egyszerűbbé tétele érdekében javasoljuk, hogy az alkalmazások egyszerre egy kulcsot használjanak. Az alkalmazások ezután újra létrehozhatják a fel nem használt kulcsot, és az új újragenerált kulcsot egy biztonságos titkos tárolóba (például Azure Key Vault) helyezhetik üzembe.

## <a name="scenario-azure-ad-role-based-access-control"></a>Forgatókönyv: Azure AD szerepköralapú hozzáférés-vezérlés

Azure Maps fiók létrehozása után a Azure Maps `x-ms-client-id` érték szerepel az Azure Portal-hitelesítés részletei lapon. Ez az érték azt a fiókot jelöli, amelyet a rendszer a REST API kérelmekhez fog használni. Ezt az értéket az alkalmazás-konfigurációban kell tárolni, és a HTTP-kérések megkezdése előtt le kell kérni. A forgatókönyv célja annak engedélyezése, hogy a démon alkalmazás hitelesítse magát az Azure AD-ben, és hívja meg Azure Maps REST API-kat.

> [!Tip]
> Azt javasoljuk, hogy az Azure Virtual Machines, Virtual Machine Scale Sets vagy App Services üzemeltetése lehetővé tegye a felügyelt identitás-összetevők előnyeit.

### <a name="daemon-hosted-on-azure-resources"></a>Az Azure-erőforrásokon üzemeltetett démon

Azure-erőforrásokon való futtatáskor konfigurálja az Azure által felügyelt identitásokat, hogy az alacsony költségeket, a minimális hitelesítőadat-kezelési erőfeszítést engedélyezze. 

A felügyelt identitások alkalmazáshoz való hozzáférésének engedélyezéséhez tekintse meg a [felügyelt identitások áttekintését](../active-directory/managed-identities-azure-resources/overview.md) .

Felügyelt identitás előnyei:

* Azure rendszerfelügyelt X509 tanúsítvány nyilvános kulcsú titkosításának hitelesítése.
* Az Azure AD biztonsága X509-tanúsítványokkal, az ügyfél titkos kulcsainak használata helyett.
* Az Azure kezeli és megújítja a felügyelt identitás erőforrásával kapcsolatos összes tanúsítványt.
* Egyszerűsített hitelesítő adatok működési felügyelete a biztonságos titkos adattároló szolgáltatás, például a Azure Key Vault szükségességének eltávolításával. 

### <a name="daemon-hosted-on-non-azure-resources"></a>Nem Azure-erőforrásokon üzemeltetett démon

A nem Azure-beli környezetekben felügyelt identitások nem érhetők el. Ezért egy egyszerű szolgáltatásnevet kell konfigurálnia egy Azure AD-alkalmazás regisztrálásával a Daemon-alkalmazáshoz.

1. Az Azure Portal az Azure-szolgáltatások listájában válassza a **Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.  

    > [!div class="mx-imgBorder"]
    > ![Alkalmazásregisztráció](./media/how-to-manage-authentication/app-registration.png)

2. Ha már regisztrálta az alkalmazást, folytassa a következő lépéssel. Ha még nem regisztrálta az alkalmazást, adjon meg egy **nevet**, válassza ki a **támogatási fiók típusát**, majd válassza a **regisztráció** lehetőséget.  

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás regisztrációjának részletei](./media/how-to-manage-authentication/app-create.png)

3. Ha delegált API-engedélyeket szeretne hozzárendelni Azure Mapshoz, nyissa meg az alkalmazást. Ezután a **Alkalmazásregisztrációk** alatt válassza az **API-engedélyek**  >  **Hozzáadás engedélyt**. A **szervezetem által használt API**-k alatt keresse meg és válassza a **Azure Maps** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás API-engedélyeinek hozzáadása](./media/how-to-manage-authentication/app-permissions.png)

4. Jelölje be a **hozzáférési Azure Maps** melletti jelölőnégyzetet, majd válassza az **engedélyek hozzáadása** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás API-engedélyeinek kiválasztása](./media/how-to-manage-authentication/select-app-permissions.png)

5. Az alábbi lépések végrehajtásával hozzon létre egy ügyfél-titkos kulcsot, vagy konfigurálja a tanúsítványt.

    * Ha az alkalmazása kiszolgálói vagy alkalmazás-hitelesítést használ, akkor az alkalmazás regisztrációs oldalán lépjen a **tanúsítványok & Secrets** elemre. Ezután töltsön fel egy nyilvános kulcsú tanúsítványt, vagy hozzon létre egy jelszót az **új ügyfél-titok** lehetőség kiválasztásával.

        > [!div class="mx-imgBorder"]
        > ![Ügyfél titkos kulcsának létrehozása](./media/how-to-manage-authentication/app-keys.png)

    * A **Hozzáadás** gombra kattintva másolja a titkos kulcsot, és tárolja biztonságosan egy olyan szolgáltatásban, mint például a Azure Key Vault. Tekintse át [Azure Key Vault fejlesztői útmutatót](../key-vault/general/developers-guide.md) a tanúsítvány vagy a titkos kulcs biztonságos tárolásához. Ezt a titkot fogja használni a jogkivonatok Azure AD-ből való lekéréséhez.

        > [!div class="mx-imgBorder"]
        > ![Ügyfél titkos kulcsának hozzáadása](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>Szerepköralapú hozzáférés biztosítása a Daemon-alkalmazáshoz Azure Maps

Az *Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)* a létrehozott felügyelt identitás vagy az egyszerű szolgáltatásnév hozzárendelésével adhatja meg egy vagy több Azure Maps szerepkör-definícióhoz. Az Azure Maps számára elérhető Azure-szerepkör-definíciók megtekintéséhez lépjen a **hozzáférés-vezérlés (iam)** elemre. Válassza ki a **szerepkörök** elemet, majd keressen rá a *Azure Maps* kezdetű szerepkörökre. Ezek a Azure Maps szerepkörök azok a szerepkörök, amelyekhez hozzáférést biztosíthat.

> [!div class="mx-imgBorder"]
> ![Elérhető szerepkörök megtekintése](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. Nyissa meg **Azure Maps-fiókját**. Válassza a **Hozzáférés-vezérlés (IAM)** > **Szerepkör-hozzárendelések** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Hozzáférés biztosítása az Azure RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. A **szerepkör-hozzárendelések** lapon **adjon hozzá** egy szerepkör-hozzárendelést. 

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel: a kiválasztott hozzáadással rendelkező roll-hozzárendelések megjelenítése.](./media/how-to-manage-authentication/add-role-assignment.png)

3. Válasszon egy beépített Azure Maps szerepkör-definíciót, például **Azure Maps Adatolvasó** vagy **Azure Maps adatközreműködő**. A **hozzáférés hozzárendelése** területen válassza ki az **Azure ad-felhasználó, csoport vagy egyszerű szolgáltatásnév** vagy felügyelt identitás elemet a **felhasználóhoz rendelt felügyelt identitásrendszer**  /  **hozzárendelt felügyelt identitásával**. Válassza ki a rendszerbiztonsági tag. Kattintson a **Mentés** gombra.

    > [!div class="mx-imgBorder"]
    > ![Szerepkör-hozzárendelés hozzáadása](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. Megerősítheti, hogy a szerepkör-hozzárendelés a szerepkör-hozzárendelés lapon lett alkalmazva.

## <a name="request-token-with-managed-identity"></a>Kérelem jogkivonata felügyelt identitással

Miután konfigurálta a felügyelt identitást a szolgáltatói erőforráshoz, az Azure SDK vagy a REST API használatával szerezzen be jogkivonatot a Azure Mapshoz, a [hozzáférési token beszerzésével](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)kapcsolatos részletekért lásd:. Az útmutató alapján a várt érték azt jelzi, hogy a rendszer egy hozzáférési jogkivonatot ad vissza, amely REST API kérések esetén használható.

## <a name="request-token-with-application-registration"></a>Igénylési jogkivonat az alkalmazás regisztrálásával

Az alkalmazás regisztrálása és Azure Mapshoz való hozzárendelése után hozzáférési jogkivonatokat kérhet le.

* Azure AD-erőforrás azonosítója `https://atlas.microsoft.com/`
* Azure AD alkalmazás azonosítója
* Az Azure AD-bérlő azonosítója
* Azure AD alkalmazás regisztrációs ügyfél titka

Kérés:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

Válasz:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

Részletesebb példákat az [Azure ad hitelesítési forgatókönyvei](../active-directory/develop/authentication-vs-authorization.md)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

A Azure Maps-fiók API-használati metrikáinak megkeresése:
> [!div class="nextstepaction"]
> [Használati metrikák megtekintése](how-to-view-api-usage.md)

Ismerkedjen meg az Azure AD és a Azure Maps integrációját bemutató példákkal:
> [!div class="nextstepaction"]
> [Azure Maps minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)