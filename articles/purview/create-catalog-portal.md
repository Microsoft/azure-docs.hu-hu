---
title: 'Rövid útmutató: Azure Purview-fiók létrehozása a Azure Portal (előzetes verzió)'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Purview-fiókot, és hogyan konfigurálhatja az engedélyeket a használat megkezdéséhez.
author: nayenama
ms.author: nayenama
ms.date: 10/23/2020
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-portal
ms.openlocfilehash: 158eed6d287fa384023defbb20a7a1c39ea3d838
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728589"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Rövid útmutató: Azure Purview-fiók létrehozása a Azure Portal

> [!IMPORTANT]
> Az Azure Purview jelenleg előzetes verzióban érhető el. A [Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziók kiegészítő használati feltételei további jogi feltételeket tartalmaznak, amelyek a bétaverzióban, előzetes verzióban vagy más, általánosan még nem elérhető Azure-funkciókra vonatkoznak.

Ebben a rövid útmutatóban egy Azure Purview-fiókot hoz létre.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Saját [Azure Active Directory-bérlő](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* A fióknak engedéllyel kell rendelkeznie erőforrások létrehozásához az előfizetésben

* Ha nem **Azure Policy** az összes alkalmazás számára a **Storage-fiók** és az **EventHub-névtér** létrehozását, szabályzati kivételt kell létrehoznia a címke használatával, amelyet a Purview-fiók létrehozása során lehet megadni. Ennek fő oka az, hogy minden létrehozott Purview-fiókhoz létre kell hoznia egy felügyelt erőforráscsoportot, valamint ezen az erőforráscsoporton belül egy Storage-fiókot és egy EventHub-névteret.

    > [!important]
    > Nem kell követnie ezt a lépést, ha nem Azure Policy vagy egy meglévő Azure Policy nem blokkolja a **Storage-fiók** és az **EventHub-névtér létrehozását.**

    1. Lépjen a Azure Portal, és keressen rá a Szabályzat **kifejezésre.**
    1. Operátorral [és címkével két](../governance/policy/tutorials/create-custom-policy-definition.md) kivétel hozzáadásához kövesse az Egyéni szabályzatdefiníció létrehozása vagy a meglévő szabályzat `not` módosítása `resourceBypass` szabályzatot:

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
        > A címke lehet bármi más mellett, és Ön határozhatja meg az értéket a Nézet létrehozása során az utóbbi lépésekben, ha a szabályzat képes `resourceBypass` észlelni a címkét.

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="Képernyőkép a szabályzatdefiníciók létrehozásáról.":::

    1. [Hozzon létre egy szabályzat-hozzárendelést](../governance/policy/assign-policy-portal.md) a létrehozott egyéni szabályzat használatával.

        [![Képernyőkép a szabályzat-hozzárendelés létrehozásáról](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="configure-your-subscription"></a>Az előfizetés konfigurálása

Ha szükséges, az alábbi lépésekkel konfigurálhatja az előfizetését, hogy engedélyezze az Azure Purview futtatását az előfizetésben:

   1. A Azure Portal keresse meg és válassza az **Előfizetések lehetőséget.**

   1. Az előfizetések listájából válassza ki a használni kívánt előfizetést. Rendszergazdai hozzáférési engedély szükséges az előfizetéshez.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Képernyőkép az előfizetés kiválasztásáról a Azure Portal.":::

   1. Az előfizetéséhez válassza az **Erőforrás-szolgáltatók lehetőséget.** Az **Erőforrás-szolgáltatók panelen** keresse meg és regisztrálja mindhárom erőforrás-szolgáltatót: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Ha nincsenek regisztrálva, regisztrálja a Regisztráció **lehetőség kiválasztásával.**

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Képernyőkép a Microsoft dot Azure Purview erőforrás-szolgáltató regisztrálásának Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Azure Purview-fiókpéldány létrehozása

1. Az új Azure **Purview-fiók** létrehozásához Azure Portal a  Fiók véglegesnézete lapra, majd válassza a Hozzáadás lehetőséget. Másik lehetőségként a Marketplace-en keresse meg a **Purview Accounts (Fiók** végleges keresése) et, és válassza a **Create (Létrehozás) lehetőséget.** Vegye figyelembe, hogy egyszerre csak egy Azure Purview-fiókot adhat hozzá.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Képernyőkép az Azure Purview-fiókpéldány létrehozásáról a Azure Portal.":::

    > [!Note] 
    > Az Azure Purview nem támogatja a fiók régiók közötti áthelyezését. Erről az Áthelyezési művelet támogatása [erőforrásokhoz oldalon talál további információt.](../azure-resource-manager/management/move-support-resources.md)

1. Az Alapvető **beállítások lapon** tegye a következőket:
    1. Válasszon ki egy **erőforráscsoportot.**
    1. Adja meg a **katalógus Purview-fiókjának** nevét. Szóközök és szimbólumok nem használhatók.
    1. Válasszon egy **helyet,** majd válassza a **Tovább: Konfiguráció lehetőséget.**
1. A **Konfiguráció lapon** válassza ki a kívánt **platformméretet** – az engedélyezett értékek 4 kapacitásegység (CU) és 16 CU. Válassza **a Tovább: Címkék lehetőséget.**
1. A Címkék **lapon** hozzáadhat egy vagy több címkét is. Ezek a címkék csak az azure Azure Portal ban használhatók, az Azure Purview-ban nem. 

    > [!Note] 
    > Ha már van **Azure Policy,** és kivételt kell hozzáadnia az **Előfeltételek** részen, akkor hozzá kell adni a megfelelő címkét. Hozzáadhatja például a következő `resourceBypass` címkét: Címke hozzáadása a :::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="Purview fiókhoz.":::

1. Válassza **a & létrehozása,** majd a Létrehozás **lehetőséget.** A létrehozás befejezése néhány percet vesz igénybe. Az újonnan létrehozott Azure Purview-fiókpéldány megjelenik a **Purview-fiókok oldalán található** listában.
1. Ha az új fiók kiépítése befejeződött, válassza az **Erőforráshoz való ugrás lehetőséget.**

    > [!Note]
    > Ha a kiépítés állapottal meghiúsult, az azt jelenti, hogy egy Azure-szabályzat blokkolja `Conflict` a Purview-t a **Storage-fiók** és a **EventHub-névtér létrehozásában.** A kivételek hozzáadásához  végig kell mennie az Előfeltételek lépésen.
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Az ütközések végleges nézetének hibaüzenete":::

1. Válassza **a Launch purview account (Új fiók indítása) lehetőséget.**

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Képernyőkép az Azure Purview-fiókkatalógus indításához kiválasztott beállításról.":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Rendszerbiztonsági tag hozzáadása adatsík-szerepkörhöz

Ahhoz, hogy Ön vagy csapata megkezdje az Azure Purview használatát, hozzá kell adni egy vagy több rendszerbiztonsági szolgáltatásokat az előre meghatározott adatsík-szerepkörök egyikéhez: **Purview Data Reader (Adatolvasó** végleges megtekintése, **Purview Data Curator)** vagy **Purview Data Source Administrator**(Adatforrás-rendszergazda végleges megtekintése). További információ az Azure Purview-Data Catalog [katalógusengedélyekkel kapcsolatban.](catalog-permissions.md)

Rendszerbiztonsági tag hozzáadása a **Purview Data Curator** adatsík-szerepkörhöz egy Azure Purview-fiókban:

1. A [**nézetben**](https://aka.ms/purviewportal) válassza a Fiók végleges Azure Portal.

1. Válassza ki a módosítani kívánt Azure Purview-fiókot.

1. A **Fiók végleges megtekintése lapon** válassza a **Hozzáférés-vezérlés (IAM) lapot.**

1. Kattintson **a + Hozzáadás gombra**

Ha a Hozzáadás gombra kattintva két választási lehetőség jelenik meg, amelyeken mindkét lehetőség meg van jelölve (letiltva), az azt jelenti, hogy nem rendelkezik a megfelelő engedélyekkel ahhoz, hogy valakit adatsík-szerepkörhöz adjon az Azure Purview-fiókban. Meg kell találnia egy tulajdonost, egy felhasználói hozzáférés-rendszergazdát vagy egy másik szerepkör-hozzárendelési jogosultsággal rendelkező felhasználót az Azure Purview-fiókjában. A megfelelő személyeket a  Szerepkör-hozzárendelések lap kiválasztásával, majd lefelé görgetve megkeresheti a tulajdonost vagy a felhasználói hozzáférés rendszergazdáját, és kapcsolatba léphet velük.

1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.

1. A **Purview Data Curator Role** (A Purview Data Curator szerepkör vagy a **Purview** Data [](catalog-permissions.md) Source Administrator Role) szerepkör esetén attól függően, hogy a rendszerbiztonsági tag mire lesz használva (a részletekért tekintse meg a katalógus engedélyeinek és alkalmazás- és [szolgáltatásnév-objektumokkal](../active-directory/develop/app-objects-and-service-principals.md) kapcsolatos részt a Azure Active Directory).

1. A **Hozzáférés hozzárendelése beállításnál** hagyja meg az alapértelmezett **felhasználó, csoport vagy szolgáltatásnév értéket.**

1. A **Kijelölés** mezőben adja meg a felhasználó nevét, Azure Active Directory hozzárendelni kívánt csoportot vagy szolgáltatásnevet, majd kattintson a nevére az eredménypanelen.

1. Kattintson a **Mentés gombra.**

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége erre az Azure Purview-fiókra, törölje a következő lépésekkel:

1. A **nézetben** válassza a Fiók végleges Azure Portal.

2. Válassza ki a rövid útmutató elején létrehozott Azure Purview-fiókot. Válassza **a Törlés** lehetőséget, adja meg a fiók nevét, majd válassza a Törlés **lehetőséget.**

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Purview-fiókot.

A következő cikkből megtudhatja, hogyan engedélyezheti a felhasználóknak az Azure Purview-fiókhoz való hozzáférést. 

> [!div class="nextstepaction"]
> [Felhasználók hozzáadása az Azure Purview-fiókhoz](catalog-permissions.md)
