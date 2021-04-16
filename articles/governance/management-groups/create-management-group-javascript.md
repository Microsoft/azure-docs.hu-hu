---
title: 'Rövid útmutató: Felügyeleti csoport létrehozása JavaScripttel'
description: Ebben a rövid útmutatóban JavaScript használatával hoz létre egy felügyeleti csoportot, amely erőforrás-hierarchiába rendezi az erőforrásokat.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 553133f30bd16543444356e8cda3e70458247b5c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536003"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Rövid útmutató: Felügyeleti csoport létrehozása JavaScripttel

A felügyeleti csoportok olyan tárolók, amelyek segítségével több előfizetés hozzáférését, szabályzatát és megfelelőségét kezelheti. Hozza létre ezeket a tárolókat egy hatékony és [](../policy/overview.md) hatékony hierarchia létrehozásához, amely a Azure Policy azure-beli szerepköralapú [hozzáférés-vezérléssel együtt használható.](../../role-based-access-control/overview.md) További információ a felügyeleti csoportokról: [Erőforrások rendszerezése Azure-beli felügyeleti csoportokkal.](overview.md)

A címtárban létrehozott első felügyeleti csoport létrehozása akár 15 percet is igénybe vehet. Vannak olyan folyamatok, amelyek az első alkalommal futnak, amikor beállítják a felügyeleticsoport-szolgáltatást az Azure-ban a címtárhoz. A folyamat befejezésekor értesítést kap. További információkért lásd a [felügyeleti csoportok kezdeti beállítását.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Mielőtt elkezdené, győződjön meg arról, hogy aNode.jslegalább [ 12-es ](https://nodejs.org/) verziója telepítve van.

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoport írási engedélye hozzá lenne rendelve a felhasználóhoz, ha a [hierarchiavédelem](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nincs engedélyezve. Ez az új felügyeleti csoport a gyökér [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) felügyeleti csoport vagy az alapértelmezett felügyeleti csoport gyermeke lesz, és a létrehozó "Tulajdonos" szerepkör-hozzárendelést kap. A felügyeleticsoport-szolgáltatás lehetővé teszi ezt a képességet, így a gyökérszinten nincs szükség szerepkör-hozzárendelésre. Létrehozáskor egyetlen felhasználó sem fér hozzá a gyökér felügyeleti csoporthoz. Annak érdekében, hogy ne kelljen megtalálni az Azure AD globális rendszergazdáit a felügyeleti csoportok használatának elkezdésében, engedélyezhetjük a kezdeti felügyeleti csoportok létrehozását a gyökérszinten.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Alkalmazásbeállítás

Ahhoz, hogy a JavaScript kezelni tudja a felügyeleti csoportokat, be kell állítani a környezetet. Ez a beállítás bárhol működik, ahol a JavaScript használható, beleértve a [Bash on Windows 10.](/windows/wsl/install-win10)

1. Állítson be egy új Node.js a következő parancs futtatásával.

   ```bash
   npm init -y
   ```

1. Adjon hozzá egy hivatkozást a yargs modulhoz.

   ```bash
   npm install yargs
   ```

1. Adjon hozzá egy hivatkozást a Azure Resource Graph modulhoz.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Adjon hozzá egy, az Azure-beli hitelesítési kódtárra vonatkozó hivatkozást.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Ellenőrizze, _hogy apackage.js_ `@azure/arm-managementgroups` **1.1.0-s** vagy újabb, és `@azure/ms-rest-nodeauth` **3.0.5-ös** vagy újabb verziójú-e.

## <a name="create-the-management-group"></a>A felügyeleti csoport létrehozása

1. Hozzon létre egy új, _index.js_ nevű fájlt, és írja be a következő kódot.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.managementGroups.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. Írja be a következő parancsot a terminálba:

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   Minden jogkivonat helyőrzőt cserélje le a felügyeleti csoport azonosítójára, illetve a felügyeleti csoport `<>` _rövid_  nevére.

   Amikor a szkript megkísérli a hitelesítést, a terminálon a következőhöz hasonló üzenet jelenik meg:

   > A bejelentkezéshez egy webböngészővel nyissa meg az oldalt, és írja be az https://microsoft.com/devicelogin FGB56WJUGK kódot a hitelesítéshez.

   A böngészőben való hitelesítés után a szkript továbbra is futni fog.

A felügyeleti csoport létrehozásának eredménye a konzol kimenete lesz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a telepített kódtárakat az alkalmazásból, futtassa a következő parancsot.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport előfizetéseket vagy más felügyeleti csoportokat is képes tartani.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezelésével kapcsolatban, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)
