---
title: 'Oktatóanyag: hitelesítés engedélyezése egy Python-webalkalmazásban'
titleSuffix: Azure AD B2C
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Active Directory B2Ct egy Python-lombik webalkalmazás felhasználói bejelentkezésének biztosításához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: 56e92a698446bd417c74820d6da662ad9ee55c77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555685"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>Oktatóanyag: hitelesítés engedélyezése egy Python-webalkalmazásban Azure AD B2C

Ez az oktatóanyag bemutatja, hogyan használható a Azure Active Directory B2C (Azure AD B2C) a felhasználók regisztrálására és bejelentkezésére egy Python-lombik webalkalmazásban.

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Válasz URL-címének hozzáadása a Azure AD B2C-bérlőben regisztrált alkalmazáshoz
> * Mintakód letöltése a GitHubról
> * A minta alkalmazás kódjának módosítása a Bérlővel való együttműködéshez
> * Regisztráció a regisztrációs/bejelentkezési felhasználói folyamat használatával

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek folytatása előtt a következő Azure AD B2C erőforrásokra van szükség:

* [Azure AD B2C bérlő](tutorial-create-tenant.md)
* Az [alkalmazás regisztrálva](tutorial-register-applications.md) van a bérlőben, valamint az *alkalmazás (ügyfél) azonosítója* és az *ügyfél titka*
* A bérlőn [létrehozott felhasználói folyamatok](tutorial-create-user-flows.md)

Emellett a helyi fejlesztési környezetben a következőkre lesz szüksége:

* [Visual Studio Code](https://code.visualstudio.com/) vagy más Kódszerkesztő
* [Python](https://nodejs.org/en/download/) 2.7 + vagy 3 +

## <a name="add-a-redirect-uri"></a>Átirányítási URI hozzáadása

Az előfeltételek részeként elvégzett második oktatóanyagban egy webalkalmazást regisztrált Azure AD B2Cban. Ebben az oktatóanyagban a kód mintával való kommunikáció engedélyezéséhez adjon hozzá egy válasz URL-címet (más néven átirányítási URI-t) az alkalmazás regisztrálásához.

Ha frissíteni szeretne egy alkalmazást a Azure AD B2C-bérlőben, használhatja az új egyesített **Alkalmazásregisztrációk** -élményt vagy az örökölt  **alkalmazások (örökölt)** felületét. [További információ az új felületről](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, válassza a **birtokolt alkalmazások** fület, majd válassza ki a *webapp1* alkalmazást.
1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
1. A **web** alatt válassza az **URI hozzáadása** hivatkozást, majd `http://localhost:5000/getAToken` a szövegmezőbe írja be a szöveget.
1. Kattintson a **Mentés** gombra.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az **összes szolgáltatás** elemet a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **alkalmazások (örökölt)** lehetőséget, majd válassza ki a *webapp1* alkalmazást.
1. A **Válasz URL-cím** területen adja hozzá a címet `http://localhost:5000/getAToken` .
1. Kattintson a **Mentés** gombra.
* * *

## <a name="get-the-sample-code"></a>A mintakód letöltése

Ebben az oktatóanyagban egy, a GitHubról letöltött mintakód-mintát konfigurál a B2C-Bérlővel való együttműködéshez. A minta azt mutatja be, hogy a Python-lombik webalkalmazások hogyan használhatják Azure AD B2C felhasználói regisztrációhoz és bejelentkezéshez.

[Töltse le a. ZIP Archive](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip) vagy Clone The [Code Sample adattár](https://github.com/Azure-Samples/ms-identity-python-webapp) a githubról.

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>A minta frissítése

A minta beszerzését követően konfigurálja úgy az alkalmazást, hogy az Azure AD B2C bérlőt, az alkalmazás regisztrációját és a felhasználói folyamatokat használja.

A projekt gyökérkönyvtárában:

1. Nevezze át a *app_config.* rajzfájl fájlt *app_config. a. old névre.*
1. Nevezze át a *app_config_b2c.* a *app_config.*

Frissítse az újonnan átnevezett *app_config.* a (z) Azure ad B2C bérlő és az alkalmazás-regisztráció értékeit, amelyeket az előfeltételek részeként hozott létre.

1. Nyissa meg a *app_config.* a fájlt a szerkesztőben.
1. Frissítse az `b2c_tenant` értéket a Azure ad B2C bérlő nevével, például *contosob2c*.
1. Módosítsa az egyes `*_user_flow` értékeket úgy, hogy azok megfeleljenek az előfeltételek részeként létrehozott felhasználói folyamatok nevének.
1. Frissítse az `CLIENT_ID` értéket az előfeltételek részeként regisztrált webalkalmazás **alkalmazás (ügyfél) azonosítójával** .
1. Frissítse az `CLIENT_SECRET` értéket az előfeltételekben létrehozott **ügyfél-titkos kulcs** értékével. A fokozott biztonság érdekében, érdemes inkább egy **környezeti változóban** tárolni, ahogy azt a megjegyzések is javasolták.

A *app_config. a.* a legfelső szakasznak ekkor a következő kódrészlethez hasonlóan kell kinéznie:

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> A kódrészlet megjegyzései szerint azt javasoljuk, hogy ne **tárolja a titkos kulcsokat** az alkalmazás kódjában. A hardcoded változó *csak kényelmi* célokat szolgál a kód mintában. Érdemes lehet környezeti változót vagy titkos tárolót használni, például Azure Key Vault.

## <a name="run-the-sample"></a>Minta futtatása

1. A konzolon vagy a terminálon váltson a mintát tartalmazó könyvtárra. Például:

    ```console
    cd ms-identity-python-webapp
    ```
1. Futtassa a következő parancsokat a szükséges csomagok telepítéséhez a PyPi-ből, és futtassa a webalkalmazást a helyi gépen:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    A konzol ablakban a helyileg futó alkalmazás portszáma látható:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. A gombra kattintva `http://localhost:5000` megtekintheti a helyi gépen futó webalkalmazást.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="Webböngésző, amelyen a Python-lombik webalkalmazása helyileg fut":::

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

Ez a minta alkalmazás támogatja a regisztrációt, a bejelentkezést és a jelszó-visszaállítást. Ebben az oktatóanyagban egy e-mail-cím használatával regisztrálhat.

1. Válassza a **Bejelentkezés** lehetőséget a korábbi lépésben megadott *B2C_1_signupsignin1* felhasználói folyamat elindításához.
1. A Azure AD B2C egy regisztrációs hivatkozást tartalmazó bejelentkezési oldalt jelenít meg. Mivel még nem rendelkezik fiókkal, válassza a **regisztráció most** hivatkozást.
1. A regisztrációs munkafolyamat egy olyan oldalt jelenít meg, amely az e-mail-cím használatával gyűjti és ellenőrzi a felhasználó identitását. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Azure AD B2C felhasználói folyamat által megjelenített regisztrációs oldal":::

1. A **Létrehozás** gombra kattintva helyi fiókot hozhat létre a Azure ad B2C könyvtárban.

A **Létrehozás** gombra kattintva az alkalmazás megjeleníti a bejelentkezett felhasználó nevét.

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="Webböngésző, amely bemutatja a Python-lombik webalkalmazást a bejelentkezett felhasználóval":::

Ha tesztelni szeretné a bejelentkezést, válassza ki a **kijelentkezési** hivatkozást, majd válassza a **Bejelentkezés** lehetőséget, és jelentkezzen be a regisztráció során megadott e-mail-címmel és jelszóval.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Python-lombik webalkalmazást konfigurált a Azure AD B2C-bérlőben lévő felhasználói folyamattal való együttműködésre a regisztrálási és bejelentkezési képesség biztosításához. A következő lépéseket végezte el:

> [!div class="checklist"]
> * A Azure AD B2C-bérlőben regisztrált alkalmazáshoz tartozó válasz URL-cím hozzáadva
> * Mintakód letöltése a GitHubról
> * Módosította a minta alkalmazás kódját a Bérlővel való együttműködéshez
> * Regisztráció a regisztrációs/bejelentkezési felhasználói folyamat használatával

Következő lépésként megtudhatja, hogyan szabhatja testre a felhasználók számára megjelenített felhasználói folyamatok lapjait Azure AD B2C:

> [!div class="nextstepaction"]
> [A felhasználói élmények felületének testreszabása Azure AD B2Cban >](customize-ui.md)