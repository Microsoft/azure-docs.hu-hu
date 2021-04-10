---
title: Egyéni eseményindítók létrehozása a Azure Data Factoryban
description: Megtudhatja, hogyan hozhat létre egyéni eseményindítót a Azure Data Factoryban, amely egy folyamatot futtat egy Event Grid közzétett egyéni eseményre válaszul.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2d2f26b24e7fa10d9244de8f99d78c64a44b3d61
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785648"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>Egy folyamatot futtató eseményindító létrehozása egy egyéni eseményre adott válaszként (előzetes verzió)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azokat az egyéni eseményindítókat ismerteti, amelyeket a Data Factory-folyamatokban hozhat létre.

Az eseményvezérelt architektúra (EDA) olyan közös adatintegrációs minta, amely az események gyártását, észlelését, felhasználását és reagálását foglalja magában. Az adatintegrációs forgatókönyvek gyakran megkövetelik, hogy Data Factory ügyfelek bizonyos események alapján indítsák el a folyamatokat. Data Factory natív integrációja [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) mostantól [Egyéni eseményekre](../event-grid/custom-topics.md)is kiterjed: az ügyfelek tetszőleges eseményeket küldhetnek egy Event Grid-témakörbe, és Data Factory előfizetnek a témakörre, és ennek megfelelően elindítják a folyamatokat.

> [!NOTE]
> A cikkben ismertetett integráció a [Azure Event Gridtól](https://azure.microsoft.com/services/event-grid/)függ. Győződjön meg arról, hogy az előfizetés regisztrálva van a Event Grid erőforrás-szolgáltatónál. További információ: erőforrás- [szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). A *Microsoft. EventGrid/eventSubscriptions/** művelet végrehajtásához képesnek kell lennie. Ez a művelet a EventGrid EventSubscription közreműködő beépített szerepkörének részét képezi.

Emellett a folyamat-és az egyéni eseményindítók kombinálásával az ügyfelek elemezni tudják és hivatkozhatnak az egyéni _adattartalomra_ a folyamat futásakor.  az egyéni esemény adattartalma egy szabad formátumú JSON kulcs-érték struktúra, amely lehetővé teszi az ügyfelek számára az eseményvezérelt folyamat futtatásának maximális felügyeletét.

> [!IMPORTANT]
> Előfordulhat, hogy az paraméterezés-ben hivatkozott egyik kulcs hiányzik az egyéni esemény hasznos adatai között. Az _trigger futtatása_ hibával meghiúsul, és a rendszer nem értékeli ki a kifejezést, mert a tulajdonság _Kulcsnév_ nem létezik. Az esemény __nem__ indítja el a _folyamat futását_ .

## <a name="setup-event-grid-custom-topic"></a>Egyéni témakör beállítása Event Grid

A Data Factory egyéni eseményindítójának használatához _először_ be kell állítania egy [Egyéni témakört a Event Gridban](../event-grid/custom-topics.md). A munkafolyamat különbözik a tárolási események eseményindítótól, ahol a Data Factory a témakört fogja beállítani. Itt kell navigálnia a Azure Event Grid, és létre kell hoznia a témakört. Az egyéni témakör létrehozásával kapcsolatos további információkért lásd: Azure Event Grid [portál oktatóanyagok](../event-grid/custom-topics.md#azure-portal-tutorials) és parancssori felületi [oktatóanyagok](../event-grid/custom-topics.md#azure-cli-tutorials)

Az adatüzemek elvárják, hogy az események kövessék [Event Grid eseményvezérelt sémát](../event-grid/event-schema.md). Győződjön meg arról, hogy az esemény-adattartalom a következő mezőket tartalmazta.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

Ebből a szakaszból megtudhatja, hogyan hozhat létre tárolási esemény-eseményindítókat a Azure Data Factory felhasználói felületen belül.

1. Váltson a **Szerkesztés** lapra, amely egy ceruza szimbólummal jelenik meg.

1. Válassza az **aktiválás** lehetőséget a menüben, majd válassza az **új/szerkesztés** lehetőséget.

1. Az **Eseményindítók hozzáadása** lapon válassza az **eseményindító kiválasztása...**, majd az **+ új** lehetőséget.

1. Válassza az aktiválási típus **Egyéni események** lehetőséget.

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Képernyőkép a szerző lapról új egyéni eseményindító létrehozásához Data Factory felhasználói felületen." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Válassza ki az egyéni témakört az Azure-előfizetések legördülő menüből, vagy manuálisan adja meg az esemény témakörének hatókörét.

   > [!NOTE]
   > Új vagy meglévő egyéni eseményindító létrehozása vagy módosítása esetén a Data Factoryba való bejelentkezéshez használt Azure-fióknak és a tárolási esemény eseményindítójának közzétételéhez megfelelő szerepköralapú hozzáférés-vezérlési (Azure RBAC) engedéllyel kell rendelkeznie a következő témakörben:. Nincs szükség további engedélyre: a Azure Data Factory tartozó szolgáltatásnév _nem_ igényel speciális engedélyeket Event Gridhoz. A hozzáférés-vezérléssel kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés](#role-based-access-control) szakasz.

1. A **tulajdonos** a következővel kezdődik, és a **Tárgy vége a** tulajdonságok lehetőséggel szűrheti azokat az eseményeket, amelyekhez a folyamatot aktiválni szeretné. Mindkét tulajdonság nem kötelező.

1. Az **+ új** lehetőséggel hozzáadhat olyan **típusú eseményeket** , amelyeket szűrni szeretne. Egyéni esemény-eseményindító alkalmazott vagy kapcsolat a listához: Ha egy egyéni eseményhez tartozik egy _EventType_ tulajdonság, amely az itt felsoroltnak felel meg, elindítja a folyamat futtatását. Az esemény típusa nem megkülönbözteti a kis-és nagybetűket. Az alábbi képernyőképen például az trigger az összes _copycompleted_ -vagy _copysucceeded_ -eseménynek megfelel a _gyárakkal_ kezdődően

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Képernyőfelvétel: az eseményindító szerkesztése lap az eseménytípus és a tulajdonos szűrésének magyarázata Data Factory felhasználói felületen.":::

1. Az egyéni esemény eseményindítója elemezheti és elküldheti a folyamatra _vonatkozó egyéni adattartalmat._ Először hozza létre a folyamat paramétereit, majd adja meg az értékeket a **Paraméterek** lapon. Használja a Format **@triggerBody (). Event._Form formátumot._** az adattartalom elemzéséhez és a folyamat paramétereinek átadásához adja meg a kulcsnév értékét. Részletes magyarázatot a következő témakörben talál: az [eseményindító metaadatainak referenciái a folyamatokban](how-to-use-trigger-parameterization.md) és [a rendszerváltozókban az egyéni esemény eseményindítójában](control-flow-system-variables.md#custom-event-trigger-scope)

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Képernyőkép a folyamat paramétereinek beállításáról.":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Képernyőkép a parameters (paraméterek) oldalról, amely az egyéni eseményekben található adattartalomra hivatkozik.":::

1. Ha elkészült, kattintson **az OK gombra** .

## <a name="json-schema"></a>JSON-séma

Az alábbi táblázat áttekintést nyújt az egyéni esemény-eseményindítókkal kapcsolatos séma-elemekről:

| **JSON-elem** | **Leírás** | **Típus** | **Megengedett értékek** | **Kötelező** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **hatókör** | Az Event Grid-témakör Azure Resource Manager erőforrás-azonosítója. | Sztring | Azure Resource Manager azonosítója | Yes |
| **események** | A triggert tüzet kiváltó események típusa. | Karakterláncok tömbje    |  | Igen, legalább egy értéket meg kell várni |
| **subjectBeginsWith** | A tulajdonos mezőnek a triggerhez megadott mintázattal kell kezdődnie. Például `factories` csak az eseményhez tartozó eseményindítót indítja el a-től kezdődően `factories` . | Sztring   | | No |
| **subjectEndsWith** | A tulajdonos mezőnek a triggerhez megadott mintázattal kell végződnie. | Sztring   | | No |

## <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés

A Azure Data Factory az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) használja annak biztosítására, hogy a jogosulatlan hozzáférés meghallgassa, előfizesse a frissítéseket, és az egyéni eseményekhez kapcsolódó folyamatokat aktiváljon, szigorúan tilos.

* Ahhoz, hogy sikeresen létrehozzon egy újat, vagy frissítenie kell egy meglévő egyéni eseményindítót, a Data Factoryba bejelentkezett Azure-fióknak megfelelő hozzáférésre van szüksége a megfelelő Storage-fiókhoz. Ellenkező esetben a művelet sikertelen lesz, mert a _hozzáférés megtagadva_.
* Data Factory nem igényel külön engedélyt a Event Gridhoz, és _nem_ kell speciális Azure RBAC engedélyt rendelnie a művelethez Data Factory egyszerű szolgáltatáshoz.

Pontosabban, az ügyfélnek szüksége van a _Microsoft. EventGrid/EventSubscriptions/Write_ engedélyre a _/Subscriptions/# # # #/resourceGroups//# # # #/Providers/Microsoft.EventGrid/topics/someTopics_

## <a name="next-steps"></a>Következő lépések

* Az eseményindítókkal kapcsolatos részletes információkért lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#trigger-execution).
* Megtudhatja, hogyan hivatkozhat az trigger-metaadatok a folyamat során: [hivatkozás trigger metaadatainak a folyamat futtatása során](how-to-use-trigger-parameterization.md)
