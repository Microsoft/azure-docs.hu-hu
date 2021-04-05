---
title: 'Rövid útmutató: az első go-lekérdezés'
description: Ebben a rövid útmutatóban az erőforrás-gráf csomagjának engedélyezéséhez szükséges lépéseket követheti, és futtathatja az első lekérdezést.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 4949801b3dc97904680d09e685fd225812a0e14d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920062"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>Rövid útmutató: az első Resource Graph-lekérdezés futtatása a go használatával

Az Azure Resource Graph használatának első lépése, hogy meggyőződjön arról, hogy telepítve vannak-e a szükséges csomagok a Go-hoz. Ez a rövid útmutató végigvezeti a csomagok go-telepítéshez való hozzáadásának folyamatán.

A folyamat végén hozzá kell adni a csomagokat a go-telepítéshez, és futtatnia kell az első Resource Graph-lekérdezést.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="add-the-resource-graph-package"></a>Az erőforrás-gráf csomag hozzáadása

Ha engedélyezni szeretné az Azure Resource Graph lekérdezését, fel kell vennie a csomagot. Ez a csomag bárhol használható, beleértve [a Windows 10 vagy a](/windows/wsl/install-win10) helyileg telepített bash-et is.

1. Győződjön meg arról, hogy a legújabb go telepítve van (legalább **1,14**). Ha még nincs telepítve, töltse le a következő címen: [Golang.org](https://golang.org/dl/).

1. Győződjön meg arról, hogy a legújabb Azure CLI telepítve van (legalább **2.5.1**). Ha még nincs telepítve, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

   > [!NOTE]
   > Az alábbi példában szereplő metódus használatához az Azure CLI szükséges `auth.NewAuthorizerFromCLI()` . További információ az egyéb lehetőségekről: [Go nyelvhez készült Azure SDK – további hitelesítési részletek](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Hitelesítés az Azure CLI-n keresztül.

   ```azurecli
   az login
   ```

1. A go-környezetében telepítse az Azure Resource Graph szükséges csomagjait:

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Ha a go-csomagok bekerülnek a környezetbe, itt az ideje, hogy kipróbáljon egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást adja vissza az egyes erőforrások **nevével** és **erőforrás-típusával** .

1. Hozza létre a go alkalmazást, és mentse a következő forrást `argQuery.go` :

   ```Go
   package main
   
   import (
      "fmt"
      "os"
      "context"
      "strconv"
      arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
      "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
       // Get variables from command line arguments
       var query = os.Args[1]
       var subList = os.Args[2:]
   
       // Create and authorize a ResourceGraph client
       argClient := arg.New()
       authorizer, err := auth.NewAuthorizerFromCLI()
       if err == nil {
           argClient.Authorizer = authorizer
       } else {
           fmt.Printf(err.Error())
       }
     
       // Set options
       RequestOptions := arg.QueryRequestOptions {
           ResultFormat: "objectArray",
       }
     
       // Create the query request
       Request := arg.QueryRequest {
           Subscriptions: &subList,
           Query: &query,
           Options: &RequestOptions,
       }
     
       // Run the query and get the results
       var results, queryErr = argClient.Resources(context.Background(), Request)
       if queryErr == nil {
           fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
           fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
       } else {
           fmt.Printf(queryErr.Error())
       }
   }
   ```

1. A Go-alkalmazás összeállítása:

   ```bash
   go build argQuery.go
   ```

1. Futtassa az első Azure Resource Graph-lekérdezést a lefordított Go-alkalmazás használatával. A helyére írja `<SubID>` be az előfizetés-azonosítóját:

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > Ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint az `order by`, tehát ha többször is futtatja, valószínűleg minden kéréssel eltérő erőforráslistát fog kapni.

1. Módosítsa az első paramétert, `argQuery` és módosítsa a lekérdezést `order by` a **Name (név** ) tulajdonságra. A helyére írja `<SubID>` be az előfizetés-azonosítóját:

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancs először a lekérdezés eredményeit korlátozza, majd megrendeli azokat.

1. Módosítsa az első paramétert, `argQuery` és módosítsa a lekérdezést a `order by` **Name (név** ) tulajdonságra, majd az első `limit` öt találatra. A helyére írja `<SubID>` be az előfizetés-azonosítóját:

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

Ha a végső lekérdezés többször is fut, feltételezve, hogy a környezetében semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** tulajdonság szerint vannak rendezve, de továbbra is az első öt találatra korlátozódnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a telepített csomagokat a go-környezetből, ezt a következő paranccsal teheti meg:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzáadta az erőforrás-Graph-csomagokat a go-környezethez, és futtatta az első lekérdezést. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)