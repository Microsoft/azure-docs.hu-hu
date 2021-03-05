---
title: Azure Functions integrálása virtuális hálózattal a privát végpontok használatával
description: Lépésenkénti oktatóanyag, amely bemutatja, hogyan csatlakoztatható egy függvény egy Azure-beli virtuális hálózathoz, és hogyan zárható le saját végpontokkal.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200206"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>Oktatóanyag: Azure Functions integrálása Azure-beli virtuális hálózattal privát végpontok használatával

Ez az oktatóanyag azt mutatja be, hogyan használható a Azure Functions az Azure-beli virtuális hálózatban található erőforrásokhoz privát végpontokkal való kapcsolódáshoz. Egy olyan függvényt hoz létre, amely egy Service Bus-várólista-triggert használó virtuális hálózat mögött található Storage-fiókkal rendelkezik.

> [!div class="checklist"]
> * Function-alkalmazás létrehozása a prémium csomaggal
> * Azure-erőforrások létrehozása (Service Bus, Storage-fiók, Virtual Network)
> * A Storage-fiók zárolása privát végpont mögött
> * Service Bus zárolása privát végpont mögött
> * Service Bus-és HTTP-eseményindítókkal is üzembe helyezhet egy Function alkalmazást.
> * A Function alkalmazás zárolása privát végpont mögött
> * Ellenőrizze, hogy a Function alkalmazás biztonságos-e a virtuális hálózat mögött
> * Az erőforrások eltávolítása

## <a name="create-a-function-app-in-a-premium-plan"></a>Function-alkalmazás létrehozása prémium csomaggal

Először hozzon létre egy .NET-függvény alkalmazást a [prémium] csomagban, mivel ez az oktatóanyag a C# nyelvet fogja használni. A Windows más nyelveket is támogat. Ez a csomag kiszolgáló nélküli skálázást biztosít a virtuális hálózatok integrálásának támogatása mellett.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

1. Az **új** lapon válassza a **számítási**  >  **függvényalkalmazás** elemet.

1. Az **alapvető** beállítások lapon használja az alábbi táblázatban megadott Function App-beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **Függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Az érvényes karakterek az `a-z` (kis- és nagybetűk megkülönböztetése nélkül) `0-9`és az `-`.  |
    |**Közzététel**| Kód | Kódfájlok közzétételét teszi lehetővé egy Docker-tárolóban. |
    | **Futtatókörnyezet verme** | .NET | Ez az oktatóanyag a .NET-et használja |
    |**Régió**| Előnyben részesített régió | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) vagy a funkciókhoz tartozó egyéb szolgáltatások közelében. |

1. Válassza a **Tovább: üzemeltetés** lehetőséget. Az **üzemeltetés** lapon adja meg a következő beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Storage-fiók](../storage/common/storage-account-create.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [Storage-fiókra vonatkozó követelményeknek](./storage-considerations.md#storage-account-requirements). |
    |**Operációs rendszer**| Windows | Ez az oktatóanyag a Windowst használja |
    | **[Felkészülés](./functions-scale.md)** | Prémium | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Válassza a **prémium** lehetőséget. Alapértelmezés szerint a rendszer új App Service tervet hoz létre. Az alapértelmezett **SKU és size** érték a **EP1**, ahol az EP a _rugalmas prémium szintű támogatást_ nyújtja. További információért lásd a [prémium SKU-ket tartalmazó listát](./functions-premium-plan.md#available-instance-skus).<br/>A JavaScript-függvények prémium csomagon való futtatásakor olyan példányt válasszon, amelynek kevesebb vCPU van. További információ: az [egymagos prémium csomagok kiválasztása](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Válassza a **Tovább: figyelés** lehetőséget. A **figyelés** lapon adja meg a következő beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Alapértelmezett | Létrehoz egy Application Insights erőforrást ugyanahhoz az *alkalmazáshoz* a legközelebbi támogatott régióban. A beállítás kibontásával módosíthatja az **új erőforrás nevét** , vagy kiválaszthat egy másik **helyet** az [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/) az adatai tárolásához. |

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget az alkalmazás-konfiguráció kiválasztásának áttekintéséhez.

1. A **felülvizsgálat + létrehozás** oldalon tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget a Function alkalmazás kiépítéséhez és üzembe helyezéséhez.

1. Válassza az **értesítések** ikont a portál jobb felső sarkában, és figyelje meg az **üzembe helyezés sikeres** üzenetét.

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a rögzítés az irányítópulton** lehetőséget is. A rögzítéssel egyszerűbbé válik a Function app-erőforráshoz való visszatérés az irányítópultról.

1. Gratulálunk! Sikeresen létrehozta a Premium Function alkalmazást!

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

A virtuális hálózatok esetében egy különálló Storage-fiók szükséges a Function app kezdeti létrehozásához.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

1. Az új lapon keresse meg a **Storage-fiók** elemet, és válassza a **Létrehozás** lehetőséget.

1. Az **alapvető** beállítások lapon adja meg az alábbi táblázatban megadott beállításokat. A REST lehet alapértelmezett:

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Válassza ki a Function alkalmazással létrehozott erőforráscsoportot. |
    | **Név** | mysecurestorage| Annak a Storage-fióknak a neve, amelyre a magánhálózati végpontot alkalmazni fogja. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Válassza ki azt a régiót, amelyben a Function alkalmazást létrehozta. |

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejezése után válassza a **Létrehozás** lehetőséget.

### <a name="create-a-service-bus"></a>Service Bus létrehozása

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

1. Az új lapon keressen rá **Service Bus** és válassza a **Létrehozás** lehetőséget.

1. Az **alapvető** beállítások lapon adja meg az alábbi táblázatban megadott beállításokat. A REST lehet alapértelmezett:

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. |
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Válassza ki a Function alkalmazással létrehozott erőforráscsoportot. |
    | **Név** | myServiceBus| Annak a szolgáltatás-busznak a neve, amelyre a magánhálózati végpontot alkalmazni fogja. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Válassza ki azt a régiót, amelyben a Function alkalmazást létrehozta. |
    | **Tarifacsomag** | Prémium | Válassza ezt a szintet, ha Service Buskal rendelkező privát végpontokat szeretne használni. |

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejezése után válassza a **Létrehozás** lehetőséget.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Az oktatóanyagban szereplő Azure-erőforrások vagy egy virtuális hálózaton belül vannak integrálva. A virtuális hálózatban lévő hálózati forgalom megőrzéséhez privát végpontokat kell használnia.

Az oktatóanyag két alhálózatot hoz létre:
- **alapértelmezett**: alhálózat magánhálózati végpontokhoz. A magánhálózati IP-címek ezen az alhálózaton vannak megadva.
- **functions**: alhálózat Azure functions virtuális hálózati integrációhoz. Ez az alhálózat delegálva van a Function alkalmazásnak.

Most hozza létre azt a virtuális hálózatot, amelyhez a Function app integrálva van.

1. Az Azure Portal menüjében vagy a Kezdőlapon válassza az **Erőforrás létrehozása** elemet.

1. Az új lapon keressen rá **Virtual Network** és válassza a **Létrehozás** lehetőséget.

1. Az **alapvető** beállítások lapon használja a virtuális hálózati beállításokat az alábbiakban megadott módon:

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Válassza ki a Function alkalmazással létrehozott erőforráscsoportot. |
    | **Név** | myVirtualNet| Annak a virtuális hálózatnak a neve, amelyhez a Function alkalmazás csatlakozni fog. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Válassza ki azt a régiót, amelyben a Function alkalmazást létrehozta. |

1. Az **IP-címek** lapon válassza az **alhálózat hozzáadása** elemet. Alhálózat hozzáadásakor használja az alább megadott beállításokat:

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Képernyőkép a virtuális hálózat létrehozása nézetről.":::

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Alhálózat neve** | funkciók | Annak az alhálózatnak a neve, amelyhez a Function alkalmazás csatlakozni fog. | 
    | **Alhálózati címtartomány** | 10.0.1.0/24 | Figyelje meg, hogy a fenti képen az IPv4-címterület 10.0.0.0/16. Ha a fentiek 10.1.0.0/16, a javasolt *alhálózati címtartomány* a 10.1.1.0/24 lenne. |

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejezése után válassza a **Létrehozás** lehetőséget.

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>A Storage-fiók zárolása privát végpontokkal

Az Azure Private-végpontok adott Azure-erőforrásokhoz való kapcsolódásra szolgálnak magánhálózati IP-címmel. Ez a kapcsolat biztosítja, hogy a hálózati forgalom a kiválasztott virtuális hálózaton belül maradjon, és a hozzáférés csak adott erőforrásokhoz érhető el. Most hozza létre az Azure file Storage és az Azure Blob Storage privát végpontját a Storage-fiókjával.

1. Az új Storage-fiókban a bal oldali menüben válassza a **hálózatkezelés** lehetőséget.

1. Válassza a **privát végponti kapcsolatok** fület, és válassza a **privát végpont** lehetőséget.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Képernyőfelvétel: privát végpontok létrehozása a Storage-fiókhoz.":::

1. Az **alapvető** beállítások lapon használja a magánhálózati végpont beállításait az alább megadott módon:

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Válassza ki a Function alkalmazással létrehozott erőforráscsoportot. | |
    | **Név** | fájl – végpont | A Storage-fiókból származó fájlokhoz tartozó magánhálózati végpont neve. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Válassza ki azt a régiót, amelyben a Storage-fiókot létrehozta. |

1. Az **erőforrás** lapon használja a magánhálózati végpont beállításait az alább megadott módon:

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **Erőforrás típusa**  | Microsoft. Storage/storageAccounts | Ez a Storage-fiókok erőforrás-típusa. |
    | **Erőforrás** | mysecurestorage | Az imént létrehozott Storage-fiók |
    | **Célzott alerőforrás** | file | Ezt a magánhálózati végpontot fogja használni a rendszer a Storage-fiókból származó fájlokhoz. |

1. A **konfiguráció** lapon válassza az **alapértelmezett** lehetőséget az alhálózat beállításnál.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejezése után válassza a **Létrehozás** lehetőséget. A virtuális hálózat erőforrásai mostantól megadhatják a tárolási fájlokat.

1. Hozzon létre egy másik privát végpontot a Blobok számára. A **Resources (erőforrások** ) lapon használja az alábbi beállításokat. Az összes többi beállítás esetében ugyanazokat a beállításokat használja, mint a fájlhoz tartozó privát végpontok létrehozási lépései.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **Erőforrás típusa**  | Microsoft. Storage/storageAccounts | Ez a Storage-fiókok erőforrás-típusa. |
    | **Erőforrás** | mysecurestorage | Az imént létrehozott Storage-fiók |
    | **Célzott alerőforrás** | blob | Ezt a magánhálózati végpontot fogja használni a rendszer a Storage-fiókban lévő blobokhoz. |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>A Service Bus zárolása privát végponttal

Most hozza létre a Azure Service Bus saját végpontját.

1. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget az új Service Bus-ben.

1. Válassza a **privát végponti kapcsolatok** fület, és válassza a **privát végpont** lehetőséget.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Képernyőkép arról, hogyan lehet navigálni a Service Bus privát végpontokhoz.":::

1. Az **alapvető** beállítások lapon használja a magánhálózati végpont beállításait az alább megadott módon:

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Válassza ki a Function alkalmazással létrehozott erőforráscsoportot. |
    | **Név** | SB – végpont | A Storage-fiókból származó fájlokhoz tartozó magánhálózati végpont neve. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Válassza ki azt a régiót, amelyben a Storage-fiókot létrehozta. |

1. Az **erőforrás** lapon használja a magánhálózati végpont beállításait az alább megadott módon:

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **Erőforrás típusa**  | Microsoft. ServiceBus/névterek | Ez a Service Bus erőforrás-típusa. |
    | **Erőforrás** | myServiceBus | Az oktatóanyagban korábban létrehozott Service Bus. |
    | **Cél alerőforrás** | névtér | Ezt a privát végpontot fogja használni a rendszer a Service Bus-névtérhez. |

1. A **konfiguráció** lapon válassza az **alapértelmezett** lehetőséget az alhálózat beállításnál.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejezése után válassza a **Létrehozás** lehetőséget. A virtuális hálózat erőforrásai mostantól a Service Bus szolgáltatással is beszélgethet.

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

1. A létrehozott Storage-fiókban válassza a **fájlmegosztás** lehetőséget a bal oldali menüben.

1. Válassza a **+ fájlmegosztás** lehetőséget. Adja meg a **fájlokat** a fájlmegosztás neveként az oktatóanyag szempontjából.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Képernyőkép a fájlmegosztás létrehozásáról a Storage-fiókban.":::

## <a name="get-storage-account-connection-string"></a>Storage-fiókhoz tartozó kapcsolatok karakterláncának beolvasása

1. A létrehozott Storage-fiókban a bal oldali menüben válassza a **hozzáférési kulcsok** elemet.

1. Válassza a **kulcsok megjelenítése** lehetőséget. Másolja a key1-beli kapcsolatok karakterláncát, és mentse azt. Az Alkalmazásbeállítások konfigurálásakor később erre a kapcsolódási sztringre lesz szükség.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Képernyőfelvétel a Storage-fiók kapcsolódási karakterláncának beszerzéséről.":::

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Ez lesz az a várólista, amelyre a Azure Functions Service Bus trigger beolvassa az eseményeket.

1. A Service Bus-ben válassza a bal oldali menüben a **várólisták** elemet.

1. Válassza a **megosztott hozzáférési szabályzatok** lehetőséget. Adja **meg a várólista nevét** a várólista számára az oktatóanyag szempontjából.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="A Service Bus-várólista létrehozásának képernyőképe.":::

## <a name="get-service-bus-connection-string"></a>Service Bus-kapcsolatok karakterláncának beolvasása

1. A Service Bus-ben válassza a bal oldali menüben a **megosztott hozzáférési házirendek** elemet.

1. Válassza a **RootManageSharedAccessKey** lehetőséget. Másolja ki az **elsődleges kapcsolatok karakterláncát**, és mentse. Az Alkalmazásbeállítások konfigurálásakor később erre a kapcsolódási sztringre lesz szükség.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="A Service Bus-kapcsolatok karakterláncának beszerzését bemutató képernyőkép.":::

## <a name="integrate-function-app-with-your-virtual-network"></a>A Function app integrálása a virtuális hálózattal

Ha a Function alkalmazást virtuális hálózatokkal szeretné használni, csatlakoztatnia kell azt egy alhálózathoz. Egy adott alhálózatot használunk a Azure Functions virtuális hálózati integrációhoz és az alapértelmezett alhálóhoz az oktatóanyagban létrehozott összes többi privát végponthoz.

1. A Function alkalmazásban válassza a bal oldali menü **hálózatkezelés** elemét.

1. Válassza **a kattintson ide a VNet-integráció alatt beállításhoz** .

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Képernyőkép a virtuális hálózatok integrálásáról.":::

1. Válassza a **VNet hozzáadása** lehetőséget

1. A **Virtual Network** alatt megnyíló panelen válassza ki a korábban létrehozott virtuális hálózatot.

1. Válassza ki azt az **alhálózatot** , amelyet korábban a **functions** néven hoztunk létre. A Function alkalmazás most már integrálva van a virtuális hálózattal.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Képernyőkép egy Function-alkalmazás alhálózathoz való összekapcsolásáról.":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>A Function alkalmazás beállításainak konfigurálása privát végpontokhoz

1. A Function alkalmazásban válassza a bal oldali menü **konfiguráció** elemét.

1. Ha a Function alkalmazást virtuális hálózatokkal szeretné használni, a következő Alkalmazásbeállítások frissítésére lesz szükség. Válassza az **+ új Alkalmazásbeállítás** vagy a ceruza lehetőséget az Alkalmazásbeállítások tábla jobb szélső oszlopában **, ha** szükséges. Ha elkészült, kattintson a **Mentés** elemre.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Képernyőkép a Function app-beállítások privát végpontok számára történő konfigurálásáról.":::

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | A létrehozott Storage-fiókhoz tartozó kapcsolatok karakterlánca. A Storage- [fiókhoz tartozó kapcsolatok karakterláncának beolvasása](#get-storage-account-connection-string). A beállítás módosításával a Function alkalmazás mostantól a biztonságos Storage-fiókot fogja használni a normál működéshez futásidőben. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | A létrehozott Storage-fiókhoz tartozó kapcsolatok karakterlánca. Ennek a beállításnak a megváltoztatásával a Function alkalmazás mostantól a biztonságos Storage-fiókot fogja használni a Azure Files számára, amelyet a telepítésekor használ a rendszer. |
    | **WEBSITE_CONTENTSHARE** | fájlok | A Storage-fiókban létrehozott fájlmegosztás neve. Ez az Alkalmazásbeállítások a WEBSITE_CONTENTAZUREFILECONNECTIONSTRINGsal együtt használható. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Hozzon létre egy alkalmazás-beállítást a Service Bus-beli kapcsolatok karakterláncához. Ez a Storage-kapcsolatok karakterlánca a [Service Bus-kapcsolatok karakterláncának lekérése](#get-service-bus-connection-string)során.|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Hozza létre ezt az alkalmazás-beállítást. Az 1 érték lehetővé teszi a Function alkalmazás méretezését, ha a Storage-fiókja egy virtuális hálózatra van korlátozva. Ezt a beállítást akkor érdemes engedélyezni, ha a Storage-fiókot egy virtuális hálózatra korlátozza. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Hozza létre ezt az alkalmazás-beállítást. Ha az alkalmazás integrálva van egy virtuális hálózattal, akkor ugyanazt a DNS-kiszolgálót fogja használni, mint a virtuális hálózatot. Ez a két beállítás egyike, amelyekhez a Function alkalmazásnak Azure DNS privát zónával kell rendelkeznie, és a magánhálózati végpontok használatakor szükséges. Ezek a beállítások az alkalmazásból érkező összes kimenő hívást elküldik a virtuális hálózatnak. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Hozza létre ezt az alkalmazás-beállítást. Ha az alkalmazás integrálva van egy virtuális hálózattal, akkor ugyanazt a DNS-kiszolgálót fogja használni, mint a virtuális hálózatot. Ez a két beállítás egyike, amelyekhez a Function alkalmazásnak Azure DNS privát zónával kell rendelkeznie, és a magánhálózati végpontok használatakor szükséges. Ezek a beállítások az alkalmazásból érkező összes kimenő hívást elküldik a virtuális hálózatnak. |

1. Maradjon a **konfiguráció** nézetben, és válassza a **Function Runtime Settings (függvény futtatókörnyezet beállításai** ) lapot.

1. Állítsa be a **futásidejű méretezés figyelését** a be értékre, majd válassza **a** **Mentés** lehetőséget. A futásidejű vezérelt skálázás lehetővé teszi a nem HTTP-trigger függvények összekapcsolását a virtuális hálózaton belül futó szolgáltatásokhoz.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Képernyőfelvétel: a futásidejű vezérelt skálázás engedélyezése Azure Functions számára.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>Service Bus-trigger és http-trigger üzembe helyezése a Function alkalmazásban

1. A GitHubon keresse meg a következő minta-tárházat, amely két függvényt tartalmazó Function alkalmazást, egy HTTP-triggert és egy Service Bus üzenetsor-triggert tartalmaz.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. A lap tetején kattintson az **elágazás** gombra, és hozzon létre egy elágazást a saját GitHub-fiókjában vagy-szervezetében.

1. A Function alkalmazásban válassza a bal oldali menü **központi telepítési központ** elemét. Ezután válassza a **Beállítások** lehetőséget.

1. A **Beállítások** lapon adja meg a központi telepítési beállításokat az alábbi módon:

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Forrás** | GitHub | Létre kell hoznia egy GitHub-tárházat a 2. lépésben szereplő mintakód-kóddal. | 
    | **Szervezet**  | myOrganization | Ez az a szervezet, amelybe a tárház be van jelölve, általában a fiókja. |
    | **Adattár** | myRepo | A mintakód által létrehozott tárház. |
    | **Ág** | main | Ez az imént létrehozott tárház, ezért használja a fő ágat. |
    | **Futtatókörnyezet verme** | .NET | A mintakód a C# nyelven érhető el. |

1. Kattintson a **Mentés** gombra. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Képernyőkép: Azure Functions kód üzembe helyezése a portálon keresztül.":::

1. A kezdeti üzembe helyezés néhány percet is igénybe vehet. Az alkalmazás sikeres üzembe helyezése után a **naplók** lapon megjelenik a **sikeres (aktív)** állapotjelző üzenet. Ha szükséges, frissítse az oldalt. 

1. Gratulálunk! Sikeresen telepítette a minta Function alkalmazást.

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>A Function alkalmazás zárolása privát végponttal

Most hozzon létre egy privát végpontot a Function alkalmazáshoz. Ez a privát végpont privát IP-cím használatával csatlakozik a Function alkalmazáshoz, és biztonságosan csatlakozik a virtuális hálózathoz. A privát végpontokkal kapcsolatos további információkért nyissa meg a [privát végpontok dokumentációját](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

1. A Function alkalmazásban válassza a bal oldali menü **hálózatkezelés** elemét.

1. Válassza **a kattintson ide a** privát végponti kapcsolatok alatt történő konfiguráláshoz.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Képernyőkép a függvényalkalmazás privát végpontjának megnyitásáról.":::

1. Válassza a **Hozzáadás** lehetőséget.

1. A megnyíló menüben használja a magánhálózati végpont beállításait az alább megadott módon:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Képernyőkép függvényalkalmazás privát végpont létrehozásáról.":::

1. A privát végpont hozzáadásához kattintson **az OK gombra** . Gratulálunk! A Function app, a Service Bus és a Storage-fiók privát végpontokkal való biztonságossá tétele sikeresen megtörtént.

### <a name="test-your-locked-down-function-app"></a>A zárolt funkció alkalmazásának tesztelése

1. A Function alkalmazásban válassza a bal oldali menü **függvények** elemét.

1. Válassza ki a **ServiceBusQueueTrigger**.

1. A bal oldali menüben válassza a **figyelő** lehetőséget. láthatja, hogy nem tudja figyelni az alkalmazást. Ennek az az oka, hogy a böngésző nem fér hozzá a virtuális hálózathoz, így nem tud közvetlenül hozzáférni a virtuális hálózaton belüli erőforrásokhoz. Most bemutatjuk egy másik módszert, amellyel továbbra is figyelheti a függvényt, Application Insights.

1. A Function alkalmazásban válassza a bal oldali menüben a **Application Insights** lehetőséget, majd válassza az **Application Insights adatok megtekintése** lehetőséget.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Képernyőkép arról, hogyan lehet megtekinteni az Application bepillantást egy függvényalkalmazás.":::

1. Válassza a bal oldali menü **élő metrikák** elemét.

1. Nyisson meg egy új lapot. A Service Bus a bal oldali menüben válassza a **várólisták** elemet.

1. Válassza ki a várólistát.

1. Válassza a bal oldali menü **Service Bus Explorer** elemét. A **Küldés** területen válassza a **szöveg/egyszerű** lehetőséget a **tartalom típusaként** , és adjon meg egy üzenetet. 

1. Az üzenet elküldéséhez válassza a **Küldés** lehetőséget.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Képernyőkép Service Bus üzenetek küldéséről a portál használatával.":::

1. Az **élő metrikákat** tartalmazó lapon meg kell jelennie, hogy az Service Bus üzenetsor-trigger aktiválva lett. Ha nem, küldje el újra az üzenetet a **Service Bus Explorerben**

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Képernyőkép arról, hogyan lehet üzeneteket megtekinteni a Function apps élő metrikái használatával.":::

1. Gratulálunk! Sikeresen tesztelte a Function alkalmazást a saját végpontok beállításával!

### <a name="private-dns-zones"></a>saját DNS zónák
Ha privát végpontot használ az Azure-erőforrásokhoz való csatlakozáshoz, az azt jelenti, hogy a nyilvános végpont helyett magánhálózati IP-címhez csatlakozik. A meglévő Azure-szolgáltatások úgy vannak konfigurálva, hogy a meglévő DNS-t használják a nyilvános végponthoz való kapcsolódáshoz. A DNS-konfigurációt felül kell bírálni a magánhálózati végponthoz való kapcsolódáshoz.

A rendszer létrehoz egy magánhálózati DNS-zónát minden olyan Azure-erőforráshoz, amely privát végponttal van konfigurálva. A rendszer egy DNS-rekordot hoz létre a magánhálózati végponthoz társított minden magánhálózati IP-címhez.

Ebben az oktatóanyagban a következő DNS-zónák jöttek létre:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozta a Premium Function alkalmazást, a Storage-fiókot és a Service Bust, és a privát végpontok mögött biztosította azokat. További információ az alábbi, különböző hálózatkezelési funkciókról:

> [!div class="nextstepaction"]
> [További tudnivalók a függvények hálózati beállításairól](./functions-networking-options.md)

[Prémium szintű csomag]: functions-premium-plan.md
