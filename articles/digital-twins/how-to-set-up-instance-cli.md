---
title: Példány és hitelesítés beállítása (parancssori felület)
titleSuffix: Azure Digital Twins
description: 'Lásd: az Azure Digital Twins szolgáltatás egy példányának beállítása a parancssori felület használatával'
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2969ee5339306e30044a6a80260f912d45d0c8a2
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105058"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Azure digitális Twins-példány és-hitelesítés beállítása (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Ez a cikk az **új Azure Digital Twins-példány beállításának** lépéseit ismerteti, beleértve a példány létrehozását és a hitelesítés beállítását. A cikk elvégzése után egy Azure Digital Twins-példánnyal kell elkezdenie a programozást.

A cikk jelen verziója ezeket a lépéseket manuálisan, egyenként, a parancssori felület használatával hajtja végre.
* Ha ezeket a lépéseket manuálisan szeretné elvégezni a Azure Portal használatával, tekintse meg a jelen cikk portáljának verzióját: [*útmutató: példány és hitelesítés beállítása (portál)*](how-to-set-up-instance-portal.md).
* Ha az üzembe helyezési parancsfájl mintájának használatával szeretne automatikus telepítést végezni, tekintse meg a jelen cikk parancsfájlokkal ellátott verzióját: [*útmutató: példány és hitelesítés beállítása (megírt)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Cloud Shell munkamenet beállítása
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Az Azure Digital Twins-példány létrehozása

Ebben a szakaszban az **Azure Digital Twins új példányát fogja létrehozni** a Cloud Shell parancs használatával. A következőket kell megadnia:
* Egy erőforráscsoport, amelyben a példány üzembe lesz helyezve. Ha még nem rendelkezik már meglévő erőforráscsoporthoz, létrehozhat egyet ezzel a paranccsal:
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Egy régió az üzemelő példányhoz. Ha szeretné megtekinteni, hogy mely régiók támogatják az Azure Digital Twins-t, látogasson el az [*Azure-termékek területre*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* A példány neve. Ha az előfizetése egy másik Azure Digital Twins-példánnyal rendelkezik abban a régióban, amely már a megadott nevet használja, a rendszer arra kéri, hogy válasszon másik nevet.

A példány létrehozásához használja ezeket az értékeket a következő parancsban:

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Sikeres ellenőrzés és fontos értékek összegyűjtése

Ha a példány létrehozása sikeresen megtörtént, a Cloud Shell eredmény a következőképpen néz ki: a létrehozott erőforrással kapcsolatos információk kihelyezése:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Parancsablak az erőforráscsoport és az Azure Digital Twins-példány sikeres létrehozásával":::

Jegyezze fel az Azure Digital Twins-példány **állomásneve**, **neve** és **resourceGroup** a kimenetből. Ezek mind olyan fontos értékek, amelyekre szüksége lehet az Azure Digital Twins-példánnyal való munka folytatásához, a hitelesítés és a kapcsolódó Azure-erőforrások beállításához. Ha más felhasználók is programozást végeznek a példányon, ezeket az értékeket meg kell osztani velük.

> [!TIP]
> Ezeket a tulajdonságokat, valamint a példány összes tulajdonságát a futtatásával bármikor megtekintheti `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely készen áll a használatra. Ezután meg kell adnia a megfelelő Azure-felhasználói engedélyeket a kezeléséhez.

## <a name="set-up-user-access-permissions"></a>Felhasználói hozzáférési engedélyek beállítása

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

A következő parancs használatával rendelje hozzá a szerepkört (az Azure-előfizetéshez [megfelelő engedélyekkel](#prerequisites-permission-requirements) rendelkező felhasználónak kell futtatnia). A parancshoz a szerepkörhöz hozzárendelni kívánt felhasználó Azure AD-fiókjához tartozó *egyszerű* felhasználónevet kell átadnia. A legtöbb esetben ez megegyezik a felhasználó e-mail-címével az Azure AD-fiókban.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

Ennek a parancsnak az eredménye a létrehozott szerepkör-hozzárendeléssel kapcsolatos információ.

> [!NOTE]
> Ha a parancs hibát ad vissza, ami azt jelzi, hogy a parancssori **felület nem találja a felhasználó vagy az egyszerű szolgáltatásnév kifejezést a Graph adatbázisban**:
>
> Rendelje hozzá a szerepkört a felhasználó *objektumazonosító-azonosítójával* . Ez akkor fordulhat elő, ha a felhasználók személyes [Microsoft-fiókokkal (MSAs)](https://account.microsoft.com/account)rendelkeznek. 
>
> [Azure Active Directory felhasználók Azure Portal oldalán](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) válassza ki a felhasználói fiókot, és nyissa meg a részleteit. A felhasználó *ObjectId* másolása:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="A felhasználó oldalának nézete Azure Portal kiemelve a GUID azonosítót az &quot;objektumazonosító&quot; mezőben" lightbox="media/includes/user-id.png":::
>
> Ezután ismételje meg a szerepkör-hozzárendelési lista parancsát a felhasználó *objektumazonosító* használatával a `assignee` fenti paraméterhez.

### <a name="verify-success"></a>Sikeres ellenőrzés

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely készen áll a használatra, és hozzárendelt engedélyekkel rendelkezik a kezeléséhez.

## <a name="next-steps"></a>Következő lépések

Tesztelje az egyes REST API hívásokat a példányon az Azure Digital Twins CLI parancsaival: 
* [az DT Reference](/cli/azure/dt)
* [*Útmutató: az Azure digitális Twins parancssori felületének használata*](how-to-use-cli.md)

Vagy tekintse meg a következő témakört: ügyfélalkalmazás összekötése a példánnyal a hitelesítési kóddal:
* [*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)