---
title: Végpontok és útvonalak kezelése (portál)
titleSuffix: Azure Digital Twins
description: 'Lásd: a végpontok és az események útvonalának beállítása és kezelése az Azure digitális Twins-adataihoz a Azure Portal használatával.'
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8a8cc6563d8e3cae942c4dfb43afa3d05f6f2b75
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257303"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Végpontok és útvonalak kezelése az Azure Digital Twins-ban (portál)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Az Azure Digital Twins-ban átirányíthatja az [eseményekre vonatkozó értesítéseket](concepts-event-notifications.md) az alsóbb rétegbeli szolgáltatásokhoz vagy a csatlakoztatott számítási erőforrásokhoz. Ehhez először be kell állítani az eseményeket fogadó **végpontokat**. Ezután létrehozhat olyan [**esemény-útvonalakat**](concepts-route-events.md) , amelyekkel megadhatja, hogy az Azure Digital Twins mely eseményeit adja meg a végpontok.

Ez a cikk végigvezeti a végpontok és útvonalak létrehozásának folyamatán a [Azure Portal](https://portal.azure.com)használatával.

Azt is megteheti, hogy a végpontokat és útvonalakat az [Event Routes API](/rest/api/digital-twins/dataplane/eventroutes)-kkal, az [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)-kkal vagy az [Azure digitális Twins parancssori](how-to-use-cli.md)felülettel kezeli. A jelen cikk a portál helyett ezeket a mechanizmusokat használó verziója [*: útmutató: végpontok és útvonalak kezelése (API-k és parancssori felület)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Előfeltételek

* Szüksége lesz egy **Azure-fiókra** ( [itt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)akár ingyenesen is beállíthatja)
* Azure-beli **digitális Twins-példányra** lesz szüksége az Azure-előfizetésében. Ha még nem rendelkezik példányokkal, létrehozhat egyet a következő útmutató lépéseit követve [*: példány és hitelesítés beállítása*](how-to-set-up-instance-portal.md). A telepítésből származó alábbi értékekkel a cikk későbbi részében használhatja a következő értékeket:
    - Példány neve
    - Erőforráscsoport

Ezeket a részleteket a [Azure Portal](https://portal.azure.com) a példány beállítása után találhatja meg. Jelentkezzen be a portálra, és keresse meg a példány nevét a portál keresési sávján.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Képernyőkép: Azure Portal keresési sáv." lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

Válassza ki a példányt az eredmények közül, és tekintse meg ezeket az adatokat a példány áttekintésében:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Képernyőkép az Azure Digital Twins-példány áttekintés oldaláról. A név és az erőforráscsoport ki van emelve.":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Végpont létrehozása az Azure Digital Twins számára

Ezek a példányok által létrehozható támogatott típusú végpontok:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Szolgáltatásbusz](../service-bus-messaging/service-bus-messaging-overview.md)

További információ a különböző végpontok típusairól: [*választás az Azure Messaging Services között*](../event-grid/compare-messaging-services.md).

Ez a szakasz azt ismerteti, hogyan hozható létre a végpontok egyike a [Azure Portal](https://portal.azure.com).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>A végpont létrehozása 

Miután létrehozta a végponti erőforrásokat, használhatja őket egy Azure digitális Twins-végponthoz. Új végpont létrehozásához nyissa meg a példányának lapját a [Azure Portalban](https://portal.azure.com) (a példány megkereséséhez írja be a nevét a portál keresési sávjába).

1. A példány menüben válassza a _végpontok_ lehetőséget. Ezután az alábbi *végpontok* lapról válassza a *+ végpont létrehozása* lehetőséget. Ekkor megnyílik a *végpont létrehozása* lap, ahol a következő lépésekben található mezőket kell kitöltenie.

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Képernyőkép: Event Grid típusú végpont létrehozása." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. Adja meg a végpont **nevét** , és válassza ki a **végpont típusát**.

1. Hajtsa végre a végpont típusához szükséges egyéb adatokat, beleértve az előfizetést és a [fent](#prerequisite-create-endpoint-resources)leírt végponti erőforrásokat.
    1. Az Event hub és a Service Bus végpontok esetében ki kell választania egy **hitelesítési típust**. Ha a végpontot [felügyelt identitással](concepts-security.md#managed-identity-for-accessing-other-resources-preview) szeretné használni az Azure Digital Twins-példányhoz, használhatja a kulcs alapú hitelesítést egy előre létrehozott engedélyezési szabállyal vagy egy identitás-alapú hitelesítéssel. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Képernyőkép az Event hub típusú végpont létrehozásáról." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. Fejezze be a végpont létrehozását a _Save (Mentés_) gombra kattintva.

>[!IMPORTANT]
> Ahhoz, hogy sikeresen használhassa az identitás-alapú hitelesítést a végponthoz, létre kell hoznia egy felügyelt identitást a példányhoz a következő témakörben ismertetett lépéseket követve [*: felügyelt identitás engedélyezése útválasztási eseményekhez (előzetes verzió)*](./how-to-enable-managed-identities-portal.md).

A végpont létrehozása után ellenőrizheti, hogy a végpont sikeresen létrejött-e az értesítés ikonjának a felső Azure Portal sávban való ellenőrzésével: 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Képernyőkép az értesítésről a végpont létrehozásának ellenőrzéséhez. A portál felső sávján a harang alakú ikon van kiválasztva, és a &quot;végpont ADT – eh-Endpoint sikeresen létrehozva&quot; értesítés jelenik meg.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Ha a végpont létrehozása sikertelen, figyelje meg a hibaüzenetet, és néhány perc múlva próbálkozzon újra.

Megtekintheti az Azure Digital Twins-példány *végpontok* lapján létrehozott végpontot is.

Az Event Grid, az Event hub vagy a Service Bus témakör az Azure digitális Twins-beli végpontként érhető el, a végponthoz választott néven. Ezt a nevet általában egy **esemény-útvonal** céljaként fogja használni, amelyet később fog létrehozni [ebben a cikkben](#create-an-event-route).

### <a name="create-an-endpoint-with-dead-lettering"></a>Végpont létrehozása a kézbesítetlen levelekkel

Ha egy végpont nem tud eseményt kézbesíteni egy adott időszakon belül, vagy ha az eseményt bizonyos számú alkalommal próbálta kézbesíteni, akkor a kézbesítetlen eseményt elküldheti egy Storage-fiókba. Ezt a folyamatot **Kézbesítetlen levélnek** nevezzük.

Ha olyan végpontot szeretne létrehozni, amelyen engedélyezve van a kézbesítetlen üzenetek használata, a [CLI-parancsokat](how-to-use-cli.md) vagy a [sík API-kat](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) kell használnia a végpont létrehozásához, nem pedig a Azure Portal.

Ezekről az eszközökről a jelen cikk [*API-k és CLI-*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) verziójában talál útmutatást.

## <a name="create-an-event-route"></a>Esemény útvonalának létrehozása

Az Azure digitális Twins-ból egy végpontba küldött adatok tényleges elküldéséhez meg kell határoznia egy **esemény-útvonalat**. Ezek az útvonalak lehetővé teszik a fejlesztők számára, hogy az események áramlását a rendszeren és az alárendelt szolgáltatásokon keresztül továbbítsák. További információk az esemény-útvonalakról a [*fogalmakban: az Azure Digital Twins eseményeinek továbbítása*](concepts-route-events.md).

**Előfeltétel**: a cikk korábbi részében leírtak szerint hozzon létre végpontokat, mielőtt továbblép az útvonal létrehozásához. Ha befejezte a végpontok beállítását, folytassa az esemény-útvonal létrehozásával.

>[!NOTE]
>Ha nemrég telepítette a végpontokat, ellenőrizze, hogy készen állnak-e a telepítésre, **mielőtt** új esemény-útvonalra próbálja használni őket. Ha nem tudja beállítani az útvonalat, mert a végpontok nem állnak készen, várjon néhány percet, és próbálkozzon újra.

### <a name="creation-steps-with-the-azure-portal"></a>A létrehozás lépései a Azure Portal

Az események útvonalának definíciója az alábbi elemeket tartalmazza:
* A használni kívánt útvonal neve
* A használni kívánt végpont neve
* Egy szűrő, amely meghatározza, hogy mely események kerülnek a végponthoz
    - Ha le szeretné tiltani az útvonalat, hogy ne küldjön eseményeket, használja a `false`
    - Ha olyan útvonalat szeretne engedélyezni, amely nem rendelkezik adott szűréssel, használja a szűrő értékét `true`
    - A más típusú szűrők részleteiről az alábbi, az [*Események szűrése*](#filter-events) című szakaszban olvashat.

Egyetlen útvonalon több értesítés és eseménytípus is kijelölhető.

Az esemény-útvonal létrehozásához nyissa meg az Azure Digital Twins-példány részletek lapját a [Azure Portalban](https://portal.azure.com) (a példány megkereséséhez írja be a nevét a portál keresési sávjába).

A példány menüben válassza az _esemény útvonalak_ elemet. Ezután az alábbi *esemény-útvonalak* lapon válassza az *+ esemény-útvonal létrehozása* lehetőséget. 

A megnyíló *esemény-útvonal létrehozása* oldalon válassza a minimum:
* A _név_ mezőben adja meg az útvonal nevét.
* Az útvonal létrehozásához használni kívánt _végpont_ 

Ahhoz, hogy az útvonal engedélyezve legyen, legalább **egy esemény-útválasztási szűrőt is hozzá** kell adnia `true` . (Az alapértelmezett érték kihagyása `false` esetén az útvonalat hozza létre, de a rendszer nem küldi el az eseményeket.) Ehhez állítsa be a kapcsolót a _speciális szerkesztő_ számára az engedélyezéshez, majd írja `true` be a *szűrőt a szűrő* mezőbe.

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Képernyőkép a példányhoz tartozó esemény-útvonal létrehozásáról." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

Ha elkészült, nyomja meg a _Save (Mentés_ ) gombot az esemény útvonalának létrehozásához.

## <a name="filter-events"></a>Események szűrése

A fentiekben leírtak szerint az útvonalak **szűrő** mezővel rendelkeznek. Ha az útvonalon található szűrő értéke, a rendszer `false` nem küld eseményeket a végpontnak. 

A minimális szűrő engedélyezése után a `true` végpontok számos eseményt kapnak az Azure Digital ikrekből:
* Az Azure Digital Twins szolgáltatás API-ját használó [digitális Twins](concepts-twins-graph.md) telemetria
* A Twin Property változási értesítései az Azure Digital Twins-példányon található bármelyik Twin tulajdonság változásakor
* Életciklussal kapcsolatos események, az ikrek vagy kapcsolatok létrehozásakor vagy törlésekor

Megadhatja az elküldött események típusát egy konkrétabb szűrő definiálásával.

Ha esemény-útvonal létrehozása közben szeretne hozzáadni egy eseményt, az esemény útvonalának *létrehozása* lap _esemény útvonalának szűrése_ szakaszát használhatja. 

Kiválaszthat néhány alapszintű általános szűrési lehetőséget, vagy a speciális szűrési beállítások használatával írhat saját egyéni szűrőket is.

>[!NOTE]
> A szűrők **megkülönböztetik a kis-** és nagybetűket, és meg kell egyezniük a hasznos adatokkal. 
>
> Az telemetria szűrők esetében ez azt jelenti, hogy a burkolatnak meg kell egyeznie az eszköz által eljuttatott telemetria, nem feltétlenül a Twin modellben definiált burkolattal.

#### <a name="use-the-basic-filters"></a>Az egyszerű szűrők használata

Az alapszintű szűrők használatához bontsa ki az _eseménytípus_ lehetőséget, majd jelölje be a végpontnak elküldeni kívánt eseményekhez tartozó jelölőnégyzeteket. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Képernyőkép egy alapszintű szűrővel rendelkező esemény-útvonal létrehozásáról. Az események jelölőnégyzetének bejelölése.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Ezzel automatikusan feltölti a szűrő szövegmezőt a kiválasztott szűrő szövegével:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Képernyőkép egy alapszintű szűrővel rendelkező esemény-útvonal létrehozásáról. Az automatikusan feltöltött szűrő szövegének megjelenítése az események kiválasztása után.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>A speciális szűrők használata

Másik lehetőségként használhatja a speciális szűrő lehetőséget is a saját egyéni szűrők írására.

Ha speciális szűrési beállításokkal szeretne eseményvezérelt útvonalat létrehozni, állítsa be a _speciális szerkesztő_ kapcsolóját, hogy engedélyezze azt. Ezután megírhatja a saját események szűrőit a *szűrő* mezőbe:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Képernyőkép egy speciális szűrővel rendelkező esemény-útvonal létrehozásáról.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Itt láthatók a támogatott útválasztási szűrők. A *szűrési szöveg sémája* oszlopban szereplő részletek a szűrő mezőbe beírható szöveg.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Következő lépések

További információ a fogadott üzenetek különböző típusairól:
* [*Fogalmak: események értesítései*](concepts-event-notifications.md)