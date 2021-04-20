---
title: Azure IoT Hub hálózatok támogatása
description: Virtuális hálózatok kapcsolati mintája és IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jlian
ms.openlocfilehash: df38f9b3482847ea0415af5cb47540e244b0510b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739889"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT Hub és felügyelt identitással Private Link virtuális hálózatok támogatása

Alapértelmezés szerint IoT Hub gazdagépnevei egy nyilvános végpontra vannak leképezve, amely nyilvánosan átirányítható IP-címmel van megjelölve az interneten keresztül. A különböző ügyfelek ezt IoT Hub nyilvános végponton keresztül, a nagy kiterületű hálózatokon és helyszíni hálózatokon található IoT-eszközök pedig mind hozzáférhetnek.

![IoT Hub végpont](./media/virtual-network-support/public-endpoint.png)

IoT Hub szolgáltatásokhoz, [](./iot-hub-devguide-messages-d2c.md)például az üzenetek útválasztásához, [](./iot-hub-devguide-file-upload.md)a fájlfeltöltéshez és a tömeges [eszközimporthoz/exportáláshoz](./iot-hub-bulk-identity-mgmt.md) is szükség van a IoT Hub és egy ügyfél tulajdonában lévő Azure-erőforrás közötti kapcsolatra a nyilvános végponton keresztül. Ezek a kapcsolódási útvonalak együttesen a bejövő forgalomból az ügyfelek IoT Hub teszik ki.

Érdemes lehet korlátozni az Azure-erőforrásokhoz (beleértve a IoT Hub) való csatlakozást egy Ön tulajdonában álló és működtetett virtuális hálózaton keresztül. Ezek az okok a következők:

* Az IoT Hub hálózatelszigetelésének bevezetése a kapcsolat nyilvános internettel való kitettségének megakadályozásával.

* A helyszíni hálózati eszközök privát kapcsolati élményének lehetővé tételével biztosíthatja, hogy az adatok és a forgalom közvetlenül az Azure gerinchálózatára továbbítva jöjjön létre.

* A bizalmas helyszíni hálózatokból való kiszivárgási támadások megelőzése. 

* A már létrehozott Azure-beli kapcsolati mintákat követve privát [végpontokat használ.](../private-link/private-endpoint-overview.md)

Ez a cikk bemutatja, hogyan érheti el ezeket a célokat a [Azure Private Link](../private-link/private-link-overview.md) használatával a IoT Hub-hoz való bejövő forgalomhoz való csatlakozáshoz és megbízható Microsoft-szolgáltatások-kivételekhez a IoT Hub és más Azure-erőforrások között.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Bejövő kapcsolatok IoT Hub Azure Private Link

A privát végpontok az ügyfél tulajdonában lévő virtuális hálózaton belül lefoglalt privát IP-címek, amelyeken keresztül egy Azure-erőforrás elérhető. Az Azure Private Link segítségével privát végpontot állíthat be az IoT Hubhoz, hogy a VNeten belüli szolgáltatások elér IoT Hub-t anélkül, hogy a IoT Hub nyilvános végpontjára kellene küldenie a forgalmat. Hasonlóképpen, a helyszíni eszközök virtuális [magánhálózati (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy ExpressRoute-társviszony-létesítés használatával létesíthetik a kapcsolatot a virtuális hálózattal és a IoT Hub (annak privát végpontján keresztül). [](https://azure.microsoft.com/services/expressroute/) Ennek eredményeképpen egy IP-szűrővel vagy a nyilvános hálózati hozzáférés váltógombbal korlátozhatja vagy teljesen letilthatja az IoT [IoT Hub](./iot-hub-ip-filtering.md) Hub nyilvános végpontjaihoz való [csatlakozást.](iot-hub-public-network-access.md) Ez a megközelítés megtartja a hubhoz való csatlakozást az eszközök privát végpontjának használatával. A beállítás elsősorban a helyszíni hálózaton belüli eszközökre koncentrál. Ez a beállítás nagy kiszéles hálózatban üzembe helyezett eszközökhöz nem javasolt.

![IoT Hub hálózati bejövő forgalom](./media/virtual-network-support/virtual-network-ingress.png)

A folytatás előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Létrehozott egy [Azure VNetet](../virtual-network/quick-create-portal.md) egy alhálózattal, amelyben a privát végpont létre lesz hozva.

* A helyszíni hálózatokon működő eszközökhöz állítson be virtuális [magánhálózatot (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoute privát](https://azure.microsoft.com/services/expressroute/) társviszonyt az Azure-beli virtuális hálózattal.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Privát végpont beállítása a IoT Hub számára

A privát végpont IoT Hub API-khoz (például az eszközről a felhőbe küldött üzenetekhez), valamint a szolgáltatási API-khoz (például eszközök létrehozásához és frissítéséhez).

1. A Azure Portal válassza a **Hálózat,** Privát **végpontkapcsolatok** lehetőséget, majd kattintson a + Privát **végpontra.**

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Képernyőkép a privát végpontok hozzáadásának IoT Hub":::

1. Adja meg az előfizetést, az erőforráscsoportot, a nevet és a régiót az új privát végpont létrehozásához. Ideális esetben a privát végpontot ugyanabban a régióban kell létrehozni, mint a központot.

1. Kattintson a **Tovább:** Erőforrás elemre, és adja meg az IoT Hub-erőforrás előfizetését, majd válassza a **"Microsoft.Devices/IotHubs"** lehetőséget erőforrástípusként, az IoT Hub erőforrás nevét pedig erőforrásként, cél-alerőforrásként pedig az **iotHubot.** 

1. Kattintson **a Tovább: Konfigurálás** elemre, és adja meg a virtuális hálózatot és az alhálózatot a privát végpont létrehozásához. Szükség esetén válassza az Azure privát DNS-zónával való integráció lehetőséget.

1. Kattintson **a Tovább: Címkék** elemre, és opcionálisan adja meg az erőforrás címkéit.

1. Kattintson **az Áttekintés + létrehozás gombra** a privát kapcsolati erőforrás létrehozásához.

### <a name="built-in-event-hub-compatible-endpoint"></a>Beépített Event Hub-kompatibilis végpont 

A [beépített Event Hub-kompatibilis végpont](iot-hub-devguide-messages-read-builtin.md) privát végponton keresztül is elérhető. A privát kapcsolat konfigurálásakor egy további privát végpontkapcsolatot kell látnia a beépített végponthoz. Ez az `servicebus.windows.net` FQDN-ben a következővel: .

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="Két privát végpontot bemutató kép IoT Hub kapcsolathoz":::

IoT Hub [IP-szűrője](iot-hub-ip-filtering.md) szabályozhatja a beépített végpont nyilvános hozzáférését. 

Az IoT Hub nyilvános hálózati hozzáférésének teljes letiltásához kapcsolja ki a nyilvános hálózati [hozzáférést,](iot-hub-public-network-access.md) vagy használjon IP-szűrőt az összes IP-cím blokkolásához, és válassza a szabályok beépített végpontra való alkalmazásának beállítását.

### <a name="pricing-for-private-link"></a>A Private Link

A díjszabásról további információt a [díjszabást Azure Private Link talál.](https://azure.microsoft.com/pricing/details/private-link)

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>A forgalom és a IoT Hub azure-erőforrások között

IoT Hub azure blobtárolóhoz, eseményközponthoz, Service Bus-erőforrásokhoz csatlakozhat az üzenetek [útválasztásához,](./iot-hub-devguide-messages-d2c.md)a fájlfeltöltéshez [és](./iot-hub-devguide-file-upload.md)az eszközök tömeges importálásához/exportálásához az erőforrások nyilvános végpontjára. [](./iot-hub-bulk-identity-mgmt.md) Az erőforrás virtuális hálózathoz való kötése alapértelmezés szerint letiltja az erőforrással való kapcsolatot. Ennek eredményeképpen ez a konfiguráció megakadályozza, IoT Hub a felhasználók adatokat küldenek az erőforrásoknak. A probléma megoldásához engedélyezze a IoT Hub-erőforrás és a tárfiók, az eseményközpont vagy a Service Bus-erőforrások kapcsolatát a **megbízható Microsoft-szolgáltatás lehetőséggel.**

### <a name="turn-on-managed-identity-for-iot-hub"></a>Felügyelt identitások IoT Hub

Ahhoz, hogy más szolgáltatások megbízható Microsoft-szolgáltatásként találják meg az IoT Hubot, rendszer által hozzárendelt felügyelt identitással kell rendelkezik.

1. Lépjen az **Identity (Identitás)** lapra IoT Hub portálon

1. Az **Állapot alatt** válassza a **Be** lehetőséget, majd kattintson a **Mentés gombra.**

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Képernyőkép a felügyelt identitások IoT Hub":::

Az Azure CLI használata a felügyelt identitás bekapcsolához:

```azurecli-interactive
az iot hub update --name <iot-hub-resource-name> --set identity.type="SystemAssigned"
```

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Felügyelt identitás hozzárendelése a IoT Hub létrehozásakor ARM-sablon használatával

Ha felügyelt identitást szeretne hozzárendelni az IoT Hubhoz az erőforrás kiépítésekor, használja az alábbi ARM-sablont. Ez az ARM-sablon két szükséges erőforrással rendelkezik, és mindkettőt üzembe kell helyezni, mielőtt további erőforrásokat hoz létre, például `Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups` a-t. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "createIotHub",
      "dependsOn": [
        "[resourceId('Microsoft.Devices/IotHubs', '<provide-a-valid-resource-name>')]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

A , és erőforrás értékeinek behelyettesítését követően az Azure CLI használatával üzembe helyezheti az erőforrást `name` `location` egy meglévő `SKU.name` `SKU.tier` erőforráscsoportban a következővel:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Az erőforrás létrehozása után lekérheti a központhoz rendelt felügyeltszolgáltatás-identitást az Azure CLI használatával:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>A felügyelt identitások díjszabása

A Microsoft megbízható belső szolgáltatásainak kivételi funkciója ingyenes. A kiépített tárfiókok, eseményközpontok vagy Service Bus-erőforrások díjai külön vonatkoznak.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Kimenő forgalom csatlakozása a tárfiók végpontjaihoz útválasztáshoz

IoT Hub egy ügyfél tulajdonában lévő tárfiókba irányíthatja az üzeneteket. Ahhoz, hogy az útválasztási funkció hozzáférjen a tárfiókhoz, amíg tűzfalkorlátozások vannak érvényben, a központnak felügyelt identitást kell használnia a tárfiók eléréséhez. Először is a központnak szüksége lesz egy [felügyelt identitásra.](#turn-on-managed-identity-for-iot-hub) A felügyelt identitás kiépítése után kövesse az alábbi lépéseket, hogy Azure RBAC-engedélyt adjon a hub erőforrás-identitásának a tárfiók eléréséhez.

1. A Azure Portal nyissa meg a tárfiók Hozzáférés-vezérlés **(IAM)** lapját, és kattintson a Szerepkör-hozzárendelés hozzáadása szakasz Hozzáadás **gombjára.** 

2. Válassza a  **Storage-blobadatok** közreműködője (nem Közreműködő vagy Tárfiók-közreműködő) szerepkört, Azure [](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) **AD-felhasználót,** csoportot vagy szolgáltatásnevet a Hozzáférés hozzárendeléseként, majd válassza ki a IoT Hub erőforrásának nevét a legördülő listában.  Kattintson a **Mentés** gombra.

3. Lépjen a **tárfiók Tűzfalak és virtuális hálózatok** lapjára, és engedélyezze a Hozzáférés engedélyezése a kiválasztott **hálózatokról** beállítást. A Kivételek **listában** jelölje be az Allow trusted Microsoft-szolgáltatások to access this storage account (Megbízható felhasználók hozzáférésének engedélyezése a **tárfiókhoz) jelölőnégyzetet.** Kattintson a **Mentés** gombra.

4. A IoT Hub erőforráslapján lépjen az **Üzenetirányítás lapra.**

5. Lépjen az **Egyéni végpontok szakaszra,** és kattintson a **Hozzáadás gombra.** **Végponttípusként** válassza a Storage lehetőséget.

6. A megjelenő oldalon adja meg a végpont nevét, válassza ki a blobtárolóban használni kívánt tárolót, adja meg a kódolást és a fájlnév formátumát. A tárolási végpont  **hitelesítéstípusaként** válassza az Identitásalapú lehetőséget. Kattintson a **Létrehozás** gombra.

Most az egyéni tárolási végpont úgy van beállítva, hogy a központ rendszer által hozzárendelt identitását használja, és a tűzfalkorlátozások ellenére is rendelkezik engedéllyel a tárerőforrás eléréséhez. Most már használhatja ezt a végpontot egy útválasztási szabály beállításához.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Kimenő forgalom csatlakozása event hubs-végpontokra útválasztáshoz

IoT Hub konfigurálható úgy, hogy az üzeneteket egy ügyfél tulajdonában lévő Event Hubs-névtérbe irányítsa. Ahhoz, hogy az útválasztási funkció hozzáférjen egy Event Hubs-erőforráshoz, amíg tűzfalkorlátozások vannak érvényben, az IoT Hub-nek felügyelt identitással kell hozzáférnie az Event Hubs-erőforráshoz. Először is a központnak szüksége lesz egy felügyelt identitásra. A felügyelt identitás létrehozása után kövesse az alábbi lépéseket, hogy Azure RBAC-engedélyt adjon a központ erőforrás-identitásának az eseményközpontok eléréséhez.

1. A Azure Portal lépjen az Event Hubs Hozzáférés-vezérlés **(IAM)**  lapra, és kattintson a Hozzáadás elemre a Szerepkör-hozzárendelés **hozzáadása szakaszban.**

2. Válassza **Event Hubs** az Adatküldő szerepkör, **Azure AD-felhasználó,**  -csoport vagy -szolgáltatásnév lehetőséget a Hozzáférés hozzárendelése a következőhöz: beállításnál, majd válassza ki a IoT Hub erőforrásnevét a legördülő listából.  Kattintson a **Mentés** gombra.

3. Lépjen az **eseményközpontok** Tűzfalak és virtuális hálózatok lapra, és engedélyezze a Hozzáférés engedélyezése **a kiválasztott hálózatokról** beállítást. A Kivételek **listában** jelölje be az Allow trusted Microsoft-szolgáltatások to access event hubs (Megbízható felhasználók hozzáférhetnek az **Event Hubshoz) jelölőnégyzetet.** Kattintson a **Mentés** gombra.

4. A IoT Hub erőforráslapján lépjen az **Üzenetirányítás lapra.**

5. Lépjen az **Egyéni végpontok szakaszra,** és kattintson a **Hozzáadás gombra.** **Végponttípusként válassza** az Eseményközpontok lehetőséget.

6. A megjelenő oldalon adja meg a végpont nevét, majd válassza ki az Event Hubs-névteret és -példányt. **Hitelesítéstípusként válassza az Identitásalapú** **lehetőséget,** majd kattintson a **Létrehozás gombra.**

Most az egyéni Event Hubs-végpont úgy van beállítva, hogy a hub rendszer által hozzárendelt identitását használja, és a tűzfalkorlátozásai ellenére is rendelkezik engedéllyel az Event Hubs-erőforrás eléréséhez. Most már használhatja ezt a végpontot egy útválasztási szabály beállításához.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Kimenő forgalom csatlakozása Service Bus-végpontokra útválasztáshoz

IoT Hub konfigurálható úgy, hogy az üzeneteket egy ügyfél tulajdonában lévő Service Bus-névtérhez irányítsa. Ahhoz, hogy az útválasztási funkció hozzáférjen egy Service Bus-erőforráshoz, amíg tűzfalkorlátozások vannak érvényben, a IoT Hub felügyelt identitással kell hozzáférnie a Service Bus-erőforráshoz. Először is a központnak szüksége lesz egy felügyelt identitásra. A felügyelt identitás kiépítése után kövesse az alábbi lépéseket, hogy Azure RBAC-engedélyt adjon a központ erőforrás-identitásának a Service Bus eléréséhez.

1. A Azure Portal lépjen a Service Bus Hozzáférés-vezérlés **(IAM)** lapjára, és kattintson a Hozzáadás gombra a Szerepkör-hozzárendelés **hozzáadása szakaszban.** 

2. Válassza a Service  **Bus-adatküldő** szerepkör, Azure **AD-felhasználó,** -csoport vagy -szolgáltatásnév lehetőséget hozzáférés hozzárendeléseként, majd válassza ki a IoT Hub erőforrásnevét a legördülő listában.  Kattintson a **Mentés** gombra.

3. Lépjen a Service Bus **tűzfalak** és virtuális hálózatok lapjára, és engedélyezze **a Hozzáférés engedélyezése a kiválasztott hálózatokról** beállítást. A **Kivételek listában** jelölje be az Allow trusted Microsoft-szolgáltatások to access this Service Bus (Megbízható felhasználók hozzáférhetnek a **Service Bushoz) jelölőnégyzetet.** Kattintson a **Mentés** gombra.

4. A IoT Hub erőforráslapján lépjen az **Üzenetirányítás lapra.**

5. Lépjen az **Egyéni végpontok szakaszra,** és kattintson a **Hozzáadás gombra.** Válassza **a Service Bus-üzenetsor** **vagy Service Bus témakört** (ha van) végponttípusként.

6. A megjelenő oldalon adja meg a végpont nevét, és válassza ki a Service Bus névterét és üzenetsorát vagy témakörét (ha van). Hitelesítési **típusként válassza** az Identitásalapú **lehetőséget,** majd kattintson a **Létrehozás gombra.**

Most az egyéni Service Bus-végpont úgy van beállítva, hogy a hub rendszer által hozzárendelt identitását használja, és a tűzfalkorlátozások ellenére is rendelkezik engedéllyel a Service Bus-erőforrás eléréséhez. Most már használhatja ezt a végpontot egy útválasztási szabály beállításához.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>A tárfiókok bejövő adatkapcsolata fájlfeltöltéshez

IoT Hub eszköz fájlfeltöltési funkciója lehetővé teszi, hogy az eszközök fájlokat töltsenek fel egy ügyfél tulajdonában lévő tárfiókba. A fájlfeltöltés működéséhez az eszközöknek és IoT Hub kell a tárfiókhoz. Ha a tárfiókon tűzfalkorlátozások vannak érvényben, az eszközöknek a támogatott tárfiókok bármelyik [](../virtual-network/virtual-network-service-endpoints-overview.md)mechanizmusát (beleértve a privát végpontokat, [](../private-link/tutorial-private-endpoint-storage-portal.md)a szolgáltatásvégpontokat vagy a közvetlen tűzfalkonfigurációt) [](../storage/common/storage-network-security.md)kell használniuk a kapcsolathoz. Hasonlóképpen, ha tűzfalkorlátozások vannak érvényben a tárfiókon, IoT Hub konfigurálni kell a tárerőforrás megbízható Microsoft-szolgáltatások keresztül. Ebből a célból a IoT Hub felügyelt identitással kell lennie. A felügyelt identitás kiépítése után kövesse az alábbi lépéseket, hogy Azure RBAC-engedélyt adjon a hub erőforrás-identitásának a tárfiók eléréséhez.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. A Azure Portal nyissa meg a tárfiók Hozzáférés-vezérlés **(IAM)** lapját, és kattintson a Hozzáadás elemre a Szerepkör-hozzárendelés **hozzáadása szakaszban.** 

2. Válassza a  **Storage-blobadatok** közreműködője (nem közreműködő vagy tárfiók-közreműködő) szerepkört, Azure [](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) **AD-felhasználót,** csoportot vagy szolgáltatásnevet a Hozzáférés hozzárendelése a következőhöz: beállításnál, majd válassza ki a IoT Hub erőforrásának nevét a legördülő listából.  Kattintson a **Mentés** gombra.

3. Lépjen a **tárfiók Tűzfalak és virtuális hálózatok** lapjára, és engedélyezze a Hozzáférés engedélyezése a kiválasztott **hálózatokról** beállítást. A Kivételek **listában** jelölje be az Allow trusted Microsoft-szolgáltatások to access this storage account (Megbízható felhasználók hozzáférhetnek a **tárfiókhoz) jelölőnégyzetet.** Kattintson a **Mentés** gombra.

4. A IoT Hub erőforráslapján lépjen a **Fájlfeltöltés lapra.**

5. A megjelenő oldalon válassza ki a blobtárolóban használni kívánt tárolót, és szükség szerint konfigurálja a Fájlértesítési **beállításokat,** az **SAS TTL-t,** az alapértelmezett **TTL-t** és a Maximális **kézbesítési** számlálót. Válassza **az Identitásalapú** hitelesítési **típust** a tárolóvégponthoz. Kattintson a **Létrehozás** gombra. Ha ebben a lépésben hibaüzenetet kap, ideiglenesen állítsa be a tárfiókot úgy, hogy engedélyezze a hozzáférést a **Minden** hálózatból, majd próbálkozzon újra. A fájlfeltöltés konfigurálása után konfigurálhatja a tűzfalat a tárfiókon.

A fájlfeltöltés tárolási végpontja most már be van állítva a hub rendszer által hozzárendelt identitásának használatára, és a tűzfalkorlátozások ellenére is rendelkezik engedéllyel a tárolási erőforrás eléréséhez.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>A tárfiókok bejövő és bejövő kapcsolata eszközök tömeges importálása/exportálása esetén

IoT Hub támogatja az eszközök információinak tömeges [importálását/exportálását](./iot-hub-bulk-identity-mgmt.md) egy ügyfél által megadott tárolóblobba vagy blobba. A tömeges importálási/exportálási funkció működéséhez az eszközöknek és IoT Hub kell a tárfiókhoz való csatlakozáshoz.

Ehhez a funkcióhoz kapcsolatra van IoT Hub a tárfiókhoz. Ha egy Service Bus-erőforráshoz úgy fér hozzá, hogy érvényben vannak tűzfalkorlátozások, a IoT Hub felügyelt identitással kell rendelkezik. A felügyelt identitás kiépítése után kövesse az alábbi lépéseket, hogy Azure RBAC-engedélyt adjon a központ erőforrás-identitásának a Service Bus eléréséhez.

1. A Azure Portal nyissa meg a tárfiók Hozzáférés-vezérlés **(IAM)** lapját, és kattintson a Szerepkör-hozzárendelés hozzáadása szakasz Hozzáadás **gombjára.** 

2. Válassza a  **Storage-blobadatok** közreműködője (nem közreműködő vagy tárfiók-közreműködő) szerepkört, Azure [](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) **AD-felhasználót,** csoportot vagy szolgáltatásnevet a Hozzáférés hozzárendelése a következőhöz: beállításnál, majd válassza ki a IoT Hub erőforrásának nevét a legördülő listából.  Kattintson a **Mentés** gombra.

3. Lépjen a **tárfiók Tűzfalak és virtuális hálózatok** lapjára, és engedélyezze a Hozzáférés engedélyezése a kiválasztott **hálózatokról** beállítást. A Kivételek **listában** jelölje be az Allow trusted Microsoft-szolgáltatások to access this storage account (Megbízható felhasználók hozzáférésének engedélyezése a **tárfiókhoz) jelölőnégyzetet.** Kattintson a **Mentés** gombra.

Most már használhatja az Azure IoT REST API-kat importálási exportálási feladatok létrehozásához a tömeges importálási/exportálási funkció használatával kapcsolatos információkért. [](/rest/api/iothub/service/jobs/getimportexportjobs) Meg kell adnia a et a kérelem törzsében, és a és a adatokat kell használnia a tárfiók bemeneti és kimeneti `storageAuthenticationType="identityBased"` `inputBlobContainerUri="https://..."` `outputBlobContainerUri="https://..."` URL-címeiként.

Azure IoT Hub az SDK-k is támogatják ezt a funkciót a szolgáltatás ügyfél beállításjegyzék-kezelője számára. Az alábbi kódrészlet bemutatja, hogyan kezdeményezhet importálási feladatot vagy exportálási feladatot a -ben a C# SDK használatával.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```

Az Azure IoT SDK-k ezen verziójának használata A C#, a Java és a Node.js:

1. Hozzon létre egy nevű környezeti `EnableStorageIdentity` változót, és állítsa annak értékét `1` értékre.

2. Az SDK: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [Node.js](https://aka.ms/vnetnodesdk)
 
Python esetén töltse le a korlátozott verziókat a GitHubról.

1. Lépjen a [GitHub kiadási oldalára.](https://aka.ms/vnetpythonsdk)

2. Töltse le a következő fájlt, amely a kiadási oldal alján, az assets fejléc **alatt található.**
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Nyisson meg egy terminált, és keresse meg a mappát a letöltött fájllal.

4. Futtassa a következő parancsot a Python service SDK virtuális hálózatok támogatásával való telepítéséhez:
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozásokon további információt olvashat a IoT Hub funkcióiról:

* [Üzenetek útválasztása](./iot-hub-devguide-messages-d2c.md)
* [Fájlfeltöltés](./iot-hub-devguide-file-upload.md)
* [Eszközök tömeges importálása/exportálása](./iot-hub-bulk-identity-mgmt.md)
