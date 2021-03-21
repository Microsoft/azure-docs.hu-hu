---
title: Azure IoT Hub-támogatás virtuális hálózatokhoz
description: Virtuális hálózatok kapcsolódási mintájának használata IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jlian
ms.openlocfilehash: 1e28c7767868904fb20ae6d27c6aea9e7077eb62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98630293"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT Hub a magánhálózati és felügyelt identitású virtuális hálózatok támogatása

Alapértelmezés szerint a IoT Hub állomásneve egy nyilvános végpontra mutat, amely az interneten keresztül nyilvánosan irányítható IP-címmel rendelkezik. A különböző ügyfelek megoszthatják ezt a IoT Hub nyilvános végpontot, és IoT a nagy területű hálózatokon és a helyszíni hálózatokon található eszközöket.

![IoT Hub nyilvános végpont](./media/virtual-network-support/public-endpoint.png)

IoT Hub funkciók, például az [üzenetek útválasztása](./iot-hub-devguide-messages-d2c.md), a [fájlok feltöltése](./iot-hub-devguide-file-upload.md)és a [tömeges eszközök importálása/exportálása](./iot-hub-bulk-identity-mgmt.md) a nyilvános végponton keresztül a IoT hub kapcsolatát is igényli a felhasználó által birtokolt Azure-erőforráshoz. Ezek a csatlakozási útvonalak együttesen alkotják a kimenő forgalmat IoT Hubról az ügyfelek erőforrásaira.

Előfordulhat, hogy korlátozni szeretné az Azure-erőforrások (például IoT Hub) kapcsolatát egy Ön által birtokolt és üzemeltetett VNet keresztül. Ezek az okok a következők:

* Az IoT hub hálózati elkülönítésének bemutatása azáltal, hogy megakadályozza a nyilvános internetre való kapcsolódást.

* A helyszíni hálózati eszközök privát kapcsolódási élményének lehetővé tétele, amely biztosítja, hogy az adatok és a forgalom közvetlenül az Azure gerinc-hálózatra legyen továbbítva.

* A bizalmas helyszíni hálózatok kiszűrése elleni támadásának megakadályozása. 

* Az Azure-szintű csatlakozási mintákat a [privát végpontok](../private-link/private-endpoint-overview.md)használatával követte.

Ez a cikk azt ismerteti, hogyan valósíthatók meg ezek a célok az [Azure Private-hivatkozással](../private-link/private-link-overview.md) , amellyel a IoT hub és a megbízható Microsoft-szolgáltatások használata kivételt jelent a kimenő adatok IoT hub más Azure-erőforrásokhoz való csatlakoztatásához.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Csatlakozás IoT Hub az Azure Private link használatával

A privát végpontok egy olyan magánhálózati IP-cím, amely az Azure-erőforrások elérhetőségét biztosító, az ügyfél tulajdonában lévő VNet van lefoglalva. Az Azure privát kapcsolaton keresztül beállíthat egy privát végpontot az IoT hub számára, hogy lehetővé tegye a VNet belüli szolgáltatások IoT Hub elérését anélkül, hogy a IoT Hub nyilvános végpontjának kellene elküldeni a forgalmat. Hasonlóképpen a helyszíni eszközök is használhatják a [virtuális magánhálózati (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy a [ExpressRoute](https://azure.microsoft.com/services/expressroute/) -társítást, hogy hozzáférjenek a VNet és a IoT hub (a saját végpontján keresztül). Ennek eredményeképpen korlátozhatja vagy teljesen letilthatja az IoT hub nyilvános végpontokhoz való kapcsolódást [IoT hub IP-szűrő](./iot-hub-ip-filtering.md) vagy [a nyilvános hálózati hozzáférés váltógomb](iot-hub-public-network-access.md)használatával. Ez a megközelítés a saját hubhoz való kapcsolódást az eszközök magánhálózati végpontjának használatával tartja fenn. Ennek a beállításnak a fő témája a helyszíni hálózaton belüli eszközök. Ez a beállítás nem ajánlott a nagy kiterjedésű hálózatban üzembe helyezett eszközök esetében.

![Virtuális hálózati engress IoT Hub](./media/virtual-network-support/virtual-network-ingress.png)

A folytatás előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* [Létrehozott egy Azure-VNet](../virtual-network/quick-create-portal.md) egy olyan alhálózattal, amelyben a magánhálózati végpont létre lesz hozva.

* A helyszíni hálózatokban üzemelő eszközök esetében [virtuális magánhálózati (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privát társak beállítása az Azure-VNet.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Magánhálózati végpont beállítása IoT Hub bejövő forgalomhoz

A magánhálózati végpont a IoT Hub eszköz API-k (például az eszközről a felhőbe irányuló üzenetek) és a szolgáltatási API-k (például eszközök létrehozása és frissítése) esetében működik.

1. A Azure Portal területen válassza a **hálózatkezelés**, **privát végponti kapcsolatok** lehetőséget, majd kattintson a **+ privát végpontra**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Képernyőfelvétel: a IoT Hub privát végpontjának hozzáadása":::

1. Adja meg az előfizetést, az erőforráscsoportot, a nevet és a régiót, és hozza létre az új privát végpontot a alkalmazásban. Ideális esetben a magánhálózati végpontot ugyanabban a régióban kell létrehozni, ahol a hub található.

1. Kattintson a **Tovább gombra: erőforrás**, és adja meg az előfizetést a IoT hub erőforráshoz, és válassza a **"Microsoft. Devices/IotHubs"** lehetőséget az erőforrás típusaként, a IoT hub nevét **erőforrásként**, és **iotHub** .

1. Kattintson a **Tovább gombra: konfigurálás** lehetőségre, és adja meg a virtuális hálózatot és az alhálózatot a privát végpont létrehozásához a alkalmazásban. Ha szükséges, válassza az Azure Private DNS-zónával való integráció lehetőségét.

1. Kattintson a **Tovább gombra: címkék**, és opcionálisan adja meg az erőforráshoz tartozó címkéket.

1. A privát kapcsolati erőforrás létrehozásához kattintson a **felülvizsgálat + létrehozás** elemre.

### <a name="built-in-event-hub-compatible-endpoint"></a>Beépített Event hub-kompatibilis végpont 

A [beépített Event hub-kompatibilis végpont](iot-hub-devguide-messages-read-builtin.md) privát végponton keresztül is elérhető. Ha a magánhálózati hivatkozás konfigurálva van, a beépített végponthoz egy további privát végponti kapcsolatot kell látnia. Ez a `servicebus.windows.net` teljes tartománynévben található.

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="A kép két privát végpontot mutat IoT Hub privát kapcsolaton keresztül":::

IoT Hub [IP-szűrője](iot-hub-ip-filtering.md) opcionálisan képes a beépített végponthoz való nyilvános hozzáférés szabályozására. 

Ha teljes mértékben le szeretné tiltani az IoT hub nyilvános hálózati hozzáférését, [kapcsolja ki a nyilvános hálózati hozzáférést](iot-hub-public-network-access.md) , vagy használja az IP-szűrő funkciót az összes IP-cím letiltásához, és válassza a szabályok alkalmazása a beépített végpontra lehetőséget.

### <a name="pricing-for-private-link"></a>Privát hivatkozás díjszabása

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Kimenő kapcsolatok IoT Hubról más Azure-erőforrásokra

IoT Hub tud csatlakozni az Azure Blob Storage-hoz, az Event hub-hoz, a Service Bus erőforrásaihoz az [üzenetek útválasztásához](./iot-hub-devguide-messages-d2c.md), a [fájlfeltöltés](./iot-hub-devguide-file-upload.md)és a [tömeges eszközök importálásához és exportálásához](./iot-hub-bulk-identity-mgmt.md) az erőforrások nyilvános végpontján keresztül. Az erőforrás egy VNet való kötése alapértelmezés szerint blokkolja az erőforráshoz való kapcsolódást. Ennek eredményeképpen ez a konfiguráció megakadályozza, hogy a IoT Hub az adatok adatküldését az erőforrásokra. A probléma megoldásához engedélyezze a kapcsolatot a IoT Hub-erőforrásról a Storage-fiókjába, az Event hub-ba vagy a Service Bus-erőforrásokhoz a **megbízható Microsoft szolgáltatás** használatával.

### <a name="turn-on-managed-identity-for-iot-hub"></a>Felügyelt identitás bekapcsolása a IoT Hubhoz

Ahhoz, hogy más szolgáltatások megbízható Microsoft-szolgáltatásként megtalálják az IoT hubot, rendelkeznie kell egy rendszerhez rendelt felügyelt identitással.

1. A IoT Hub-portálon navigáljon az **identitáshoz**

1. Az **állapot** területen válassza **a be** lehetőséget, majd kattintson a **Mentés** gombra.

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="A IoT Hub felügyelt identitásának bekapcsolását bemutató képernyőkép":::

Az Azure CLI használata a felügyelt identitás bekapcsolásához:

```azurecli-interactive
az iot hub update --name <iot-hub-resource-name> --set identity.type="SystemAssigned"
```

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Felügyelt identitás kiosztása a IoT Hub a létrehozáskor az ARM-sablon használatával

Ha a felügyelt identitást erőforrás-kiépítési időszakban szeretné hozzárendelni az IoT hubhoz, használja az alábbi ARM-sablont. Ez az ARM-sablon két szükséges erőforrással rendelkezik, és mindkettőt telepíteni kell, mielőtt más erőforrásokat hozna létre, például: `Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups` . 

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

Az erőforráshoz tartozó értékek helyettesítése után `name` `location` `SKU.name` `SKU.tier` Az Azure CLI használatával telepítheti az erőforrást egy meglévő erőforráscsoporthoz a következő használatával:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Az erőforrás létrehozása után lekérheti a hubhoz rendelt felügyelt szolgáltatás identitását az Azure CLI használatával:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>A felügyelt identitás díjszabása

A megbízható Microsoft első féltől származó szolgáltatások kivételi funkciója díjmentes. A kiépített Storage-fiókok, az Event hubok vagy a Service Bus-erőforrások díjai külön vonatkoznak.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Kimenő kapcsolatok a Storage-fiókok végpontjai számára az útválasztáshoz

A IoT Hub az üzeneteket az ügyfél által birtokolt Storage-fiókba irányíthatja. Ha engedélyezni szeretné, hogy az útválasztási funkció hozzáférjen egy Storage-fiókhoz, amíg a tűzfal korlátozásai teljesülnek, a IoT Hub [felügyelt identitással](#turn-on-managed-identity-for-iot-hub)kell rendelkeznie. A felügyelt identitás kiosztása után kövesse az alábbi lépéseket, hogy az Azure RBAC engedélyt adjon a hub erőforrás-identitásához a Storage-fiók eléréséhez.

1. A Azure Portal nyissa meg a Storage-fiók **hozzáférés-vezérlés (iam)** lapját, és kattintson a **Hozzáadás** lehetőségre a **szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza a **Storage blob-adatközreműködői** ([*nem* közreműködő vagy a Storage-fiók közreműködői](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) lehetőséget **szerepkörként**, **Azure ad-felhasználóként, csoportként vagy egyszerű szolgáltatásnévként** **, és válassza ki a IoT hub** erőforrás nevét a legördülő listából. Kattintson a **Mentés** gombra.

3. Navigáljon a Storage-fiók **tűzfalak és virtuális hálózatok** lapjára, és engedélyezze a **hozzáférés engedélyezése a kiválasztott hálózatokból** lehetőséget. A **kivételek** listájában jelölje be a **megbízható Microsoft-szolgáltatások elérésének engedélyezése a Storage-fiók** számára jelölőnégyzetet. Kattintson a **Mentés** gombra.

4. A IoT Hub erőforrás lapján navigáljon az üzenet- **Útválasztás** lapra.

5. Navigáljon az **Egyéni végpontok** szakaszhoz, és kattintson a **Hozzáadás** gombra. Válassza a **tároló** lehetőséget a végpont típusaként.

6. A megjelenített oldalon adja meg a végpont nevét, válassza ki a blob Storage-ban használni kívánt tárolót, adja meg a kódolást és a fájlnév formátumát. Válassza az **identitás-alapú** **hitelesítési típust** a tárolási végpontnak. Kattintson a **Létrehozás** gombra.

Most, hogy az egyéni tárolási végpont a hub rendszerhez rendelt identitásának használatára van beállítva, és a tűzfal korlátozásai ellenére jogosult a tárolási erőforrás elérésére. Ezt a végpontot mostantól útválasztási szabály beállításához is használhatja.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Kimenő kapcsolat az Event hub-végpontokkal az útválasztáshoz

A IoT Hub konfigurálható úgy, hogy üzeneteket továbbítson az ügyfél által birtokolt Event hub-névtérnek. Ha engedélyezni szeretné, hogy az útválasztási funkció hozzáférjen egy Event hubok-erőforráshoz, amíg a tűzfal korlátozásai teljesülnek, a IoT Hub felügyelt identitással kell rendelkeznie. Miután létrehozta a felügyelt identitást, kövesse az alábbi lépéseket, hogy az Azure RBAC engedélyt adjon a hub erőforrás-identitásának az esemény-hubok eléréséhez.

1. A Azure Portal navigáljon az Event hub- **hozzáférés-vezérlés (iam)** lapra, és kattintson a **Hozzáadás** lehetőségre a **szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza ki a **szerepkör**, az **Azure ad-felhasználó, a csoport vagy az egyszerű szolgáltatásnév** **Event Hubs adatfeladó** lehetőséget **, és válassza** ki a IoT hub erőforrás nevét a legördülő listából. Kattintson a **Mentés** gombra.

3. Navigáljon a **tűzfalak és a virtuális hálózatok** lapra az Event hubokban, és engedélyezze a **hozzáférést a kiválasztott hálózatokból** lehetőséggel. A **kivételek** listájában jelölje be a **megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése az Event hubok** számára jelölőnégyzetet. Kattintson a **Mentés** gombra.

4. A IoT Hub erőforrás lapján navigáljon az üzenet- **Útválasztás** lapra.

5. Navigáljon az **Egyéni végpontok** szakaszhoz, és kattintson a **Hozzáadás** gombra. Válassza az **Event hubok** lehetőséget a végpont típusaként.

6. A megjelenített oldalon adja meg a végpont nevét, válassza ki az Event hub-névteret és-példányt. Válassza a **hitelesítési típusként** az **identitás-alapú** lehetőséget, majd kattintson a **Létrehozás** gombra.

Most az egyéni Event hub-végpont úgy van beállítva, hogy a hub rendszerhez rendelt identitását használja, és a tűzfal korlátozásai ellenére jogosult az Event hub-erőforrás elérésére. Ezt a végpontot mostantól útválasztási szabály beállításához is használhatja.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Kimenő kapcsolatok a Service Bus-végpontokhoz útválasztáshoz

A IoT Hub konfigurálható úgy, hogy üzeneteket továbbítson az ügyfél által birtokolt Service Bus-névtérhez. Ha engedélyezni szeretné, hogy az útválasztási funkció hozzáférjen egy Service Bus-erőforráshoz, amíg a tűzfal korlátozásai teljesülnek, a IoT Hub felügyelt identitással kell rendelkeznie. A felügyelt identitás kiosztása után kövesse az alábbi lépéseket, hogy az Azure RBAC engedélyt nyújtson a hub erőforrás-identitásához a Service Bus eléréséhez.

1. A Azure Portal nyissa meg a Service Bus hozzáférés- **vezérlés (iam)** lapját, és kattintson a **Hozzáadás** lehetőségre a **szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza ki a **Service Bus-Adatfeladót** **szerepkörként**, **Azure ad-felhasználóként, csoportként vagy egyszerű szolgáltatásnévként** **, és válassza** ki a IoT hub erőforrás nevét a legördülő listából. Kattintson a **Mentés** gombra.

3. Navigáljon a Service Bus **tűzfalak és virtuális hálózatok** lapjára, és engedélyezze a **hozzáférés engedélyezése a kiválasztott hálózatokból** lehetőséget. A **kivételek** listájában jelölje be a **megbízható Microsoft-szolgáltatások elérésének engedélyezése a Service Bus** számára jelölőnégyzetet. Kattintson a **Mentés** gombra.

4. A IoT Hub erőforrás lapján navigáljon az üzenet- **Útválasztás** lapra.

5. Navigáljon az **Egyéni végpontok** szakaszhoz, és kattintson a **Hozzáadás** gombra. A végpont típusaként válassza a **Service Bus-várólista** vagy a **Service Bus témakört** (ha alkalmazható).

6. A megjelenített oldalon adja meg a végpont nevét, válassza ki a Service Bus-névteret és-várólistát vagy-témakört (a megfelelő módon). Válassza a **hitelesítési típusként** az **identitás-alapú** lehetőséget, majd kattintson a **Létrehozás** gombra.

Most az egyéni Service Bus-végpont úgy van beállítva, hogy a hub rendszerhez rendelt identitását használja, és a tűzfal korlátozásai ellenére jogosult a Service Bus-erőforrás elérésére. Ezt a végpontot mostantól útválasztási szabály beállításához is használhatja.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>A fájlok feltöltésekor használt Storage-fiókokhoz való kimenő kapcsolatok

IoT Hub fájlfeltöltés funkciója lehetővé teszi, hogy az eszközök fájlokat töltsenek fel egy ügyfél tulajdonú Storage-fiókjába. Ha engedélyezni szeretné a fájl feltöltését, mindkét eszköznek és a IoT Hubnak kapcsolódnia kell a Storage-fiókhoz. Ha a Storage-fiókban tűzfal-korlátozások vannak érvényben, az eszköznek a támogatott Storage-fiók mechanizmusát (beleértve a [magánhálózati végpontokat](../private-link/tutorial-private-endpoint-storage-portal.md), a [szolgáltatási végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md)vagy a [közvetlen tűzfal-konfigurációt](../storage/common/storage-network-security.md)) kell használnia a kapcsolat eléréséhez. Hasonlóképpen, ha a Storage-fiókban tűzfal-korlátozások vannak érvényben, IoT Hub konfigurálnia kell a tárolási erőforrás elérését a megbízható Microsoft Services-kivétel használatával. Erre a célra a IoT Hub felügyelt identitással kell rendelkeznie. A felügyelt identitás kiosztása után kövesse az alábbi lépéseket, hogy az Azure RBAC engedélyt adjon a hub erőforrás-identitásához a Storage-fiók eléréséhez.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. A Azure Portal nyissa meg a Storage-fiók **hozzáférés-vezérlés (iam)** lapját, és kattintson a **Hozzáadás** lehetőségre a **szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza a **Storage blob-adatközreműködői** ([*nem* közreműködő vagy a Storage-fiók közreműködői](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) lehetőséget **szerepkörként**, **Azure ad-felhasználóként, csoportként vagy egyszerű szolgáltatásnévként** **, és válassza ki a IoT hub** erőforrás nevét a legördülő listából. Kattintson a **Mentés** gombra.

3. Navigáljon a Storage-fiók **tűzfalak és virtuális hálózatok** lapjára, és engedélyezze a **hozzáférés engedélyezése a kiválasztott hálózatokból** lehetőséget. A **kivételek** listájában jelölje be a **megbízható Microsoft-szolgáltatások elérésének engedélyezése a Storage-fiók** számára jelölőnégyzetet. Kattintson a **Mentés** gombra.

4. A IoT Hub erőforrás lapján navigáljon a **fájlfeltöltés** lapra.

5. A megjelenő oldalon válassza ki a blob Storage-ban használni kívánt tárolót, konfigurálja a **fájl értesítési beállításait**, az **sas TTL**, az **alapértelmezett TTL** és a **maximális kézbesítések** értéket a kívánt módon. Válassza az **identitás-alapú** **hitelesítési típust** a tárolási végpontnak. Kattintson a **Létrehozás** gombra. Ha ebben a lépésben hibaüzenetet kap, átmenetileg állítsa be a Storage-fiókot, hogy engedélyezze a hozzáférést az **összes hálózatról**, majd próbálkozzon újra. A Storage-fiókban beállíthatja a tűzfalat, miután befejeződött a fájlfeltöltés konfigurálása.

A file upload tárolási végpontja most úgy van beállítva, hogy a hub rendszerhez rendelt identitását használja, és a tűzfal korlátozásai ellenére jogosult a tárolási erőforrás elérésére.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Kilépési kapcsolat a Storage-fiókokkal a tömeges eszközök importálásához/exportálásához

A IoT Hub támogatja az eszközök tömeges [importálására/exportálására](./iot-hub-bulk-identity-mgmt.md) vonatkozó adatokat az ügyfél által megadott Storage-blobba/-ból. Ha engedélyezni szeretné a tömeges importálási/exportálási funkciót, mindkét eszköznek és a IoT Hubnak kapcsolódnia kell a Storage-fiókhoz.

Ehhez a funkcióhoz IoT Hub kapcsolat szükséges a Storage-fiókhoz. Ha tűzfal-korlátozásokkal szeretné elérni a Service Bus-erőforrást, a IoT Hub felügyelt identitással kell rendelkeznie. A felügyelt identitás kiosztása után kövesse az alábbi lépéseket, hogy az Azure RBAC engedélyt nyújtson a hub erőforrás-identitásához a Service Bus eléréséhez.

1. A Azure Portal nyissa meg a Storage-fiók **hozzáférés-vezérlés (iam)** lapját, és kattintson a **Hozzáadás** lehetőségre a **szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza a **Storage blob-adatközreműködői** ([*nem* közreműködő vagy a Storage-fiók közreműködői](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) lehetőséget **szerepkörként**, **Azure ad-felhasználóként, csoportként vagy egyszerű szolgáltatásnévként** **, és válassza ki a IoT hub** erőforrás nevét a legördülő listából. Kattintson a **Mentés** gombra.

3. Navigáljon a Storage-fiók **tűzfalak és virtuális hálózatok** lapjára, és engedélyezze a **hozzáférés engedélyezése a kiválasztott hálózatokból** lehetőséget. A **kivételek** listájában jelölje be a **megbízható Microsoft-szolgáltatások elérésének engedélyezése a Storage-fiók** számára jelölőnégyzetet. Kattintson a **Mentés** gombra.

Most már használhatja az Azure IoT REST API-kat [importálási exportálási feladatok létrehozásához](/rest/api/iothub/service/jobs/getimportexportjobs) a tömeges importálási/exportálási funkciók használatával kapcsolatban. Meg kell adnia a `storageAuthenticationType="identityBased"` kérés törzsét, és használnia kell a `inputBlobContainerUri="https://..."` és `outputBlobContainerUri="https://..."` a, valamint a Storage-fiók bemeneti és kimeneti URL-címét.

Az Azure IoT Hub SDK-k a szolgáltatás-ügyfél beállításjegyzék-kezelőjében is támogatják ezt a funkciót. A következő kódrészletből megtudhatja, hogyan kezdeményezzen importálási feladatot vagy exportálási feladatot a C# SDK használatával.

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

Ha az Azure IoT SDK-k ezen verzióját szeretné használni a C#, a Java és a Node.js virtuális hálózati támogatásával:

1. Hozzon létre egy nevű környezeti változót `EnableStorageIdentity` , és állítsa be a értékét a következőre: `1` .

2. Az SDK letöltése: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [Node.js](https://aka.ms/vnetnodesdk)
 
A Python esetében töltse le a korlátozott verziót a GitHubról.

1. Navigáljon a [GitHub kiadási oldalára](https://aka.ms/vnetpythonsdk).

2. Töltse le a következő fájlt, amelyet a kiadási oldal alján talál az **eszközök** nevű fejléc alatt.
    > *azure_iot_hub-2.2.0_limited-py2. py3-none-any. WHL*

3. Nyisson meg egy terminált, és navigáljon a letöltött fájllal rendelkező mappához.

4. A következő parancs futtatásával telepítse a Python Service SDK-t a virtuális hálózatok támogatásával:
    > pip install./azure_iot_hub-2.2.0_limited-py2. py3-none-any. WHL


## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozásokra kattintva további információt találhat IoT Hub szolgáltatásairól:

* [Üzenetek útválasztása](./iot-hub-devguide-messages-d2c.md)
* [Fájlfeltöltés](./iot-hub-devguide-file-upload.md)
* [Tömeges eszköz importálása/exportálása](./iot-hub-bulk-identity-mgmt.md)
