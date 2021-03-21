---
title: Rövid útmutató – privát végpont létrehozása ARM-sablonnal
description: Ebben a rövid útmutatóban egy Azure Resource Manager sablont (ARM-sablont) használ egy privát végpont létrehozásához.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: e80adc16e08e676ac2daabec01a11c10d537c547
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562945"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-arm-template"></a>Rövid útmutató: privát végpont létrehozása ARM-sablonnal

Ebben a rövid útmutatóban egy Azure Resource Manager sablont (ARM-sablont) használ egy privát végpont létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ezt a rövid útmutatót a [Azure Portal](create-private-endpoint-portal.md), [Azure PowerShell](create-private-endpoint-powershell.md)vagy az [Azure CLI](create-private-endpoint-cli.md)használatával is elvégezheti.

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiókra van szüksége. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>A sablon áttekintése

Ez a sablon egy Azure SQL Database-példányhoz hoz létre egy privát végpontot.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/) közül származik.

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. SQL/Servers**](/azure/templates/microsoft.sql/servers): a mintaadatbázis SQL Database példánya.
- [**Microsoft. SQL/kiszolgálók/adatbázisok**](/azure/templates/microsoft.sql/servers/databases): a mintaadatbázis.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): az a virtuális hálózat, amelyben a magánhálózati végpont üzembe lett helyezve.
- [**Microsoft. Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints): a SQL Database példányának eléréséhez használt magánhálózati végpont.
- [**Microsoft. Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones): a magánhálózati végpont IP-címének feloldásához használt zóna.
- [**Microsoft. Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft. Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups): a magánhálózati végpont magánhálózati DNS-zónához való hozzárendeléséhez használt zóna csoport.
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicIpAddresses): a virtuális gép eléréséhez használt nyilvános IP-cím.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): a virtuális gép hálózati adaptere.
- [**Microsoft. számítási/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): az a virtuális gép, amely a privát végponttal való magánhálózati kapcsolódás tesztelésére használatos a SQL Database példányával.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az ARM-sablon üzembe helyezése az Azure-ban:

1. Az Azure-ba való bejelentkezéshez és a sablon megnyitásához válassza az **üzembe helyezés az Azure**-ban lehetőséget. A sablon létrehozza a privát végpontot, a SQL Database példányát, a hálózati infrastruktúrát és az érvényesíteni kívánt virtuális gépet.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Válassza ki vagy hozza létre az erőforráscsoportot.
3. Írja be az SQL-rendszergazda bejelentkezési és jelszavát.
4. Írja be a virtuális gép rendszergazdai felhasználónevét és jelszavát.
5. Olvassa el a feltételek és kikötések utasítást. Ha elfogadja, jelölje be az Elfogadom a vásárláskor **megjelenő feltételeket és kikötéseket**  >  . Az üzembe helyezés akár 20 percet vagy hosszabb időt is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

> [!NOTE]
> Az ARM-sablon egyedi nevet hoz létre a (z)<b>{UniqueID}</b> virtuális gép myVm, valamint a SQL Database SQLServer<b>{UniqueID}</b> erőforráshoz. Helyettesítse be a generált értéket **{UniqueID}** értékre.

### <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Kapcsolódjon a _(z) {UniqueID}_ virtuális gép myVm az internetről a következőképpen:

1. A portál keresősáv mezőjébe írja be a _myVm {UniqueID}_ értéket.

2. Válassza a **Kapcsolódás** lehetőséget. Megnyílik **a virtuális géphez való kapcsolódás** .

3. Válassza az **RDP-fájl letöltése** lehetőséget. Az Azure létrehoz egy RDP protokoll (_. rdp_) fájlt, és letölti a számítógépre.

4. Nyissa meg a letöltött .rdp fájlt.

   a. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

   b. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

      > [!NOTE]
      > Előfordulhat, hogy a   >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania **egy másik fiók használatával**.

5. Válassza az **OK** lehetőséget.

6. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás** lehetőséget.

7. A virtuális gép asztalának megjelenése után csökkentse a helyi asztalra való visszalépést.

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>A SQL Database-kiszolgáló magánhálózati elérése a virtuális gépről

A következőképpen csatlakozhat a virtuális gépről a SQL Database-kiszolgálóhoz a privát végpont használatával.

1.  A _myVM ({UniqueID}_) távoli asztal nyissa meg a PowerShellt.
2.  Adja meg a következőt: nslookup SQLServer {UniqueID}. database. Windows. net. 
    Egy ehhez hasonló üzenet jelenik meg:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Telepítse a SQL Server Management Studio.
4.  A **Kapcsolódás a kiszolgálóhoz** lapon adja meg vagy válassza ki az alábbi adatokat:
    - **Kiszolgáló típusa**: válassza az **adatbázismotor** lehetőséget.
    - **Kiszolgáló neve**: válassza a **SQLServer {UniqueID}. database. Windows. net** elemet.
    - **Felhasználónév**: adjon meg egy, a létrehozás során megadott felhasználónevet.
    - **Password (jelszó**): adja meg a létrehozás során megadott jelszót.
    - **Jelszó megjegyzése**: válassza az  **Igen** lehetőséget.

5.  Válassza a **Kapcsolódás** lehetőséget.
6.  A bal oldali menüben válassza az **adatbázisok** elemet.
7.  Lehetőség van arra is, hogy adatokat hozzon létre vagy lekérdezzen a _Sample-db-_ ből.
8.  A _(távoli asztal) {UniqueID} myVm_ való kapcsolódás lezárása.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a privát végponttal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a magánhálózati végpontot és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

A privát végpontot támogató szolgáltatásokkal kapcsolatos további információkért lásd:
> [!div class="nextstepaction"]
> [Privát kapcsolat elérhetősége](private-link-overview.md#availability)
