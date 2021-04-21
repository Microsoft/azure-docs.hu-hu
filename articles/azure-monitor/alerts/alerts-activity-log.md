---
title: Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése a Azure Monitor
description: Hozzon létre tevékenységnapló-riasztásokat a Azure Portal, Azure Resource Manager sablonnal és Azure PowerShell.
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 647378d7e93ab383441b363315a84cea8a5ab773
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772540"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése a Azure Monitor  

## <a name="overview"></a>Áttekintés

A tevékenységnapló-riasztások azok a riasztások, amelyek akkor aktiválódnak, ha új tevékenységnapló-esemény következik be, amely megfelel a riasztásban megadott feltételeknek.

Ezek a riasztások Azure-erőforrásokhoz használhatók, és egy új sablonnal Azure Resource Manager létre. Emellett a következő adatokat is lehet létrehozni, frissíteni vagy törölni a Azure Portal. Általában tevékenységnapló-riasztásokat hoz létre, amelyek értesítéseket fogadnak, ha bizonyos változások lépnek fel az Azure-előfizetés erőforrásaiban. A riasztások hatóköre gyakran adott erőforráscsoportokra vagy erőforrásokra terjed ki. Előfordulhat például, hogy értesítést szeretne arról, ha a **minta erőforráscsoportban lévő myProductionResourceGroup** virtuális gép törlődik. Az is előfordulhat, hogy értesítést szeretne kapni, ha új szerepkörök vannak hozzárendelve egy felhasználóhoz az előfizetésben.

> [!IMPORTANT]
> A szolgáltatás állapotértesítésére vonatkozó riasztások nem létrehozására a tevékenységnapló-riasztások létrehozására vonatkozó interfészen keresztül lehet. A szolgáltatás állapotértesítések létrehozásáról és használatával kapcsolatos további információkért lásd: Tevékenységnapló-riasztások fogadása a szolgáltatás [állapotértesítéseiről.](../../service-health/alerts-activity-log-service-notifications-portal.md)

Riasztási szabályok létrehozásakor győződjön meg a következőkről:

- A hatókörben az előfizetés nem különbözik attól az előfizetéstől, amelyben a riasztást létrehozták.
- A feltételeknek annak a szintnek, állapotnak, hívónak, erőforráscsoportnak, erőforrás-azonosítónak vagy erőforrástípus-eseménykategóriának kell lennie, amelyen a riasztás konfigurálva van.
- Csak egy "allOf" feltétel engedélyezett.
- Az "AnyOf" több feltétel több mezőre is alkalmazható (például ha az "állapot" vagy a "subStatus" mező egy adott értékkel egyenlő). Vegye figyelembe, hogy az "AnyOf" használata jelenleg a riasztási szabály ARM-sablonnal való üzembe helyezéssel való létrehozására korlátozódik.
- A "ContainsAny" használható ugyanannak a mezőnek több értékére is (például ha a "művelet" értéke "delete" vagy "modify"). Vegye figyelembe, hogy a "ContainsAny" használata jelenleg a riasztási szabály ARM-sablon üzembe helyezéssel való létrehozására korlátozódik.
- Ha a kategória "rendszergazda", meg kell adnia a fenti feltételek legalább valamelyikét a riasztásban. Nem hozhat létre olyan riasztást, amely minden alkalommal aktiválódik, amikor létrejön egy esemény a tevékenységnaplókban.
- A Tevékenységnapló Riasztás kategóriájába tartozó eseményekhez nem lehet riasztásokat létrehozni.

## <a name="azure-portal"></a>Azure Portal

A tevékenységnapló-riasztási Azure Portal létrehozására és módosítására használhatja a szabályzatot. A felület integrálva van egy Azure-tevékenységnaplóval, így zökkenőmentes riasztás-létrehozást biztosít adott eseményekhez.

### <a name="create-with-the-azure-portal"></a>Létrehozás a Azure Portal

Az alábbi eljárást kell követnie.

1. A Azure Portal a   >  **Riasztások figyelése lehetőséget.**
2. Válassza **az Új riasztási** szabály lehetőséget a Riasztások ablak bal **felső sarkában.**

     ![Új riasztási szabály](media/alerts-activity-log/AlertsPreviewOption.png)

     Megjelenik **a Szabály** létrehozása ablak.

      ![Új riasztási szabályok beállításai](media/alerts-activity-log/create-new-alert-rule-options.png)

3. A **Riasztási feltétel meghatározása alatt** adja meg a következő adatokat, majd válassza a Kész **lehetőséget:**

   - **Riasztási cél:** Az új riasztás célértékének megtekintéséhez és kiválasztásához használja a **Szűrés** előfizetés szerint  /  **Szűrés erőforrástípus szerint lehetőséget.** Válassza ki az erőforrást vagy erőforráscsoportot a megjelenő listából.

     > [!NOTE]
     > 
     > Egy tevékenységnapló [jeléhez Azure Resource Manager](../../azure-resource-manager/management/overview.md) erőforrást, erőforráscsoportot vagy egy teljes előfizetést választhat ki. 

     **Riasztási cél mintanézete**

     ![Cél kiválasztása](media/alerts-activity-log/select-target.png)

   - A **Cél feltételei alatt** válassza a Feltételek hozzáadása **lehetőséget.** Megjelenik a célhoz elérhető összes jel, beleértve a különböző **tevékenységnapló-kategóriákból származó jeleket is.** A kategória neve hozzá lesz fűzve a **Figyelés szolgáltatás nevéhez.**

   - Válassza ki a jelet a tevékenységnapló típusú különböző lehetséges műveletek **listájából.**

     Kiválaszthatja a naplóelőzmények idővonalát és a céljelhez tartozó riasztási logikát:

     **Feltételek hozzáadása képernyő**

     ![Feltételek hozzáadása](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  A jó minőségű és hatékony szabályok érdekében kérjük, hogy adjon hozzá legalább egy további feltételt a "Minden rendszergazda" jelzéssel rendelkező szabályokhoz. 
     > A riasztás definíciójának részeként ki kell töltenie az egyik legördülő menüt: "Eseményszint", "Állapot" vagy "Kezdeményező", és a szabály pontosabb lesz.

     - **Előzmények ideje:** A kiválasztott művelethez elérhető események az elmúlt 6, 12 vagy 24 óra vagy az elmúlt hét folyamán ábrázolhatóak.

     - **Riasztási logika:**

       - **Eseményszint:** Az esemény súlyossági szintje: _Részletes,_ _Tájékoztató,_ _Figyelmeztetés,_ _Hiba_, vagy _Kritikus._
       - **Állapot:** Az esemény állapota: _Elindítva,_ _Sikertelen,_ vagy _Sikeres._
       - **Esemény kezdeményezője:**– Más néven a hívó. A műveletet Azure Active Directory felhasználó e-mail-címe vagy azonosítója.

       Ez a minta jelgrafikon alkalmazza a riasztási logikát:

       ![Kiválasztott feltételek](media/alerts-activity-log/criteria-selected.png)

4. A **Riasztás részleteinek megadása alatt** adja meg a következő adatokat:

    - **Riasztási szabály neve:** Az új riasztási szabály neve.
    - **Leírás:** Az új riasztási szabály leírása.
    - **Riasztás mentése erőforráscsoportba:** Válassza ki azt az erőforráscsoportot, amelybe az új szabályt menteni szeretné.

5. A **Műveletcsoport** alatt a legördülő menüben adja meg az új riasztási szabályhoz hozzárendelni kívánt műveletcsoportot. Vagy [hozzon létre egy új műveletcsoportot,](./action-groups.md) és rendelje hozzá az új szabályhoz. Új csoport létrehozásához válassza az **+ Új csoport lehetőséget.**

6. Ha a létrehozás után szeretné engedélyezni a szabályokat, válassza az **Igen** lehetőséget **a Szabály engedélyezése létrehozáskor beállításhoz.**
7. Válassza a **Riasztási szabály létrehozása** lehetőséget.

    Létrejön a tevékenységnapló új riasztási szabálya, és megjelenik egy megerősítő üzenet az ablak jobb felső sarkában.

    Engedélyezheti, letilthatja, szerkesztheti vagy törölheti a szabályokat. További információ a tevékenységnapló-szabályok kezelésével kapcsolatban.


A tevékenységnaplóban létrehozható riasztási szabályok feltételeinek megértéséhez egy egyszerű hasonlat az események feltárása vagy szűrése a tevékenységnaplóban a [Azure Portal.](../essentials/activity-log.md#view-the-activity-log) A **Azure Monitor –** Tevékenységnapló képernyőn szűrheti vagy megkeresheti a szükséges eseményt, majd riasztást hozhat létre a Tevékenységnapló-riasztás **hozzáadása gombbal.** Ezután kövesse a korábban bemutatott 4–7. lépést.
    
 ![Riasztás hozzáadása a tevékenységnaplóból](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Megtekintés és kezelés a Azure Portal

1. A Azure Portal a   >  **Riasztások figyelése lehetőséget.** Válassza **a Riasztási szabályok kezelése** lehetőséget az ablak bal felső sarkában.

    ![Képernyőkép a tevékenységnaplóról, kiemelt keresőmezővel.](media/alerts-activity-log/manage-alert-rules.png)

    Megjelenik az elérhető szabályok listája.

2. Keresse meg a módosítsa tevékenységnapló-szabályt.

    ![Keresési tevékenységnapló riasztási szabályai](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    A szerkeszteni kívánt tevékenységszabály megkeresése érdekében használhatja az elérhető szűrőket _(Előfizetés,_ Erőforráscsoport, _Erőforrás,_ _Jeltípus_, vagy _Állapot)._

   > [!NOTE]
   > 
   > Csak a Leírás, **a** **Cél feltételei** és a **Műveletcsoportok szerkeszthetők.**

3. Válassza ki a szabályt, és kattintson duplán a szabálybeállítások szerkesztéséhez. Tegye meg a szükséges módosításokat, majd válassza a **Mentés lehetőséget.**

   ![Riasztási szabályok kezelése](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Engedélyezheti, letilthatja vagy törölheti a szabályokat. Válassza ki a megfelelő lehetőséget az ablak tetején, miután kiválasztotta a szabályt a 2. lépésben leírtak szerint.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Ha egy tevékenységnapló-riasztási szabályt egy Azure Resource Manager sablonnal hoz létre, hozzon létre egy típusú `microsoft.insights/activityLogAlerts` erőforrást. Ezután meg kell töltenie az összes kapcsolódó tulajdonságot. Ez a sablon egy tevékenységnapló-riasztási szabályt hoz létre:

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
Az előző JSON-minta menthető például sampleActivityLogAlert.js-ként a következő célra: , és üzembe helyezhető a Azure Resource Manager használatával [Azure Portal.](../../azure-resource-manager/templates/deploy-portal.md)

  > [!NOTE]
  > 
  > Figyelje meg, hogy a legmagasabb szintű tevékenységnapló-riasztások definiálhatóak az előfizetés.
  > Ez azt jelenti, hogy nincs lehetőség arra, hogy riasztást határozzon meg néhány előfizetéshez, ezért a definíciónak előfizetésenként riasztásnak kell lennie.

A feltételek mezőinek Azure Resource Manager-sablonban a következő mezőket használhatja: Figyelje meg, hogy az "Resource Health", az "Advisor" és az "Service Health" mezők további tulajdonságmezőkkel rendelkeznek a speciális mezőikhez. 
1. resourceId: Az érintett erőforrás erőforrás-azonosítója abban a tevékenységnapló-eseményben, amely alapján a riasztást létre kell hoznunk.
2. category: A kategóriája a tevékenységnapló eseményében. Például: Rendszergazda, SzolgáltatásBiztonság, Erőforrás-biztonság, Automatikus skálázás, Biztonság, Javaslat, Szabályzat.
3. hívó: Annak a felhasználónak az Azure Active Directory e-mail-címe vagy azonosítója, aki a tevékenységnapló-esemény műveletét végrehajtotta.
4. level: Annak a tevékenységnapló-eseménynek a szintje, amely alapján a riasztást létre kell hoznunk. Például: Kritikus, Hiba, Figyelmeztetés, Tájékoztató, Részletes.
5. operationName: A művelet neve a tevékenységnapló eseményében. Például: Microsoft.Resources/deployments/write
6. resourceGroup: Az érintett erőforrás erőforráscsoportja neve a tevékenységnapló eseményében.
7. resourceProvider: [Azure-erőforrásszolgáltatók és -típusok magyarázata.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0) Az erőforrás-szolgáltatók Azure-szolgáltatásokra való leképező listájáért lásd: Erőforrás-szolgáltatók [Azure-szolgáltatásokhoz.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)
8. status: A művelet állapotát leíró sztring a tevékenységeseményben. Például: Elindítva, Folyamatban, Sikeres, Sikertelen, Aktív, Feloldva
9. subStatus: Általában a megfelelő REST-hívás HTTP-állapotkódja, de tartalmazhat más sztringeket is, amelyek egy alállapotot írnak le.   Például: OK (HTTP-állapotkód: 200), Létrehozva (HTTP-állapotkód: 201), Elfogadva (HTTP-állapotkód: 202), Nincs tartalom (HTTP-állapotkód: 204), Hibás kérés (HTTP-állapotkód: 400), Nem található (HTTP-állapotkód: 404), ütközés (HTTP-állapotkód: 409), belső kiszolgálóhiba (HTTP-állapotkód: 500), szolgáltatás nem érhető el (HTTP-állapotkód: 503), átjáró időtúllépése (HTTP-állapotkód: 504).
10. resourceType: Az esemény által érintett erőforrás típusa. Például: Microsoft.Resources/deployments

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
A tevékenységnapló mezőivel kapcsolatos további részleteket itt [találja:](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Akár 5 percig is eltarthat, hogy az új tevékenységnapló-riasztási szabály aktívvá váljon.

## <a name="rest-api"></a>REST API 
A [Azure Monitor Tevékenységnapló-riasztások API](/rest/api/monitor/activitylogalerts) egy REST API. Teljes mértékben kompatibilis a Azure Resource Manager REST API. A PowerShellen keresztül használható a Resource Manager parancsmag vagy az Azure CLI használatával.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Az Resource Manager sablon üzembe helyezése a PowerShell használatával
Ha a PowerShell használatával üzembe Resource Manager előző Azure Resource Manager [](#azure-resource-manager-template) szakaszban látható mintasablont, használja a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

ahol a sampleActivityLogAlert.parameters.jsa riasztási szabály létrehozásához szükséges paraméterekhez megadott értékeket tartalmazza.

### <a name="use-activity-log-powershell-cmdlets"></a>Tevékenységnapló PowerShell-parancsmagok használata

A tevékenységnapló-riasztások dedikált PowerShell-parancsmagokkal érhetők el:

- [Set-AzActivityLogAlert:](/powershell/module/az.monitor/set-azactivitylogalert)Új tevékenységnapló-riasztást hoz létre, vagy frissíti a meglévő tevékenységnapló-riasztást.
- [Get-AzActivityLogAlert:](/powershell/module/az.monitor/get-azactivitylogalert)Lekért egy vagy több tevékenységnapló-riasztási erőforrást.
- [Enable-AzActivityLogAlert:](/powershell/module/az.monitor/enable-azactivitylogalert)Engedélyezi a meglévő tevékenységnapló-riasztásokat, és beállítja annak címkéit.
- [Disable-AzActivityLogAlert:](/powershell/module/az.monitor/disable-azactivitylogalert)Letilt egy meglévő tevékenységnapló-riasztást, és beállítja annak címkéit.
- [Remove-AzActivityLogAlert:](/powershell/module/az.monitor/remove-azactivitylogalert)Eltávolít egy tevékenységnapló-riasztást.

## <a name="azure-cli"></a>Azure CLI

A tevékenységnapló-riasztási szabályok kezeléséhez az [az monitor activity-log alert](/cli/azure/monitor/activity-log/alert) készlet alatt dedikált Azure CLI-parancsok érhetők el.

Új tevékenységnapló-riasztási szabály létrehozásához használja az alábbi parancsokat ebben a sorrendben:

1. [az monitor activity-log alert create:](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_create)Hozzon létre egy új tevékenységnapló-riasztási szabály erőforrást.
1. [az monitor activity-log alert scope:](/cli/azure/monitor/activity-log/alert/scope)Hatókör hozzáadása a létrehozott tevékenységnapló-riasztási szabályhoz.
1. [az monitor activity-log alert action-group:](/cli/azure/monitor/activity-log/alert/action-group)Adjon hozzá egy műveletcsoportot a tevékenységnapló-riasztási szabályhoz.

Egy tevékenységnapló-riasztási szabály erőforrásának lekéréséhez használja az [az monitor activity-log alert show Azure CLI-parancsot.](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_show
) Az erőforráscsoportban található összes tevékenységnapló-riasztási szabály erőforrásának megtekintéséhez használja [az az monitor activity-log alert list et.](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_list)
A tevékenységnapló-riasztási szabályok erőforrásai az [az monitor activity-log alert delete Azure CLI-paranccsal távolíthatók el.](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_delete)

## <a name="next-steps"></a>Következő lépések

- A [tevékenységnaplók webhooksémának ismertetése.](./activity-log-alerts-webhook.md)
- Olvassa el [a tevékenységnaplók áttekintését.](./activity-log-alerts.md)
- További információ a [műveletcsoportokról:](./action-groups.md).  
- Tudnivalók a [szolgáltatás állapotértesítéseiről.](../../service-health/service-notifications.md)
