---
title: 'Gyors útmutató: felügyeleti csoport létrehozása JavaScript-sel'
description: Ebben a rövid útmutatóban a JavaScript használatával hozzon létre egy felügyeleti csoportot, amely erőforrás-hierarchiába rendezi az erőforrásokat.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 25fd93e51c1b49519bb315aab28cdfc44a2a0c58
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593976"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Gyors útmutató: felügyeleti csoport létrehozása JavaScript-sel

A felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférést, a szabályzatot és a megfelelőséget több előfizetés között. Hozza létre ezeket a tárolókat egy olyan hatékony és hatékony hierarchia létrehozásához, amely a [Azure Policy](../policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md)használható. A felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](overview.md).

A címtárban létrehozott első felügyeleti csoport akár 15 percet is igénybe vehet. Az Azure-ban a címtárhoz a felügyeleti csoportok szolgáltatás beállításához első alkalommal futó folyamatok futnak. A folyamat befejezésekor értesítést kap. További információ: [a felügyeleti csoportok kezdeti beállítása](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Mielőtt elkezdené, győződjön meg arról, hogy a [Node.js](https://nodejs.org/) legalább 12-ös verziója telepítve van.

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoport írási engedélye nincs hozzárendelve ehhez a felhasználóhoz, ha nincs engedélyezve a [hierarchia védelme](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Ez az új felügyeleti csoport a gyökérszintű felügyeleti csoport vagy az [alapértelmezett felügyeleti](./how-to/protect-resource-hierarchy.md#setting---default-management-group) csoport gyermeke lesz, a létrehozó pedig "tulajdonos" szerepkör-hozzárendelést kap. A felügyeleti csoport szolgáltatás lehetővé teszi, hogy a szerepkör-hozzárendelések nem szükségesek a gyökérszintű szinten. A létrehozáskor egyetlen felhasználó sem férhet hozzá a gyökérszintű felügyeleti csoporthoz. Ha el szeretné kerülni, hogy az Azure AD globális rendszergazdái megkeressék a felügyeleti csoportokat, lehetővé tesszük a kezdeti felügyeleti csoportok legfelső szintű létrehozását.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Alkalmazásbeállítás

Annak engedélyezéséhez, hogy a JavaScript lekérdezze az Azure Resource Graphot, a környezetet be kell állítani. Ez a beállítás bárhol használható a JavaScript használatával, beleértve [a bash használatát a Windows 10 rendszeren](/windows/wsl/install-win10).

1. Hozzon létre egy új Node.js projektet a következő parancs futtatásával.

   ```bash
   npm init -y
   ```

1. Adjon hozzá egy hivatkozást a yargs modulhoz.

   ```bash
   npm install yargs
   ```

1. Adjon hozzá egy hivatkozást az Azure Resource Graph-modulhoz.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Adjon hozzá egy hivatkozást az Azure Authentication Library-hez.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Ellenőrizze, hogy a (z) _package.js_ -es `@azure/arm-managementgroups` verziója **1.1.0** vagy újabb, és `@azure/ms-rest-nodeauth` a verziója **3.0.5** vagy újabb.

## <a name="create-the-management-group"></a>A felügyeleti csoport létrehozása

1. Hozzon létre egy _index.js_ nevű új fájlt, és adja meg a következő kódot.

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

1. Adja meg a következő parancsot a terminálon:

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   Ügyeljen arra, hogy az egyes jogkivonat- `<>` helyőrzőket cserélje le a _felügyeleti csoport azonosítóját_ és a _felügyeleti csoport rövid nevét_.

   Ahogy a parancsfájl megkísérli a hitelesítést, az alábbi üzenethez hasonló üzenet jelenik meg a terminálon:

   > A bejelentkezéshez egy webböngésző segítségével nyissa meg a lapot, https://microsoft.com/devicelogin és adja meg a hitelesíteni kívánt FGB56WJUGK.

   Miután végzett a hitelesítéssel a böngészőben, a szkript továbbra is futni fog.

A felügyeleti csoport létrehozásának eredménye kimenet a konzolon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a telepített kódtárakat az alkalmazásból, futtassa a következő parancsot.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport rendelkezhet előfizetésekkel vagy más felügyeleti csoportokkal.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezeléséről, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)