---
title: Riasztások konfigurálása és mérőszámok használata az Azure VMware-megoldásban
description: Ismerje meg, hogyan fogadhat értesítéseket a riasztások használatával. Azt is megtudhatja, hogyan dolgozhat a mérőszámokkal, hogy mélyebb betekintést nyerjen az Azure VMware-megoldás saját felhőbe.
ms.topic: how-to
ms.date: 04/02/2021
ms.openlocfilehash: f021662658399111187e9963fc5caec434fabf4a
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096795"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Azure-riasztások konfigurálása az Azure VMware-megoldásban 

Ebből a cikkből megtudhatja, hogyan konfigurálhat [Azure-műveleti csoportokat](/azure/azure-monitor/alerts/action-groups) [Microsoft Azure riasztásokban](/azure/azure-monitor/alerts/alerts-overvie) , hogy fogadja az Ön által meghatározott aktivált események értesítéseit. Azt is megismerheti, [Azure monitor metrikák](/azure/azure-monitor/essentials/data-platform-metrics) használatával mélyebb információkhoz juthat az Azure VMware-megoldás privát felhőben.


## <a name="supported-metrics-and-activities"></a>Támogatott mérőszámok és tevékenységek

A következő metrikák láthatók Azure Monitor metrikák használatával.

| **Jel neve**                                                         | **Jel típusa** | **Szolgáltatás figyelése** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| Adattár lemezének teljes kapacitása                                           | Metric          | Platform            |
| Felhasznált adattároló-lemez százalékos aránya                                          | Metric          | Platform            |
| Százalékos processzorhasználat                                                          | Metric          | Platform            |
| Átlagos tényleges memória                                                | Metric          | Platform            |
| Átlagos memória terhelése                                                 | Metric          | Platform            |
| Teljes memória átlagos száma                                                    | Metric          | Platform            |
| Memóriahasználat átlagos kihasználtsága                                                    | Metric          | Platform            |
| Felhasznált adattár-lemez                                                     | Metric          | Platform            |
| Minden felügyeleti művelet                                           | Tevékenységnapló    | Adminisztratív      |
| Regisztrálja a Microsoft. AVS erőforrás-szolgáltatót. (Microsoft. AVS/privateClouds) | Tevékenységnapló    | Adminisztratív      |
| PrivateCloud létrehozása vagy frissítése. (Microsoft. AVS/privateClouds)          | Tevékenységnapló    | Adminisztratív      |
| PrivateCloud törlése. (Microsoft. AVS/privateClouds)                    | Tevékenységnapló    | Adminisztratív      |

## <a name="configure-an-alert-rule"></a>Riasztási szabály konfigurálása
1. Az Azure VMware-megoldás saját felhőben válassza a **figyelési**  >  **riasztások**, majd az **új riasztási szabály** lehetőséget.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Képernyőfelvétel: a riasztási szabály konfigurálásának helye az Azure VMware-megoldás saját felhőben." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   Megnyílik egy új konfigurációs képernyő, ahol A következőket végezheti el:
   - Hatókör meghatározása
   - Feltétel konfigurálása
   - A műveleti csoport beállítása
   - A riasztási szabály részleteinek megadása
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png" alt-text="A riasztási szabály létrehozása ablakot megjelenítő képernyőkép." lightbox="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png":::

1. A **hatókör** területen válassza ki a figyelni kívánt erőforrást. Alapértelmezés szerint az Azure VMware-megoldás saját felhője, ahonnan a riasztások menüt megnyitotta.

1. A **feltétel** területen válassza a **feltétel hozzáadása** lehetőséget, és a megnyíló ablakban kiválasztja a riasztási szabályhoz létrehozni kívánt jelet. 

   A példánkban a **felhasznált százalékos adattár-lemez** van kiválasztva, amely az [Azure VMware-megoldás SLA](https://aka.ms/avs/sla) -szemszögéből vonatkozik. 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="Képernyőfelvétel: a jel logikai beállítása ablak előre definiált jelek nevével."::: 

1. Adja meg azt a logikát, amely elindítja a riasztást, majd válassza a **kész** lehetőséget. 

   A példánkban csak a **küszöbérték** és a **kiértékelés gyakorisága** lett módosítva. 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="Képernyőkép, amely a kiválasztott jel logikájának információit jeleníti meg."::: 

1. A **műveletek** területen válassza a **műveleti csoportok hozzáadása** elemet. A műveleti csoport határozza meg az *értesítés fogadásának és* fogadásának *módját* .   Értesítéseket kaphat e-mailben, SMS-ben, [Azure Mobile App leküldéses értesítésben](https://azure.microsoft.com/features/azure-portal/mobile-app/) vagy hangüzenetben.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group.png" alt-text="Képernyőkép, amely megjeleníti a meglévő műveleti csoportokat, és hogy hol hozzon létre egy új műveleti csoportot.":::

1. A megnyíló ablakban válassza a **műveleti csoport létrehozása** lehetőséget.

   >[!TIP]
   > Meglévő műveleti csoportot is használhat.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/select-action-group-for-alert-rule.png" alt-text="A riasztáshoz kiválasztani kívánt műveleti csoportokat bemutató képernyőkép."::: 

 

 
1. A megnyíló ablakban az **alapok** lapon adja meg a műveleti csoport nevét és a megjelenítendő nevet.

1. Válassza ki az **értesítések** lapot, és válassza ki az **értesítés típusát** és **nevét**. Ez után válassza az **OK** gombot.

   A példánkban az e-mailes értesítésen alapul.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png" alt-text="A riasztáshoz tartozó e-mailek, SMS-üzenetek, leküldéses és hangbeállításokat bemutató képernyőkép." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png":::     

1. Választható Konfigurálja a **műveleteket** , ha proaktív műveleteket kíván végrehajtani, és értesítést kap az eseményről. Válasszon ki egy elérhető **Művelettípus-típust** , majd válassza a **felülvizsgálat + létrehozás** elemet. 
   - Automatizálási forgatókönyvek
   - Azure Functions – egyéni eseményvezérelt kiszolgáló nélküli kódfuttatást tesz lehetővé
   - ITSM – integrálható egy olyan szolgáltatóval, mint a ServiceNow egy jegy létrehozásához
   - Logikai alkalmazás – összetettebb munkafolyamat-előkészítés
   - Webhookok – egy folyamat elindítása egy másik szolgáltatásban

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png" alt-text="Képernyőfelvétel: a műveleti csoport létrehozása ablak, amelynek fókusza a művelet típusa legördülő menü." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png":::     

1. A **riasztási szabály részletei** területen adjon meg egy nevet, egy leírást, egy erőforráscsoportot a riasztási szabály tárolására, a súlyosságot. Ezután válassza a **riasztási szabály létrehozása** lehetőséget.
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-rule-details.png" alt-text="A riasztási szabály részleteit megjelenítő képernyőkép."::: 
 
   A riasztási szabály látható, és a Azure Portal felügyelhető.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="Képernyőfelvétel: az új riasztási szabály megjelenítése a szabályok ablakban." lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::     

   Amint egy metrika eléri a riasztási szabályban meghatározott küszöbértéket, a **riasztások** menü frissül, és láthatóvá válik.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="Képernyőkép, amely a megadott küszöbérték elérése után megjeleníti a riasztást." lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   A konfigurált műveleti csoporttól függően a konfigurált médiumon keresztül kap értesítést. A példánkban konfiguráltunk egy e-mailt.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="Képernyőkép egy Azure Monitor riasztásról a hibaüzenettel, valamint a dátum-és időesemény kiváltása."::: 

## <a name="work-with-metrics"></a>Mérőszámok használata

1. Az Azure VMware-megoldás saját felhőben válassza a **figyelés**  >  **mérőszámok** lehetőséget. Ezután válassza ki a kívánt mérőszámot a legördülő listából.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="A mérőszámok ablakát és a metrika legördülő menüre mutató fókuszt megjelenítő képernyőkép." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. Módosíthatja a diagram paramétereit, például az **időtartományt** vagy az **idő részletességét**. 

   További lehetőségek:
   - **Naplókba való részletezés** és a kapcsolódó log Analytics munkaterület adatainak lekérdezése
   - **A diagramot** egy Azure-irányítópultra rögzítheti a kényelem érdekében.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="A metrika időtartományát és időrészletességi beállításait bemutató képernyőkép." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>Következő lépések

Most, hogy beállított egy riasztási szabályt az Azure VMware-megoldás privát felhőhöz, érdemes még többet megtudni a következőről:
- [Azure Monitor-metrikák](/azure/azure-monitor/essentials/data-platform-metrics)
- [Azure Monitor-riasztások](/azure/azure-monitor/alerts/alerts-overview)
- [Azure-műveleti csoportok](/azure/azure-monitor/alerts/action-groups)

Folytathatja a többi [Azure VMware-megoldás](index.yml) útmutatóinak egyikét is.





