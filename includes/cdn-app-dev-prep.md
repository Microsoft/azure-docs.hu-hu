---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 4967991b0edaa854acbf6b308596859d662311fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993380"
---
## <a name="prerequisites"></a>Előfeltételek
A CDN felügyeleti kód írása előtt el kell végeznie néhány előkészítést, hogy a kód működjön a Azure Resource Managerval. Az előkészületek elvégzéséhez a következőket kell tennie:

* Hozzon létre egy erőforráscsoportot, amely tartalmazza az oktatóanyagban létrehozott CDN-profilt
* Azure Active Directory konfigurálása az alkalmazás hitelesítésének biztosításához
* Engedélyek alkalmazása az erőforráscsoporthoz úgy, hogy csak az Azure AD-bérlő jogosult felhasználói használhatják a CDN-profilt

### <a name="creating-the-resource-group"></a>Az erőforráscsoport létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson **az erőforrás létrehozása** elemre.
3. Keresse meg az **erőforráscsoportot** , és az erőforráscsoport ablaktáblán kattintson a **Létrehozás** elemre.

    ![Új erőforráscsoport létrehozása](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Nevezze el az erőforráscsoport *CdnConsoleTutorial*.  Válassza ki az előfizetését, és válasszon egy Önhöz közeli helyet.  Ha szeretné, kattintson a **rögzítés az irányítópulton** jelölőnégyzetre az erőforráscsoport az irányítópulton való rögzítéséhez a portálon.  A rögzítés megkönnyíti a későbbi keresését.  Miután végzett a beállításokat, kattintson a **Létrehozás** gombra.

    ![Képernyőkép az erőforráscsoport párbeszédpanelről.](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Ha az erőforráscsoport létrejött, ha nem rögzítette az irányítópulton, akkor a **Tallózás**, majd az **erőforráscsoportok** elemre kattintva keresheti meg.  A megnyitásához kattintson az erőforráscsoport elemre.  Jegyezze fel az előfizetés- **azonosítóját**. Később szükség van rá.

    ![Képernyőkép a C D N konzolról – oktatóanyag szakasz.](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Az Azure AD-alkalmazás létrehozása és engedélyek alkalmazása
Az alkalmazások hitelesítésének két módja van Azure Active Directory: egyéni felhasználókkal vagy egyszerű szolgáltatással. Egy egyszerű szolgáltatásnév hasonló a Windowshoz tartozó szolgáltatásfiók számára.  Ahelyett, hogy egy adott felhasználói jogosultságot adna a CDN-profilokkal való kommunikációhoz, az engedélyeket az egyszerű szolgáltatásnév adja meg.  Az egyszerű szolgáltatásokat általában automatizált, nem interaktív folyamatokhoz használják.  Bár ez az oktatóanyag egy interaktív konzolos alkalmazást ír, az egyszerű szolgáltatásnév megközelítésére fogunk összpontosítani.

Az egyszerű szolgáltatás létrehozása több lépésből áll, beleértve az Azure Active Directory alkalmazások létrehozását is.  A létrehozáshoz [ezt az oktatóanyagot](../articles/active-directory/develop/howto-create-service-principal-portal.md)fogjuk követni.

> [!IMPORTANT]
> Ügyeljen arra, hogy kövesse a [csatolt oktatóanyag](../articles/active-directory/develop/howto-create-service-principal-portal.md)összes lépését.  *Fontos* , hogy pontosan a leírt módon végezze el.  Ügyeljen arra, hogy a **bérlői azonosítót**, a **bérlői tartománynevet** (általában *. onmicrosoft.com* tartomány, hacsak nem adott meg egyéni TARTOMÁNYT), az **ügyfél-azonosítót** és az **ügyfél-hitelesítési kulcsot** jegyezze fel, mivel ezekre az információkra később szükség van.  Ügyeljen arra, hogy megvédje az **ügyfél-azonosítót** és az ügyfél- **hitelesítési kulcsot**, mivel ezeket a hitelesítő adatokat bárki felhasználhatja, hogy az egyszerű szolgáltatásként hajtsa végre a műveleteket.
>
> Ha megtekinti a több-bérlős alkalmazás konfigurálása nevű lépést, válassza a **nem** lehetőséget.
>
> Amikor megkapja az [alkalmazás szerepkörhöz való hozzárendelésének](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)lépését, használja a korábban létrehozott erőforráscsoportot, a  *CdnConsoleTutorial*, de az **olvasó** szerepkör helyett a **CDN-profil közreműködői** szerepkört.  Miután hozzárendelte az alkalmazást a **CDN-profil közreműködői** szerepkörhöz az erőforráscsoporthoz, térjen vissza ehhez az oktatóanyaghoz. 
>
>

Miután létrehozta a szolgáltatásnevet, és hozzárendelte a **CDN-profil közreműködői** szerepkört, az erőforráscsoport **felhasználói** paneljének az alábbi képhez hasonlóan kell kinéznie.

![Felhasználók panel](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interaktív felhasználói hitelesítés
Ha az egyszerű szolgáltatásnév helyett inkább interaktív egyéni felhasználói hitelesítésre van szüksége, a folyamat hasonló ahhoz, mint az egyszerű szolgáltatásnév.  Valójában ugyanezt az eljárást kell követnie, de néhány kisebb módosítást is végre kell hajtania.

> [!IMPORTANT]
> Csak akkor hajtsa végre a következő lépéseket, ha egyéni felhasználói hitelesítést szeretne használni egy egyszerű szolgáltatásnév helyett.
>
>

1. Az alkalmazás létrehozásakor a **webalkalmazás** helyett válassza a **natív alkalmazás** lehetőséget.

    ![Natív alkalmazás](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. A következő oldalon egy **átirányítási URI**-t kell megadnia.  Az URI nem lesz érvényesítve, de ne feledje, hogy mi adta meg. Később szüksége lesz rá.
3. Nem kell **ügyfél-hitelesítési kulcsot** létrehoznia.
4. Az egyszerű szolgáltatásoknak a **CDN-profil közreműködői** szerepkörhöz való társítása helyett egyéni felhasználókat vagy csoportokat fogunk hozzárendelni.  Ebben a példában láthatja, hogy a  *CDN-bemutató felhasználót* a **CDN-profil közreműködői** szerepkörhöz rendeltem.  

    ![Egyéni felhasználói hozzáférés](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
