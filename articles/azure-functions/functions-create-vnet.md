---
title: Azure Functions integrálása virtuális hálózattal a privát végpontok használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztatható egy függvény egy Azure-beli virtuális hálózathoz, és hogyan zárható le a saját végpontok használatával.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: e8ca853908b366b99e150f04ced404f42acc7d21
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027414"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>Oktatóanyag: Azure Functions integrálása Azure-beli virtuális hálózattal privát végpontok használatával

Ebből az oktatóanyagból megtudhatja, hogyan használhatók a Azure Functions egy Azure-beli virtuális hálózatban lévő erőforrásokhoz privát végpontok használatával való kapcsolódáshoz. Egy függvényt a virtuális hálózat mögött zárolt Storage-fiók használatával hozhat létre. A virtuális hálózat egy Service Bus-várólista triggert használ.

Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Hozzon létre egy Function alkalmazást a prémium csomagban.
> * Azure-erőforrások, például a Service Bus, a Storage-fiók és a virtuális hálózat létrehozása.
> * Zárolja a Storage-fiókot egy privát végpont mögött.
> * A Service Bus zárolása privát végpont mögött.
> * Telepítsen egy, a Service Bus és a HTTP-eseményindítókat egyaránt használó Function alkalmazást.
> * Zárolja a Function alkalmazást egy privát végpont mögött.
> * Ellenőrizze, hogy a Function alkalmazás biztonságos-e a virtuális hálózaton belül.
> * Az erőforrások eltávolítása.

## <a name="create-a-function-app-in-a-premium-plan"></a>Function-alkalmazás létrehozása prémium csomaggal

Létrehoz egy .NET Function-alkalmazást a prémium csomagban, mert ez az oktatóanyag C# nyelvet használ. A Windows más nyelveket is támogat. A Prémium csomag kiszolgáló nélküli skálázást biztosít a virtuális hálózatok integrálásának támogatása mellett.

1. A Azure Portal menüben vagy a **kezdőlapon** válassza az **erőforrás létrehozása** lehetőséget.

1. Az **új** lapon válassza a **számítási**  >  **függvényalkalmazás** elemet.

1. Az **alapvető** beállítások lapon a következő táblázat segítségével konfigurálja a Function app beállításait.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az új Function alkalmazást létrehozták. |
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létre fogja hozni a Function alkalmazást. |
    | **Függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Az érvényes karakterek az `a-z` (kis- és nagybetűk megkülönböztetése nélkül) `0-9`és az `-`.  |
    |**Közzététel**| Kód | Megadhatja a Kódszerkesztő vagy a Docker-tároló közzétételét. |
    | **Futtatókörnyezet verme** | .NET | Ez az oktatóanyag a .NET-et használja. |
    |**Régió**| Előnyben részesített régió | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) vagy más, a funkciókhoz hozzáférő szolgáltatások közelében. |

1. Válassza a **Tovább: üzemeltetés** lehetőséget. Az **üzemeltetés** lapon adja meg a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Storage-fiók](../storage/common/storage-account-create.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A Storage-fiókok nevének 3 – 24 karakter hosszúnak kell lennie. Csak számokat és kisbetűket tartalmazhatnak. Meglévő fiókot is használhat, amelynek meg kell felelnie a [Storage-fiókra vonatkozó követelményeknek](./storage-considerations.md#storage-account-requirements). |
    |**Operációs rendszer**| Windows | Ez az oktatóanyag a Windowst használja. |
    | **[Felkészülés](./functions-scale.md)** | Prémium | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Alapértelmezés szerint a **prémium** lehetőség kiválasztásakor létrejön egy új App Service-csomag. Az alapértelmezett **SKU és size** érték a **EP1**, ahol az *EP* a _rugalmas prémium szintű támogatást_ nyújtja. További információkért lásd a [prémium SKU](./functions-premium-plan.md#available-instance-skus)-ket tartalmazó listát.<br/><br/>Ha a JavaScript-függvényeket prémium csomagon futtatja, válasszon olyan példányt, amely kevesebb vCPU rendelkezik. További információ: az [egymagos prémium csomagok kiválasztása](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Válassza a **Tovább: figyelés** lehetőséget. A **figyelés** lapon adja meg a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Alapértelmezett | Hozzon létre egy Application Insights erőforrást ugyanahhoz az alkalmazáshoz a legközelebbi támogatott régióban. Bontsa ki ezt a beállítást, ha módosítania kell az **új erőforrás nevét** , vagy az adatait egy másik **helyen** kell tárolnia az [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/). |

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget az alkalmazás-konfiguráció kiválasztásának áttekintéséhez.

1. A **felülvizsgálat + létrehozás** oldalon tekintse át a beállításokat. Ezután válassza a **Létrehozás** lehetőséget a Function alkalmazás kiépítéséhez és üzembe helyezéséhez.

1. A portál jobb felső sarkában válassza az **értesítések** ikont, és figyelje meg az **üzembe helyezés sikeres** üzenetét.

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a rögzítés az irányítópulton** lehetőséget is. A rögzítéssel egyszerűbbé válik a Function app-erőforráshoz való visszatérés az irányítópultról.

Gratulálunk! Sikeresen létrehozta a Premium Function alkalmazást.

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

Ezután hozzon létre egy Storage-fiókot, egy Service Bus-t és egy virtuális hálózatot. 
### <a name="create-a-storage-account"></a>Tárfiók létrehozása

A virtuális hálózatoknak olyan Storage-fiókra van szükségük, amely elkülönül a Function alkalmazással létrehozott fióktól.

1. A Azure Portal menüben vagy a **kezdőlapon** válassza az **erőforrás létrehozása** lehetőséget.

1. Az **új** lapon keresse meg a *Storage-fiók* kifejezést. Ezután kattintson a **Létrehozás** elemre.

1. Az **alapok** lapon a következő táblázat segítségével konfigurálja a Storage-fiók beállításait. Minden más beállítás használhatja az alapértelmezett értékeket.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | A Function alkalmazással létrehozott erőforráscsoport. |
    | **Név** | mysecurestorage| Annak a Storage-fióknak a neve, amelyre a magánhálózati végpont vonatkozik. |
    | **[Régió](https://azure.microsoft.com/regions/)** | myFunctionRegion | Az a régió, amelyben létrehozta a Function alkalmazást. |

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejeződése után válassza a **Létrehozás** lehetőséget.

### <a name="create-a-service-bus"></a>Service Bus létrehozása

1. A Azure Portal menüben vagy a **kezdőlapon** válassza az **erőforrás létrehozása** lehetőséget.

1. Az **új** lapon keressen a *Service Bus* kifejezésre. Ezután kattintson a **Létrehozás** elemre.

1. Az **alapok** lapon a következő táblázat segítségével konfigurálja a Service Bus beállításait. Minden más beállítás használhatja az alapértelmezett értékeket.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. |
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | A Function alkalmazással létrehozott erőforráscsoport. |
    | **Név** | myServiceBus| Annak a szolgáltatás-busznak a neve, amelyre a magánhálózati végpont vonatkozik. |
    | **[Régió](https://azure.microsoft.com/regions/)** | myFunctionRegion | Az a régió, amelyben létrehozta a Function alkalmazást. |
    | **Tarifacsomag** | Prémium | Válassza ezt a szintet, ha Azure Service Buskal rendelkező privát végpontokat szeretne használni. |

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejeződése után válassza a **Létrehozás** lehetőséget.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Az oktatóanyagban szereplő Azure-erőforrások vagy egy virtuális hálózaton belül vannak integrálva. A magánhálózati végpontokat a virtuális hálózaton belüli hálózati forgalom tárolására fogja használni.

Az oktatóanyag két alhálózatot hoz létre:
- **alapértelmezett**: alhálózat magánhálózati végpontokhoz. A magánhálózati IP-címek ezen az alhálózaton vannak megadva.
- **functions**: alhálózat Azure functions virtuális hálózati integrációhoz. Ez az alhálózat delegálva van a Function alkalmazásnak.

Hozza létre azt a virtuális hálózatot, amelyhez a Function app integrálva van:

1. A Azure Portal menüben vagy a **kezdőlapon** válassza az **erőforrás létrehozása** lehetőséget.

1. Az **új** lapon keressen rá a *virtuális hálózat* kifejezésre. Ezután kattintson a **Létrehozás** elemre.

1. Az **alapok** lapon a következő táblázat segítségével konfigurálja a virtuális hálózati beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | A Function alkalmazással létrehozott erőforráscsoport. |
    | **Név** | myVirtualNet| Annak a virtuális hálózatnak a neve, amelyhez a Function alkalmazás csatlakozni fog. |
    | **[Régió](https://azure.microsoft.com/regions/)** | myFunctionRegion | Az a régió, amelyben létrehozta a Function alkalmazást. |

1. Az **IP-címek** lapon válassza az **alhálózat hozzáadása** elemet. Az alhálózati beállítások konfigurálásához használja a következő táblázatot.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Képernyőkép a virtuális hálózat létrehozása nézetről.":::

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Alhálózat neve** | funkciók | Annak az alhálózatnak a neve, amelyhez a Function alkalmazás csatlakozni fog. | 
    | **Alhálózati címtartomány** | 10.0.1.0/24 | Az alhálózati címtartomány. Figyelje meg, hogy az előző képen az IPv4-címtartomány 10.0.0.0/16. Ha az érték 10.1.0.0/16, a javasolt alhálózati címtartomány a 10.1.1.0/24 lenne. |

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejeződése után válassza a **Létrehozás** lehetőséget.

## <a name="lock-down-your-storage-account"></a>A Storage-fiók zárolása

Az Azure Private-végpontok egy magánhálózati IP-cím használatával kapcsolódnak bizonyos Azure-erőforrásokhoz. Ez a kapcsolat biztosítja, hogy a hálózati forgalom a kiválasztott virtuális hálózaton belül maradjon, és a hozzáférés csak adott erőforrásokhoz érhető el. 

Hozza létre a Azure Files Storage és az Azure Blob Storage magánhálózati végpontját a Storage-fiók használatával:

1. Az új Storage-fiókban a bal oldali menüben válassza a **hálózatkezelés** lehetőséget.

1. A **privát végpont kapcsolatai** lapon válassza a **privát végpont** elemet.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Képernyőkép arról, hogyan hozhatók létre magánhálózati végpontok a Storage-fiókhoz.":::

1. Az **alapvető** beállítások lapon használja az alábbi táblázatban látható titkos végpontok beállításait.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Válassza ki a Function alkalmazással létrehozott erőforráscsoportot. | |
    | **Név** | fájl – végpont | A Storage-fiókból származó fájlokhoz tartozó magánhálózati végpont neve. |
    | **[Régió](https://azure.microsoft.com/regions/)** | myFunctionRegion | Válassza ki azt a régiót, ahol létrehozta a Storage-fiókot. |

1. Az **erőforrás** lapon használja az alábbi táblázatban látható titkos végpontok beállításait.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **Erőforrás típusa**  | Microsoft. Storage/storageAccounts | A Storage-fiókok erőforrás-típusa. |
    | **Erőforrás** | mysecurestorage | A létrehozott Storage-fiók. |
    | **Célzott alerőforrás** | file | Az a magánhálózati végpont, amelyet a rendszer a Storage-fiók fájljaihoz fog használni. |

1. A **konfiguráció** lapon az **alhálózat** beállításnál válassza az **alapértelmezett** lehetőséget.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejeződése után válassza a **Létrehozás** lehetőséget. A virtuális hálózat erőforrásai mostantól kommunikálhatnak a tárolási fájlokkal.

1. Hozzon létre egy másik privát végpontot a Blobok számára. Az **erőforrások** lapon használja az alábbi táblázatban látható beállításokat. Az összes többi beállításnál használja ugyanazt az értéket, amelyet a privát végpont fájlokhoz való létrehozásához használt.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **Erőforrás típusa**  | Microsoft. Storage/storageAccounts | A Storage-fiókok erőforrás-típusa. |
    | **Erőforrás** | mysecurestorage | A létrehozott Storage-fiók. |
    | **Célzott alerőforrás** | blob | Az a magánhálózati végpont, amelyet a rendszer a Storage-fiókban lévő blobokhoz fog használni. |

## <a name="lock-down-your-service-bus"></a>A Service Bus zárolása

Hozza létre a privát végpontot a Service Bus zárolásához:

1. Az új Service Bus-ben a bal oldali menüben válassza a **hálózatkezelés** lehetőséget.

1. A **privát végpont kapcsolatai** lapon válassza a **privát végpont** elemet.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Képernyőkép arról, hogyan léphet be a Service Bus magánhálózati végpontokra.":::

1. Az **alapvető** beállítások lapon használja az alábbi táblázatban látható titkos végpontok beállításait.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | A Function alkalmazással létrehozott erőforráscsoport. |
    | **Név** | SB – végpont | A Storage-fiókból származó fájlokhoz tartozó magánhálózati végpont neve. |
    | **[Régió](https://azure.microsoft.com/regions/)** | myFunctionRegion | Az a régió, ahol létrehozta a Storage-fiókot. |

1. Az **erőforrás** lapon használja az alábbi táblázatban látható titkos végpontok beállításait.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **Erőforrás típusa**  | Microsoft. ServiceBus/névterek | A Service Bus erőforrás-típusa. |
    | **Erőforrás** | myServiceBus | Az oktatóanyagban korábban létrehozott Service Bus. |
    | **Cél alerőforrás** | névtér | A magánhálózati végpont, amelyet a rendszer a Service Bus névteréhez fog használni. |

1. A **konfiguráció** lapon az **alhálózat** beállításnál válassza az **alapértelmezett** lehetőséget.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejeződése után válassza a **Létrehozás** lehetőséget. 

A virtuális hálózatban található erőforrások mostantól kommunikálhatnak a Service Bus szolgáltatással.

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

1. A létrehozott Storage-fiókban a bal oldali menüben válassza a **fájlmegosztás** lehetőséget.

1. Válassza a **+ fájlmegosztás** lehetőséget. Ebben az oktatóanyagban nevezze el a fájlmegosztás *fájljait*.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Képernyőkép a fájlmegosztás létrehozásáról a Storage-fiókban.":::

1. Válassza a **Létrehozás** lehetőséget.

## <a name="get-the-storage-account-connection-string"></a>A Storage-fiókhoz tartozó kapcsolatok karakterláncának beolvasása

1. A létrehozott Storage-fiókban a bal oldali menüben válassza a **hozzáférési kulcsok** elemet.

1. Válassza a **kulcsok megjelenítése** lehetőséget. Másolja ki és mentse a **key1** tartozó kapcsolatok karakterláncát. Az Alkalmazásbeállítások konfigurálásakor szüksége lesz erre a kapcsolódási karakterláncra.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Képernyőfelvétel a Storage-fiók kapcsolódási karakterláncának beszerzéséről.":::

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Hozza létre azt a várólistát, ahol a Azure Functions Service Bus-trigger az eseményeket fogja kapni:

1. A Service Bus-ben a bal oldali menüben válassza a **várólisták** elemet.

1. Válassza a **megosztott hozzáférési szabályzatok** lehetőséget. Ebben az oktatóanyagban nevezze el a lista *várólistáját*.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="A Service Bus-várólista létrehozásának képernyőképe.":::

1. Válassza a **Létrehozás** lehetőséget.

## <a name="get-a-service-bus-connection-string"></a>Service Bus-kapcsolatok karakterláncának beolvasása

1. A Service Bus-ben a bal oldali menüben válassza a **megosztott elérési házirendek** elemet.

1. Válassza a **RootManageSharedAccessKey** lehetőséget. Másolja és mentse az **elsődleges kapcsolatok sztringjét**. Az Alkalmazásbeállítások konfigurálásakor szüksége lesz erre a kapcsolódási karakterláncra.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="A Service Bus-kapcsolatok karakterláncának beszerzését bemutató képernyőkép.":::

## <a name="integrate-the-function-app"></a>A Function alkalmazás integrálása

Ha a Function alkalmazást virtuális hálózatokkal szeretné használni, csatlakoztatnia kell azt egy alhálózathoz. Egy adott alhálózatot fog használni a Azure Functions virtuális hálózati integrációhoz. Az ebben az oktatóanyagban létrehozott többi privát végponthoz az alapértelmezett alhálózatot fogja használni.

1. A Function alkalmazás bal oldali menüjében válassza a **hálózatkezelés** lehetőséget.

1. A **VNet-integráció** területen válassza **a kattintson ide a konfiguráláshoz** lehetőséget.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Képernyőkép a virtuális hálózatok integrálásáról.":::

1. Válassza a **VNet hozzáadása** lehetőséget.

1. A **Virtual Network** alatt válassza ki a korábban létrehozott virtuális hálózatot.

1. Válassza ki a korábban létrehozott **functions** -alhálózatot. A Function alkalmazás most már integrálva van a virtuális hálózattal.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Képernyőkép egy Function-alkalmazás alhálózathoz való összekapcsolásáról.":::

## <a name="configure-your-function-app-settings"></a>A Function alkalmazás beállításainak konfigurálása

1. A Function alkalmazás bal oldali menüjében válassza a **Konfigurálás** lehetőséget.

1. Ha a Function alkalmazást virtuális hálózatokkal szeretné használni, frissítse az alábbi táblázatban látható beállításokat. Egy beállítás hozzáadásához vagy szerkesztéséhez válassza az **+ új alkalmazás** vagy a **Szerkesztés** ikont az Alkalmazásbeállítások tábla jobb szélső oszlopában. Ha végzett, válassza a **Mentés** lehetőséget.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Képernyőkép a Function app-beállítások privát végpontok számára történő konfigurálásáról.":::

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | A létrehozott Storage-fiókhoz tartozó kapcsolatok karakterlánca. Ez a tárolási kapcsolatok karakterlánca a [Storage-fiók csatlakoztatási karakterláncának beolvasása](#get-the-storage-account-connection-string) szakaszból származik. Ez a beállítás lehetővé teszi, hogy a Function alkalmazás a biztonságos Storage-fiókot használja a normál működéshez futásidőben. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | A létrehozott Storage-fiókhoz tartozó kapcsolatok karakterlánca. Ez a beállítás lehetővé teszi, hogy a Function alkalmazás a biztonságos Storage-fiókot használja Azure Files számára, amelyet a rendszer az üzembe helyezés során használ. |
    | **WEBSITE_CONTENTSHARE** | fájlok | A Storage-fiókban létrehozott fájlmegosztás neve. Ezt a beállítást WEBSITE_CONTENTAZUREFILECONNECTIONSTRING használatával használhatja. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Hozza létre ezt az Alkalmazásbeállítások a Service Bus-beli kapcsolatok karakterláncához. Ez a tárolási kapcsolatok karakterlánca a [Service Bus-kapcsolatok karakterláncának beolvasása](#get-a-service-bus-connection-string) szakaszból származik.|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Hozza létre ezt az alkalmazás-beállítást. Az 1 érték lehetővé teszi a Function alkalmazás méretezését, ha a Storage-fiók egy virtuális hálózatra van korlátozva. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Hozza létre ezt az alkalmazás-beállítást. Ha az alkalmazás integrálva van egy virtuális hálózattal, akkor ugyanazt a DNS-kiszolgálót fogja használni, mint a virtuális hálózatot. A Function alkalmazásnak szüksége van erre a beállításra, hogy együttműködjön Azure DNS privát zónákkal. Privát végpontok használatakor szükséges. Ez a beállítás és WEBSITE_VNET_ROUTE_ALL az alkalmazásból érkező összes kimenő hívást a virtuális hálózatba küldi. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Hozza létre ezt az alkalmazás-beállítást. Ha az alkalmazás integrálva van egy virtuális hálózattal, akkor ugyanazt a DNS-kiszolgálót használja, mint a virtuális hálózat. A Function alkalmazásnak szüksége van erre a beállításra, hogy együttműködjön Azure DNS privát zónákkal. Privát végpontok használatakor szükséges. Ez a beállítás és WEBSITE_DNS_SERVER az alkalmazásból érkező összes kimenő hívást a virtuális hálózatba küldi. |

1. A **konfiguráció** nézetben válassza a **függvény futtatókörnyezet beállításai** lapot.

1. Állítsa be a **futásidejű méretezés figyelését** **a** következőre:. Kattintson a **Mentés** gombra. A futtatókörnyezet által vezérelt skálázás lehetővé teszi a nem HTTP-trigger függvények összekapcsolását a virtuális hálózaton belül futó szolgáltatásokhoz.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Képernyőkép arról, hogyan engedélyezhető a futásidejű vezérelt skálázás a Azure Functions számára.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Service Bus-trigger és HTTP-trigger üzembe helyezése

1. A GitHubban nyissa meg az alábbi minta tárházat. Tartalmaz egy Function alkalmazást és két függvényt, egy HTTP-triggert és egy Service Bus-várólista-triggert.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. A lap tetején válassza az **elágazás** lehetőséget, és hozzon létre egy elágazást a saját GitHub-fiókjában vagy szervezetében.

1. A Function alkalmazás bal oldali menüjében válassza a **központi telepítési központ** elemet. Ezután válassza a **Beállítások** lehetőséget.

1. A **Beállítások** lapon használja az alábbi táblázatban látható központi telepítési beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Forrás** | GitHub | Létre kell hoznia egy GitHub-tárházat a mintakód számára a 2. lépésben. | 
    | **Szervezet**  | myOrganization | Az a szervezet, amelybe a tárház be van jelölve. Ez általában a fiókja. |
    | **Adattár** | myRepo | A mintakód számára létrehozott tárház. |
    | **Ág** | main | A létrehozott tárház fő ága. |
    | **Futtatókörnyezet verme** | .NET | A mintakód a C# nyelven érhető el. |

1. Kattintson a **Mentés** gombra. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Képernyőkép: Azure Functions kód üzembe helyezése a portálon keresztül.":::

1. A kezdeti üzembe helyezés néhány percet is igénybe vehet. Az alkalmazás sikeres üzembe helyezése után a **naplók** lapon megjelenik egy **sikeres (aktív)** állapotjelző üzenet. Ha szükséges, frissítse az oldalt.

Gratulálunk! Sikeresen telepítette a minta Function alkalmazást.

## <a name="lock-down-your-function-app"></a>A Function alkalmazás zárolása

Most hozza létre a privát végpontot a Function alkalmazás zárolásához. Ez a privát végpont privát IP-cím használatával csatlakozik a Function alkalmazáshoz, és biztonságosan csatlakozik a virtuális hálózathoz. 

További információt a [privát végpont dokumentációjában](../private-link/private-endpoint-overview.md)talál.

1. A Function alkalmazás bal oldali menüjében válassza a **hálózatkezelés** lehetőséget.

1. A **magánhálózati végponti kapcsolatok** területen válassza **a privát végponti kapcsolatok konfigurálása** lehetőséget.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Képernyőkép arról, hogyan lehet navigálni egy Function app Private-végponthoz.":::

1. Válassza a **Hozzáadás** lehetőséget.

1. A megnyíló panelen használja a következő magánhálózati végpont-beállításokat:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Képernyőkép a Function app privát végpontjának létrehozásáról. A név functionapp-Endpoint. Az előfizetés &quot;Private test sub CACHHAI&quot;. A virtuális hálózat MyVirtualNet-oktatóanyag. Az alhálózat alapértelmezett.":::

1. A privát végpont hozzáadásához kattintson **az OK gombra** . 
 
Gratulálunk! Saját végpontok hozzáadásával sikeresen biztonságossá tette a Function app, a Service Bus és a Storage-fiókot.

### <a name="test-your-locked-down-function-app"></a>A lezárt Function alkalmazás tesztelése

1. A Function alkalmazás bal oldali menüjében válassza a **függvények** lehetőséget.

1. Válassza a **ServiceBusQueueTrigger** lehetőséget.

1. A bal oldali menüben válassza a **figyelő** elemet. 
 
Láthatja, hogy nem tudja figyelni az alkalmazást. A böngésző nem fér hozzá a virtuális hálózathoz, így nem tud közvetlenül hozzáférni a virtuális hálózaton belüli erőforrásokhoz. 
 
A következő módszerekkel figyelheti a függvényt Application Insights használatával:

1. A Function alkalmazás bal oldali menüjében válassza a **Application Insights** lehetőséget. Ezután válassza ki **Application Insights az adatmegjelenítést**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Képernyőkép arról, hogyan tekintheti meg az Application bepillantást egy Function alkalmazáshoz.":::

1. A bal oldali menüben válassza az **élő metrikák** lehetőséget.

1. Nyisson meg egy új lapot. A Service Bus-ben a bal oldali menüben válassza a **várólisták** elemet.

1. Válassza ki a várólistát.

1. A bal oldali menüben válassza a **Service Bus Explorer** lehetőséget. A **Küldés** területen a **tartalom típusa mezőben** válassza a **szöveg/egyszerű** lehetőséget. Ezután adjon meg egy üzenetet. 

1. Az üzenet elküldéséhez válassza a **Küldés** lehetőséget.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Képernyőkép a Service Bus-üzenetek küldéséről a portál használatával.":::

1. Az **élő metrikák** lapon láthatja, hogy a Service Bus-üzenetsor triggere elkezdődött. Ha nem, küldje el újra az üzenetet a **Service Bus Explorerben**.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Képernyőkép arról, hogyan tekintheti meg az üzeneteket a Function apps élő metrikái használatával.":::

Gratulálunk! Sikeresen tesztelte a Function app-telepítést privát végpontokkal.

## <a name="understand-private-dns-zones"></a>Privát DNS-zónák ismertetése
Saját végpontot használt az Azure-erőforrásokhoz való kapcsolódáshoz. A nyilvános végpont helyett magánhálózati IP-címhez csatlakozik. A meglévő Azure-szolgáltatások úgy vannak konfigurálva, hogy egy meglévő DNS-t használjanak a nyilvános végponthoz való kapcsolódáshoz. A magánhálózati végponthoz való kapcsolódáshoz felül kell bírálnia a DNS-konfigurációt.

A rendszer minden olyan Azure-erőforráshoz létrehoz egy magánhálózati DNS-zónát, amely privát végponttal lett konfigurálva. A rendszer létrehoz egy DNS-rekordot a magánhálózati végponthoz társított minden magánhálózati IP-címhez.

Ebben az oktatóanyagban a következő DNS-zónák jöttek létre:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozta a Premium Function alkalmazást, a Storage-fiókot és a Service Bus-t. Ezeket az erőforrásokat a privát végpontok mögött biztosította. 

Az alábbi hivatkozásokat követve további információkat tudhat meg az elérhető hálózatkezelési funkciókról:

> [!div class="nextstepaction"]
> [Hálózati beállítások a Azure Functions](./functions-networking-options.md)


> [!div class="nextstepaction"]
> [Prémium csomag Azure Functions](./functions-premium-plan.md)