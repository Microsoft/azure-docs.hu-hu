---
title: Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor
description: Hozzon létre műveletnapló-riasztásokat a Azure Portal, egy Azure Resource Manager sablon és a Azure PowerShell használatával.
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 26ca755f6675fa19c3b122c3528e05d1e8d76845
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045530"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával  

## <a name="overview"></a>Áttekintés

A műveletnapló riasztásai olyan riasztások, amelyek akkor jelentkeznek be, amikor egy új műveletnapló esemény következik be, amely megfelel a riasztásban megadott feltételeknek.

Ezek a riasztások az Azure-erőforrásokhoz használhatók, és Azure Resource Manager sablon használatával hozhatók létre. Emellett a Azure Portal is létrehozhatók, frissíthetők és törölhetők. Az Azure-előfizetésben lévő erőforrásokra vonatkozó bizonyos változások esetén a rendszer általában a tevékenység naplójának riasztásait hozza létre az értesítések fogadásához. A riasztásokat gyakran meghatározott erőforráscsoportok vagy erőforrások hatóköre képezi. Előfordulhat például, hogy értesítést szeretne kapni, ha a minta erőforráscsoport **myProductionResourceGroup** található bármely virtuális gép törlődik. Vagy előfordulhat, hogy értesítést szeretne kapni, ha minden új szerepkör hozzá van rendelve egy felhasználóhoz az előfizetésében.

> [!IMPORTANT]
> A szolgáltatás állapotáról szóló értesítés nem hozható létre az illesztőfelületen keresztül a tevékenység naplójának riasztásának létrehozásához. Ha többet szeretne megtudni a szolgáltatás állapotával kapcsolatos értesítések létrehozásáról és használatáról, tekintse meg a következő témakört: [fogadási tevékenység naplójának riasztásai a szolgáltatás állapotáról](../../service-health/alerts-activity-log-service-notifications-portal.md)

Riasztási szabályok létrehozásakor ügyeljen a következőkre:

- A hatókörbeli előfizetés nem különbözik a riasztást létrehozó előfizetéstől.
- A feltételnek az a szint, állapot, hívó, Erőforráscsoport, erőforrás-azonosító vagy erőforrástípus típusú eseménynek kell lennie, amelyen a riasztás konfigurálva van.
- Csak egy "allOf" feltétel engedélyezett.
- A "AnyOf" több feltételt is lehetővé tesz több mező esetében (például ha az "állapot" vagy az "alállapot" mező értéke egy bizonyos érték). Vegye figyelembe, hogy a "AnyOf" használata jelenleg csak a riasztási szabály létrehozásához használható ARM-sablonok használatával.
- A "ContainsAny" lehetővé teszi ugyanazon mező több értékének engedélyezését (például ha a "művelet" egyenlő a "Delete" vagy a "Modify" kifejezéssel). Vegye figyelembe, hogy a "ContainsAny" használata jelenleg csak a riasztási szabály létrehozásához használható ARM-sablonok használatával.
- Ha a kategória "adminisztratív", akkor a riasztásban meg kell adnia legalább az előző feltétel egyikét. Előfordulhat, hogy nem hoz létre olyan riasztást, amely minden alkalommal aktiválódik, amikor létrejön egy esemény a tevékenység naplóiban.
- Nem hozhatók létre riasztások a tevékenység naplójának riasztási kategóriájában lévő eseményekhez.

## <a name="azure-portal"></a>Azure Portal

A Azure Portal a műveletnapló riasztási szabályainak létrehozására és módosítására használható. A felhasználói élmény az Azure-tevékenység naplójában van integrálva, így biztosítva a zökkenőmentes riasztások létrehozását az adott események esetében.

### <a name="create-with-the-azure-portal"></a>Létrehozás a Azure Portal

Használja az alábbi eljárást.

1. A Azure Portal válassza a   >  **riasztások** figyelése lehetőséget.
2. A **riasztások** ablak bal felső sarkában válassza az **új riasztási szabály** lehetőséget.

     ![Új riasztási szabály](media/alerts-activity-log/AlertsPreviewOption.png)

     Megjelenik a **szabály létrehozása** ablak.

      ![Új riasztási szabály beállításai](media/alerts-activity-log/create-new-alert-rule-options.png)

3. A **riasztási feltétel meghatározása** területen adja meg a következő információkat, majd válassza a **kész** lehetőséget:

   - **Riasztási cél:** Az új riasztás céljának megtekintéséhez és kiválasztásához használja az **előfizetés szerinti** szűrés az  /  **erőforrástípus alapján értéket**. Válassza ki az erőforrást vagy erőforráscsoportot a megjelenő listából.

     > [!NOTE]
     > 
     > Csak [Azure Resource Manager](../../azure-resource-manager/management/overview.md) követett erőforrást, erőforráscsoportot vagy teljes előfizetést választhat a tevékenység naplójának jelzéséhez. 

     **Riasztási cél minta nézete**

     ![Cél kiválasztása](media/alerts-activity-log/select-target.png)

   - A **célként megadott feltételek** területen válassza a **feltétel hozzáadása** lehetőséget. Megjelenik a cél összes elérhető jele, amely magában foglalja a különböző kategóriájú **tevékenységek naplóit**. A rendszer hozzáfűzi a kategória nevét a **figyelő szolgáltatás** nevéhez.

   - Válassza ki a típus **tevékenység naplója** számára lehetséges különböző műveletekből megjelenített jelet.

     A naplózási előzmények idővonalát és a hozzá tartozó riasztási logikát kiválaszthatja a cél jelzéshez:

     **Feltételek hozzáadása képernyő**

     ![Feltételek hozzáadása](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  Ahhoz, hogy magas színvonalú és hatékony szabályok legyenek, legalább egy feltételt fel kell vennie az "összes rendszergazda" jellel való szabályokra. 
     > A riasztás definíciójának részeként ki kell töltenie a legördülő lista egyikét: "Event level", "status" vagy "kezdeményező", valamint azzal, hogy a szabály konkrétabb lesz.

     - **Előzmények időpontja**: a kiválasztott művelethez rendelkezésre álló események az elmúlt 6, 12 vagy 24 órában, vagy az elmúlt héten láthatók.

     - **Riasztási logika**:

       - **Esemény szintje**: az esemény súlyossági szintje: _részletes_, _tájékoztató_, _Figyelmeztetés_, _hiba_ vagy _kritikus_.
       - **Állapot**: az esemény állapota: _elindítva_, _sikertelen_, vagy _sikeres_.
       - A (z) **által kezdeményezett esemény**, más néven a hívó. A műveletet végrehajtó felhasználó e-mail-címe vagy Azure Active Directory azonosítója.

       Ez a mintavételi gráf a riasztási logikát alkalmazza:

       ![Kijelölt feltételek](media/alerts-activity-log/criteria-selected.png)

4. A **riasztás részleteinek megadása** területen adja meg a következő adatokat:

    - **Riasztási szabály neve**: az új riasztási szabály neve.
    - **Leírás**: az új riasztási szabály leírása.
    - **Riasztás mentése az erőforráscsoporthoz**: válassza ki azt az erőforráscsoportot, amelybe menteni szeretné ezt az új szabályt.

5. A **Művelettípus** alatt, a legördülő menüből válassza ki azt a műveleti csoportot, amelyet hozzá szeretne rendelni ehhez az új riasztási szabályhoz. Vagy [hozzon létre egy új műveleti csoportot](./action-groups.md) , és rendelje hozzá az új szabályhoz. Új csoport létrehozásához válassza az **+ új csoport** lehetőséget.

6. Ha a létrehozás után engedélyezni szeretné a szabályokat, válassza az **Igen** lehetőséget a **szabály engedélyezése a létrehozáskor** beállításnál.
7. Válassza a **Riasztási szabály létrehozása** lehetőséget.

    A rendszer létrehozza a műveletnapló új riasztási szabályát, és egy megerősítő üzenet jelenik meg az ablak jobb felső sarkában.

    Engedélyezheti, letilthatja, szerkesztheti vagy törölheti a szabályokat. További információ a műveletnapló szabályainak kezeléséről.


A tevékenység naplójában a riasztási szabályok létrehozására szolgáló feltételek értelmezése egyszerű analóg, ha az eseményeket a [Azure Portal tevékenységi naplójából](../essentials/activity-log.md#view-the-activity-log)vizsgálja vagy szűri. A **Azure monitor-Activity napló** képernyőn szűrheti vagy megkeresheti a szükséges eseményt, majd létrehozhatja a riasztást a **műveletnapló riasztás hozzáadása** gomb használatával. Ezután kövesse a 4 – 7. lépést a korábban bemutatott módon.
    
 ![Riasztás hozzáadása a tevékenység naplójából](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Megtekintés és kezelés a Azure Portal

1. A Azure Portal válassza a   >  **riasztások** figyelése lehetőséget. Az ablak bal felső sarkában válassza a **riasztási szabályok kezelése** lehetőséget.

    ![A képernyőfelvétel megjeleníti a tevékenység naplóját a Kiemelt keresési mezővel.](media/alerts-activity-log/manage-alert-rules.png)

    Megjelenik az elérhető szabályok listája.

2. Keresse meg a módosítandó tevékenység-naplózási szabályt.

    ![Keresési tevékenység naplójának riasztási szabályai](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    A szerkeszteni kívánt tevékenységi szabály megkereséséhez használhatja az elérhető szűrőket, _előfizetést_, _erőforráscsoportot_,  _erőforrást_, _jel típusát_ vagy _állapotát_.

   > [!NOTE]
   > 
   > Csak a **Leírás**, a **célként megadott feltételek** és a **műveleti csoportok** szerkeszthetők.

3. Válassza ki a szabályt, majd kattintson duplán a szabály beállításainak szerkesztéséhez. Végezze el a szükséges módosításokat, majd kattintson a **Mentés** gombra.

   ![Riasztási szabályok kezelése](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Engedélyezheti, letilthatja vagy törölheti a szabályokat. Válassza ki a megfelelő lehetőséget az ablak tetején, miután kiválasztotta a szabályt a 2. lépésben leírtak szerint.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
A műveletnapló riasztási szabályának Azure Resource Manager sablon használatával történő létrehozásához létre kell hoznia egy típusú erőforrást `microsoft.insights/activityLogAlerts` . Ezután töltse ki az összes kapcsolódó tulajdonságot. Az alábbi sablon egy műveletnapló riasztási szabályt hoz létre:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Az előző minta JSON menthető, például a sampleActivityLogAlert.json, az útmutató céljára, és [a Azure Portal Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md)használatával is telepíthető.

  > [!NOTE]
  > 
  > Figyelje meg, hogy a legmagasabb szintű tevékenységek naplójának riasztásai megadhatók előfizetés.
  > A néhány előfizetésre vonatkozóan nincs lehetőség a riasztás meghatározására, ezért a definíciónak az előfizetéshez tartozó riasztást kell tartalmaznia.

A feltételek mezőkhöz a Azure Resource Manager sablonban használható beállítások a következők: figyelje meg, hogy a "Resource Health", az "Advisor" és a "Service Health" mező további tulajdonságokkal rendelkezik a speciális mezőkhöz. 
1. resourceId: a tevékenység naplójának érintett erőforrásának erőforrás-azonosítója, amelyet a riasztásnak generálni kell.
2. Kategória: a tevékenység napló eseményének kategóriája. Például: adminisztráció, ServiceHealth, ResourceHealth, autoscale, Security, javaslat, szabályzat.
3. hívó: annak a felhasználónak az e-mail-címe vagy Azure Active Directory azonosítója, aki végrehajtotta a tevékenység napló eseményének működését.
4. szint: a tevékenység naplózási eseményének azon szintje, amelyben a riasztást létre kell állítani. Például: kritikus, hiba, figyelmeztetés, tájékoztatás, részletes.
5. operationName: a művelet neve a tevékenység napló eseményében. Például: Microsoft. erőforrások/központi telepítés/írás
6. resourceGroup: az érintett erőforráshoz tartozó erőforráscsoport neve a tevékenység naplójának eseményében.
7. resourceProvider: [Azure erőforrás-szolgáltatók és típusok magyarázata](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Az erőforrás-szolgáltatókat az Azure-szolgáltatásokhoz leképező listán tekintse meg az [Azure-szolgáltatások erőforrás-szolgáltatóit](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)ismertető témakört.
8. Status: a tevékenység eseményében szereplő művelet állapotát leíró karakterlánc. Például: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva
9. Részállapot: általában a megfelelő REST-hívás HTTP-állapotkód, de tartalmazhat más, alállapotot leíró karakterláncokat is.   Például: OK (HTTP-állapotkód: 200), létrehozva (HTTP-állapotkód: 201), elfogadva (HTTP-állapotkód: 202), nincs tartalom (HTTP-állapotkód: 204), hibás kérés (HTTP-állapotkód: 400), nem található (HTTP-állapotkód: 404), ütközés (HTTP-állapotkód: 409), belső kiszolgálóhiba (HTTP-állapotkód: 500), a szolgáltatás nem érhető el (HTTP-állapotkód: 503), átjáró időkorlátja (http-állapotkód: 504).
10. resourceType: az esemény által érintett erőforrás típusa. Például: Microsoft. Resources/üzemelő példányok

Például:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
További részletek a tevékenység naplójának mezőiről [itt](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)talál további információt.



> [!NOTE]
> Akár 5 percet is igénybe vehet, amíg az új tevékenység naplójának riasztási szabálya aktívvá válik.

## <a name="rest-api"></a>REST API 
A [Azure monitor Activity log-riasztások API](/rest/api/monitor/activitylogalerts) egy REST API. A Azure Resource Manager REST API teljes mértékben kompatibilis. A PowerShell használatával a Resource Manager-parancsmaggal vagy az Azure CLI-vel is használható.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>A Resource Manager-sablon üzembe helyezése a PowerShell-lel
Ha a PowerShell használatával szeretné telepíteni az előző [Azure Resource Manager sablonban](#azure-resource-manager-template) látható Resource Manager-sablont, használja a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

ahol a sampleActivityLogAlert.parameters.jsa riasztási szabály létrehozásához szükséges paraméterekhez megadott értékeket tartalmazza.

### <a name="use-activity-log-powershell-cmdlets"></a>A log PowerShell-parancsmagok használata

A műveletnapló riasztásai dedikált PowerShell-parancsmagokat is elérhetők:

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert): új műveletnapló riasztást hoz létre, vagy egy meglévő műveletnapló riasztását frissíti.
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert): egy vagy több műveletnapló riasztási erőforrásának beolvasása.
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert): lehetővé teszi egy meglévő műveletnapló riasztását, és beállítja annak címkéit.
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert): letiltja a meglévő műveletnapló riasztását, és beállítja annak címkéit.
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert): eltávolítja a tevékenység naplójának riasztását.

## <a name="azure-cli"></a>Azure CLI

A set az [monitor Activity-log riasztás](/cli/azure/monitor/activity-log/alert) alatt DEDIKÁLT Azure CLI-parancsok érhetők el a műveletnapló riasztási szabályainak kezeléséhez.

Új műveletnapló riasztási szabály létrehozásához használja a következő parancsokat ebben a sorrendben:

1. [az monitor Activity-log riasztás létrehozása](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): hozzon létre egy új műveletnapló riasztási szabály erőforrást.
1. az [monitor Activity-log riasztás hatóköre](/cli/azure/monitor/activity-log/alert/scope): a létrehozott műveletnapló riasztási szabály hatókörének hozzáadása.
1. az [monitor Activity-log riasztás műveleti-csoport](/cli/azure/monitor/activity-log/alert/action-group): műveleti csoport hozzáadása a műveletnapló riasztási szabályához.

Az egyik műveletnapló riasztási szabály erőforrásának lekéréséhez használja az Azure CLI-parancsot az [monitor Activity-log Alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)paranccsal. Ha meg szeretné tekinteni az összes műveletnapló riasztási szabályának erőforrását egy erőforráscsoporthoz, használja az [az monitor Activity-log riasztások listáját](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
A műveletnapló riasztási szabályának erőforrásai eltávolíthatók az Azure CLI-paranccsal az [monitor Activity-log riasztás törlése](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)lehetőséggel.

## <a name="next-steps"></a>Következő lépések

- További információ a [tevékenységi naplók webhook-sémájáról](./activity-log-alerts-webhook.md).
- Olvassa el [a Tevékenységnaplók áttekintését](./activity-log-alerts.md).
- További információ a [műveleti csoportokról](./action-groups.md).  
- Tudnivalók a [szolgáltatás állapotával kapcsolatos értesítésekről](../../service-health/service-notifications.md).
