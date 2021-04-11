---
title: Felügyeleti eszköz üzembe helyezése Azure Resource Manager sablonnal – Azure
description: Felhasználói felületi eszköz telepítése Azure Resource Manager sablonnal a Windows rendszerű virtuális asztali (klasszikus) erőforrások kezeléséhez.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 1d562c483b340bee5f1c1aa5f63c068de6f54e42
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444394"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-an-azure-resource-manager-template"></a>Windows rendszerű virtuális asztali (klasszikus) felügyeleti eszköz üzembe helyezése Azure Resource Manager sablonnal

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat.

A cikkben szereplő utasítások alapján megtudhatja, hogyan helyezheti üzembe a felhasználói felületet egy Azure Resource Manager sablon használatával.

## <a name="important-considerations"></a>Fontos szempontok

Mivel az alkalmazásnak hozzá kell járulnia a Windows virtuális asztal használatához, az eszköz nem támogatja a vállalatközi (B2B) forgatókönyveket. Minden Azure Active Directory (HRE) bérlő előfizetéséhez a felügyeleti eszköz saját külön üzembe helyezésére lesz szükség.

Ez a felügyeleti eszköz egy minta. A Microsoft fontos biztonsági és minőségi frissítéseket biztosít. A [forráskód a githubon érhető el](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Az ügyfelek és a partnerek számára ajánlott az eszköz testreszabása az üzleti igényeknek megfelelően.

A következő böngészők kompatibilisek a felügyeleti eszközzel:
- Google Chrome 68 vagy újabb verzió
- Microsoft Edge 40,15063 vagy újabb verzió
- Mozilla Firefox 52,0 vagy újabb verzió
- Safari 10 vagy újabb (csak macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>A felügyeleti eszköz üzembe helyezéséhez szükséges tudnivalók

A felügyeleti eszköz telepítése előtt szüksége lesz egy Azure Active Directory (Azure AD) felhasználóra az alkalmazások regisztrálásának létrehozásához és a felügyeleti felhasználói felület telepítéséhez. A felhasználónak a következőket kell tennie:

- Az Azure AD Multi-Factor Authentication (MFA) le van tiltva
- Engedéllyel rendelkezik erőforrások létrehozásához az Azure-előfizetésében
- Engedéllyel rendelkezik Azure AD-alkalmazás létrehozásához. Kövesse az alábbi lépéseket annak ellenőrzéséhez, hogy a felhasználó rendelkezik-e a szükséges engedélyekkel a [szükséges engedélyek](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)utasításait követve.

A felügyeleti eszköz üzembe helyezése és konfigurálása után javasoljuk, hogy kérje meg a felhasználót, hogy indítsa el a felügyeleti felhasználói felületet, és győződjön meg róla, hogy minden működik. A felügyeleti felhasználói felületet indító felhasználónak szerepkör-hozzárendeléssel kell rendelkeznie, amely lehetővé teszi a Windows rendszerű virtuális asztali bérlő megtekintését vagy szerkesztését.

## <a name="deploy-the-management-tool"></a>A felügyeleti eszköz üzembe helyezése

Mielőtt elkezdené, győződjön meg arról, hogy a kiszolgáló és az ügyfélalkalmazások beleegyeznek a Azure Active Directory (HRE) által képviselt [Windows virtuális asztali engedélyezési oldalára](https://rdweb.wvd.microsoft.com) .

Az Azure Resource Management-sablon üzembe helyezéséhez kövesse az alábbi utasításokat:

1. Lépjen a [GitHub Azure RDS-Templates oldalára](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Helyezze üzembe a sablont az Azure-ban.
    - Ha vállalati előfizetésben végez üzembe helyezést, görgessen le, és válassza **az üzembe helyezés az Azure**-ban lehetőséget.
    - Ha felhőalapú megoldás-szolgáltatói előfizetést használ, kövesse az alábbi utasításokat az Azure-ba történő üzembe helyezéshez:
        1. Görgessen le, és kattintson **a jobb gombbal az Azure**-ba, majd válassza a **hivatkozás helyének másolása** lehetőséget.
        2. Nyisson meg egy szövegszerkesztőt, például a jegyzettömböt, és illessze be a hivatkozást.
        3. Közvetlenül <https://portal.azure.com/> a hashtag (#) után és azt megelőzően adja meg a (z) (@) jelet a bérlői tartománynév után. Íme egy példa a következő formátumra: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/> .
        4. Jelentkezzen be a Azure Portal felhasználóként rendszergazdai/közreműködői engedélyekkel a felhőalapú megoldás-szolgáltató előfizetéséhez.
        5. Illessze be a szövegszerkesztőbe másolt hivatkozást a címsorba.
3. A paraméterek megadásakor tegye a következőket:
    - A **isServicePrincipal** paraméternél válassza a **false (hamis**) lehetőséget.
    - A hitelesítő adatok esetében adja meg az Azure AD-beli hitelesítő adatait a multi-Factor Authentication szolgáltatás letiltásával. A rendszer ezeket a hitelesítő adatokat fogja használni az Azure AD-alkalmazás és az Azure-erőforrások létrehozásához. További információért lásd: [mire van szükség a felügyeleti eszköz telepítéséhez](#what-you-need-to-deploy-the-management-tool).
    - Az **applicationName** esetében használjon egy egyedi nevet az alkalmazás számára, amely regisztrálva lesz a Azure Active Directoryban. Ezt a nevet a rendszer a webalkalmazás URL-címére is felhasználja. Használhatja például a "Apr3UX" nevet.
4. A paraméterek megadása után fogadja el a használati feltételeket, majd válassza a **vásárlás** lehetőséget.

## <a name="provide-consent-for-the-management-tool"></a>Adja meg a felügyeleti eszköz beleegyezikét

A GitHub-Azure Resource Manager sablon befejezése után egy olyan erőforráscsoportot talál, amely a Azure Portal egy app Service-csomaggal együtt két app Services-csomagot tartalmaz.

A bejelentkezés és a felügyeleti eszköz használata előtt meg kell adnia a felügyeleti eszközhöz társított új Azure AD-alkalmazáshoz való hozzájárulásukat. A belefoglalt engedély lehetővé teszi, hogy a felügyeleti eszköz a Windows virtuális asztali felügyeleti hívásokat a felhasználó nevében az eszközre bejelentkezett felhasználó nevében végezze el.

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely a felhasználói felületi felügyeleti eszközhöz való hozzáféréskor megadott engedélyeket mutatja.](../media/management-ui-delegated-permissions.png)

Az eszközre való bejelentkezéshez használható felhasználó meghatározásához lépjen a [Azure Active Directory felhasználói beállítások lapra](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) , és jegyezze fel, hogy a felhasználók milyen értékkel férhetnek hozzá a **vállalati adatokhoz a nevükben**.

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely azt mutatja, hogy a felhasználók megadhatnak-e jóváhagyást az alkalmazásoknak csak a felhasználó számára.](../media/management-ui-user-consent-allowed.png)

- Ha az érték értéke **Igen**, akkor a Azure Active Directory bármely felhasználói fiókjával bejelentkezhet, és csak az adott felhasználó beleegyezett. Ha azonban később egy másik felhasználóval jelentkezik be a felügyeleti eszközre, akkor újra kell végrehajtania ugyanezt a hozzájárulásukat.
- Ha a **nem** értékre van állítva, akkor a Azure Active Directory globális rendszergazdájaként kell bejelentkeznie, és rendszergazdai jogosultsággal kell rendelkeznie a címtár összes felhasználója számára. Más felhasználók nem fognak megjelenni a jóváhagyásban.


Ha eldöntötte, hogy melyik felhasználót fogja használni a beleegyezikés megadásához, kövesse az alábbi utasításokat az eszköz beleegyezikének biztosításához:

1. Nyissa meg az Azure-erőforrásokat, válassza ki az Azure App Services erőforrást a sablonban megadott névvel (például Apr3UX), és keresse meg a hozzá társított URL-címet. például:  `https://rdmimgmtweb-210520190304.azurewebsites.net` .
2. Jelentkezzen be a megfelelő Azure Active Directory felhasználói fiókkal.
3. Ha globális rendszergazdai jogosultsággal rendelkezik, most bejelölheti a **szervezet nevében** való engedélyezéshez szükséges jelölőnégyzetet. Az **elfogadás** lehetőséget választva adja meg a beleegyező értéket.

   > [!div class="mx-imgBorder"]
   > ![A felhasználó vagy a rendszergazda által megjelenő teljes körű beleegyezikés oldalra mutató képernyőkép.](../media/management-ui-consent-page.png)

Ekkor a felügyeleti eszközre kerül.

## <a name="use-the-management-tool"></a>A felügyeleti eszköz használata

Miután beleegyezett a szervezetbe vagy egy adott felhasználóhoz, bármikor hozzáférhet a felügyeleti eszközhöz.

Az eszköz elindításához kövesse az alábbi utasításokat:

1. Válassza ki az Azure App Services erőforrást a sablonban megadott névvel (például Apr3UX), és navigáljon a hozzá társított URL-címhez. például:  `https://rdmimgmtweb-210520190304.azurewebsites.net` .
2. Jelentkezzen be a Windows rendszerű virtuális asztali hitelesítő adataival.
3. Amikor a rendszer kéri, hogy válasszon ki egy bérlői csoportot, válassza ki az **alapértelmezett bérlői csoportot** a legördülő listából.
4. Amikor kiválasztja az **alapértelmezett bérlői csoportot**, egy menü jelenik meg az ablak bal oldalán. Ebben a menüben keresse meg a bérlői csoport nevét, és jelölje ki.

  > [!NOTE]
  > Ha egyéni bérlői csoporttal rendelkezik, a legördülő listából válassza a nevet manuálisan.

## <a name="report-issues"></a>Problémák bejelentése

Ha a felügyeleti eszközzel vagy más Windowsos virtuális asztali eszközökkel kapcsolatos problémákba ütközik, kövesse az [Azure Resource Manager-távoli asztali szolgáltatások sablonok](https://github.com/Azure/RDS-Templates/blob/master/README.md) című témakör utasításait a githubon való jelentéséhez.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan helyezheti üzembe és csatlakozhat a felügyeleti eszközhöz, megtudhatja, hogyan használhatja az Azure szolgáltatás súgóját a szolgáltatással kapcsolatos problémák és az egészségügyi tanácsadók figyelésére. További információ: [set up Service riasztások oktatóanyaga](set-up-service-alerts-2019.md).