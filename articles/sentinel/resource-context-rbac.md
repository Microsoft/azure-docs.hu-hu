---
title: Az Azure Sentinel-adatforrásokhoz való hozzáférés kezelése erőforrás alapján | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan kezelheti az Azure Sentinel-információkhoz való hozzáférést a felhasználó által elérhető erőforrások alapján. Az erőforrásokhoz való hozzáférés kezelése lehetővé teszi, hogy csak a teljes Azure Sentinel-élmény nélkül biztosítson hozzáférést a megadott adatforrásokhoz. Ezt a metódust erőforrás-környezet RBAC is nevezik.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: 26124f8f650e1006244b4871e26962d417d90fd4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054822"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>Az Azure Sentinel-adatforrásokhoz való hozzáférés kezelése

Az Azure Sentinel-munkaterületekhez hozzáférő felhasználók általában hozzáférhetnek az összes munkaterület-információhoz, beleértve a biztonsági tartalmakat is. A rendszergazdák az [Azure-szerepkörök](roles.md) használatával konfigurálhatják az Azure Sentinel adott funkcióinak hozzáférését a csapatuk hozzáférési követelményeitől függően.

Előfordulhat azonban, hogy vannak olyan felhasználók, akiknek csak bizonyos adatait kell elérniük az Azure Sentinel-munkaterületen, de nem férnek hozzá a teljes Azure Sentinel-környezethez. Előfordulhat például, hogy meg szeretné adni a nem biztonsági műveletek (nem SOC) csoport számára a Windows-események azon kiszolgálóihoz való hozzáférését, amelyeknek a tulajdonosai.

Ilyen esetekben azt javasoljuk, hogy a szerepköralapú hozzáférés-vezérlést (RBAC) a felhasználók számára engedélyezett erőforrások alapján konfigurálja, ahelyett, hogy hozzáférést kellene biztosítani számukra az Azure Sentinel-munkaterülethez vagy az adott Azure Sentinel-funkciókhoz. Ezt a metódust az **erőforrás-környezet RBAC** beállításának is nevezzük.

Ha a felhasználók az Azure Sentinel munkaterület helyett az általa elérhető erőforrásokon keresztül férnek hozzá az Azure Sentinel-adataihoz, a következő módszerekkel tekinthetik meg a naplókat és a munkafüzeteket:

- **Maga az erőforráson**, például egy Azure-beli virtuális gépen keresztül. Ezzel a módszerrel egy adott erőforráshoz tartozó naplókat és munkafüzeteket tekinthet meg.

- **Azure monitoron keresztül**. Ezt a módszert akkor használja, ha több erőforrást és/vagy erőforráscsoportot érintő lekérdezéseket szeretne létrehozni. Amikor Azure Monitorban lévő naplókra és munkafüzetekre navigál, definiálja a hatókört egy vagy több konkrét erőforráscsoport vagy erőforrás számára.

Az erőforrás-környezet RBAC engedélyezése a Azure Monitorban. További információ: [a hozzáférés kezelése a naplózási adatokhoz és munkaterületekhez Azure monitor](/azure/azure-monitor/logs/manage-access).

> [!NOTE]
> Ha az adatok nem Azure-erőforrások, például syslog-, CEF-vagy HRE-adatok, vagy egy egyéni gyűjtő által összegyűjtött adatok, manuálisan kell konfigurálnia az adatok azonosításához és a hozzáférés engedélyezéséhez használt erőforrás-azonosítót.
>
> További információ: [explicit módon konfigurálja az erőforrás-környezet RBAC](#explicitly-configure-resource-context-rbac).
>
## <a name="scenarios-for-resource-context-rbac"></a>Az erőforrás-környezet RBAC forgatókönyvei

Az alábbi táblázat azokat a forgatókönyveket mutatja be, amelyekben az erőforrás-környezet RBAC leghasznosabb. Vegye figyelembe, hogy a SOC csapatok és a nem SOC csapatok közötti hozzáférési követelmények eltérőek.

| Követelmény típusa |SOC-csapat  |Nem SOC csapat  |
|---------|---------|---------|
|**Engedélyek**     | A teljes munkaterület        |   Csak meghatározott erőforrások      |
|**Adathozzáférés**     |  A munkaterület összes adathalmaza       | Csak olyan erőforrásokhoz tartozó információk, amelyekhez a csapat jogosult        |
|**Élmény**     |  A teljes Azure Sentinel-élmény, amelyet valószínűleg a felhasználóhoz rendelt [funkcionális engedélyek](roles.md) korlátoznak       |  Lekérdezések és munkafüzetek naplózása       |
|     |         |         |

Ha a csapata hasonló hozzáférési követelményekkel rendelkezik a fenti táblázatban leírt nem SOC-csoporthoz, az erőforrás-kontextus RBAC hasznos megoldás lehet a szervezet számára.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>Alternatív módszerek az erőforrás-környezet RBAC megvalósításához

A szervezete számára szükséges engedélyektől függően előfordulhat, hogy az erőforrás-környezet RBAC nem biztosítanak teljes megoldást.

Az alábbi lista azokat a forgatókönyveket ismerteti, amelyekben az adatokhoz való hozzáférés egyéb megoldásai jobban illeszkednek a igényeihez:

|Eset  |Megoldás  |
|---------|---------|
|**A leányvállalat egy SoC-csapattal rendelkezik, amelyhez teljes körű Azure Sentinel-élmény szükséges**.     |  Ebben az esetben a több munkaterületet használó architektúrával elkülönítheti az adatengedélyeit. <br><br>További információkért lásd: <br>- [Az Azure Sentinel bővítése munkaterületeken és bérlők között](extend-sentinel-across-workspaces-tenants.md)<br>    - [Incidensek használata egyszerre sok munkaterületen](multiple-workspace-view.md)          |
|**Egy adott típusú eseményhez szeretne hozzáférést biztosítani**.     |  Adjon meg például egy Windows-rendszergazdát, amely hozzáférést biztosít a Windows biztonsági eseményeihez az összes rendszerben. <br><br>Ilyen esetekben a [táblázat szintű RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) használatával határozhatja meg az egyes táblák engedélyeit.       |
| **Korlátozza a hozzáférést egy részletesebb szintre, vagy nem az erőforrás alapján, vagy egy esemény mezőinek egy részhalmazára.**   |   Előfordulhat például, hogy egy felhasználó leányvállalata alapján szeretné korlátozni az Office 365-naplók elérését. <br><br>Ebben az esetben az [Power bi irányítópultokkal és jelentésekkel](/azure/azure-monitor/platform/powerbi)való beépített integráció használatával biztosíthatja az adathozzáférést.      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>Az erőforrás-környezet RBAC explicit konfigurálása

Az alábbi lépések végrehajtásával konfigurálhatja az erőforrás-környezet RBAC, de az adatai nem Azure-erőforrások.

Például az Azure Sentinel-munkaterületen található, nem Azure-erőforrásokba tartozó adatok közé tartoznak a syslog-, CEF-vagy HRE-adatok, illetve az egyéni gyűjtő által gyűjtött adatok.

**Az erőforrás-környezet RBAC explicit konfigurálása**:

1. Győződjön meg arról, hogy [engedélyezte az erőforrás-környezet RBAC](/azure/azure-monitor/platform/manage-access) a Azure monitorban. 

1. [Hozzon létre egy erőforráscsoportot](/azure/azure-resource-manager/management/manage-resource-groups-portal) minden olyan felhasználói csapat számára, akiknek a teljes Azure Sentinel-környezet nélkül hozzá kell férniük az erőforrásokhoz.

    A csapattagok összes tagjához tartozó [log Reader-engedélyek](/azure/azure-monitor/platform/manage-access#resource-permissions) kiosztása.

1. Rendeljen erőforrásokat a létrehozott erőforráscsoport-csoportokhoz, és címkézse az eseményeket a megfelelő erőforrás-azonosítókkal.

    Ha az Azure-erőforrások adatokat küldenek az Azure Sentinelnek, a rendszer automatikusan címkézi a napló rekordjait az adatforrás erőforrás-azonosítójával.

    > [!TIP]
    > Azt javasoljuk, hogy csoportosítsa azokat az erőforrásokat, amelyekhez hozzáférést szeretne biztosítani egy adott erőforráscsoport számára, amely a célra lett létrehozva.
    >
    > Ha nem tudja, ellenőrizze, hogy a csapata rendelkezik-e közvetlenül az elérni kívánt erőforrásokhoz tartozó log Reader-engedélyekkel.
    >

    További információ az erőforrás-azonosítókkal kapcsolatban:

    - [Erőforrás-azonosítók naplózási továbbítással](#resource-ids-with-log-forwarding)
    - [Erőforrás-azonosítók Logstash-gyűjteménysel](#resource-ids-with-logstash-collection)
    - [Erőforrás-azonosítók a Log Analytics API-gyűjteménysel](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>Erőforrás-azonosítók naplózási továbbítással

Ha az eseményeket a [Common Event Format (CEF)](connect-common-event-format.md) vagy a [syslog](connect-syslog.md)használatával gyűjti össze, a rendszer naplózza a naplózást a több forrásból származó események összegyűjtéséhez.

Ha például egy CEF vagy syslog-továbbító virtuális gép figyeli a syslog-eseményeket küldő forrásokat, és továbbítja azokat az Azure Sentinelnek, a rendszer az összes továbbított eseményhez hozzárendeli a naplózási virtuális gép erőforrás-AZONOSÍTÓját.

Ha több csapattal rendelkezik, győződjön meg arról, hogy külön naplózási továbbítási virtuális gépek vannak feldolgozva az egyes különálló csoportok eseményeiről.

A virtuális gépek elválasztása például biztosítja, hogy az A csapathoz tartozó syslog-eseményeket az A gyűjtő virtuális gép használatával gyűjti a rendszer.

> [!TIP]
> - Helyszíni virtuális gép vagy más Felhőbeli virtuális gép (például AWS) használatakor a napló-továbbítóként ellenőrizze, hogy rendelkezik-e erőforrás-AZONOSÍTÓval az [Azure arc](/azure/azure-arc/servers/overview)megvalósításával.
> - A naplók továbbítására szolgáló virtuálisgép-környezet skálázásához érdemes létrehozni egy virtuálisgép- [méretezési készletet](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) , amely ÖSSZEGYŰJTI a CEF és a Sylog naplókat.


### <a name="resource-ids-with-logstash-collection"></a>Erőforrás-azonosítók Logstash-gyűjteménysel

Ha az Azure Sentinel [Logstash kimeneti beépülő modullal](connect-logstash.md)gyűjti az adatokat, a **azure_resource_id** mezőben konfigurálhatja az egyéni gyűjtőt úgy, hogy az tartalmazza az erőforrás-azonosítót a kimenetben.

Ha erőforrás-környezeti RBAC használ, és azt szeretné, hogy az API által összegyűjtött események elérhetők legyenek bizonyos felhasználók számára, használja a [felhasználók számára létrehozott](#explicitly-configure-resource-context-rbac)erőforráscsoport erőforrás-azonosítóját.

Az alábbi kód például egy minta Logstash-konfigurációs fájlt mutat be:

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> Előfordulhat, hogy több szakaszt is fel szeretne venni `output` , hogy megkülönböztesse a különböző eseményekre alkalmazott címkéket.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>Erőforrás-azonosítók a Log Analytics API-gyűjteménysel

A [log Analytics adatgyűjtő API](/azure/azure-monitor/platform/data-collector-api)-val való összegyűjtéskor a http [*x-MS-erőforrásazonosító*](/azure/azure-monitor/platform/data-collector-api#request-headers) kérelem fejlécével hozzárendelhet ESEMÉNYEKET egy erőforrás-azonosítóval.

Ha erőforrás-környezeti RBAC használ, és azt szeretné, hogy az API által összegyűjtött események elérhetők legyenek bizonyos felhasználók számára, használja a [felhasználók számára létrehozott](#explicitly-configure-resource-context-rbac)erőforráscsoport erőforrás-azonosítóját.


## <a name="next-steps"></a>Következő lépések

További információ: [engedélyek az Azure sentinelben](roles.md).
