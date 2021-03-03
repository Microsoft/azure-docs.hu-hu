---
title: Állásidő, SLA és kimaradási munkafüzet – Application Insights
description: Kiszámíthatja és bejelentheti az SLA-t a webes tesztek számára egyetlen üvegtábla használatával a Application Insights-erőforrások és az Azure-előfizetések között.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714051"
---
# <a name="downtime-sla-and-outages-workbook"></a>Állásidő, SLA és kimaradási munkafüzet

Bemutatjuk egy egyszerű módszert az SLA (szolgáltatói szerződés) kiszámítására és jelentésére a webes tesztek számára a Application Insights-erőforrások és az Azure-előfizetések egyetlen ablaktábláján keresztül. Az állásidő és a leállási jelentés hatékony, előre elkészített lekérdezéseket és adatvizualizációkat biztosít az ügyfelek kapcsolatának, a tipikus alkalmazások válaszideje és a leállási idő megismerése érdekében.

Az SLA-munkafüzet sablonja elérhető a Application Insights-erőforrás munkafüzet-katalógusában vagy a rendelkezésre állás lapon a felső **SLA-jelentések** kiválasztásával.
:::image type="content" source="./media/sla-report/availability.png" alt-text="Képernyőkép a rendelkezésre állás lapról a Kiemelt SLA-jelentésekkel." lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="Képernyőfelvétel a munkafüzet-katalógusról a leállás és kimaradások munkafüzet kiemelésével." lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>Paraméter rugalmassága

A munkafüzetben beállított paraméterek befolyásolják a jelentés hátralévő részét.

:::image type="content" source="./media/sla-report/outages.png" alt-text=" Képernyőfelvétel a leállás/karbantartás paraméterek lapról az állásidő és kimaradások munkafüzetben." lightbox ="./media/sla-report/outages.png":::

`Subscriptions`, `App Insights Resources` és `Web Test` paraméterek határozzák meg a magas szintű erőforrás-beállításokat. Ezek a paraméterek a log Analytics-lekérdezéseken alapulnak, és minden jelentés lekérdezésében szerepelnek.

`Failure Threshold` és `Outage Window` lehetővé teszi, hogy meghatározza saját feltételeit a szolgáltatás leállására vonatkozóan, például az alkalmazás-megállapítások feltételeit a sikertelen hely számlálója alapján kiválasztott időszakban. A tipikus küszöbérték egy öt perces időszakon belül három helyszín.

`Maintenance Period` lehetővé teszi a tipikus karbantartási gyakoriság kiválasztását, és `Maintenance Window` egy datetime választót egy példa karbantartási időszakra. Az eredményekben a rendszer figyelmen kívül hagyja az azonosított időszak során megjelenő összes adatmennyiséget.

`Availability Target 9s` Megadja a cél 9-es-célkitűzést két 9-es és öt 9-es között.

## <a name="overview-page"></a>Áttekintő oldal

Az Áttekintés oldalon magas szintű információk találhatók a teljes SLA-ról (kivéve a karbantartási időszakokat, ha vannak ilyenek), a végpontok és a végpontok leállási példányai, valamint az alkalmazások állásidője. A kimaradási példányok meghatározása akkor történik meg, ha egy teszt nem sikerül, amíg a leállási paraméterek alapján nem sikerül. Ha a teszt a 8:00-kor meghiúsul, és a következő időpontban sikerül újra működnie: 10:00 órakor, akkor a teljes adatmennyiség ugyanazt a kimaradást veszi figyelembe.

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" A GIF of Overview (áttekintés) oldal, amely az áttekintő táblázatot mutatja teszt alapján." lightbox="./media/sla-report/overview.gif":::

Azt is megvizsgálhatja, hogy a leghosszabb leállás a jelentési időszak során történt-e.

Bizonyos tesztek visszalinkable a Application Insights erőforrásra a további vizsgálathoz, de ez csak a [munkaterület-alapú Application Insights erőforrásban](create-workspace-resource.md)lehetséges.

## <a name="downtime-outages-and-failures"></a>Leállás, kimaradások és hibák

Az kimaradások **és a leállás** lapon a teszt által lebontott összes leállási példányra és a teljes leállási időre vonatkozó információk szerepelnek. A **meghibásodások helye** lapon a sikertelen tesztelési helyszínek földrajzi térképe a lehetséges problémás kapcsolódási területek azonosítása érdekében.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" A leállás és állásidő lap és a hiba helye lapon lévő, leállási és leállási munkafüzetekből származó GIF." lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>A jelentés szerkesztése

A jelentést szerkesztheti úgy, mint bármely más [Azure monitor munkafüzetet](../visualize/workbooks-overview.md). A csoportok igényei alapján testreszabhatja a lekérdezéseket és a vizualizációkat.

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" A Szerkesztés gombra kattintva megváltoztathatja a vizualizációt egy tortadiagramon." lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

A lekérdezések [log Analytics](../logs/log-analytics-overview.md) futnak, és más jelentésekben vagy irányítópultokon is használhatók. Távolítsa el a paraméter korlátozását, és használja újra az alapvető lekérdezést.

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" A napló lekérdezésének GIF-je." lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>Hozzáférés és megosztás

A jelentés megosztható a csapatokkal, a vezetőséggel, vagy az irányítópultra rögzítve további használatra. A felhasználónak olvasási engedéllyel kell rendelkeznie, vagy hozzá kell férnie az Applications-adatforráshoz, ahol a tényleges munkafüzetet tárolja.

:::image type="content" source="./media/sla-report/share.png" alt-text=" Képernyőkép a sablon megosztásáról." lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>Következő lépések

- [Log Analytics lekérdezés-optimalizálási tippek](../logs/query-optimization.md).
- Megtudhatja, hogyan [hozhat létre diagramot a munkafüzetekben](../visualize/workbooks-chart-visualizations.md).
- Ismerje meg, hogyan figyelheti meg webhelyét a [rendelkezésre állási tesztekkel](monitor-web-app-availability.md).