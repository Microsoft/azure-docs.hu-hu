---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 472c1770e2793d8da4e8fc76fafbf3b9073b746d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763426"
---
## <a name="deploy-and-configure-azure-media-services"></a>Azure Media Services üzembe helyezése és konfigurálása

A megoldás egy Azure Media Services fiókot használ az IoT Edge Gateway-eszköz által készített objektum-észlelési videoklipek tárolásához.

A Media Services fiók létrehozásakor:

- Meg kell adnia egy fióknevet, egy Azure-előfizetést, egy helyet, egy erőforráscsoportot és egy Storage-fiókot. Hozzon létre egy új Storage-fiókot az alapértelmezett beállításokkal a Media Services fiók létrehozásakor.

- Válassza ki az **USA keleti** régióját a helyhez.

- Hozzon létre egy *LVA-RG* nevű új ERŐFORRÁSCSOPORTOT az **USA keleti** régiójában a Media Services és a Storage-fiókokhoz. Az oktatóanyagok befejezése után egyszerűen eltávolíthatja az összes erőforrást a *LVA-RG* erőforráscsoport törlésével.

Hozza létre a [Media Services fiókot a Azure Portalban](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Ezek az oktatóanyagok az **USA keleti** régióját használják a példákban. Ha szeretné, a legközelebbi régiót használhatja.

Jegyezze fel **Media Services** fiókjának nevét a *scratchpad.txt* fájlban.

Ha a telepítés befejeződött, nyisson meg egy Cloud Shell, és futtassa a következő parancsot a Media Service-fiók **erőforrás-azonosítójának** lekéréséhez:

```azurecli
az resource list --resource-group lva-rg --resource-type microsoft.media/mediaservices --output table --query "[].{ResourceID:id}"
```

:::image type="content" source="media/iot-central-video-analytics-part2/get-resource-id.png" alt-text="A Cloud Shell használata az erőforrás-azonosító lekéréséhez":::

Jegyezze fel az erőforrás- **azonosítót** a *scratchpad.txt* fájlban, ezt az értéket később a IoT Edge modul konfigurálásakor kell használni.

Ezután konfiguráljon egy Azure Active Directory egyszerű szolgáltatásnevet a Media Services erőforráshoz. Válassza az **API-hozzáférés** , majd az **egyszerű szolgáltatás hitelesítés** lehetőséget. Hozzon létre egy új Azure Active Directory alkalmazást a Media Services-erőforrás nevével megegyező névvel, és hozzon létre egy titkos kulcsot a Leírás *IoT Edge a hozzáférés*.

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Azure A D alkalmazás konfigurálása Azure Media Serviceshoz":::

A titkos kód létrehozásakor görgessen le a **hitelesítő adatok másolása az egyszerű szolgáltatásnév alkalmazásához** szakaszhoz. Ezután válassza a **JSON** elemet. A hitelesítő adatokat innen másolhatja egy menetben. Jegyezze fel ezt az információt a *scratchpad.txt* fájlban, amelyet később a IoT Edge eszköz konfigurálásakor fog használni.

> [!WARNING]
> Ez az egyetlen lehetőség a titok megtekintésére és mentésére. Ha elveszíti, egy újabb titkot kell kiállítania.

## <a name="create-the-azure-iot-central-application"></a>Az Azure IoT Central-alkalmazás létrehozása

Ebben a szakaszban új Azure IoT Central alkalmazást hoz létre egy sablonból. Ezt az alkalmazást az oktatóanyag-sorozaton belül egy teljes megoldás létrehozásához használhatja.

Új Azure IoT Central-alkalmazás létrehozása:

1. Navigáljon az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyére.

1. Jelentkezzen be az Azure-előfizetés eléréséhez használt hitelesítő adatokkal.

1. Új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **új alkalmazás** lehetőséget a **Build** lapon.

1. Válassza a **kereskedelmi** lehetőséget. A kiskereskedelmi oldalon számos kereskedelmi alkalmazás-sablon látható.

Új video Analytics-alkalmazás létrehozása:

1. Válassza ki a **videó-elemzés-objektum és a mozgásérzékelő** alkalmazás sablonját. Ez a sablon tartalmazza az oktatóanyagban használt eszközökhöz tartozó eszközök sablonjait. A sablon olyan minta-irányítópultokat tartalmaz, amelyeket az operátorok olyan feladatok végrehajtására használhatnak, mint például a kamerák figyelése és kezelése.

1. Igény szerint válasszon egy felhasználóbarát **nevet**. Ez az alkalmazás a Northwind Traders nevű kitalált kereskedelmi áruházon alapul. Az oktatóanyag a *Northwind Traders video Analytics* **alkalmazás nevét** használja.

    > [!NOTE]
    > Ha felhasználóbarát **alkalmazást** használ, továbbra is egyedi értéket kell használnia az alkalmazás **URL-címéhez**.

1. Ha rendelkezik Azure-előfizetéssel, válassza ki a **címtárat**, az **Azure-előfizetést** és a **Egyesült Államok** **helyét**. Ha nem rendelkezik előfizetéssel, engedélyezheti a **7 napos ingyenes próbaidőszakot** , és elvégezheti a szükséges kapcsolattartási adatokat. Ez az oktatóanyag három eszközt használ – két kamerát és egy IoT Edge eszközt – ha nem használja az ingyenes próbaverziót, a használatért kell fizetnie.

    További információ a címtárakról, előfizetésekről és helyszínekről: [alkalmazás létrehozása](../articles/iot-central/core/quick-deploy-iot-central.md)– rövid útmutató.

1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Azure IoT Central Alkalmazás létrehozása oldal":::

### <a name="retrieve-the-configuration-data"></a>Konfigurációs adatlekérdezés

Az oktatóanyag későbbi részében a IoT Edge-átjáró konfigurálásakor szükség van néhány konfigurációs adatokra a IoT Central alkalmazásból. A IoT Edge eszköznek szüksége van ezekre az információkra a regisztrálásához és az alkalmazáshoz való kapcsolódáshoz.

Az **Adminisztráció** szakaszban válassza ki az **alkalmazást** , és jegyezze fel az **alkalmazás URL-címét** és az **alkalmazás azonosítóját** a *scratchpad.txt* fájlban:

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="Képernyőfelvétel: az U R L-t és az alkalmazás I D-t tartalmazó video Analytics-oldal adminisztrációs paneljét jeleníti meg.":::

Válassza ki az **API-jogkivonatokat** , és állítson elő egy **LVAEdgeToken** nevű új jogkivonatot az **operátori** szerepkörhöz:

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="Jogkivonat előállítása":::

Jegyezze fel a tokent a *scratchpad.txt* fájlban később. A párbeszédpanel bezárása után a tokent nem lehet újból megtekinteni.

Az **Adminisztráció** szakaszban válassza az **eszköz csatlakoztatása** lehetőséget, majd válassza az **sas-IoT-Devices** elemet.

Jegyezze fel az eszközök **elsődleges kulcsát** a *scratchpad.txt* fájlban. Ezt az *elsődleges csoport megosztott hozzáférési aláírási tokenjét* később, az IoT Edge eszköz konfigurálásakor kell használni.
