---
title: 'Rövid útmutató: új szabályzat-hozzárendelés JavaScripttel'
description: Ebben a rövid útmutatóban a JavaScript használatával hozzon létre egy Azure Policy-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 09/24/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 7548053e4bd5be214bf7de3eef3dc4c6c95442d4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91349001"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához a JavaScript használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre a felügyelt lemezeket nem használó virtuális gépek azonosításához. Ha elkészült, azonosíthatja azokat a virtuális gépeket, amelyek _nem megfelelőek_.

A JavaScript-kódtár az Azure-erőforrások parancssorból vagy parancsfájlokból való kezelésére szolgál. Ez az útmutató ismerteti, hogyan hozhat létre szabályzat-hozzárendelést a JavaScript-kódtár használatával.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- **Node.js**: a 12-es vagy újabb verziójú [Node.js](https://nodejs.org/) megadása kötelező.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>A házirend-kódtárak hozzáadása

Annak engedélyezéséhez, hogy a JavaScript működjön a Azure Policyval, a kódtárakat hozzá kell adni. Ezek a kódtárak bárhol használhatók a JavaScript használatára, beleértve [a bash-et a Windows 10 rendszeren](/windows/wsl/install-win10)is.

1. Hozzon létre egy új Node.js projektet a következő parancs futtatásával.

   ```bash
   npm init -y
   ```

1. Adjon hozzá egy hivatkozást a yargs-könyvtárhoz.

   ```bash
   npm install yargs
   ```

1. Adjon hozzá egy hivatkozást az Azure Policy-tárakhoz.

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Adjon hozzá egy hivatkozást az Azure Authentication Library-hez.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > A (z) _package.json_ `@azure/arm-policy` verzió **3.1.0** vagy újabb verziója a következő: 3.2.0 vagy újabb verzió, `@azure/arm-policyinsights` és  `@azure/ms-rest-nodeauth` **3.0.5** vagy újabb verziójú.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és hozzárendeli **azokat a naplózási virtuális gépeket, amelyek nem használnak Managed Disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ) definíciót. Ez a szabályzat-definíció olyan erőforrásokat azonosít, amelyek nem felelnek meg a szabályzat-definícióban meghatározott feltételeknek.

1. Hozzon létre egy _policyAssignment.js_ nevű új fájlt, és adja meg a következő kódot.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. Adja meg a következő parancsot a terminálon:

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

Az előző parancsok a következő információkat használják:

- **subID** – a hitelesítési környezet előfizetés-azonosítója. Ne felejtse el lecserélni az `{subscriptionId}` előfizetését.
- **név** – a házirend-hozzárendelési objektum egyedi neve. A fenti példa a _manageddisks-_ t használja.
- **DisplayName** – a szabályzat-hozzárendelés megjelenítendő neve. Ebben az esetben a _virtuális gépek naplózása felügyelt lemezek hozzárendelése nélkül_ történik.
- **policyDefID** – a házirend-definíció elérési útja, amely alapján létrehozza a hozzárendelést. Ebben az esetben ez a házirend-definíciós _virtuális gépek azonosítója, amelyek nem használnak felügyelt lemezeket_.
- **description (Leírás** ) – mélyebb magyarázat arról, hogy mit tesz a szabályzat, vagy miért van hozzárendelve ehhez a hatókörhöz.
- **hatókör** – a hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrásokat vagy erőforrásokat kíván érvényesíteni. Egy felügyeleti csoportból egy adott erőforrásra terjedhet. Ügyeljen arra, hogy a `{scope}` következő minták egyikét cserélje le:
  - Felügyeleti csoport: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Előfizetés: `/subscriptions/{subscriptionId}`
  - Erőforráscsoport: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Erőforrás `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelőségi állapotának megismerése érdekében.

## <a name="identify-non-compliant-resources"></a>A nem megfelelő erőforrások azonosítása

Most, hogy létrehozta a szabályzat-hozzárendelést, azonosíthatja azokat az erőforrásokat, amelyek nem megfelelőek.

1. Hozzon létre egy _policyState.js_ nevű új fájlt, és adja meg a következő kódot.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. Adja meg a következő parancsot a terminálon:

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

A helyére írja be azt `{subscriptionId}` az előfizetést, amelyben meg szeretné tekinteni az előző lépésben létrehozott "audit-VM-manageddisks" nevű szabályzat-hozzárendelés megfelelőségi eredményeit. A további hatókörök és az adatösszesítési módok listáját lásd: [PolicyStates *](/javascript/api/@azure/arm-policyinsights/) metódusok.

Az eredmények a következő példához hasonlók:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Az eredmények megegyeznek a szabályzat-hozzárendelés **erőforrás-megfelelőség** lapján látható Azure Portal nézetben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

- Törölje a házirend-hozzárendelési _naplózási virtuális gépeket a felügyelt lemezek nélkül_ , a portálon keresztül. A házirend-definíció beépített, így nem lehet eltávolítani a definíciót.

- Ha el szeretné távolítani a telepített kódtárakat az alkalmazásból, futtassa a következő parancsot.

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

Ha többet szeretne megtudni a szabályzat-definíciók hozzárendeléséről, hogy ellenőrizze, hogy az új erőforrások megfelelőek-e, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)