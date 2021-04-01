---
title: Egyéni Azure-szerepkörök létrehozása vagy frissítése a Azure Portal-Azure RBAC használatával
description: Ismerje meg, hogyan hozhat létre egyéni Azure-szerepköröket a Azure Portal és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával. Ez magában foglalja az egyéni szerepkörök listázását, létrehozását, frissítését és törlését.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/11/2020
ms.author: rolyon
ms.openlocfilehash: ecda0edcd34999e8cbb6c7ab9039953d17c119e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369226"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>Egyéni Azure-szerepkörök létrehozása vagy módosítása az Azure Portal segítségével

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját egyéni Azure-szerepköröket is. A beépített szerepkörökhöz hasonlóan egyéni szerepköröket is hozzárendelhet a felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokhoz az előfizetéshez és az erőforráscsoport-hatókörökhöz. Az egyéni szerepkörök egy Azure Active Directory (Azure AD) könyvtárban tárolódnak, és az előfizetések között megoszthatók. Minden címtárhoz legfeljebb 5000 egyéni szerepkör tartozhat. Egyéni szerepkörök hozhatók létre a Azure Portal, a Azure PowerShell, az Azure CLI vagy a REST API használatával. Ez a cikk azt ismerteti, hogyan hozhat létre egyéni szerepköröket a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

Egyéni szerepkörök létrehozásához a következőkre lesz szüksége:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>1. lépés: a szükséges engedélyek meghatározása

Az Azure-ban több ezer engedély található, amelyeket esetleg belefoglalhat az egyéni szerepkörbe. Íme néhány módszer, amelyek segítségével meghatározhatja az egyéni szerepkörhöz hozzáadni kívánt engedélyeket:

- Tekintse meg a meglévő [beépített szerepköröket](built-in-roles.md).
- Sorolja fel azokat az Azure-szolgáltatásokat, amelyekhez hozzáférést szeretne biztosítani.
- Határozza meg az [Azure-szolgáltatásokhoz hozzárendelt erőforrás-szolgáltatókat](../azure-resource-manager/management/azure-services-resource-providers.md). A keresési módszert a [4. lépés: engedélyek című szakasza](#step-4-permissions)írja le.
- Keresse meg az [elérhető engedélyeket](resource-provider-operations.md) a felvenni kívánt engedélyek megkereséséhez. A keresési módszert a [4. lépés: engedélyek című szakasza](#step-4-permissions)írja le.

## <a name="step-2-choose-how-to-start"></a>2. lépés: a kezdési módjának kiválasztása

Három módon hozhat létre egyéni szerepkört. Egy meglévő szerepkört is klónozott, teljesen új vagy egy JSON-fájllal kezdheti meg. A legegyszerűbb módja, ha olyan meglévő szerepkört keres, amely rendelkezik a szükséges engedélyekkel, majd klónozott és módosítható a forgatókönyvhöz. 

### <a name="clone-a-role"></a>Szerepkör klónozása

Ha egy meglévő szerepkör nem rendelkezik a szükséges engedélyekkel, akkor klónozott, majd módosíthatja az engedélyeket. A szerepkörök klónozásának megkezdéséhez kövesse az alábbi lépéseket.

1. A Azure Portal nyisson meg egy előfizetést vagy erőforráscsoportot, amelyhez az egyéni szerepkört hozzá szeretné rendelni, majd nyissa meg a **hozzáférés-vezérlés (iam)** lehetőséget.

    Az alábbi képernyőfelvételen az előfizetéshez megnyitott hozzáférés-vezérlés (IAM) oldal látható.

    ![Az előfizetés hozzáférés-vezérlés (IAM) lapja](./media/custom-roles-portal/access-control-subscription.png)

1. Kattintson a **szerepkörök** fülre az összes beépített és egyéni szerepkör listájának megtekintéséhez.

1. Keressen olyan szerepkört, amelyet klónozottként szeretne használni, például a számlázási olvasó szerepkört.

1. A sor végén kattintson a három pontra (**...**), majd a **klónozás** elemre.

    ![Klónozási helyi menü](./media/custom-roles-portal/clone-menu.png)

    Ekkor megnyílik az egyéni szerepkör-szerkesztő a **klónozott szerepkör** lehetőség kiválasztásával.

1. Folytassa a [3. lépéssel: alapjaival](#step-3-basics).

### <a name="start-from-scratch"></a>Kezdés a nulláról

Ha szeretné, az alábbi lépéseket követve elindíthat egy egyéni szerepkört a semmiből.

1. A Azure Portal nyisson meg egy előfizetést vagy erőforráscsoportot, amelyhez az egyéni szerepkört hozzá szeretné rendelni, majd nyissa meg a **hozzáférés-vezérlés (iam)** lehetőséget.

1. Kattintson a **Hozzáadás** , majd az **Egyéni szerepkör hozzáadása** elemre.

    ![Egyéni szerepkör hozzáadása menü](./media/custom-roles-portal/add-custom-role-menu.png)

    Ekkor megnyílik az egyéni szerepkörök szerkesztője, és kiválaszthatja a **kezdés elölről** lehetőséget.

1. Folytassa a [3. lépéssel: alapjaival](#step-3-basics).

### <a name="start-from-json"></a>Kezdés a JSON-ból

Ha szeretné, megadhatja a legtöbb egyéni szerepkör-értéket egy JSON-fájlban. Nyissa meg a fájlt az egyéni szerepkörök szerkesztőjében, végezze el a további módosításokat, majd hozza létre az egyéni szerepkört. Kövesse az alábbi lépéseket egy JSON-fájl elindításához.

1. Hozzon létre egy JSON-fájlt, amely a következő formátumú:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. A JSON-fájlban határozza meg a különböző tulajdonságok értékeit. Íme egy példa néhány hozzáadott értékkel. További információ a különböző tulajdonságokkal kapcsolatban: az [Azure szerepkör-definíciók ismertetése](role-definitions.md).

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. A Azure Portal nyissa meg a **hozzáférés-vezérlés (iam)** lapot.

1. Kattintson a **Hozzáadás** , majd az **Egyéni szerepkör hozzáadása** elemre.

    ![Egyéni szerepkör hozzáadása menü](./media/custom-roles-portal/add-custom-role-menu.png)

    Ekkor megnyílik az egyéni szerepkörök szerkesztője.

1. Az alapbeállítások lap alapkonfiguráció **engedélyei** területén válassza az **Indítás a JSON-ból** lehetőséget.

1. A fájl kiválasztása mező mellett kattintson a mappa gombra a Megnyitás párbeszédpanel megnyitásához.

1. Válassza ki a JSON-fájlt, majd kattintson a **Megnyitás** gombra.

1. Folytassa a [3. lépéssel: alapjaival](#step-3-basics).

## <a name="step-3-basics"></a>3. lépés: alapismeretek

Az **alapvető beállítások** lapon megadhatja az egyéni szerepkör nevét, leírását és alapkonfigurációjának engedélyeit.

1. Az **Egyéni szerepkör neve** mezőben adja meg az egyéni szerepkör nevét. A névnek egyedinek kell lennie az Azure AD-címtárban. A név tartalmazhat betűket, számokat, szóközöket és speciális karaktereket.

1. A **Leírás** mezőbe írja be az egyéni szerepkör leírását (nem kötelező). Ez lesz az egyéni szerepkör elemleírása.

    Az alapbeállítások beállításának már be kell **állítania az előző** lépés alapján, de módosíthatja is.

    ![Alapbeállítások lap megadott értékekkel](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>4. lépés: engedélyek

Az **engedélyek** lapon megadhatja az egyéni szerepkörhöz tartozó engedélyeket. Attól függően, hogy klónozott-e egy szerepkört, vagy ha a JSON-t választotta, előfordulhat, hogy az engedélyek lap már felsorolja az engedélyeket.

![Egyéni szerepkör létrehozása engedélyek lapja](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Engedélyek hozzáadása vagy eltávolítása

Kövesse az alábbi lépéseket az egyéni szerepkörhöz tartozó engedélyek hozzáadásához vagy eltávolításához.

1. Az engedélyek hozzáadásához kattintson az **engedélyek hozzáadása** elemre az engedélyek hozzáadása ablaktábla megnyitásához.

    Ez a panel felsorolja a különböző kategóriákba csoportosított összes rendelkezésre álló engedélyt a kártya formátumában. Mindegyik kategória egy *erőforrás-szolgáltatót* jelöl, amely az Azure-erőforrásokat ellátó szolgáltatás.

1. Az **engedély keresése** mezőbe írjon be egy karakterláncot az engedélyek megkereséséhez. A számlázással kapcsolatos engedélyek megkereséséhez például keressen rá a *számla* kifejezésre.

    Az erőforrás-szolgáltatói kártyák listája a keresési sztring alapján jelenik meg. Az erőforrás-szolgáltatók Azure-szolgáltatásokhoz való leképezésének listáját az [Azure-szolgáltatások erőforrás-szolgáltatói](../azure-resource-manager/management/azure-services-resource-providers.md)című témakörében tekintheti meg.

    ![Engedélyek hozzáadása ablaktábla erőforrás-szolgáltatóval](./media/custom-roles-portal/add-permissions-provider.png)

1. Kattintson egy erőforrás-szolgáltató kártyára, amely rendelkezhet az egyéni szerepkörhöz hozzáadni kívánt engedélyekkel, például a **Microsoft számlázással**.

    Az erőforrás-szolgáltató felügyeleti engedélyeinek listája a keresési karakterlánc alapján jelenik meg.

    ![Engedélyek listájának hozzáadása](./media/custom-roles-portal/add-permissions-list.png)

1. Ha az adatsíkon érvényes engedélyeket keres, kattintson az **adatműveletek** lehetőségre. Ellenkező esetben hagyja meg **a műveletek váltógomb** beállítást a felügyeleti síkon érvényes engedélyek listázásához. További információ a felügyeleti sík és az adatsík közötti különbségekről: [felügyeleti és adatműveletek](role-definitions.md#management-and-data-operations).

1. Ha szükséges, frissítse a keresési karakterláncot, hogy tovább pontosítsa a keresést.

1. Ha egy vagy több olyan engedélyt talál, amelyet hozzá szeretne adni az egyéni szerepkörhöz, vegyen fel egy pipát az engedélyek mellett. Például vegyen fel egy pipát a másik melletti jelölőnégyzetbe **: töltse le a számlát** a számlák letöltéséhez szükséges engedély hozzáadásához.

1. Kattintson a **Hozzáadás** gombra az engedélyek listához való hozzáadásához.

    Az engedély a `Actions` vagy a lesz hozzáadva `DataActions` .

    ![Engedély hozzáadva](./media/custom-roles-portal/permissions-list-add.png)

1. Az engedélyek eltávolításához kattintson a sor végén található Törlés ikonra. Ebben a példában, mivel a felhasználónak nem kell támogatási jegyeket létrehoznia, az `Microsoft.Support/*` engedélyt törölni lehet.

### <a name="add-wildcard-permissions"></a>Helyettesítő karakteres engedélyek hozzáadása

Attól függően, hogy hogyan döntött, az engedélyek listájában szerepelhetnek helyettesítő karakterekkel ( `*` ). A helyettesítő karakter ( `*` ) minden olyan engedélyt kiterjeszt, amely megfelel az Ön által megadott műveleti karakterláncnak. A következő helyettesítő karakterlánc például a Azure Cost Management és az exportáláshoz kapcsolódó összes engedélyt hozzáadja. Ez magában foglalja az esetleg hozzáadott jövőbeli exportálási engedélyeket is.

```
Microsoft.CostManagement/exports/*
```

Ha új helyettesítő karaktert szeretne hozzáadni, az **engedélyek hozzáadása** panelen nem adhatja hozzá. Helyettesítő karakterek hozzáadásához manuálisan kell hozzáadnia azt a **JSON** lapon. További információ [: 6. lépés: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Engedélyek kizárása

Ha a szerepkör helyettesítő () engedéllyel rendelkezik, és ki kívánja `*` zárni vagy kivonni a helyettesítő jogosultságokból származó konkrét engedélyeket, kizárhatja őket. Tegyük fel például, hogy a következő helyettesítő karakteres engedélyekkel rendelkezik:

```
Microsoft.CostManagement/exports/*
```

Ha nem szeretné engedélyezni az Exportálás törlését, kizárhatja a következő törlési engedélyt:

```
Microsoft.CostManagement/exports/delete
```

Ha kizár egy engedélyt, azt a vagy a-ként adja hozzá a rendszer `NotActions` `NotDataActions` . Az érvényben lévő felügyeleti engedélyek kiszámításához adja hozzá az összeset, `Actions` majd vonja ki az összeset `NotActions` . Az érvényes adatokra vonatkozó engedélyek kiszámítása úgy történik, hogy hozzáadja az összeset, `DataActions` majd kivonja az összeset `NotDataActions` .

> [!NOTE]
> Az engedélyek kizárása nem ugyanaz, mint a megtagadás. Az engedélyek kizárása egyszerűen egy kényelmes módszer az engedélyek kivonására egy helyettesítő jogosultsággal.

1. Ha ki szeretne zárni vagy ki kíván vonni egy engedélyt egy engedélyezett helyettesítő karakterből, kattintson az **engedélyek kizárása** elemre az engedélyek kizárása panel megnyitásához.

    Ezen a panelen adhatja meg a kizárt vagy kivont felügyeleti vagy adatengedélyeket.

1. Miután megtalálta a kizárni kívánt engedélyeket, vegyen fel egy pipát az engedélyek mellett, majd kattintson a **Hozzáadás** gombra.

    ![Engedélyek kizárása panel – kijelölt engedély](./media/custom-roles-portal/exclude-permissions-select.png)

    Az engedély a vagy a lesz hozzáadva `NotActions` `NotDataActions` .

    ![Kizárt engedély](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>5. lépés: hozzárendelhető hatókörök

A **hozzárendelhető hatókörök** lapon adhatja meg, hogy az egyéni szerepkör hol érhető el a hozzárendeléshez, például az előfizetéshez vagy az erőforráscsoporthoz. Az indítás helyétől függően ez a lap felsorolja azt a hatókört, ahol megnyitotta a hozzáférés-vezérlés (IAM) lapot. A hozzárendelhető hatókör a gyökérszintű hatókörre ("/") való beállítása nem támogatott. Jelenleg nem adhat hozzá hozzárendelhető hatókörként felügyeleti csoportot.

1. Kattintson a **hozzárendelhető hatókörök hozzáadása** elemre a hozzárendelhető hatókörök hozzáadása panel megnyitásához.

    ![Hozzárendelhető hatókörök lap](./media/custom-roles-portal/assignable-scopes.png)

1. Kattintson egy vagy több használni kívánt hatókörre, általában az előfizetésre.

    ![Hozzárendelhető hatókörök hozzáadása](./media/custom-roles-portal/add-assignable-scopes.png)

1. A hozzárendelhető hatókör hozzáadásához kattintson a **Hozzáadás** gombra.

## <a name="step-6-json"></a>6. lépés: JSON

A **JSON** lapon megtekintheti a JSON-ban formázott egyéni szerepkört. Ha szeretné, közvetlenül is szerkesztheti a JSON-t. Ha helyettesítő karaktert () szeretne hozzáadni `*` , ezt a fület kell használnia.

1. A JSON szerkesztéséhez kattintson a **Szerkesztés** gombra.

    ![Egyéni szerepkört mutató JSON-lap](./media/custom-roles-portal/json.png)

1. Módosítsa a JSON-t.

    Ha a JSON formátuma nem megfelelő, akkor egy piros szaggatott vonal és egy kijelző jelenik meg a függőleges csatornán.

1. A Szerkesztés befejezése után kattintson a **Mentés** gombra.

## <a name="step-7-review--create"></a>7. lépés: felülvizsgálat + létrehozás

A **felülvizsgálat + létrehozás** lapon áttekintheti az egyéni szerepkör beállításait.

1. Tekintse át az egyéni szerepkör beállításait.

    ![Felülvizsgálat + Létrehozás lap](./media/custom-roles-portal/review-create.png)

1. Az egyéni szerepkör létrehozásához kattintson a **Létrehozás** gombra.

    Néhány pillanat múlva megjelenik egy üzenet, amely jelzi, hogy az egyéni szerepkör létrehozása sikerült.

    ![Egyéni szerepkörű üzenet létrehozása](./media/custom-roles-portal/custom-role-success.png)

    Ha bármilyen hiba észlelhető, egy üzenet jelenik meg.

    ![Felülvizsgálat + létrehozási hiba](./media/custom-roles-portal/review-create-error.png)

1. Tekintse meg az új egyéni szerepkört a **szerepkörök** listájában. Ha nem látja az egyéni szerepkört, kattintson a **frissítés** gombra.

     Néhány percet is igénybe vehet, amíg az egyéni szerepkör mindenhol megjelenik.

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

Az alábbi lépéseket követve megtekintheti az egyéni szerepköröket.

1. Nyisson meg egy előfizetést vagy erőforráscsoportot, majd nyissa meg a **hozzáférés-vezérlés (iam)** t.

1. Kattintson a **szerepkörök** fülre az összes beépített és egyéni szerepkör listájának megtekintéséhez.

1. A **Type (típus** ) listában válassza a **CustomRole** lehetőséget, hogy csak az egyéni szerepköröket lássuk.

    Ha most hozta létre az egyéni szerepkört, és nem jelenik meg a listában, kattintson a **frissítés** gombra.

    ![Egyéni szerepkörök listája](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

1. A cikk korábbi részében leírtak szerint nyissa meg az egyéni szerepkörök listáját.

1. Kattintson a frissíteni kívánt egyéni szerepkör három pontra (**...**), majd a **Szerkesztés** elemre. Vegye figyelembe, hogy a beépített szerepkörök nem frissíthetők.

    Az egyéni szerepkör a szerkesztőben nyílik meg.

    ![Egyéni szerepkör menü](./media/custom-roles-portal/edit-menu.png)

1. A különböző lapok használatával frissítse az egyéni szerepkört.

1. Ha befejezte a módosításokat, kattintson a **felülvizsgálat + létrehozás** lapra a módosítások áttekintéséhez.

1. Az egyéni szerepkör frissítéséhez kattintson a **frissítés** gombra.

## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

1. A cikk korábbi részében leírtak szerint nyissa meg az egyéni szerepkörök listáját.

1. Távolítson el minden olyan szerepkör-hozzárendelést, amely az egyéni szerepkört használja.

1. Kattintson a törölni kívánt egyéni szerepkör három pontra (**...**), majd a **Törlés** gombra.

    ![Képernyőkép, amely megjeleníti a törlésre kiválasztható egyéni szerepkörök listáját.](./media/custom-roles-portal/delete-menu.png)

    Az egyéni szerepkör teljes törlése eltarthat néhány percig.

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: egyéni Azure-szerepkör létrehozása Azure PowerShell használatával](tutorial-custom-role-powershell.md)
- [Egyéni Azure-szerepkörök](custom-roles.md)
- [Azure erőforrás-szolgáltatói műveletek](resource-provider-operations.md)
