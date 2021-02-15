---
title: 'Gyors útmutató: Azure-beli hatáskörébe tartozó fiók létrehozása a Azure Portalban (előzetes verzió)'
description: Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Azure-beli hatáskörébe tartozó fiókot, és hogyan konfigurálhat engedélyeket a használatának megkezdéséhez.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: 82b72567a0db4dc9dbff9128402d0ee9f40f3512
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094151"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Gyors útmutató: Azure-beli hatáskörébe tartozó fiók létrehozása a Azure Portal

> [!IMPORTANT]
> Az Azure-beli hatáskörébe jelenleg előzetes verzió érhető el. A [Microsoft Azure előzetes verzióinak kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) közé tartoznak azok a további jogi feltételek, amelyek az Azure olyan szolgáltatásaira vonatkoznak, amelyek béta-, előzetes verziójú vagy egyéb módon még nem lettek nyilvánosan elérhetők.

Ebben a rövid útmutatóban létrehoz egy Azure-beli hatáskörébe tartozó fiókot.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Saját [Azure Active Directory-bérlő](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* A fióknak engedéllyel kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre az előfizetésben

* Ha **Azure Policy** blokkolja az összes alkalmazást a Storage- **fiók** és a **EventHub-névtér** létrehozásához, akkor a címkével kell megadnia a szabályzatot, amely a hatáskörébe tartozó fiók létrehozásának folyamata során megadható. Ennek fő oka, hogy minden létrehozott felügyeleti fiókhoz létre kell hoznia egy felügyelt erőforráscsoportot, és ezen az erőforráscsoport, egy Storage-fiók és egy EventHub-névtér között.

    > [!important]
    > Nem kell követnie ezt a lépést, ha nincs Azure Policy, vagy egy meglévő Azure Policy nem blokkolja a **Storage-fiók** és a **EventHub-névtér** létrehozását.

    1. Navigáljon a Azure Portalre, és keressen rá **a szabályzat** kifejezésre.
    1. Kövesse az [egyéni házirend-definíció létrehozása](../governance/policy/tutorials/create-custom-policy-definition.md) vagy a meglévő szabályzat módosítása lehetőséget két kivétel hozzáadásához az `not` operátorral és a `resourceBypass` címkével:

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > A címke bármi lehet a mellett, `resourceBypass` és akár Ön is meghatározhatja az értéket, amikor az utóbbi lépésekben létrehozza a hatáskörébe tartozó teendőket, feltéve, hogy a szabályzat képes azonosítani a címkét.

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="A házirend-definíció létrehozását bemutató képernyőkép.":::

    1. [Hozzon létre egy szabályzat-hozzárendelést](../governance/policy/assign-policy-portal.md) a létrehozott egyéni szabályzat használatával.

        [![A szabályzat-hozzárendelés létrehozását bemutató képernyőkép](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="configure-your-subscription"></a>Az előfizetés konfigurálása

Ha szükséges, kövesse az alábbi lépéseket az előfizetés konfigurálásához, hogy az Azure-beli hatáskörébe az előfizetésében fusson:

   1. A Azure Portal keresse meg és válassza ki az **előfizetések** elemet.

   1. Az előfizetések listájából válassza ki a használni kívánt előfizetést. Az előfizetéshez rendszergazdai hozzáférési engedély szükséges.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="A Azure Portal-előfizetés kiválasztását bemutató képernyőkép.":::

   1. Az előfizetéséhez válassza az **erőforrás-szolgáltatók** lehetőséget. Az **erőforrás-szolgáltatók** ablaktáblán keresse meg és regisztrálja mind a három erőforrás-szolgáltatót: 
       1. **Microsoft. hatáskörébe**
       1. **Microsoft.Storage**
       1. **Microsoft. EventHub** 
      
      Ha nincsenek regisztrálva, regisztrálja azt a **regisztráció** lehetőség kiválasztásával.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Képernyőfelvétel: a Microsoft dot Azure-beli hatáskörébe tartozó erőforrás-szolgáltató regisztrálása a Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Azure-beli hatáskörébe tartozó fiók példányának létrehozása

1. Lépjen a Azure Portal **hatáskörébe tartozó fiókok** lapra, majd a **Hozzáadás** gombra kattintva hozzon létre egy új Azure-beli hatáskörébe tartozó fiókot. Azt is megteheti, hogy a piactéren megkeresi a **hatáskörébe tartozó fiókokat** , és kiválasztja a **Létrehozás** lehetőséget. Vegye figyelembe, hogy egyszerre csak egy Azure-beli hatáskörébe tartozó fiókot adhat hozzá.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Képernyőfelvétel: Azure hatáskörébe tartozó fiók példányának létrehozása a Azure Portal.":::

    > [!Note] 
    > Az Azure-beli hatáskörébe nem támogatott a fiók áthelyezése a régiók között. Erről az [Azure által támogatott szolgáltatások oldalon](https://docs.microsoft.com/azure/azure-resource-manager/management/region-move-support)talál további információt.

1. Az **alapok** lapon tegye a következőket:
    1. Válasszon ki egy **erőforráscsoportot**.
    1. Adja meg a katalógushoz a **hatáskörébe tartozó fiók nevét** . Szóközök és szimbólumok használata nem engedélyezett.
    1. Válasszon egy  **helyet**, majd válassza a **Next (Konfigurálás**) lehetőséget.
1. A **konfiguráció** lapon válassza ki a kívánt **platform méretét** – az engedélyezett értékek: 4 kapacitási egység (CU) és 16 cu. Kattintson a **Tovább gombra: címkék**.
1. A **címkék** lapon opcionálisan hozzáadhat egy vagy több címkét is. Ezek a címkék csak a Azure Portal használhatók, nem az Azure hatáskörébe. 

    > [!Note] 
    > Ha **Azure Policy** van, és kivételt kell hozzáadnia az **előfeltételekhez**, hozzá kell adnia a megfelelő címkét. Hozzáadhat például egy címkét `resourceBypass` : :::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="címke hozzáadása a hatáskörébe fiókhoz.":::

1. Válassza a **felülvizsgálat & létrehozás** lehetőséget, majd válassza a **Létrehozás** lehetőséget. A létrehozás elvégzése néhány percet vesz igénybe. Az újonnan létrehozott Azure hatáskörébe tartozó fiók példánya megjelenik a hatáskörébe tartozó **fiókok** lapjának listájában.
1. Ha az új fiók üzembe helyezése befejeződött, válassza az **erőforrás** megnyitása lehetőséget.

    > [!Note]
    > Ha a kiépítés állapota nem sikerült `Conflict` , az azt jelenti, hogy egy Azure-házirend blokkolja a szabályzatot a **Storage-fiók** és a EventHub- **névtér** létrehozásához. A kivételek hozzáadásához el kell végeznie az **Előfeltételek** lépéseit.
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="A hatáskörébe ütköző hibaüzenet üzenete":::

1. Válassza a **hatáskörébe tartozó fiók elindítása** lehetőséget.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Képernyőkép a kijelölésről az Azure hatáskörébe tartozó fiók katalógusának elindításához.":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Rendszerbiztonsági tag hozzáadása adatsík-szerepkörhöz

Mielőtt Ön vagy csapata megkezdheti az Azure hatáskörébe való csatlakozást, egy vagy több rendszerbiztonsági tag hozzáadása az előre definiált adatközpont-szerepkörök egyikéhez kell, hogy legyen: a **hatáskörébe Adatolvasó**, a **hatáskörébe** tartozó adatkezelő vagy a **hatáskörébe tartozó adatforrások rendszergazdája**. Az Azure hatáskörébe Data Catalog engedélyekkel kapcsolatos további információkért lásd: [katalógus engedélyei](catalog-permissions.md).

Rendszerbiztonsági tag hozzáadása egy Azure- **beli hatáskörébe tartozó adatközponti** szerepkörhöz:

1. Nyissa meg a Azure Portal a [**hatáskörébe tartozó fiókok**](https://aka.ms/purviewportal) lapot.

1. Válassza ki a módosítani kívánt Azure-beli hatáskörébe tartozó fiókot.

1. A **hatáskörébe tartozó fiók** lapon válassza a **hozzáférés-vezérlés (iam) lapot.**

1. Kattintson a **+ Hozzáadás** gombra

Ha a Hozzáadás gombra kattint, két választási lehetőség látható (letiltva), ez azt jelenti, hogy nem rendelkezik a megfelelő engedélyekkel ahhoz, hogy bárkit felvegyen egy adatsík-szerepkörbe az Azure-beli hatáskörébe fiókban. Meg kell keresnie egy tulajdonost, egy felhasználói hozzáférési rendszergazdát vagy valaki másnak a szerepkör-hozzárendelési szolgáltatót az Azure hatáskörébe tartozó fiókjában. A megfelelő embereket megkeresheti a szerepkör- **hozzárendelések** lap kiválasztásával, majd lefelé görgetve megkeresheti a tulajdonosi vagy a felhasználói hozzáférési rendszergazdát, és kapcsolatba léphet ezekkel a személyekkel.

1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.

1. Ahhoz, hogy a rendszerbiztonsági tag mire lesz felhasználva (lásd: [katalógus-engedélyek](catalog-permissions.md) és az [alkalmazás-és szolgáltatásnév-objektumok a Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md) című témakörben olvashat a szerepkörhöz a **hatáskörébe tartozó adatkurátori szerepkör** vagy a **hatáskörébe tartozó adatforrás-rendszergazdai szerepkörben** .

1. Az alapértelmezett, a **felhasználó, a csoport vagy az egyszerű szolgáltatásnév** elhagyása a **hozzáférés kiosztásával** .

1. A **válassza ki** a felhasználó nevét, Azure Active Directory csoportot vagy szolgáltatásnevet, amelyet hozzá kíván rendelni, majd kattintson a nevére a találatok ablaktáblán.

1. Kattintson a **Save (Mentés**) gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége erre az Azure-beli hatáskörébe fiókra, törölje a következő lépésekkel:

1. Nyissa meg a Azure Portal a **hatáskörébe tartozó fiókok** lapot.

2. Válassza ki a rövid útmutató elején létrehozott Azure-beli hatáskörébe tartozó fiókot. Válassza a **Törlés** lehetőséget, adja meg a fiók nevét, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure-beli hatáskörébe tartozó fiókot.

A következő cikkből megtudhatja, hogyan engedélyezheti a felhasználók számára az Azure hatáskörébe tartozó fiók elérését. 

> [!div class="nextstepaction"]
> [Felhasználók hozzáadása az Azure-beli hatáskörébe-fiókhoz](catalog-permissions.md)