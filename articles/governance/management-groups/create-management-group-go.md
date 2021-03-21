---
title: 'Gyors útmutató: felügyeleti csoport létrehozása a go-val'
description: Ebben a rövid útmutatóban a go használatával hozzon létre egy felügyeleti csoportot az erőforrások erőforrás-hierarchiába rendezéséhez.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b6ca4d10f2a86ecb55fec2afe72b4aabfbb94f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101776"
---
# <a name="quickstart-create-a-management-group-with-go"></a>Gyors útmutató: felügyeleti csoport létrehozása a go-val

A felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférést, a szabályzatot és a megfelelőséget több előfizetés között. Hozza létre ezeket a tárolókat egy olyan hatékony és hatékony hierarchia létrehozásához, amely a [Azure Policy](../policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md)használható. A felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](overview.md).

A címtárban létrehozott első felügyeleti csoport akár 15 percet is igénybe vehet. Az Azure-ban a címtárhoz a felügyeleti csoportok szolgáltatás beállításához első alkalommal futó folyamatok futnak. A folyamat befejezésekor értesítést kap. További információ: [a felügyeleti csoportok kezdeti beállítása](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Egy Azure-szolgáltatásnév, beleértve a _clientId_ és a _clientSecret_. Ha nem rendelkezik a szolgáltatással való használatra Azure Policy vagy újat szeretne létrehozni, tekintse meg a .NET- [hitelesítéshez készült Azure felügyeleti kódtárakat](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ugorja át a lépést a .NET Core-csomagok telepítéséhez, ahogy azt a következő lépésekben tesszük.

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoport írási engedélye nincs hozzárendelve ehhez a felhasználóhoz, ha nincs engedélyezve a [hierarchia védelme](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Ez az új felügyeleti csoport a gyökérszintű felügyeleti csoport vagy az [alapértelmezett felügyeleti](./how-to/protect-resource-hierarchy.md#setting---default-management-group) csoport gyermeke lesz, a létrehozó pedig "tulajdonos" szerepkör-hozzárendelést kap. A felügyeleti csoport szolgáltatás lehetővé teszi, hogy a szerepkör-hozzárendelések nem szükségesek a gyökérszintű szinten. A létrehozáskor egyetlen felhasználó sem férhet hozzá a gyökérszintű felügyeleti csoporthoz. Ha el szeretné kerülni, hogy az Azure AD globális rendszergazdái megkeressék a felügyeleti csoportokat, lehetővé tesszük a kezdeti felügyeleti csoportok legfelső szintű létrehozását.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>A felügyeleti csoport csomagjának hozzáadása

Ha engedélyezni szeretné a Go-t a felügyeleti csoportok kezeléséhez, a csomagot hozzá kell adni. Ez a csomag bárhol használható, beleértve [a Windows 10 vagy a](/windows/wsl/install-win10) helyileg telepített bash-et is.

1. Győződjön meg arról, hogy a legújabb go telepítve van (legalább **1,15**). Ha még nincs telepítve, töltse le a következő címen: [Golang.org](https://golang.org/dl/).

1. Győződjön meg arról, hogy a legújabb Azure CLI telepítve van (legalább **2.5.1**). Ha még nincs telepítve, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

   > [!NOTE]
   > Az alábbi példában szereplő metódus használatához az Azure CLI szükséges `auth.NewAuthorizerFromCLI()` . További információ az egyéb lehetőségekről: [Go nyelvhez készült Azure SDK – további hitelesítési részletek](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Hitelesítés az Azure CLI-n keresztül.

   ```azurecli
   az login
   ```

1. A go-környezetében telepítse a szükséges csomagokat a felügyeleti csoportokhoz:

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>Alkalmazásbeállítás

Ha a go-csomagokat hozzáadta a környezetéhez, itt az ideje, hogy beállítsa a go alkalmazást, amely létrehoz egy felügyeleti csoportot.

1. Hozza létre a go alkalmazást, és mentse a következő forrást `mgCreate.go` :

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. A Go-alkalmazás összeállítása:

   ```bash
   go build mgCreate.go
   ```

1. Hozzon létre egy felügyeleti csoportot a lefordított Go-alkalmazás használatával. Cserélje le az `<Name>` nevet az új felügyeleti csoport nevére:

   ```bash
   mgCreate "<Name>"
   ```

Az eredmény egy új felügyeleti csoport a gyökérszintű felügyeleti csoportban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a telepített csomagokat a go-környezetből, ezt a következő paranccsal teheti meg:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport rendelkezhet előfizetésekkel vagy más felügyeleti csoportokkal.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezeléséről, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)